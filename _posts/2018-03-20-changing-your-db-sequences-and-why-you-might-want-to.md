---
layout: post
title: "Changing Your DB Sequences and Why You Might Want To"
modified:
categories: "postgres", "ops"
excerpt:
tags: ["postgres", "pg", "ops"]
image:
  feature:
date: 2018-03-20T17:59:13-05:00
---

Postgres allows you to modify the sequences that set your table ids. I had never needed to mess with these before and it was pretty neat, so I wanted to share how we changed our sequences and why.

The command to alter your sequence is pretty simple:
```sql
alter sequence "#{sequence_name}" increment 10 restart with #{base};
```

You just need to supply the sequence name, a value to increment by and a base value for the next id in the sequence.

The sequence name is the name of the table + `_id_seq` by default. The easiest way to look at your sequences is to open a `psql` console and type `\d`. This will show you something like this:

```sql
test_database=# \d
                            List of relations
 Schema |                   Name                    |   Type   |  Owner
--------+-------------------------------------------+----------+----------
 public | assets                                     | table    | dluchi
 public | assets_id_seq                              | sequence | dluchi
 public | organizations                              | table    | dluchi
 public | organizations_id_seq                       | sequence | dluchi
 public | users                                      | table    | dluchi
 public | users_id_seq                               | sequence | dluchi
```

Here is a stripped down version with only a tables so you can get the idea. We have an `assets ` table with a `assets_id_seq` sequence, an `organizations` table with a `organizations_id_seq` sequence, and a `users` table with a `users_id_seq`.

Note that I am playing around with this in my `test_database` so I can easily drop and recreate it if I screw something up.

You can inspect any of these sequences to get some information about it like so 
```sql
test_database=# select * from users_id_seq;
 sequence_name | last_value | start_value | increment_by |      max_value      | min_value | cache_value | log_cnt | is_cycled | is_called
---------------+------------+-------------+--------------+---------------------+-----------+-------------+---------+-----------+-----------
 users_id_seq   |         14 |           1 |            1 | 9223372036854775807 |         1 |           1 |      32 | f         | t
(1 row)
```

You’ll see I’m using 14 of my 9 quintillion ids and that I’m currently incrementing by 1. Totally normal.

And now, for the fun part: let’s change them.

We recently spun up our infrastructure in two new regions: Dublin and Sydney and were thinking of ways to make migrating accounts between those possible if we need to do it in the future. We considered [multi-tenancy with postgres schemas](https://influitive.io/our-multi-tenancy-journey-with-postgres-schemas-and-apartment-6ecda151a21f) and [converting our primary keys to UUIDs](https://tomharrisonjr.com/uuid-or-guid-as-primary-keys-be-careful-7b2aa3dcb439) before deciding on this approach: increment our keys by 10 and create a unique offset for each region.

Here is the migration we ended up with:
```ruby
up do
  base = case ENV['AWS_DEFAULT_REGION']
         when 'eu-west-1'
           1_000_001
         when 'ap-southeast-2'
           1_000_002
         else
           1_000_000
         end

  tables.each do |table|
    sql = <<~SQL
      alter sequence "#{table}_id_seq" increment 10 restart with #{base};
    SQL
    run(sql)
  end
end

down do
  tables.each do |table|
    max = select(:id).from(table).order(:id).last.try(:[], :id) || 0
    sql = <<~SQL
      alter sequence "#{table}_id_seq" increment 1 restart with #{max+1};
    SQL
    run(sql)
  end
end
```

The `up` restarts the sequences for all of the tables at 1,000,000 + an offset for each of our two new regions and the `down` migration, resets the sequences to increment by 1 and restart at the current max value for the table.

It is very important that your base value is higher than the largest value in your largest table. Our largest id in any table is only ~240,000, so we’ve given ourselves plenty of headroom by starting at 1 million.

The down migration is never intended to be run except for local development environments.

I’ll talk more holistically about our cross region account migration plans another time, but this will give us unique IDs for each region and allow data to be moved from region to region safely with a `pg_dump` and `pg_restore`.

One additional word of caution: this will only modify existing tables and there is no way that I know of to set defaults for new sequences that will be created for new tables. So any time you create a new table, you’ll need to remember to change the sequence when you create the table. I recommend creating a script of some kind to raise a flag in your build process whenever someone creates a new table to remind them to do this.

Its also possible that you can modify something deep in the bowels of your ORM to handle this for you. If you use [Sequel](http://sequel.jeremyevans.net/) and know how to do this. Get in touch!

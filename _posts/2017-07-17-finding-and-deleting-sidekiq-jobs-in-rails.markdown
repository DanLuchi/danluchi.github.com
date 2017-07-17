---
layout: post
title: "Finding and Deleting Sidekiq Jobs in Rails"
modified:
categories: "sidekiq"
excerpt:
tags: ["sidekiq", "code snippets", "rails"]
image:
  feature:
date: 2017-07-17T11:42:13-04:00
---

Lately, I've been working a lot with background jobs running in Sidekiq. On several occasions, I needed to dig deeper into my job queues than what is practical to do through the sidekiq dashboard.

Fortunately, Sidekiq has a [great API](https://github.com/mperham/sidekiq/wiki/API) thats makes diving deeper into your queues super easy.

### Finding all of the jobs in a particular queue

#### Regular Queues

Here is a snippet that a put together for finding all a particular kind of job in a queue

from console:
```ruby
queue = Sidekiq::Queue.new("default")
jobs = queue.map do |job|
  if job.klass == '[JOB_CLASS]'
    {job_id: job.jid, job_klass: job.klass, arguments: job.args}
  end
end.compact
```

If you have a lot of jobs, you can use `jobs` to get more information. For example, you might not care about each job individually, just the count, so you can use `jobs.count` or you might want to only look at a subset of those jobs and you can use `jobs.select` or any method of slicing Ruby arrays.

#### Retry Queue

If you want jobs from the retry queue, you can do something similar like this:

from console:
```ruby
retries = Sidekiq::RetrySet.new.select
jobs = retries.map do |job|
  if job.klass == '[JOB_CLASS]'
    {job_id: job.jid, job_klass: job.klass, arguments: job.args}
  end
end.compact
```

#### Scheduled Queue

And again with the scheduled queue like this:

from console:
```ruby
scheduled = Sidekiq::ScheduledSet.new.select
jobs = scheduled.map do |job|
  if job.klass == '[JOB_CLASS]'
    {job_id: job.jid, job_klass: job.klass, arguments: job.args}
  end
end.compact
```

### Deleting Jobs

To delete jobs, we'll use the same snippet to the ones above.

from console:
```ruby
queue = Sidekiq::Queue.new("default")
jobs = queue.map do |job|
  if job.klass == '[JOB_CLASS]'
    # job.delete
    {job_id: job.jid, job_klass: job.klass, arguments: job.args}
  end
end.compact
```
Note that I have `job.delete` commented out in the above snippet. When deleting jobs from sidekiq, I always do a dry run and interrogate the results to make sure they are what I expect before deleting the jobs. I also like to save the output containing the class and arguments in case I've deleted something in error and need to reenqueue it.



If you need to do something more complicated I encourage you to check out the [documentation for the Sidekiq API](https://github.com/mperham/sidekiq/wiki/API). It's very well put together.



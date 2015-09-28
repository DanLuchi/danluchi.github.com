---
layout: post
title: "What I'm Asking When I Ask You to Review My Pull Request"
modified:
categories: "pull requests"
excerpt:
tags: ["pull_requests"]
image:
  feature:
date: 2014-12-22T14:36:13-04:00
---

> It took me a long time to warm up to pull requests. My early experiences with code review were all a tremendous waste of time: they were a bottleneck to getting things done, a forum for people to conduct their ongoing feuds or both. After years of being staunchly against pull requests, I've come around on them in the last year at Terrible Labs.

## What I'm asking when I ask you to review my pull request

### 1. Does this make sense to you?

Start with the big picture and get more detailed from there.

My pull request should be trying to accomplish only one thing and that should be explained in enough detail in the commit message for you to understand, did I succeed? If something is confusing: you're not sure how this system works, or how this new piece of code fits in with other parts of the system, let me know and we can clarify it together.

Do the objects and methods being used make sense? Am I adding too much responsiblity to an existing object or creating a new object where one with similar functionality already exists?

Does the code in each method make sense and do the names of each method describe what it is doing?

### 2. Did I miss anything?

Sometimes I move a little too fast and I miss things. Could you double check and make sure everything looks in order?

Here are a few things that I tend to miss:
* tests
* database indexes
* checking in the updated database schema
* documentation

### 3. Was there anything that you really liked about this code?

Occasionally someone will comment on a pull request I've made and point out a piece of code, a pattern or something else they really like and its the most amazing feeling in the world. This happens far too infrequently. If you see something you like, call it out! Not only does it make me feel warm and fuzzy inside and like you a lot, it highlights cool code for everyone else on the team.

### 4. Is this ready to go?

If everything looks good and you don't have anything else to add, throw a `:+1` in the comments so I know its ready to do.

## A few things that kind of annoy me

### 1. Telling me that you would have done this differently

I often think this when reviewing other people's pull requests and I don't always remember this piece of my own advice. The thing is that this is really demoralizing to hear someone say to you and its totally unhelpful. If the two approaches are entirely equivalant, you should just go with the one that they have already implemented, tested and have working.

Chances are, however that each approach has some advantages and disadvantages over the other and these are useful to point out. The easy thing to do is just to fire off a comment saying, "I think you should have done it like this instead", but it sucks to be on the receiving end of a comment like that. Instead, take the time to really understand the two approaches: your's and their's and take it upon yourself to figure out what if any advantages your approach would have that can be incorporated into what they've already done. Maybe even go so far as to submit another pull request with some proposed changes.

### 2. Non-actionable lukewarm feelings

Youre going to come across some code and think, "I don't love this, but I don't know what to do about it." I suggest not saying this. In my experience, it just makes everyone feel bad and leaves you in a weird limbo state that you're not sure how to proceed from. Do you keep trying to rework that code and resubmit the pull request until you stumble upon something that is satisfactory to the reviewer? Do you move on to another task? Is this pull request ready to be merged? No one knows.

### 3. Pointing out violations of our styleguide

I understand that having a consistent style in your code is valuable and it is important to maintain certain aspects. I'll write another post with my feeling on styleguides, but please understand that this is NOT what provides value in reviewing a pull request.


Do you have anything else that you want or don't want from a pull request / code review? Let me know!

> This was originally posted as a [gist](https://gist.github.com/DanLuchi/3741a957a41818159b44) with excellent comments by [Thomas Mayfield](thegreata.pe) and [Mike Denomy](mdenomy.wordpress.com). [Check it out!](https://gist.github.com/DanLuchi/3741a957a41818159b44)
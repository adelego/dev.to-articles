---
published: false
title: 'CDK Scheduler'
cover_image:
description: 'Description of the article'
tags: serverless, scheduling, cdk, cdk-construct
series:
canonical_url:
---

# A serverless solution to precise scheduling

TLDR; We published a CDK construct to schedule events precisely.
Documentation is available on [the project's github](https://github.com/guiyom-e/cdk-scheduler)

When I worked on a serverless CMS with Guillaume, we had to find a way to schedule an event (publishing a new article) at a precise time with serverless tools.
AWS doesn't offer a built-in service to do so.
We found straight-forward options but none are cost-effective and precise.
We came up with a solution of our own.
We published a CDK construct for you to add our solution to your stack without boilerplate.

## Naive approaches to serverless scheduling

### Scheduling a lambda to run every minute

A first approach is to use lambda schedule trigger feature.
Lambdas can be triggered every down to every minute.
Every minute, you can query your database to find if any events need to be published now.
If so, run the action you planned (like publishing an article), else do nothing.

![Trigger a lambda every minute to check if any events needs to be scheduled](./assets/naive-approach-lambda.jpg)

This solution comes with two downsides: it's not very precise and expensive.
A lambda can be triggered down to every minute an d the trigger time has a one-minute precision guarantee.
It's good enough for some use-cases but not all.
You will also have to pay for execution time every minute. If you have chosen serverless to reduce compute cost, you will want to reduce that.

### Using DynamoDB time-to-live and streams

DynamoDB comes with a time-to-live feature.
You can add a property ttl attribute to an entry.
At said time, AWS will remove the entry form your table.
If you have a stream plugged to your table you can play an action at scheduled time.

Although this approach reduces cost, it much less precise.
AWS only guarantees a 48 hours precision on the time-to-live date.
In effect, users observe a 10-minute delay but it's not a guarantee.
If like us you work on time-sensitive events, you will need to avoid this practice.

![Use Dynaomdb time-to-live feature to trigger events with one-day precision](./assets/naive-approach-ttl.jpg)

# Introducing cdk-scheduler

To match our ambition to be both cheap and precise, we leveraged the SQS delay feature.
When publishing on an SQS queue you can set a delay that can go up to 15 minutes.

![cdk-scheduler leverages SQS delay feature to trigger events precisely](./assets/cdk-scheduler-architecture.jpg)

Instead of starting a lambda every minute, we can do it every 15 minutes.
This method has a 1-second precision gap!

In case you're in hurry to schedule, we also added a "near-future" handler.
This second lambda is plugged on a dynamoDB stream and handles events that are to be scheduled in less than fifteen minutes after their creation.

Cost-wise, we divided our bill by (almost) 15 compared to the first option.

## What's next?

Our construct is now published.
It fits the need we had when we developed a CMS like a glove.
We're now looking for your feedback?
What do you need when scheduling an event?
What configuration or features would you like to see?

---
published: false
title: 'CDK Scheduler'
cover_image:
description: 'Description of the article'
tags: serverless, scheduling, cdk, cdk-construct
series:
canonical_url:
---

# CDK Scheduler -- Insert catchy title here

When Guillaume worked on a serverless CMS, he had to find a way to schedule an event (publishing a new article) at a precise time with serverless tools. AWS doesn't offer a built-in service to do so. There are straight-forward options but none are cost-effective and precise. We published a CDK construct that is both for you to add to your stack.

# Naive approaches to serverless scheduling

## Scheduling a lambda to run every minute

A first approach is to use lambda schedule trigger feature. Lambdas can be triggered every down to every minute. Every minute, you can query your database to find if any events need to be published now. If so, run the action you planned (like publishing an article), else do nothing.

![Trigger a lambda every minute to check if any events needs to be scheduled](./assets/naive-approach-lambda.jpg)

This solution comes with two downsides : it's not very precise and expensive. A lambda can be triggered down to every minute an d the trigger time has a one-minute precision guarantee. It's good enough for some use-cases but not all. You will also have to pay for execution time every minute. If you have chosen serverless to reduce compute cost, you will want to reduce that.

## DynamoDB TTL

Utiliser les ttl - Limitations: ok mais niveau précision, c naze Schedule une lambda toute sles minutes

# Entrée fracassante de cdk-scheduler

Comment ça marche : leverage SQS delay feature

Schéma d'archi qui pue le style

# Next steps

Dead letter queue

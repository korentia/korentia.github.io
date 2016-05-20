---
layout: post
name: rethinking-the-testing-pyramid-2
title: 'Rethinking the testing pyramid (part II)'
date: 2016-05-20 09:00:00 +00:00
author: Franziska Sauerwein
image:
    src: "/assets/img/custom/blog/2016-05-20-rethinking-the-testing-pyramid/template.png"
tags:
- test
- testing-pyramid
- unit-test
- integration-test
- scope
- system-test
---

In [part I](http://codurance.com/2016/05/20/rethinking-the-testing-pyramid-1/), I talked about the problems I see in applying the testing pyramid.
As stated there, for the purpose of this post, I will focus on automated tests supporting the team (see Lisa Crispin's [Testing Quadrants](http://lisacrispin.com/2011/11/08/using-the-agile-testing-quadrants/)).

## A template to follow
When I started working at [Codurance](http://codurance.com/), we had a discussion with [Sandro](https://twitter.com/sandromancuso) about the lack of common understanding. There are formal definitions and there is a lot of literature that defines the terms, but it's not very fruitful to keep using those fuzzy terms in your team and assuming that everyone knows what they mean.
He gave us advise and a tool to use when we'd find ourselves in that situation.
If you want, you can take the following template to start a conversation in your team as well. I laid down the components of a sample application - draw the components of YOUR application on a whiteboard instead and start discussing!
<img src="/assets/img/custom/blog/2016-05-20-rethinking-the-testing-pyramid/template.png"/>
Our sample application has a database it depends on, a UI through which requests are made and external services that are used by the core business logic.

### Database
<img src="/assets/img/custom/blog/2016-05-20-rethinking-the-testing-pyramid/repositories.png"/>
Let's get started by testing all repositories we are using. Using the real DB, we make a save and load for each repository to make sure it is wired up correctly. It is feasible to have a simple CRUD repository doing that connection, as we do not need to implicitly include business rules in our tests. If we are using a library or a framework to translate from code to DB calls it's good to avoid re-testing functionality the framework provides. We want to have one test verifying our configurations or any adjustments we made to the library.
If we have any DB-specific features like stored procedures or custom SQL queries, these also need to be tested against the real DB. In this case, we can make our tests run faster on the local environment by configuring an in memory DB like H2 or HSQL. The same code with a different configuration can be tested on the build server against a production like database to catch DB-specific edge cases.
We connect our repositories to the business layer using a service class, which we test independently with a mocked repository.
<img src="/assets/img/custom/blog/2016-05-20-rethinking-the-testing-pyramid/db-services.png"/>

### Frontend
<img src="/assets/img/custom/blog/2016-05-20-rethinking-the-testing-pyramid/frontend-services.png"/>
Using automated tests, we call our business from the outside API as the UI would. By separating the concerns of the UI, we work independently of representation in these tests. The test covers that the backend services are called correctly from a REST API or similar.
<img src="/assets/img/custom/blog/2016-05-20-rethinking-the-testing-pyramid/frontend-features.png"/>
Using automated UI-tests, we test the features of our UI against static data. We focus on getting the connection to the API and the population of the presented content right. We do not worry about changes in the returned data.

### External Applications

### Sanity test
One test to rule them all
makes you sleep at night
finds wrong configurations / connections

## Duplication in test scope
What happens when it breaks
Isolate reason for test failures quickly


## A few other helpful measures to reduce work in test maintenance
- builders
- hexagonal architecture
- dynamically changing dependencies to mocked implementations and real ones / separate test suites

I'd love to hear your opinion - let me know about your experiences on [Twitter](https://twitter.com/Singsalad) or the [Global Software Craftsmanship Slack](slack.softwarecraftsmanship.org).

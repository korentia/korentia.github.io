---
layout: post
name: rethinking-the-testing-pyramid
title: 'Rethinking the testing pyramid'
date: 2015-12-28 09:00:00 +00:00
author: Franziska Sauerwein
image:
    src: "/assets/img/custom/blog/2015-12-28-rethinking-the-testing-pyramid/title.png"
tags:
- test
- testing-pyramid
- unit-test
- integration-test
- scope
- system-test
---

## A naive assumption
Whenever I start working in a new team, there is a discussion about how to cover the application with tests. I used to naively think simply following [MC.'s testig pyramid](https://www.mountaingoatsoftware.com/blog/the-forgotten-layer-of-the-test-automation-pyramid) would do the trick - surely everyone can agree that unit tests are more feasible than integration tests? And surely, everyone would follow the definitions on what an integration test is to the book?

## A constant challenge
Reality is a different story though. As I worked on different applications, I came across large suits of tests depending on the database and external services, UI-tests trying to cover business software, or even "automated tests" that wouldn't succeed unless you remembered a couple of manual steps to make them work first.
As a result, whenever we tried to improve our design or put a new feature in, we had to fix a myriad of those tests, where we sometimes didn't even remember why they were there in the first place. Yet we continued to introduce new ones with every feature or bug we found, not seeing how we were making our own lives harder.

## A convenient excuse
Often, we would justify why we needed those tests with "rational" points:

- "the only way to test workflows with the framework we are using is to run them against all real dependencies" (not true!)
- "it is too complicated to have an in-memory DB alongside the real one" (maybe still worth it?)
- "we wouldn't have caught that bug without those tests!" (maybe there is something wrong with our design instead?)

This list could continue for a while - fact of the matter is - we are not thinking systematically about what tests we actually need. We are not making informed decisions about whether to introduce a new, expensive test. We do not invest enough time to build a test suite for our business logic that is independent of external services or the database.

## An underlying assumption
Even having a discussion in your team about scopes of tests might prove difficult. At [SoCraTes Germany](https://www.socrates-conference.de/), [Thomas Bunzendorf]() did an experiment: Let everyone draw the testing pyramid as they see it. The results varied and we had different ideas about what terms like "integration test" or "acceptance test" meant. Of course, you can try to go by definitions found in books, but in the end the terms do not matter as much as agreeing in your team on them.

## A clarification
In this blogpost, I talk about tests that support the team and can be automated (see Lisa Crispin's [Testing Quadrants]() below). Other tests are out of scope for this post.

## An ideal to strive for
Ideally, I'd have a testing pyramid structured like this:
<img src="/assets/img/custom/blog/2015-12-28-rethinking-the-testing-pyramid/unit-pyramid.png"/>

- integration tests are a scam

## A couple of reasons for deviating from this testing pyramid
- Applications not developed in a TDD style, save refactoring
- Hard to understand requirements

## A template to follow
When I started working at [Codurance](http://codurance.com/), we had a discussion with [Sandro](https://twitter.com/sandromancuso) about this problem. He gave us advise and a tool to use when we'd find ourselves in that situation.
If you want, you can take the following template to start a conversation in your team as well. I laid down the components of a sample application - draw the components of your application on a whiteboard instead and start discussing!
<img src="/assets/img/custom/blog/2015-12-28-rethinking-the-testing-pyramid/template.png"/>
Our sample application has a database it depends on, a UI through which requests are made and external services that are used by the core business logic.

### Database
<img src="/assets/img/custom/blog/2015-12-28-rethinking-the-testing-pyramid/repositories.png"/>
Let's get started by testing all repositories we are using. Using the real DB, we make a save and load for each repository to make sure it is wired up correctly. It is feasible to have a simple CRUD repository doing that connection, as we do not need to implicitly include business rules in our tests. If we are using a library or a framework to translate from code to DB calls it's good to avoid re-testing functionality the framework provides. We want to have one test verifying our configurations or any adjustments we made to the library.
If we have any DB-specific features like stored procedures, these also need to be tested against the real DB.
We connect our repositories to the business layer using a service class, which we test independently with a mocked repository.
<img src="/assets/img/custom/blog/2015-12-28-rethinking-the-testing-pyramid/db-services.png"/>

### Frontend
<img src="/assets/img/custom/blog/2015-12-28-rethinking-the-testing-pyramid/frontend-services.png"/>
Using automated tests, we call our business from the outside API as the UI would. By separating the concerns of the UI, we work independently of representation in these tests. The test covers that the backend services are called correctly from a REST API or similar.
<img src="/assets/img/custom/blog/2015-12-28-rethinking-the-testing-pyramid/frontend-features.png"/>
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

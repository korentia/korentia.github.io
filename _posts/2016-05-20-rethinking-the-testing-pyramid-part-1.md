---
layout: post
name: rethinking-the-testing-pyramid-1
title: 'Rethinking the testing pyramid (part I)'
date: 2016-05-20 09:00:00 +00:00
author: Franziska Sauerwein
image:
    src: "/assets/img/custom/blog/2016-05-20-rethinking-the-testing-pyramid/title.png"
tags:
- test
- testing-pyramid
- unit-test
- integration-test
- scope
- system-test
---

## A naive assumption
Whenever I start working in a new team, there is a discussion about how to cover the application with tests. I used to naively think simply following [Mike Cohn's testing pyramid](https://www.mountaingoatsoftware.com/blog/the-forgotten-layer-of-the-test-automation-pyramid) would do the trick - surely everyone can agree that unit tests are more feasible than integration tests? And surely, everyone would follow the definitions on what an integration test is to the book?

## A constant challenge
Reality is a different story though. As I worked on different applications, I came across large suits of tests depending on the database and external services, UI-tests trying to cover business software, or even "automated tests" that wouldn't succeed unless you remembered a couple of manual steps to make them work first.
As a result, whenever we tried to improve our design or put a new feature in, we had to fix a myriad of those tests, where we sometimes didn't even remember why they were there in the first place. Yet we continued to introduce new ones with every feature or bug we found, not seeing how we were making our own lives harder.

## A convenient excuse
Often, we would justify why we needed those tests with "rational" points:

- "the only way to test workflows with the framework we are using is to run them against all real dependencies" (this wasn't actually true!)
- "it is too complicated to have an in-memory DB alongside the real one" (maybe it still would have been worth it?)
- "we wouldn't have caught that bug without those tests!" (maybe there was something wrong with our design?)

This list could continue for a while. The fact of the matter is - we were not thinking systematically about what tests we actually need. We were not making informed decisions about whether to introduce a new, expensive test. We did not invest enough time to build a test suite for our business logic that was independent of external services or the database.

## A clarification
In this blogpost, I talk about tests that support the team and can be automated (see Lisa Crispin's [Testing Quadrants](http://lisacrispin.com/2011/11/08/using-the-agile-testing-quadrants/) below). Other tests are out of scope for this post.
<img src="/assets/img/custom/blog/2016-05-20-rethinking-the-testing-pyramid/Agile-Testing-Quadrants.png"/>

## An underlying assumption
Even having a discussion in your team about scopes of tests might prove difficult. At [SoCraTes Germany 2015](https://www.socrates-conference.de/), Thorsten Brunzendorf conducted an [experiment](https://twitter.com/thbrunzendorf/status/637313127231660033): Let everyone draw the testing pyramid as they remember it. Unsurprisingly, the results varied and we had different ideas about what terms like "integration test" or "acceptance test" meant. Of course, you can try to go by definitions found in books. However, the terms will not matter as much as agreeing in your team on them.

## A zoo of possibilities
Here are some of the results we gathered in that session:

### A functional testing pyramid
<img src="/assets/img/custom/blog/2016-05-20-rethinking-the-testing-pyramid/types-and-pb-testing.png"/>
Some people prefer to combine functional programming with a strong type system, letting the compiler handle most of the constraints and contracts in your business logic. You can use types to build an [unbreakable domain model](https://youtu.be/ZJ63ltuwMaE?list=PL_aPVo2HeGF-7o9SPO5arFrAaU8bcIjba).
This is supported by verifying your algorithms using [Property based testing](http://blog.jessitron.com/2013/04/property-based-testing-what-is-it.html). Although this approach works well together with functional programming, the same considerations that should be used to decide whether to use functional concepts in the first place apply. Are you testing behaviour or algorithms? Do you have side effects that need to be tested? Deciding whether you prefer a dynamic or static typing is a whole different discussion.
Ultimately, most developers agree that types can't completely replace automated testing.

### Another axis
<img src="/assets/img/custom/blog/2016-05-20-rethinking-the-testing-pyramid/separate-business.png"/>
There might also a case to be made for explicitly clarifying the purpose of the tests by separating them along wether they are business related or technical. Some tests might help understand and document business requirements while others show that technical components work the way they are supposed to. The latter might make sense from a design point of view but not from a business point of view. Business relevant tests (some people call them acceptance tests) don't necessarily define the scope of the test. They can be situated on all levels of the classic testing pyramid, from covering a single unit to a whole process.
Be careful about purely technical classes like `DateUtil` that show you are missing a domain concept. Some parts of your system might have a technical concept as the domain, but in most cases the business focus is on something different.

### A reason to run away
<img src="/assets/img/custom/blog/2016-05-20-rethinking-the-testing-pyramid/antipatterns.png"/>
There surely are some testing pyramids that make me cringe. Be it the ice cream cone, turning the classic pyramid on it's head and running an exhaustive manual testing plan (if there is one), combined with maintaining a huge amount of tests that go through and are dependent on the UI. Your application's log in screen might be the best covered part of your application with every UI test starting of there, but beware when it changes and you have to update all of them.

## An ideal to strive for
Ideally, I'd prefer a testing pyramid structured like this:
<img src="/assets/img/custom/blog/2016-05-20-rethinking-the-testing-pyramid/unit-pyramid.png"/>
The bulk of my tests are unit tests, running fast, covering the business logic, running independently of external resources like databases, services and the clock. Yes, I believe tests that depend on time are not unit tests!
Only a couple of integrated tests make sure that my application is wired together correctly and the connections to external resources are set up.
You can read more about why too many integrated tests are a bad idea from [J. B. Rainsberger](http://blog.thecodewhisperer.com/permalink/integrated-tests-are-a-scam) as I pretty much completely agree :).

## A couple of reasons for deviating from this testing pyramid
There are cases where I have deviated from that pyramid. The most common case is when you have code that was not developed using TDD and has a bad structure. I try to cover the parts of the code I want to refactor with automated tests before refactoring it. Ideally, I would TDD a new design alongside the old and replace it gradually. In some cases, I have critical business logic I can not immediately refactor. I try to cover it with larger scoped tests to ensure it stays functional until that time comes. If you try to unit test this kind of code, you have to bend over backwards and most of those tests won't survive the refactoring.
Another good case for larger scoped tests are hard to understand business requirements.
Testing a combination of units that work together to fulfill a business need using concrete input / output examples can help verify assumptions and discover misunderstandings.
Ideally, those tests should not depend on external resources and run fast. Having your dependencies structured using [Hexagonal architecture](http://alistair.cockburn.us/Hexagonal+architecture) can help with that.

## A conclusion
The design decisions you make influence the automated testing strategy you should choose and vice versa. Defining your testing strategy and being familiar with testing techniques that support your development is an important aspect of building quality software.
Continue reading in [part II](rethinking-the-testing-pyramid-2) to find out how to have a conversation in your team about the scope of your tests.

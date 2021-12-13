# testing_in_scrum

## Introduction or what is so special about QA in Scrum?


Out of many there is one aspect of Scrum which makes it especially interesting and efficient methodology.

[Scrum guide](https://scrumguides.org/scrum-guide.html) defines something called potentially shippable product (see remark below) as an output of a sprint (also called an increment).

To understand why it is so important we need to realize that Scrum is nothing more than a baby steps approach to software development. Every sprint we are adding an increment to what we have created before.



*Remark: in recent versions of scrum guide potentially shippable product term was removed which is a mistake in my opinion. The concept is still valid but guide itself is not as expressive as it was in the past.*

## What does it mean that an increment is potentially shippable?


In a nutshell it means that it is releasable [(high quality, well tested, complete)](https://www.mountaingoatsoftware.com/blog/what-does-it-mean-to-be-potentially-releasable). It means that technically we should be ready to release it at any time (every sprint).

But it does not mean we have to! It can be incomplete from the business point of view (no feature of high business value present in an increment).

Potentially shippable increment requirement defined in the Scrum guide has a huge impact on the way we do planning and QA. On top of it baby steps approach enforces frequent changes to the product itself.

To achieve desired result we need to be much more efficient (in terms of QA/testing) than in case of any other methodology because sprint duration last only 2-3 weeks and number of tests grows with our code base!

The only known way to do it is to introduce [Continues Integration](https://less.works/less/technical-excellence/continuous-integration). This also means that there is no Scrum without CI.

## What is the purpose of a CI?


CI process simply allows us to integrate frequently (baby steps). When automated and supported by appropriate tools it can guarantee that code quality does not decrease with each new code change integration.

We now know how to take care about old code quality (the one which is already present in our repository).

How to take care about the new code? There are at least 2 ways. One possibility is to do it via peer review (at pull/merge request time). Although it may [not be desired](https://less.works/less/technical-excellence/continuous-integration#DosandDonts) when following [trunk based development](https://trunkbaseddevelopment.com/).

Second possibility is to cover it with tests which can be included in the CI. In other words introduce in-sprint automation!

This is why from CI process point of view we should ensure that every time we integrate new feature (new code) it contains desired test coverage (appropriate test implementation).

Technically this can be achieved by better planning (splitting bigger tasks to as small steps as possible). Introducing code changes on a short-lived feature branch and tests either on the same branch or as an additional PR to this particular feature branch.

If we allow to integrate new product code without desired test coverage CI can't serve its purpose.

## CI properties


Our CI to be efficient should have two properties.

First of all it should be green (always). Only in such case, when there is a failure, we are sure that it is the last change that caused it (no need for long lasting RCA). We can do a fallback, fix it and integrate it again.

Second of all it should run as often as possible (minimum at a PR level). To make it happen we need to ensure that our CI should be quick. [Optimal time is ~10 minutes](https://www.industriallogic.com/blog/how-long-is-too-long-for-a-build/).

To achieve it a single repository should be shared to host product code and test code. This means we should follow so called [trunk based or git flow](https://www.toptal.com/software/trunk-based-development-git-flow) development model.

Typically Git flow is implemented. A dedicated feature branch is used for every new implementation (including tests).

## What can we use to make our CI work as desired?


To guarantee every feature is covered with tests a [Test Driven Development](http://agiledata.org/essays/tdd.html) approach may be used. Before actual product code changes a minimum set of tests should be implemented first.

When product code is added tests are starting to pass indicating that particular feature is implemented as desired. In other words if you do not follow test first approach you do it wrong.

As additional advantage TDD makes it easier to see certain problems related to requirements engineering.

As an example it can be seen during interviews with developers that  to a question 'what do you think about TDD?' we get an answer like 'it sounds cool but I am often asked to start development without understanding what should be tested' or 'I have implemented a feature but during review (sprint's demo) client says something should be done a bit differently'.

To address such issues so called [Behavior Driven Development](https://dannorth.net/introducing-bdd/) was invented. It is an addon which can be put on top of TDD.

It is focusing on requirements analysis by ensuring that all parties are involved very early in requirements clarification. It introduces common language to describe requirements, thus increasing probability of same understanding across the team (language is called [Geherkin](https://cucumber.io/docs/gherkin/reference/)).

In case of TDD an engineer usually thinks first in terms of how to test certain requirement. In BDD he thinks in terms of what behaviors system should implement. Test descriptions become system behavior descriptions. This makes it possible to express requirements through tests (in BDD called acceptance criteria). In other words if we do not know what should be tested requirements need to be clarified.

Let us dig into that further. BDD emphasize BA (or even PO) involvement into test design (acceptance criteria) which can increase the business value of our tests. Why? Because tests can be designed by someone who knows how our system should behave. In any other case we risk that a test design will reflect an opinion of team member called ‘tester’ which is valued only because it is independent.

Considering the fact that tests with zero business value still have maintenance costs [we should avoid creating them](https://rbcs-us.com/documents/Why-Most-Unit-Testing-is-Waste.pdf).

A process which can help to address challenges of analysis called [3 amigos](https://www.agilealliance.org/glossary/three-amigos/) (periodical meeting between BA, Dev, QA to discuss requirements, tasks, risks, ideas) and [examples mapping](https://cucumber.io/blog/bdd/example-mapping-introduction/) (a 3 amigos discussion facilitation technique).



Next tool or rather a guideline we have at hand is called [Test Automation Pyramid](https://testing.googleblog.com/2015/04/just-say-no-to-more-end-to-end-tests.html). When followed it can ensure that our CI will be optimized in terms of speed and resources usage.

In this concept the highest number of tests is at the bottom of an artificial pyramid. Such tests should be unit/component tests which are using mocks to speed up execution/resource usage. The lowest number of tests is at the top of the pyramid. Such tests are usually slowest and the most resources hungry (e2e tests required to setup a full test environment). Component/Integration tests are in the middle layer of the pyramid.

Another concept which perfectly blends with our test pyramid is called a [depth of test](http://fabiopereira.me/blog/2012/03/18/introducing-depth-of-test-dot/).

Depth of Test (DOT) is the distance between the nearest and farthest software components that get visited during the execution of a test

It simply says to keep test near the code which is tested. We shouldn't require to setup web UI to tests basic operations of our app which do not require it. In same way we shouldn’t write a test with DB when only for example certain calculation logic is to be checked.



To further enhance execution speed and achieve in sprint automation so called [contract testing](https://blog.thecodewhisperer.com/permalink/getting-started-with-contract-tests) can be involved. In this concept test can utilized mocks. Their (mocks) correctness is guarded by a contract thus such tests can check system integrations without requiring us to setup fully integrated environment.

This allows to execute them quicker and earlier in the pipeline but at the same time it requires more synchronization between different teams implementing different solution/system components. Single integration test is split into 2 parts which can run as part of different CI job.

Contracts can be used also to ensure that tests implementation can be started in parallel to product feature implementation. For [example](https://www.linkedin.com/pulse/smarter-way-doing-automation-within-sprint-5-min-read-husny-jiffry/) in case of UI a wireframes can be shared between tester and dev by UI/UX designer thus Page Object Model and tests covering new functionality implementation can be immediately started.

In case of REST API multiple tools exists to generate API clients, tests templates and an interactive documentation (see [Swagger](https://swagger.io/), [openApi](https://www.openapis.org/) and [pact](https://docs.pact.io/)).



*REMARK: Unit tests typically focusing on validation of a function. In case of OOP we can assume that they should focus on particular class behaviours validation (public methods of a class). In practice single application feature utilizes not one but many classes thus technically speaking they can be called component integration tests.*



Often what we call unit tests are actually component integration tests (functional tests without DB/UI). Their biggest advantage is speed of execution. They are ~1000 times faster than functional tests run in fully integrated environment.

They can utilize contract testing approach in the sense that results of one test can be used as an input for others. They build our pyramid foundation thus can be considered the most important group of tests for us.


Why testing of a single class behviours in an isolation (unit testing) can bring poor results in case of OOP? Because to address frequent changes in the requirements developers are following best pratices like SOLID.
Thus very often implementation results in a higher number of simpler classes. Relationships between them on the other hand can be tricky to spot when some of them are composed, others are utilizing some form of inversion of control pattern like dependency injection.
In such case for a test to have a value we should focus on ensuring that relations between different classes are exercised during testing.
That is why usually it make more sense to test whole components (multiple classes responsible for certain business function delivery) and not a single class in isolation.
It is also easy to see now why even when having high code coverage we may still be struggling with product quality. Coverage says nothing about quality of our tests it just gives us a hint about their quantity.


*REMARK: Why we should focus on public methods? Because private methods are implementation detail and when we add tests for them we will have to adjust (maintain) such tests evry time we do a change. In case we focus on public methods our implementation detail can change during refactoring but our class behaviours for outside may stay not changed at all.* 



From practical point of view we should ensure that

* dev/qa engineers are using single instance of an IDE to develop tests/product code,
* test environment is kept as a code and can be setup in the same way on CI and locally (to allow for debugging purposes)
* [test data is kept as a code](https://waverleysoftware.com/blog/test-data-preparation/) to allow for rapid modifications at large scale
* test components are re-used between different kinds of tests like test runners, assertion libraries, comparators in a single team/project, for example pattern comparison should be done by same comparators in unit/e2e tests
* tests are undergoing same code review process like product code
* tests implementation planning considers DoT and pyramid
* 3 amigos process is used for tests planning per feature
* test are using same technologies (like java, js etc.) as the product under tests


Due to the nature of scrum process we can expect that requirements are going to change quite often. This requires us to re-implement some functionality to certain extent or even from scratch.

In such case to avoid so called [Pesticide Paradox](http://istqbfoundation.wikidot.com/printer--friendly/1#toc16) tests have to follow. In case our test code base is hard to change (requires significant effort due to multiple changes in many files for example) it is going to be a huge obstacle for the team.

To avoid such issues test code base have to follow known best coding practices like [SOLID, FIRST, KISS DRY](https://java-design-patterns.com/principles/) and [design patterns](https://alexilyenko.github.io/patterns-1/) etc... This guarantees that maintainability and changeability effort is reduced to minimum.

By the way mentioned good practices (especially TDD and [SOLID](https://www.baeldung.com/solid-principles)) are also managing for us one more important property of our product - testability.

Usually dev are focusing on unit testing when thinking in terms of SOLID and TDD but healthy test suites require us to follow [FIRST](https://dzone.com/articles/first-principles-solid-rules-for-tests) convention for every type of test as well.

When designing our product we need to ensure that it can be tested in an efficient way (by automated tests), for example can have an independent test and configuration data created for each test method.

That is why a healthy test automation suite requires some level of coding skills from any engineer involved in the product development.

This leads us to quite obvious conclusion testers should have software engineering skills and that developers are able to drive automated tests implementations.



Other tools we have available can do for us code analysis (scanning), for example [SonarQube](https://www.sonarqube.org/), [Google Lighthouse](https://developers.google.com/web/tools/lighthouse), [OpenJs Foundation WebHint](https://webhint.io/) etc... It can be part of a CI process and can be used to spot common usability/accessibility/performance/security issues before release.



Our CI should ensure that different kinds of tests (functional/non-functional like unit, component, integration, upgrade, performance, security etc.) will be executed as often as possible (for example at every pull request) thus it can be used to eliminate critical bugs before release.

This means that by focusing our further efforts on automation of any additional process needed to release (like documentation preparation, test reports or distribution package generation) we can be ready to release at any point in time. This release effort reduction increases our time we can devote to functional requirements implementation, effectively introducing a [devOps](https://aws.amazon.com/devops/what-is-devops/) model into our team.

Why devOps model is so important to us? Because when we offer our services in the cloud we want to reduce the operational/deployment costs to minimum. Thus development team job does not ends when a new release is out instead such team is involved in every part of the process (deployment included).  


## Final thoughts


The biggest challenges related to testing in Scrum are related test code base changeability and efficiency.

Due to the fact that requirements are changing often and with every sprint test code base should follow same (best) design and coding practices as actual product code to guarantee that it can be modified as easily and quickly as possible.

To increase efficiency tests should be executed at every pull request and to do it they have to be as fast as possible. To achieve it PO/BA, dev and test engineers should work very closely together on quality related aspects of created product. To make it happen a certain set of techniques can be applied to any product team as described above.

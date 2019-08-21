## Kick-Starting the Test-Driven Cycle

*We should be taught not to wait for inspiration to start a thing. Action always generates inspiration. Inspiration seldom generates action.*

But what about the very first feature, before we have this infrastructure? As an acceptance test, it must run end-to-end to give us the feedback we need about the system’s external interfaces, which means we must have implemented a whole automated build, deploy, and test cycle.

Deploying and testing right from the start of a project forces the team to understand how their system fits into the world. It flushes out the “unknown unknown” technical and organizational risks so they can be addressed while there’s still time. Attempting to deploy also helps the team understand who they need to liaise with, such as system administrators or external vendors, and start to build those relationships.

Starting with “build, deploy, and test” on a nonexistent system sounds odd, but we think it’s essential. The risks of leaving it to later are just too high. We have seen projects canceled after months of development because they could not reliably deploy their system. We have seen systems discarded because new features required months of manual regression testing and even then the error rates were too high. As always, we view feedback as a fundamental tool, and we want to know as early as possible whether we’re moving in the right direction. Then, once we have our first test in place, subsequent tests will be much quicker to write.

## FIRST, TEST A WALKING SKELETON

We can cut through this “first-feature paradox” by splitting it into two smaller problems. First, work out how to build, deploy, and test a “walking skeleton,” then use that infrastructure to write the acceptance tests for the first meaningful feature. After that, everything will be in place for test-driven development of the rest of the system.

A “walking skeleton” is an implementation of the thinnest possible slice of real functionality that we can automatically build, deploy, and test end-to-end [Cockburn04]. It should include just enough of the automation, the major components, and communication mechanisms to allow us to start working on the first feature. We keep the skeleton’s application functionality so simple that it’s obvious and uninteresting, leaving us free to concentrate on the infrastructure. For example, for a database-backed web application, a skeleton would show a flat web page with fields from the database. In Chapter 10, we’ll show an example that displays a single value in the user interface and sends just a handshake message to the server.

It’s also important to realize that the “end” in “end-to-end” refers to the process, as well as the system.

We’ve learned repeatedly is that nothing forces us to understand a process better than trying to automate it

In practice, of course, real end-to-end testing may be so hard to achieve that we have to start with infrastructure that implements our current understanding of what the real system will do and what its environment is. 

 One of the weaknesses of our Auction Sniper example (Part III) is that the tests run against a dummy server, not the real site.

 Whilst building the “walking skeleton,” we concentrate on the structure and don’t worry too much about cleaning up the test to be beautifully expressive. The walking skeleton and its supporting infrastructure are there to help us work out how to start test-driven development. It’s only the first step toward a complete end-to-end acceptance-testing solution. When we write the test for the first feature, then we need to “write the test you want to read” (page 42) to make sure that it’s a clear expression of the behavior of the system.

### THE IMPORTANCE OF EARLY END-TO-END TESTING
We joined a project that had been running for a couple of years but had never tested their entire system end-to-end. There were frequent production outages and deployments often failed. The system was large and complex, reflecting the complicated business transactions it managed. The effort of building an automated, end-to-end test suite was so large that an entire new team had to be formed to perform the work. It took them months to build an end-to-end test environment, and they never managed to get the entire system covered by an end-to-end test suite.

Because the need for end-to-end testing had not influenced its design, the system was difficult to test. For example, the system’s components used internal timers to schedule activities, some of them days or weeks into the future. This made it very difficult to write end-to-end tests: It was impractical to run the tests in real-time but the scheduling could not be influenced from outside the system. The developers had to redesign the system itself so that periodic activities were triggered by messages sent from a remote scheduler which could be replaced in the test environment; see “Externalize Event Sources” (page 326). This was a significant architectural change—and it was very risky because it had to be performed without end-to-end test coverage.

## DECIDING THE SHAPE OF THE WALKING SKELETON

The development of a “walking skeleton” is the moment when we start to make choices about the high-level structure of our application. We can’t automate the build, deploy, and test cycle without some idea of the overall structure. 

To design this initial structure, we have to have some understanding of the purpose of the system, otherwise the whole exercise risks being meaningless. We need a high-level view of the client’s requirements, both functional and nonfunctional, to guide our choices. This preparatory work is part of the chartering of the project, which we must leave as outside the scope of this book.

Please don’t confuse this with doing “Big Design Up Front” (BDUF) which has such a bad reputation in the Agile Development community. We’re not trying to elaborate the whole design down to classes and algorithms before we start coding. Any ideas we have now are likely to be wrong, so we prefer to discover those details as we grow the system. We’re making the smallest number of decisions we can to kick-start the TDD cycle, to allow us to start learning and improving from real feedback.

## BUILD SOURCES OF FEEDBACK

Our ideal situation is where the team releases regularly to a real production system, as in Figure 4.3. This allows the system’s stakeholders to respond to how well the system meets their needs, at the same time allowing us to judge its implementation.

We use the automation of building and testing to give us feedback on qualities of the system, such as how easily we can cut a version and deploy, how well the design works, and how good the code is. The automated deployment helps us release frequently to real users, which gives us feedback on how well we have understood the domain and whether seeing the system in practice has changed our customer’s priorities.

The great benefit is that we will be able to make changes in response to whatever we learn, because writing everything test-first means that we will have a thorough set of regression tests. No tests are perfect, of course, but in practice we’ve found that a substantial test suite allows us to make major changes safely.

## EXPOSE UNCERTAINTY EARLY

All this effort means that teams are frequently surprised by the time it takes to get a “walking skeleton” working, considering that it does hardly anything. That’s because this first step involves establishing a lot of infrastructure and asking (and answering) many awkward questions. The time to implement the first few features will be unpredictable as the team discovers more about its requirements and target environment. For a new team, this will be compounded by the social stresses of learning how to work together.

 Projects with late integration start calmly but generally turn difficult towards the end as the team tries to pull the system together for the first time.

 Our experience is that a well-run incremental development runs in the opposite direction. It starts unsettled but then, after a few features have been implemented and the project automation has been built up, settles in to a routine

 But the most important thing is to have a sense of direction and a concrete implementation to test our assumptions.

### BROWNFIELD DEVELOPMENT
We don’t always have the luxury of building a new system from the ground up. Many of our projects have started with an existing system that must be extended, adapted, or replaced. In such cases, we can’t start by building a “walking skeleton”; we have to work with what already exists, no matter how hostile its structure.

That said, the process of kick-starting TDD of an existing system is not fundamentally different from applying it to a new system—although it may be orders of magnitude more difficult because of the technical baggage the system already carries. Michael Feathers has written a whole book on the topic.

It is risky to start reworking a system when there are no tests to detect regressions. The safest way to start the TDD process is to automate the build and deploy process, and then add end-to-end tests that cover the areas of the code we need to change. With that protection, we can start to address internal quality issues with more confidence, refactoring the code and introducing unit tests as we add functionality.

The easiest way to start building an end-to-end test infrastructure is with the simplest path through the system that we can find. Like a “walking skeleton,” this lets us build up some supporting infrastructure before we tackle the harder problems of testing more complicated functionality.

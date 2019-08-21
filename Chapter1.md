# Growing-Object-Oriented-Software-Guided-by-Tests

*One must learn by doing the thing; for though you think you know it, you have no certainty, until you try.*

Almost all software projects are attempting something that nobody has done before (or at least that nobody in the organization has done before).

 For the project to succeed, the people involved have to work together just to understand what they’re supposed to achieve, and to identify and resolve misunderstandings along the way

 ## FEEDBACK IS THE FUNDAMENTAL TOOL

 Deploying completed work to some kind of environment at each cycle is critical. Every time a team deploys, its members have an opportunity to check their assumptions against reality. They can measure how much progress they’re really making, detect and correct any errors, and adapt the current plan in response to what they’ve learned. Without deployment, the feedback is not complete.

 Each feedback loop addresses different aspects of the system and development process. The inner loops are more focused on the technical detail: what a unit of code does, whether it integrates with the rest of the system. The outer loops are more focused on the organization and the team: whether the application serves its users’ needs, whether the team is as effective as it could be.

 ## PRACTICES THAT SUPPORT CHANGE

 We need constant testing to catch regression errors, so we can add new features without breaking existing ones. For systems of any interesting size, frequent manual testing is just impractical, so we must automate testing as much as we can to reduce the costs of building, deploying, and modifying versions of the system.

Second, we need to keep the code as simple as possible, so it’s easier to understand and modify. Developers spend far more time reading code than writing it, so that’s what we should optimize for.

 The test suites in the feedback loops protect us against our own mistakes as we improve (and therefore change) the code.

 The catch is that few developers enjoy testing their code. In many development groups, writing automated tests is seen as not “real” work compared to adding features, and boring as well. Most people do not do as well as they should at work they find uninspiring.

 Test-Driven Development (TDD) turns this situation on its head. We write our tests before we write the code. Instead of just using testing to verify our work after it’s done, TDD turns testing into a design activity. We use the tests to clarify our ideas about what we want the code to do. As Kent Beck described it to us, “I was finally able to separate logical from physical design. I’d always been told to do that but no one ever explained how.” We find that the effort of writing a test first also gives us rapid feedback about the quality of our design ideas—that making code accessible for testing often drives it towards being cleaner and more modular.

 ## TEST-DRIVEN DEVELOPMENT IN A NUTSHELL

 The cycle at the heart of TDD is: write a test; write some code to get it working; refactor the code to be as simple an implementation of the tested features as possible. Repeat.

  As we develop the system, we use TDD to give us feedback on the quality of both its implementation (“Does it work?”) and design (“Is it well structured?”). Developing test-first, we find we benefit twice from the effort. Writing tests:

• makes us clarify the acceptance criteria for the next piece of work—we have to ask ourselves how we can tell when we’re done (design);

• encourages us to write loosely coupled components, so they can easily be tested in isolation and, at higher levels, combined together (design);

• adds an executable description of what the code does (design); and,

• adds to a complete regression suite (implementation);

whereas running tests:

• detects errors while the context is fresh in our mind (implementation); and,

• lets us know when we’ve done enough, discouraging “gold plating” and unnecessary features (design).

## THE BIGGER PICTURE

It is tempting to start the TDD process by writing unit tests for classes in the application. This is better than having no tests at all and can catch those basic programming errors that we all know but find so hard to avoid: fencepost errors, incorrect boolean expressions, and the like. But a project with only unit tests is missing out on critical benefits of the TDD process. We’ve seen projects with high-quality, well unit-tested code that turned out not to be called from anywhere, or that could not be integrated with the rest of the system and had to be rewritten.

How do we know where to start writing code? More importantly, how do we know when to stop writing code? The golden rule tells us what we need to do: Write a failing test.

When we’re implementing a feature, we start by writing an acceptance test, which exercises the functionality we want to build. While it’s failing, an acceptance test demonstrates that the system does not yet implement that feature; when it passes, we’re done. When working on a feature, we use its acceptance test to guide us as to whether we actually need the code we’re about to write—we only write code that’s directly relevant. Underneath the acceptance test, we follow the unit level test/implement/refactor cycle to develop the feature.

Failing unit tests should never be committed to the source repository.

## TESTING END-TO-END

Wherever possible, an acceptance test should exercise the system end-to-end without directly calling its internal code. An end-to-end test interacts with the system only from the outside: through its user interface, by sending messages as if from third-party systems, by invoking its web services, by parsing reports, and so on.

### THE IMPORTANCE OF END-TO-END TESTING: A HORROR STORY
Nat was once brought onto a project that had been using TDD since its inception. The team had been writing acceptance tests to capture requirements and show progress to their customer representatives. They had been writing unit tests for the classes of the system, and the internals were clean and easy to change. They had been making great progress, and the customer representatives had signed off all the implemented features on the basis of the passing acceptance tests.

But the acceptance tests did not run end-to-end—they instantiated the system’s internal objects and directly invoked their methods. The application actually did nothing at all. Its entry point contained only a single comment:

// TODO implement this

Additional feedback loops, such as regular show-and-tell sessions, should have been in place and would have caught this problem.

For us, “end-to-end” means more than just interacting with the system from the outside—that might be better called “edge-to-edge” testing. We prefer to have the end-to-end tests exercise both the system and the process by which it’s built and deployed. An automated build, usually triggered by someone checking code into the source repository, will: check out the latest version; compile and unit-test the code; integrate and package the system; perform a production-like deployment into a realistic environment; and, finally, exercise the system through its external access points. This sounds like a lot of effort (it is), but has to be done anyway repeatedly during the software’s lifetime. Many of the steps might be fiddly and error-prone, so the end-to-end build cycle is an ideal candidate for automation. You’ll see in Chapter 10 how early in a project we get this working.

A system is deployable when the acceptance tests all pass, because they should give us enough confidence that everything works.

## LEVELS OF TESTING

**Acceptance:** Does the whole system work?

**Integration:** Does our code work against code we can’t change?

**Unit:** Do our objects do the right thing, are they convenient to work with?

We use “acceptance tests” to help us, with the domain experts, understand and agree on what we are going to build next. We also use them to make sure that we haven’t broken any existing features as we continue developing.

Our preferred implementation of the “role” of acceptance testing is to write end-to-end tests which, as we just noted, should be as end-to-end as possible; our bias often leads us to use these terms interchangeably although, in some cases, acceptance tests might not be end-to-end.

## EXTERNAL AND INTERNAL QUALITY

 External quality is how well the system meets the needs of its customers and users (is it functional, reliable, available, responsive, etc.), and internal quality is how well it meets the needs of its developers and administrators (is it easy to understand, easy to change, etc.)

 Everyone can understand the point of external quality; it’s usually part of the contract to build. The case for internal quality is equally important but is often harder to make. Internal quality is what lets us cope with continual and unanticipated change which, as we saw at the beginning of this chapter, is a fact of working with software. The point of maintaining internal quality is to allow us to modify the system’s behavior safely and predictably, because it minimizes the risk that a change will force major rework.

Running end-to-end tests tells us about the external quality of our system, and writing them tells us something about how well we (the whole team) understand the domain, but end-to-end tests don’t tell us how well we’ve written the code.

Writing unit tests gives us a lot of feedback about the quality of our code, and running them tells us that we haven’t broken any classes—but, again, unit tests don’t give us enough confidence that the system as a whole works. Integration tests fall somewhere in the middle.

So, for a class to be easy to unit-test, the class must have explicit dependencies that can easily be substituted and clear responsibilities that can easily be invoked and verified. In software engineering terms, that means that the code must be loosely coupled and highly cohesive—in other words, well-designed.

### COUPLING AND COHESION

Coupling and cohesion are metrics that (roughly) describe how easy it will be to change the behavior of some code. They were described by Larry Constantine in [Yourdon79].

Elements are coupled if a change in one forces a change in the other. For example, if two classes inherit from a common parent, then a change in one class might require a change in the other. Think of a combo audio system: It’s tightly coupled because if we want to change from analog to digital radio, we must rebuild the whole system. If we assemble a system from separates, it would have low coupling and we could just swap out the receiver. “Loosely” coupled features (i.e., those with low coupling) are easier to maintain.

An element’s cohesion is a measure of whether its responsibilities form a meaningful unit. For example, a class that parses both dates and URLs is not coherent, because they’re unrelated concepts. Think of a machine that washes both clothes and dishes—it’s unlikely to do both well.2 At the other extreme, a class that parses only the punctuation in a URL is unlikely to be coherent, because it doesn’t represent a whole concept. To get anything done, the programmer will have to find other parsers for protocol, host, resource, and so on. Features with “high” coherence are easier to maintain.

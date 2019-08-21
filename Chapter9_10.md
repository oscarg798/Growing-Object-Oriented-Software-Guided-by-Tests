# Commissioning an Auction Sniper

## TO BEGIN AT THE BEGINNING

*In which we are commissioned to build an application that automatically bids in auctions. We sketch out how it should work and what the major components should be. We put together a rough plan for the incremental steps in which we will grow the application.*

## GETTING THERE SAFELY

Even a small application like this is too large to write in one go, so we need to figure out, roughly, the steps we might take to get there. A critical technique with incremental development is learning how to slice up the functionality so that it can be built a little at a time. Each slice should be significant and concrete enough that the team can tell when it’s done, and small enough to be focused on one concept and achievable quickly. Dividing our work into small, coherent chunks also helps us manage the development risk. We get regular, concrete feedback on the progress we’re making, so we can adjust our plan as the team discovers more about the domain and the technologies.

# The Walking Skeleton

For most projects, developing the walking skeleton takes a surprising amount of effort. First, because deciding what to do will flush out all sorts of questions about the application and its place in the world. Second, because the automation of building, packaging, and deploying into a production-like environment (once we know what that means) will flush out all sorts of technical and organizational questions.

In most Agile projects, there’s a first stage where the team is doing initial analysis, setting up its physical and technical environments, and otherwise getting started. The team isn’t adding much visible functionality since almost all the work is infrastructure, so it might not make sense to count this as a conventional iteration for scheduling purposes. A common practice is to call this step iteration zero: “iteration” because the team still needs to time-box its activities and “zero” because it’s before functional development starts in iteration one. One important task for iteration zero is to use the walking skeleton to test-drive the initial architecture.

We like to start by writing a test as if its implementation already exists, and then filling in whatever is needed to make it work—what Abelson and Sussman call “programming by wishful thinking” [Abelson96]. Working backwards from the test helps us focus on what we want the system to do, instead of getting caught up in the complexity of how we will make it work. So, first we code up a test to describe our intentions as clearly as we can, given the expressive limits of a programming language. Then we build the infrastructure to support the way we want to test the system, instead of writing the tests to fit in with an existing infrastructure. This usually takes a large part of our initial effort because there is so much to get ready. With this infrastructure in place, we can implement the feature and make the test pass.

Controlling the Sniper application is more complicated. We want our skeleton test to exercise our application as close to end-to-end as possible, to show that the main() method initializes the application correctly and that the components really work together. This means that we should start by working through the publicly visible features of the application (in this case, its user interface) instead of directly invoking its domain objects. We also want our test to be clear about what is being checked, written in terms of the relationship between a Sniper and its auction, so we’ll hide all the messy code for manipulating Swing in an ApplicationRunner class. We’ll start by writing the test as if all the code it needs exists and will fill in the implementations afterwards.

## SOME INITIAL CHOICES
Now we have to make the test pass, which will require a lot of preparation. We need to find or write four components: an XMPP message broker, a stub auction that can communicate over XMPP, a GUI testing framework, and a test harness that can cope with our multithreaded, asynchronous architecture. We also have to get the project under version control with an automated build/deploy/test process. Compared to unit-testing a single class, there is a lot to do—but it’s essential. Even at this high level, the exercise of writing tests drives the development of the system. Working through our first end-to-end test will force some of the structural decisions we need to make, such as packaging and deployment.

## End-to-End Testing

End-to-end testing for event-based systems, such as our Sniper, has to cope with asynchrony. The tests run in parallel with the application and do not know precisely when the application is or isn’t ready. This is unlike unit testing, where a test drives an object directly in the same thread and so can make direct assertions about its state and behavior.

## Ready to Start

 An important part of the test-driven development skills is judging where to set the boundaries of what to test and how to eventually cover everything.

  In this case, we have to start with a fake auction service based on the documentation from Southabee’s On-Line. The documentation might or might not be correct, so we will record that as a known risk in the project plan and schedule time to test against the real server as soon as we have enough functionality to complete a meaningful transaction—even if we end up buying a hideous (but cheap) pair of candlesticks in a real auction. The sooner we find a discrepancy, the less code we will have based on that misunderstanding and the more time to fix it.

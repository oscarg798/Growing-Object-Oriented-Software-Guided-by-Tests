# Test-Driven Development with Objects

*Object-oriented design focuses more on the communication between objects than on the objects themselves.*

The big idea is “messaging” [...] The key in making great and growable systems is much more to design how its modules communicate rather than what their internal properties and behaviors should be.

An object communicates by messages: It receives messages from other objects and reacts by sending messages to other objects as well as, perhaps, returning a value or exception to the original sender. An object has a method of handling every type of message that it understands and, in most cases, encapsulates some internal state that it uses to coordinate its communication with other objects.

An object-oriented system is a web of collaborating objects. A system is built by creating objects and plugging them together so that they can send messages to one another. The behavior of the system is an emergent property of the composition of the objects—the choice of objects and how they are connected.

This lets us change the behavior of the system by changing the composition of its objects—adding and removing instances, plugging different combinations together—rather than writing procedural code. The code we write to manage this composition is a declarative definition of the how the web of objects will behave. It’s easier to change the system’s behavior because we can focus on what we want it to do, not how.

## VALUES AND OBJECTS

Values are immutable instances that model fixed quantities. They have no individual identity, so two value instances are effectively the same if they have the same state. This means that it makes no sense to compare the identity of two values; doing so can cause some subtle bugs—think of the different ways of comparing two copies of new Integer(999). That’s why we’re taught to use string1.equals(string2) in Java rather than string1 == string2.

Objects, on the other hand, use mutable state to model their behavior over time. Two objects of the same type have separate identities even if they have exactly the same state now, because their states can diverge if they receive different messages in the future.

In practice, this means that we split our system into two “worlds”: values, which are treated functionally, and objects, which implement the stateful behavior of the system

## FOLLOW THE MESSAGES

A communication pattern is a set of rules that govern how a group of objects talk to each other: the roles they play, what messages they can send and when, and so on. In languages like Java, we identify object roles with (abstract) interfaces, rather than (concrete) classes—although interfaces don’t define everything we need to say.

In our view, the domain model is in these communication patterns, because they are what gives meaning to the universe of possible relationships between the objects.

## TELL, DON’T ASK

We have objects sending each other messages, so what do they say? Our experience is that the calling object should describe what it wants in terms of the role that its neighbor plays, and let the called object decide how to make that happen. This is commonly known as the “Tell, Don’t Ask” style or, more formally, the Law of Demeter. Objects make their decisions based only on the information they hold internally or that which came with the triggering message; they avoid navigating to other objects to make things happen. Followed consistently, this style produces more flexible code because it’s easy to swap objects that play the same role. The caller sees nothing of their internal structure or the structure of the rest of the system behind the role interface.

## BUT SOMETIMES ASK

Of course we don’t “tell” everything;1 we “ask” when getting information from values and collections, or when using a factory to create new objects. Occasionally, we also ask objects about their state when searching or filtering, but we still want to maintain expressiveness and avoid “train wrecks.”

Adding a query method moves the behavior to the most appropriate object, gives it an explanatory name, and makes it easier to test.

## UNIT-TESTING THE COLLABORATING OBJECTS

We appear to have painted ourselves into a corner. We’re insisting on focused objects that send commands to each other and don’t expose any way to query their state, so it looks like we have nothing available to assert in a unit test.

One option is to replace the target object’s neighbors in a test with substitutes, or mock objects. We can specify how we expect the target object to communicate with its mock neighbors for a triggering event; we call these specifications expectations.

During the test, the mock objects assert that they have been called as expected; they also implement any stubbed behavior needed to make the rest of the test work.

## SUPPORT FOR TDD WITH MOCK OBJECTS

We use the term mockery2 for the object that holds the context of a test, creates mock objects, and manages expectations and stubbing for the test. We’ll show the practice throughout Part III, so we’ll just touch on the basics here. The essential structure of a test is:

2. This is a pun by Ivan Moore that we adopted in a fit of whimsy.

• Create any required mock objects.

• Create any real objects, including the target object.

• Specify how you expect the mock objects to be called by the target object.

• Call the triggering method(s) on the target object.

• Assert that any resulting values are valid and that all the expected calls have been made.

The unit test makes explicit the relationship between the target object and its environment. It creates all the objects in the cluster and makes assertions about the interactions between the target object and its collaborators.

 The important point, as we stress repeatedly throughout this book, is to make clear the intention of every test, distinguishing between the tested functionality, the supporting infrastructure, and the object structure.

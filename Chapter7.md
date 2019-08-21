# Achieving Object-Oriented Design

*In matters of style, swim with the current; in matters of principle, stand like a rock.*

## HOW WRITING A TEST FIRST HELPS THE DESIGN

The design principles we outlined in the previous chapter apply to finding the right boundaries for an object so that it plays well with its neighbors—a caller wants to know what an object does and what it depends on, but not how it works. We also want an object to represent a coherent unit that makes sense in its larger environment. A system built from such components will have the flexibility to reconfigure and adapt as requirements change.

There are three aspects of TDD that help us achieve this scoping. First, starting with a test means that we have to describe what we want to achieve before we consider how. This focus helps us maintain the right level of abstraction for the target object. If the intention of the unit test is unclear then we’re probably mixing up concepts and not ready to start coding. It also helps us with information hiding as we have to decide what needs to be visible from outside the object.

Second, to keep unit tests understandable (and, so, maintainable), we have to limit their scope. 

Third, to construct an object for a unit test, we have to pass its dependencies to it, which means that we have to know what they are. 

## COMMUNICATION OVER CLASSIFICATION

As we wrote in Chapter 2, we view a running system as a web of communicating objects, so we focus our design effort on how the objects collaborate to deliver the functionality we need. Obviously, we want to achieve a well-designed class structure, but we think the communication patterns between objects are more important.

In languages such as Java, we can use interfaces to define the available messages between objects, but we also need to define their patterns of communication—their communication protocols. We do what we can with naming and convention, but there’s nothing in the language to describe relationships between interfaces or methods within an interface, which leaves a significant part of the design implicit.

Steve heard this useful distinction in a conference talk: an interface describes whether two components will fit together, while a protocol describes whether they will work together.

TDD with mock objects also encourages information hiding. We should mock an object’s peers—its dependencies, notifications, and adjustments we categorized on page 52—but not its internals. Tests that highlight an object’s neighbors help us to see whether they are peers, or should instead be internal to the target object. A test that is clumsy or unclear might be a hint that we’ve exposed too much implementation, and that we should rebalance the responsibilities between the object and its neighbors.

## VALUE TYPES

Values are immutable, so they’re simpler and have no meaningful identity; objects have state, so they have identity and relationships with each other.

The more code we write, the more we’re convinced that we should define types to represent value concepts in the domain, even if they don’t do much. It helps to create a consistent domain model that is more self-explanatory. If we create, for example, an Item type in a system, instead of just using String, we can find all the code that’s relevant for a change without having to chase through the method calls. Specific types also reduce the risk of confusion—as the Mars Climate Orbiter disaster showed, feet and metres may both be represented as numbers but they’re different things.1 Finally, once we have a type to represent a concept, it usually turns out to be a good place to hang behavior, guiding us towards using a more object-oriented approach instead of scattering related behavior across the code.

We find that the discovery of value types is usually motivated by trying to follow our design principles, rather than by responding to code stresses when writing tests.

We can start pulling out cohesive units of functionality into smaller collaborating objects, which we can then unit-test independently. Splitting out a new object also forces us to look at the dependencies of the code we’re pulling out.

Break up an object if it becomes too large to test easily, or if its test failures become difficult to interpret. Then unit-test the new parts separately.

Our response is to create an interface to define the service that the object needs from the object’s point of view. We write tests for the new behavior as if the service already exists, using mock objects to help describe the relationship between the target object and its new collaborator.

The development cycle goes like this. When implementing an object, we discover that it needs a service to be provided by another object. We give the new service a name and mock it out in the client object’s unit tests, to clarify the relationship between the two. Then we write an object to provide that service and, in doing so, discover what services that object needs. We follow this chain (or perhaps a directed graph) of collaborator relationships until we connect up to existing objects, either our own or from a third-party API.

We think of this as “on-demand” design: we “pull” interfaces and their implementations into existence from the needs of the client, rather than “pushing” out the features that we think a class should provide.

 When we have a cluster of related objects that work together, we can package them up in a containing object. The new object hides the complexity in an abstraction that allows us to program at a higher level.

 We use Java interfaces more liberally than some other developers. This reflects our emphasis on the relationships between objects, as defined by their communication protocols. We use interfaces to name the roles that objects can play and to describe the messages they’ll accept.

We also prefer interfaces to be as narrow as possible, even though that means we need more of them. The fewer methods there are on an interface, the more obvious is its role in the calling object. We don’t have to worry which other methods are relevant to a particular call and which were included for convenience. Narrow interfaces are also easier to write adapters and decorators for; there’s less to implement, so it’s easier to write objects that compose together well.

## REFACTOR INTERFACES TOO

Alternatively, if similar interfaces turn out to represent different concepts, we can make a point of making them distinct, so that the compiler can ensure that we only combine objects correctly. A decision to separate similar-looking interfaces is a good time to reconsider their naming. It’s likely that there’s a more appropriate name for at least one of them.

Finally, another time to consider refactoring interfaces is when we start implementing them. For example, if we find that the structure of an implementing class is unclear, perhaps it has too many responsibilities which might be a hint that the interface is unfocused too and should be split up.

## BUILDING UP TO HIGHER-LEVEL PROGRAMMING

Our response is to organize the code into two layers: an implementation layer which is the graph of objects, its behavior is the combined result of how its objects respond to events; and, a declarative layer which builds up the objects in the implementation layer, using small “sugar” methods and syntax to describe the purpose of each fragment. The declarative layer describes what the code will do, while the implementation layer describes how the code does it. The declarative layer is, in effect, a small domain-specific language embedded (in this case) in Java.3

## AND WHAT ABOUT CLASSES?

One last point. Unusually for a book on object-oriented software, we haven’t said much about classes and inheritance. It should be obvious by now that we’ve been pushing the application domain into the gaps between the objects, the communication protocols. We emphasize interfaces more than classes because that’s what other objects see: an object’s type is defined by the roles it plays.

We view classes for objects as an “implementation detail”—a way of implementing types, not the types themselves. We discover object class hierarchies by factoring out common behavior, but prefer to refactor to delegation if possible since we find that it makes our code more flexible and easier to understand.5 Value types, on the other hand, are less likely to use delegation since they don’t have peers.

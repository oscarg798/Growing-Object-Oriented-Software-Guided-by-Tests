# Object-Oriented Style

*Always design a thing by considering it in its next larger context—a chair in a room, a room in a house, a house in an environment, an environment in a city plan.*

We value code that is easy to maintain over code that is easy to write.1 Implementing a feature in the most direct way can damage the maintainability of the system, for example by making the code difficult to understand or by introducing hidden dependencies between components. Balancing immediate and longer-term concerns is often tricky, but we’ve seen too many teams that can no longer deliver because their system is too brittle.

## DESIGNING FOR MAINTAINABILITY

Following the process we described in Chapter 5, we grow our systems a slice of functionality at a time. As the code scales up, the only way we can continue to understand and maintain it is by structuring the functionality into objects, objects into packages,2 packages into programs, and programs into systems. We use two principal heuristics to guide this structuring:

### Separation of concerns

When we have to change the behavior of a system, we want to change as little code as possible. If all the relevant changes are in one area of code, we don’t have to hunt around the system to get the job done. Because we cannot predict when we will have to change any particular part of the system, we gather together code that will change for the same reason. For example, code to unpack messages from an Internet standard protocol will not change for the same reasons as business code that interprets those messages, so we partition the two concepts into different packages.

### Higher levels of abstraction

The only way for humans to deal with complexity is to avoid it, by working at higher levels of abstraction. We can get more done if we program by combining components of useful functionality rather than manipulating variables and control flow; that’s why most people order food from a menu in terms of dishes, rather than detail the recipes used to create them.

Applied consistently, these two forces will push the structure of an application towards something like Cockburn’s “ports and adapters” architecture [Cockburn08], in which the code for the business domain is isolated from its dependencies on technical infrastructure, such as databases and user interfaces. We don’t want technical concepts to leak into the application model, so we write interfaces to describe its relationships with the outside world in its terminology (Cockburn’s ports). Then we write bridges between the application core and each technical domain (Cockburn’s adapters). This is related to what Eric Evans calls an “anticorruption layer” [Evans03].

The bridges implement the interfaces defined by the application model and map between application-level and technical-level objects (Figure 6.1). For example, a bridge might map an order book object to SQL statements so that orders are persisted in a database. To do so, it might query values from the application object or use an object-relational tool like Hibernate3 to pull values out of objects using Java reflection. We’ll show an example of refactoring to this architecture in Chapter 17.

We follow standard practices to maintain encapsulation when coding: define immutable value types, avoid global variables and singletons, copy collections and mutable values when passing them between objects, and so on. We have more about information hiding later in this chapter.

## INTERNALS VS. PEERS

As we organize our system, we must decide what is inside and outside each object, so that the object provides a coherent abstraction with a clear API. Much of the point of an object, as we discussed above, is to encapsulate access to its internals through its API and to hide these details from the rest of the system. An object communicates with other objects in the system by sending and receiving messages, as in Figure 6.2; the objects it communicates with directly are its peers.

This decision matters because it affects how easy an object is to use, and so contributes to the internal quality of the system. If we expose too much of an object’s internals through its API, its clients will end up doing some of its work. We’ll have distributed behavior across too many objects (they’ll be coupled together), increasing the cost of maintenance because any changes will now ripple across the code. 

## NO AND’S, OR’S, OR BUT’S

Every object should have a single, clearly defined responsibility; this is the “single responsibility” principle [Martin02]. When we’re adding behavior to a system, this principle helps us decide whether to extend an existing object or create a new service for an object to call.

Our heuristic is that we should be able to describe what an object does without using any conjunctions (“and,” “or”). If we find ourselves adding clauses to the description, then the object probably should be broken up into collaborating objects, usually one for each clause.

## OBJECT PEER STEREOTYPES

### Dependencies

Services that the object requires from its peers so it can perform its responsibilities. The object cannot function without these services. It should not be possible to create the object without them. For example, a graphics package will need something like a screen or canvas to draw on—it doesn’t make sense without one.

### Notifications

Peers that need to be kept up to date with the object’s activity. The object will notify interested peers whenever it changes state or performs a significant action. Notifications are “fire and forget”; the object neither knows nor cares which peers are listening. Notifications are so useful because they decouple objects from each other. For example, in a user interface system, a button component promises to notify any registered listeners when it’s clicked, but does not know what those listeners will do. Similarly, the listeners expect to be called but know nothing of the way the user interface dispatches its events.

### Adjustments

Peers that adjust the object’s behavior to the wider needs of the system. This includes policy objects that make decisions on the object’s behalf (the Strategy pattern in [Gamma94]) and component parts of the object if it’s a composite. For example, a Swing JTable will ask a TableCellRenderer to draw a cell’s value, perhaps as RGB (Red, Green, Blue) values for a color. If we change the renderer, the table will change its presentation, now displaying the HSB (Hue, Saturation, Brightness) values

Another way to look at it is that notifications are one-way: A notification listener may not return a value, call back the caller, or throw an exception, since there may be other listeners further down the chain. A dependency or adjustment, on the other hand, may do any of these, since there’s a direct relationship.

### “NEW OR NEW NOT. THERE IS NO TRY.”

We try to make sure that we always create a valid object. For dependencies, this means that we pass them in through the constructor. They’re required, so there’s no point in creating an instance of an object until its dependencies are available, and using the constructor enforces this constraint in the object’s definition.

Partially creating an object and then finishing it off by setting properties is brittle because the programmer has to remember to set all the dependencies. When the object changes to add new dependencies, the existing client code will still compile even though it no longer constructs a valid instance. At best this will cause a NullPointerException, at worst it will fail misleadingly.

Notifications and adjustments can be passed to the constructor as a convenience. Alternatively, they can be initialized to safe defaults and overwritten later (note that there is no safe default for a dependency). Adjustments can be initialized to common values, and notifications to a null object [Woolf98] or an empty collection. We then add methods to allow callers to change these default values, and add or remove listeners.

## COMPOSITE SIMPLER THAN THE SUM OF ITS PARTS

In software, a user interface component for editing money values might have two subcomponents: one for the amount and one for the currency. For the component to be useful, its API should manage both values together, otherwise the client code could just control it subcomponents directly.

```java
moneyEditor.getAmountField().setText(String.valueOf(money.amount());
moneyEditor.getCurrencyField().setText(money.currencyCode());
```

The “Tell, Don’t Ask” convention can start to hide an object’s structure from its clients but is not a strong enough rule by itself. For example, we could replace the getters in the first version with setters:

moneyEditor.setAmountField(money.amount());
moneyEditor.setCurrencyField(money.currencyCode());

This still exposes the internal structure of the component, which its client still has to manage explicitly.

We can make the API much simpler by hiding within the component everything about the way money values are displayed and edited, which in turn simplifies the client code:

```java
moneyEditor.setValue(money);
```

## CONTEXT INDEPENDENCE

While the “composite simpler than the sum of its parts” rule helps us decide whether an object hides enough information, the “context independence” rule helps us decide whether an object hides too much or hides the wrong information.

A system is easier to change if its objects are context-independent; that is, if each object has no built-in knowledge about the system in which it executes. This allows us to take units of behavior (objects) and apply them in new situations. To be context-independent, whatever an object needs to know about the larger environment it’s running in must be passed in. Those relationships might be “permanent” (passed in on construction) or “transient” (passed in to the method that needs them).

`A class that uses terms from multiple domains might be violating context independence, unless it’s part of a bridging layer.`

The effect of the “context independence” rule on a system of objects is to make their relationships explicit, defined separately from the objects themselves. First, this simplifies the objects, since they don’t need to manage their own relationships. Second, this simplifies managing the relationships, since objects at the same scale are often created and composed together in the same places, usually in mapping-layer factory objects.

Context independence guides us towards coherent objects that can be applied in different contexts, and towards systems that we can change by reconfiguring how their objects are composed.



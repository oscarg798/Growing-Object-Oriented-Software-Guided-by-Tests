# An Introduction to the Tools

*Man is a tool-using animal. Without tools he is nothing, with tools he is all.*

Test Fixtures
A test fixture is the fixed state that exists at the start of a test. A test fixture ensures that a test is repeatable—every time a test is run it starts in the same state so it should produce the same results. A fixture may be set up before the test runs and torn down after it has finished.

The fixture is usually set up by field initializers. It can also be set up by the constructor of the test class or instance initializer blocks. JUnit also lets you identify methods that set up and tear down the fixture with annotations. JUnit will run all methods annotated with @Before before running the tests, to set up the fixture, and those annotated by @After after it has run the test, to tear down the fixture. Many JUnit tests do not need to explicitly tear down the fixture because it is enough to let the JVM garbage collect any objects created when it was set up.

## Expectations

jMock’s expectation API is very expressive. It lets you precisely specify:

• The minimum and maximum number of times an invocation is expected;

• Whether an invocation is expected (the test should fail if it is not received) or merely allowed to happen (the test should pass if it is not received);

• The parameter values, either given literally or constrained by Hamcrest matchers;

• The ordering constraints with respect to other expectations; and,

• What should happen when the method is invoked—a value to return, an exception to throw, or any other behavior.

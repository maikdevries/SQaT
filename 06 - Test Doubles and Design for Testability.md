## **TEST DOUBLES AND DESIGN FOR TESTABILITY**

#### The testing pyramid
> "A **unit test** is an automated piece of code that invokes a unit of work in the system. And a unit of work can span a single method, a whole class or multiple classes working together to achieve one single logical purpose that can be verified." - Roy Osherove

Unit tests are fast, easy to control and easy to write but have a lack of reality and the fact that some type of bugs can't be caught as disadvantages.

On the other side of the spectrum there is **system testing** or black box testing which puts the entire system under test including all of its components. The way the system is build does not matter, the expected output of the entire system for a given input is tested. Obvious advantages are how realistic and the simulation of the user's perspective when performing these tests but has as downsides that they're slow, complex to write and flaky (failing tests due to uncertainties out of the developer's control).

**Integration testing** is the man-in-the-middle and has as focus the interactions between individual components rather than the entire system. Integration tests usually require an advanced setup - an example being an isolated database instance which need to be instantiated and cleaned up before and after each test.

The **testing pyramid** is a clear diagram showcasing the trade-offs for different types of testing - the higher up the pyramid, the higher the cost.

<div align="center"><img src="https://sttp.site/chapters/pragmatic-testing/img/testing-pyramid/testing_pyramid.svg"></div>

To answer the question of what type of testing to use for which parts of the system:
- **Unit tests**: when the component is about an algorithm or a single piece of business logic of the software system
- **Integration tests**: whenever the component under test interacts with an external component (e.g., a database or a web service)
- **System tests**: parts of the system that are absolutely critical and could have devastating effects if a bug is present
- **Manual tests**: should be avoided as much as possible but exploratory testing can be proven useful

The *ice-cream cone* anti-pattern should be avoided as much as possible:

<div align="center"><img src="https://sttp.site/chapters/pragmatic-testing/img/testing-pyramid/ice_cream_cone.svg"></div>

#### Test doubles
There are still challenges with unit testing classes that depend on other classes or on external infrastructure - this is where **test doubles** come in handy. These simulate the behaviour of other objects and have that they offer more control and are faster than full implementations as advantage.

There are five types of test doubles:
- **Dummy objects**: objects that are passed to the class under test but are never used
- **Fake objects**: working implementation of a class but in a much simpler way
- **Stubs**: hard-coded answers to the calls that are performed
- **Spies**: doesn't simulate but rather records interactions with underlying class
- **Mocks**: combination of fake objects and stubs such that they're pre-configured ahead of time

**Mockito** is one of the most popular mocking/stubbing frameworks for Java and is easy to set up. The most important methods are as follows:
- `Mockito.mock(<class>.class)`: creates a mock object/stub of a given class
- `when(<mock>.<method>).thenReturn(<value>)`: defines the behaviour when the given method is called on the mock
- `verify(<mock>).<method>`: asserts that the mock object was exercised in the expected way for the given method

```Java
import static java.util.Arrays.asList;
import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.Mockito.when;

public class InvoiceFilterTest {
    private final IssuedInvoices issuedInvoices = Mockito.mock(IssuedInvoices.class);
    private final InvoiceFilter filter = new InvoiceFilter(issuedInvoices);

    @Test
    void filterInvoices() {
    	final var mauricio = new Invoice("Mauricio", 20);
    	final var steve = new Invoice("Steve", 99);
    	final var arie = new Invoice("Arie", 300);

		// .lowValueInvoices() calls .all() in its implementation
    	when(issuedInvoices.all()).thenReturn(asList(mauricio, arie, steve));

    	assertThat(filter.lowValueInvoices()).containsExactlyInAnyOrder(mauricio, steve);
    }
}
```

Certain assertions are not part of JUnit's standard library but rather come with other libraries - `assertThat().containsExactlyInAnyOrder()` is one of them and is part of the AssertJ project.

It's important to note that classes which are not under test are the ones to be mocked with the assumption that they have a fully working implementation. Mocking means not only defining what methods do, but also explicitly defining how the interactions with the mock should be.

Mockito allows us to be much more restrictive as well when verifying interactions:

```Java
verify(sap, times(2)).send(any(Invoice.class));
verify(sap, times(1)).send(mauricio);
verify(sap, times(1)).send(steve);
```

Static methods cannot be mocked and thus, when a static object needs to be mocked, an abstraction is put on top such that the abstraction makes the static calls internally whilst still being mockable.

Dependencies that are slow or communicate with external infrastructure or in hard to simulate cases usually require stubbing/mocking for efficient unit testing. Entities and native libraries and methods on the other hand should not be mocked.

A disadvantage of mocks is that the test suite tests the mock and not the underlying code of the mocked object. Changes made to the object does not directly appear to the mocked object!

**Interaction testing** observes each individual interaction with the object under test rather than the output of a set of interactions.

#### Design for testability
**Dependency injection** allows for easier mocking by being able to pass a mocked object to the constructor or setter instead of instantiating one within the method.

```Java
public class InvoiceFilter {

  	public void filter() {
    	IssuedInvoices dao = new IssuedInvoices();

		// method logic
  	}
}

// INSTEAD MAKE USE OF A CONSTRUCTOR AND A SEPARATE filter() method

public InvoiceFilter(IssuedInvoices issuedInvoices) {
    this.issuedInvoices = issuedInvoices;
}
```

The **domain-infrastructure methodology** specifies that objects & logic (domain) and the means of interacting with external components (infrastructure) should be separated in the codebase - this allows for easier testing.

The **Ports and Adapters** or **Hexagonal Architecture** idea is that of instead of directly interacting with infrastructure, domains should interact with ports which then interact with the infrastructure - achieved with the use of interfaces.

<div align="center"><img src="https://sttp.site/chapters/pragmatic-testing/img/design-for-testability/hexagonal_architecture.svg"></div>

The **dependency inversion principle** states that high-level modules should not depend on low-level modules but rather on abstractions, as well as abstractions shouldn't depend on concrete implementations but concrete implementations should depend on abstractions.

At last, a handful of implementation-level tips:
- Create cohesive classes that have a single responsibility, this reduces the number of test cases for a single class
- Limit coupling of classes and thus reduce the number of dependencies between them, use abstractions
- Avoid complex conditions as they significantly increase the number of test cases
- Private methods should be tested through their public counterparts
- Steer clear of the use of static methods whenever possible

The key to testability is abstraction – dependencies should be ‘hidden’ by making use of abstraction classes which pays off in testability.

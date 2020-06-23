## **DESIGN BY CONTRACTS AND PROPERTY-BASED TESTING**

A system can be self-testing in the sense that production and test code are no longer fully separated. This is done by inserting `Assertions` into production code and having them throw an `AssertionError` when such assertion fails. Assertions are not enabled by default and are simply skipped if Java isn't run with the `java -enableassertions` argument.

```Java
assert <condition> : "<message>";
```

This asserts the `<condition>` and if false throws an `AssertionError` with the optional `<message>` for debugging. Note that assertions do not replace but rather compliment unit testing.

A **Hoare Triple** consists of a set of pre-conditions `{P}`, a program A and a set of post-conditions `{Q}` such that if all pre-conditions hold and A is executed, all post-conditions will hold.

```Java
public void merge(List<Book> books) {
    assert books != null : "The list of books is null";
    assert favourites != null : "The favourites list is null";
    assert !books.isEmpty() : "There are no books in the given list";
    assert !favorites.containsAll(books) : "There are no new books to be added";

    favourites.addAll(books);
    pushNotification.booksAdded(books);
}
```

By weakening the pre-conditions the method can handle more situations but it will require more logic to perform checks on what else would have been pre-conditions.

The post-conditions formalise the effects that a method guarantees to have when it's done with its execution. It's important to note that post-conditions aren't guaranteed if the pre-conditions do not hold before executing the method.

An **invariant** is a condition that must hold throughout the entire lifetime of a system, object or data structure - a pre-post-condition for the bigger picture. A separate `invariant()` method is usually created which is asserted before and after executing a method.

> "The **class invariant** indicates that a proposition P can be a class invariant if it holds after construction, and before and after any call to a public method assuming that the public methods are called with their pre-conditions being true." - Bertrand Meyer

#### Design by contracts
Client-server communication enforces a strict contract through the use of interfaces such that the server guarantees certain post-conditions as long as the client guarantees the pre-conditions - **design by contracts**.

This contract allows the server to implement a different implementation than the base interface. However, the pre-conditions of this new implementation should be as weak while the post-conditions should be as strong as the interface - the implementation's invariant should also be as strong.

The **Liskov Substitution Principle** states that subclasses of a certain superclass should be allowed to be substituted in any case where the superclass is expected. This principle also applies to testing where a certain superclass’ test suite contains the common test methods that its subclasses extend from. The principle behind this is called **parallel class hierarchy**.

<div align="center"><img src="https://sttp.site/chapters/testing-techniques/img/design-by-contracts/examples/parallel_architecture.svg"></div>

As `List` is an interface, so should `ListTest` be which hosts all shared tests. To assure that the subclass is used, an abstract `createList()` method is defined which needs to be overridden in the test suite for its subclasses - this will return a new instance of the subclass.
```Java
public abstract class ListTest {

  protected final List list = createList();

  protected abstract List createList();

  // Common List tests using list
}

// AND ITS IMPLEMENTATION IN THE SUBCLASSES' TEST SUITE:

public class ArrayListTest extends ListTest {

  @Override
  protected List createList() {
    return new ArrayList();
  }

  // Tests specific for the ArrayList
}
```

#### Property-based testing
Instead of just using properties in assertions, they can be used to generate test cases. These are tested through random input generation and checked using assertions. In Java *jqwik* is used as such and it will attempt to shrink the dataset once a failing input for a property has been found for easier debugging.

```Java
@Property
void concatenationLength(@ForAll String s1, @ForAll String s2) {
	String s3 = s1 + s2;

    Assertions.assertEquals(s1.length() + s2.length(), s3.length());
}
```

A complete example:
```Java
public class PassingGradesPBTest {

    private final PassingGrade pg = new PassingGrade();

    @Property
    void fail(@ForAll @FloatRange(min = 1f, max = 5.0f, maxIncluded = false) float grade) {
        assertThat(pg.passed(grade)).isFalse();
    }

    @Property
    void pass(@ForAll @FloatRange(min = 5.0f, max = 10.0f, maxIncluded = true) float grade) {
        assertThat(pg.passed(grade)).isTrue();
    }

    @Property
    void invalid(@ForAll("invalidGrades") float grade) {
        assertThatThrownBy(() -> {
            pg.passed(grade);
        })
        .isInstanceOf(IllegalArgumentException.class);
    }

    @Provide
    private Arbitrary<Float> invalidGrades() {
        return Arbitraries.oneOf(
                Arbitraries.floats().lessOrEqual(0.9f),
                Arbitraries.floats().greaterOrEqual(10.1f));
    }
}
```

## **SPECIFICATION-BASED AND BOUNDARY TESTING**

#### Specification-based testing
Specification-based testing or **black box testing** takes all possible input cases based on the provided specifications - devise a set of inputs, where each input tackles one part (or *partition*) of the program. These *partitions* or *classes* are unique, such that no two partitions exercise the same behaviour, and all behaviour for a given input can be easily verified. The idea that all inputs from a certain class result in the same output is called **equivalence partitioning**.

**Category-partition** method is a systematic way of deriving test cases and brings them down to a feasible amount:
1. Identify the arguments of the method
2. Derive the characteristics of these arguments (`int year` should be a natural number)
3. Minimise test cases by adding constraints based on the limitations of the method (remove invalid input combinations and a single exceptional case check, such as `null`, might be enough)
4. Use combinations of the input values as test cases

#### Boundary testing
**Boundary testing** explores the boundaries between partitions and test all the different sides within a single test case. A *boundary* is defined as a pair of consecutive input values which both equal to different partitions.

Some terminology associated with boundary testing now follows:
- The **on-point** is the value that is exactly on the boundary, this is the value we see in the condition itself
- The **off-point** is the value that is closest to the boundary which flips the condition - if the on-point makes the condition true, the off-point makes it false
- **In-points** are all the values that make the condition true and **out-points** are all the values that make the condition false

Testing a boundary would consist of four individual cases: one for each different type of point. Note that on- and off-points are also in- or out-points! Therefore, tests that focus only on the on- and off-points would also be testing the in- and out-points.

**Parameterised tests** allow for a single test method to be written which takes a *generator* method to feed it with inputs. This will reduce the number of test methods to one if the test cases all share similar code but take different inputs.

Instead of using the `@Test` annotation, the `@ParameterizedTest` annotation in combination with a source are used. The parameterised test takes arguments and is usually either fed a `CsvSource` or generator method which returns a `Stream<Arguments>` with the `@MethodSource` annotation.

```Java
@ParameterizedTest
@CsvSource({
	"1,1,5,0", "1,1,6,1", "1,1,7,-1", "1,1,8,-1"
})
void boundaries(int small, int big, int total, int expectedResult) {
    int result = new ChocolateBars().calculate(small, big, total);
    Assertions.assertEquals(expectedResult, result);
}

// WHICH IS EQUAL TO:

@ParameterizedTest
@MethodSource("generator")
void boundaries(int small, int big, int total, int expectedResult) {
    int result = new ChocolateBars().calculate(small, big, total);
    Assertions.assertEquals(expectedResult, result);
}

private static Stream<Arguments> generator() {
    return Stream.of(
    	Arguments.of(1,1,5,0),
    	Arguments.of(1,1,6,1),
    	Arguments.of(1,1,7,-1),
    	Arguments.of(1,1,8,-1)
    );
}
```

The **CORRECT** way of testing boundary points:
- **C**onformance
	- Many data elements must conform to a specific format - test what happens the input is not in conformance with what is expected
- **O**rdering
	- Some inputs might come in a specific order - make sure the program works even if the data comes in an unordered manner
- **R**ange
	- Inputs should usually be within a certain range - test what happens when inputs are provided outside of the expected range
- **R**eference
	- Sometimes relationships between objects are extensive and there may be external dependencies - when testing consider all conditions that must be met
- **E**xistence
	- Does "something" really exist - test whether the system behaves correctly when something that is expected to exist doesn't
- **C**ardinality
	- A loop performed one step less (or more) than it should - test loops in different situations, such as when it performs zero, one or many iterations
- **T**ime
	- Systems rely on dates and times - test whether dates, timeouts and concurrency is handled well

**Domain testing** is the combination of *equivalent class analysis* and boundary testing into one strategy.

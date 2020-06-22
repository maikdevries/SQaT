## **INTRODUCTION TO SOFTWARE TESTING**

#### **Why software testing**
Software testing is of importance due to how much of an impact even the smallest bugs can have. Developers work with the most complex systems out there, a simple manual check isn't enough to guarantee an error-free system.

There are several advantages to automated testing: less prone to mistakes than manual testing, faster than manual testing and confidence during refactoring.

#### **Software testing automation with JUnit**
Test cases (also referred to as *classes* or *partitions*) are made up of a short description, concrete example of inputs and an expected output. Tests are annotated with `@Test` and named after the case it puts under test. `Assertions` are used to assert the correct output given that the right input was passed to the method under test - if this fails an `AssertionError` is thrown and the test fails.
`assertEquals`, `assertTrue`, `assertFalse` are a few examples but many more are part of the JUnit testing framework.

All tests need to be isolated from each other to avoid a possible influence on the outcome. This is usually achieved by creating a 'clean' instance of the `Object` under test. Instead of doing this for each test method, JUnit offers the `@BeforeEach` annotation which allows for a `setup()` method to be defined which is executed before each test case. Another option is to define these `Object`s as class properties - JUnit will then create a new instance before each test.

```Java
@BeforeEach
void setup() {
	roman = new RomanNumeral();
}

// WHICH IS EQUAL TO:

final private RomanNumeral roman = new RomanNumeral();
```

It is important to treat your testing code like you would your production code and thus: get to know your testing framework and never stop refactoring.

Automated tests almost always follow the **AAA** structure: **A**rrange (define all input values to be passed to class/method under test), **A**ct (calls the methods under test) and **A**ssert (assert that the system behaved as expected).

#### **Principles of software testing**
A *mistake* or *error* by a developer can lead to a *fault*, *defect* or *bug* in the source code that will eventually result in a *failure* - not all faults directly cause failures as the code may never be executed.

**Validation** techniques focus on understanding whether the software system is delivering the business value it should deliver. **Verification** is about the system behaving as it is supposed to, according to the specification.

Exhaustive testing is impossible as not every single case can possibly tested given a limit in time and money. Bugs are also not uniformly distributed among code, certain components are more likely to fail.

The **pesticide paradox** describes the situation in which it is impossible to guarantee software to be bug-free based on a single testing strategy, a single tool cannot get the job done by itself. The testing strategy used is also determined by the context – testing is context-dependent. The **absence of errors fallacy** describes a situation in which developers focus a lot on verification but not so much on validation.

> “Program testing can be used to show the presence of bugs, but never to show their absence” – Dijkstra

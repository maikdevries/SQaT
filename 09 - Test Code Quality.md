## **TEST CODE QUALITY**

#### Test code quality
Good tests all have the five **FIRST** properties:
- **F**ast: tests need to be fast to provide the developer with instant feedback on the newly written code
- **I**solated: tests should be as cohesive, independent and isolated as possible - short and simple
- **R**epeatable: tests should report the same result no matter how many times it is executed
- **S**elf-validating: tests should validate and assert the result themselves
- **T**imely: tests should be written and run as often as possible

Other good properties for tests to have are:
- Inspiring: passing tests should create confidence
- Writeable: cheap to write relative to the cost of the code being tested
- Readable: comprehensible to an outsider
- Behavioural: changes in behaviour in code should affect test result
- Structure-insensitive: changes in code structure shouldn't affect test outcome
- Automated: tests should be run without any human input
- Specific: cause of test failing should be obvious
- Predictive: if all tests pass the code should be suitable for production

**Test code smells** are the anti-pattern of the properties mentioned above and indicate possible symptoms that might be caused by deeper problems in the source code of the system.

**Code duplication** is a very common test code smell and easily avoidable by refactoring or making use of concepts such as parameterized tests. **Assertion roulette** describes a smell where it is not clear why a test failed due to complex assertions or a high number of assertions in a single test method. It's recommended to limit the number of assertions per method and write abstractions in case of complex assertions.

The assumption that the resource is readily available and in the correct state at the start of the test is called **resource optimism**. This can be avoided by not using external resources and instead stubs and mocks or by making the test suite robust. Speaking of resources, the **test run war** smell describes a situation in which two test cases are battling for the same resource - either on the same computer or through a centralised resource accessed by multiple people at once.

A **general fixture** is a smell which sets one input to be used in all tests, these are unmaintainable and difficult to debug. **Indirect tests** or **eager tests** describe a situation in which a test suite focusses on more than one unit and instead try to test multiple properties at once without a clear distinction.

The **sensitive equality** smell is caused by not directly asserting the method to be tested and thus relying on the implementation of the indirect method to not be changed. In addition, **inappropriate assertions** are caused by making use of assertions that are not specific enough and thus not reveal much when the test fails.

Lastly, the **mystery guest** smell happens in cases where dependencies are hidden from the developer and thus making it more difficult to comprehend why a test is failing in certain cases. Usually solved by having proper error messages.

<br>

Tests should be structured in a way that it is clear to the developer what is going on - the arrange, act, assert structure improves readability significantly.

Making use of a `Builder` is an example of the **Test Data Builder** design, which improves test code readability.

```Java
public class InvoiceBuilder {

    private String country = "NL";
    private CustomerType customerType = CustomerType.PERSON;
    private BigDecimal value = new BigDecimal("500.0");

    public InvoiceBuilder withCountry(String country) {
        this.country = country;
        return this;
    }

    public InvoiceBuilder asCompany() {
        this.customerType = CustomerType.COMPANY;
        return this;
    }

    public InvoiceBuilder withAValueOf(String value) {
        this.value = new BigDecimal(value);
        return this;
    }

    public Invoice build() {
        return new Invoice(value, country, customerType);
    }
}
```

Making use of test data builders, naming variables intuitively to explain the meaning of the information, having clear assertions, and adding comments in cases where code is not expressive enough will help developers in better comprehending test code.

A test can be flaky because it relies on external or shared resources, has improper time-outs when waiting for an external service to respond or because of some hidden interactions between two test methods influencing the test result.

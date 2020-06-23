## **STATIC, FUZZ AND SECURITY TESTING**

#### Static testing
**Static testing** analyses the code without executing it - it checks the style and structure of the code.

**Pattern matching** is a static testing strategy and makes use of RegEx. An example for the regular expression `.*bug.*` which accepts any string containing the letters `b`, `u`, `g` consecutively and if at least once.

<div align="center"><img src="https://sttp.site/chapters/intelligent-testing/img/static-testing/regex3.png"></div>

To perform **syntax analysis**, *tokens* based on the code are put together in a tree according to the code's logical structure. A **parse tree** is a concrete instantiation of the code where each character has a node. **Abstract Syntax Tree (AST)** is an abstracted version of a parse tree where syntax-related characters are removed.

```Java
boolean DEBUG = false;

if (DEBUG){
  System.out.println("Debug line 1");
  System.out.println("Debug line 2");
  System.out.println("Debug line 3");
}
```

An example AST for the following code now follows:

<div align="center"><img src="https://sttp.site/chapters/intelligent-testing/img/static-testing/ast-example.png"></div>

A static analysis tool takes an AST and a rule-set as input and raises an error if a rule is violated. In addition, ASTs are used by compilers to find semantic errors (compile time errors) and for program verification, type-checking or translation.

Static analysis is very *sound* as the tools used have access to the entire codebase, but are lacking in *completeness* as it does not know the bigger picture of the application.

#### Fuzz testing
**Fuzz testing** is to dynamically test the system under test by randomly generating complex test cases in the hope that the system crashes - these can originate from failing assertions, memory leaks or improper error handling. Note that fuzzy cannot reveal bugs that do not cause a crash!

**Random fuzzing** is the most primitive type and performs black-box testing with no assumptions on the type and formatting of the input.

There are two ways of generating fuzzing test cases:
- **Mutation-based**: creates permutations from example inputs
- **Generation-based** or **Protocol fuzzing**: takes the file format and protocol specification into account

Code coverage of fuzz testing can be increased by using multiple tools, using telemetry for heuristics (stopping criteria to reduce run-time) and making use of **symbolic variables**.

#### Security testing
The goal of **security testing** is to find bugs that can potentially allow an intruder to make the software behave insecurely and is not a once and for all trick, new vulnerabilities pop-up daily.

Each vulnerability is assigned a unique **CVE (Common Vulnerabilities and Exposures)** identifier, a **CWE (Common Weakness Enumeration)** that determines the type of vulnerability, and a **CVSS (Common Vulnerability Scoring System)** score that determines the severity of the vulnerability.

A ***code injection vulnerability** allows an intruder to inject and execute code at run-time through malicious dependencies. The **type confusion vulnerability** exposes the unwanted casting of one object into another which has more privileges.

An **arbitrary code execution (ACE)** is exposed when there is improper handling of special code elements which can then cause arbitrary memory pointers to be readjusted and abused. When an ACE is triggered remotely, it is called a **Remote Code Execution (RCE)** vulnerability.

The secure software development life cycle includes security testing in every stage:

<div align="center"><img src="https://sttp.site/chapters/non-functional-testing/img/security-testing/ssdlc.png"></div>

<br>

Security testing is a broad term and covers a number of overlapping concepts:

|         |    White-box    |    Black-box    |
|------------|----------------------------------------------------|-------------------------------------------------------------------------|
|    **Static Application Security Testing**    | Code checking, Pattern matching, ASTs, CFGs,  DFDs  |  Reverse engineering|
|    **Dynamic Application Security Testing**    | Tainting, Dynamic validation, Symbolic  execution | Penetration testing,  Reverse engineering, Behavioural analysis,  Fuzzing |

Designing an ideal testing tool requires a balance between **soundness** (no false negatives – no vulnerability should be skipped) and **completeness** (no false positives – no false alarms). In addition a tool should be interpretable and scalable.

##### Static Application Security Testing (SAST)
SAST aims to find bugs without running the application and thus performs source code checking. **Risk-based testing** is a way of modelling the worst-case scenarios using threat modelling.

Pattern matching through RegEx and syntax analysis via ASTs are code checking strategies, which have been discussed in previous chapters. Control-flow graphs are used for structural security testing to show possible unintended transitions.

**Data Flow Diagrams (DFD)** are built on top of CFGs and show how the data flows through the application. Data Flow Analysis tracks user-controlled variables (Source), with the other variables being called Sinks. Source variables are tainted and need to be untainted by proper input validation before being connected to Sinks.

**Reaching Definitions Analysis** is a top-down approach that identifies all possible values of a variable using DFA.

##### Dynamic Application Security Testing (DAST)
DAST techniques execute an application and observe its behaviour. They are difficult to set up as they usually need to be hooked-up with the SUT and thus modifies the codebase.

**Taint analysis** is the dynamic version of DFA - tainted variables are kept track of in a so-called *taint table*. **Dynamic validation** performs functional testing based on the system specifications - **model checking** is a similar idea in which specifications are cross-checked with a model learnt from the SUT.

**Penetration testing** is the most commonly used type of security testing in organisations and is also known as ethical hacking. It's done from the attacker's perspective and is only possible once the application has been fully implemented.

**Behavioural logs** are used to gain insights about the behaviour of the SUT and are compared to expected behaviour. **Reverse engineering (RE)** is not specifically used as testing technique but rather to gain knowledge of the internals of the SUT. Fuzzing can also be used for security testing.

SAST tools create a lot of false positives as they don't have access to run-time behaviour and can create false negatives if they don't have access to some code. DAST tools reduce both false positives and false negatives but cannot ensure perfect testing. In addition, static analysis is more white-box with dynamic analysis being more black-box. At last, SAST is more scalable in the sense that it's faster but DAST is more generalisable.

## **STRUCTURAL-BASED TESTING**

#### Structural testing
Techniques that use the structure of the source code as a way to guide the testing are called **structural testing** techniques. In order to understand techniques related to structural testing, various *coverage criteria* need to be discussed:
- Line and statement coverage
- Block coverage
- Branch/decision coverage
- Condition coverage
- Path coverage
- MC/DC coverage

We need structural testing for two reasons: systematically derive tests from source code and when to stop testing. The different coverage criteria imply different methods of devising test cases and criteria on when to stop testing.

##### Line and statement coverage
The number of lines of code that is covered by at least one test and is computed by `(lines covered / lines in total) * 100%`. **Line coverage** is heavily influenced by the developers' personal code styles and thus doesn't offer the required insights. Certain coverage tools instead use statements executed by the program (individual instructions), but this is still not good enough.

##### Block coverage
A **control-fow graph** (CFG) consists of *basic blocks* and *decision blocks* connected by *arrows/edges* to represent all paths that can be traversed.

<div align="center"><img src="https://sttp.site/chapters/testing-techniques/img/structural-testing/examples/bj-p2.png"></div>

`for` loops are split into three blocks: *basic* variable initialisation block, *decision* condition block and *basic* increment block.

Based on CFGs, tests can be devised which aim to cover 100% of the basic and decision blocks and is calculated with `(blocks covered / blocks in total) * 100%`.

##### Branch/decision coverage
For **branch coverage** (also called decision coverage) the idea is to cover all the possible decision outcomes of the decision blocks in a CFG. This means that there are 2 tests per decision block - respectively testing the `true` and `false` branch. Calculated by computing `(decision outcomes covered / decision outcomes in total) * 100%`.

##### Condition coverage
Branch coverage doesn't consider the number of conditions that are part of a decision block and always leads to two cases: `true` and `false`. **Basic condition coverage** instead aims to cover both `true` and `false` branches for each individual condition that is part of a decision block. This is usually paired with a rewritten CFG in which all conditions part of an `if` statement have their own decision block. Coverage is calculated using `(condition outcomes covered / condition outcomes in total) * 100%`.

However, in practice **branch + condition coverage** is used due to certain cases in which 100% condition coverage is achieved but not all of the branches are executed. Instead, **C/DC** (condition/decision coverage) aims for 100% condition coverage and 100% branch coverage and is calculated as follows: `((condition outcomes covered + decision outcomes covered) / (condition outcomes in total + decision outcomes in total)) * 100%`.

##### Path coverage
**Path coverage** aims to fully cover each individual path an execution can take based on the number of conditions. In order to achieve a 100% path coverage all possible combinations of conditions have to be tested and is computed with `(paths covered / paths total) *100%`.

##### MC/DC coverage
The idea behind **Modified condition/decision coverage** is to test every single condition such that it influences the outcome at least once. The number of tests to achieve 100% MC/DC coverage is `N + 1` where N is the number of conditions.

These test cases can be found using a truth table and identifying *independence pairs* - two set of truth values where the outcome changes by a truth value change of a single condition. To achieve 100% coverage each condition needs to have a test exercising one independence pair.

<br>

The **loop boundary adequacy criterion** describes that a loop should be exercised zero, once and multiple times to fully test them.

A 100% coverage for certain strategies imply full coverage of other strategies as shown in the following figure:
<div align="center"><img src="https://sttp.site/chapters/testing-techniques/img/structural-testing/subsumption.png"></div>

A common misunderstanding is to confuse structural testing with structural coverage - structural testing is to leverage structural coverage strategies to obtain test cases. In addition, structural testing does not guarantee a valid program, requirements should always be met before attempting any other testing strategy.

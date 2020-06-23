## **MODEL-BASED TESTING**

#### Model-based testing
Models are a simpler way to describe the system under test, it holds some of the attributes of the system for which it is built.

##### Decision tables
**Decision tables** contain the conditions and the actions performed by the system based on these conditions. In certain cases a value of a condition will not affect the action, **don't care values** are  used as such.

<div align="center"><table>
  <tr><th></th><th></th><th colspan="4">Variants</th></tr>
  <tr><td rowspan="2"><br><i>Conditions</i></td>
  <td>&lt;Condition1&gt;</td><td>T</td><td>T</td><td>F<br></td><td>F</td></tr>
  <tr><td>&lt;Condition2&gt;</td><td>T<br></td><td>F</td><td>T</td><td>F</td></tr>
  <tr><td><i>Action</i></td><td>&lt;Action&gt;</td><td>value1<br></td><td>value2</td><td>value3</td><td>value4</td></tr>
</table></div>

A **default behaviour** allows for decision tables to be condensed, but these are not specified in decision table as such!

There are multiple ways of deriving test cases based on decision tables:
- Derive a test case for each column
- Derive a test case for each possible combination of condition values
- One test case for each unique outcome or action
- Each condition is true and false at least once in the test suite

MC/DC still results in the lowest number of test cases with the best coverage and test cases are devised by finding independence pairs, just like with structural-based testing. Decision tables can also contain non-boolean parameters in which case achieving full MC/DC coverage means covering all possible values for these.

Some general guidelines to keep in mind when designing decision tables:
- Keep conditions independent - the order of the conditions should not matter, otherwise a state machine might be more fitting
- Use DC values when possible
- Variants with DC values should not overlap
- Add a default column
- Consider non-boolean conditions if conditions are mutually exclusive
- If most conditions are non-boolean, consider using a different combinatorial testing technique instead

##### State machines
**State machines** describe a model based on the different states of a system and transitions between these states to illustrate a system's behaviour. A state machine starts in an initial state from which it transitions to other states through events which describe the action taken. State machines also contain conditional transitions (annotated with `[condition]`) and actions (annotated with `/action`).

<div align="center"><img src="https://sttp.site/chapters/testing-techniques/img/model-based-testing/examples/blocked_complete_machine.svg"></div>

There are three main ways to define test coverage for state machines:
- **State coverage**: each state has to be reached at least once
- **Transition coverage**: each transition has to be exercised at least once
- **Paths**: all possible paths that can be taken through the system

State coverage is tested by bringing a system into a state through transitions and asserting the state. Testing transitions is a bit more cumbersome and requires the following steps:
1. Get the system in the state to transition out of
2. Assert the current state
3. Trigger the transition
4. Check if action happened if any at all
5. Assert the new state

Testing all possible paths is infeasible and thus requires a different approach. A **transition tree** spans the graph of the state machine and is defined as follows:
- Root node is initial state of state machine
- For each node that has not been covered before add the outgoing transitions to new child nodes

<div align="center"><img src="https://sttp.site/chapters/testing-techniques/img/model-based-testing/examples/transition_tree/transition_tree_3.svg"></div>

The leaf nodes are used to derive test cases, each represent a unique path through the state machine and thus a single case to be tested.

A **sneak path** is a path that should not be allowed to be taken. A *transition table* helps us identify possible sneak paths:

<div align="center"><table>
  <tr><td>STATES</td><td colspan="5">Events</td></tr>
  <tr><td></td><td>home</td><td>wrong password</td><td>correct password</td><td>lock button</td><td>long lock button</td></tr>
  <tr><td>OFF</td><td>LOCKED</td><td></td><td></td><td></td><td></td></tr>
  <tr><td>LOCKED</td><td></td><td>LOCKED</td><td>UNLOCKED</td><td></td><td>OFF</td></tr>
  <tr><td>UNLOCKED</td><td></td><td></td><td></td><td>LOCKED</td><td>OFF</td></tr>
</table></div>

All empty cells in the transition table represent one test case in which the state is asserted before and after triggering an event to rule out possible sneak paths.

When state machines grow too large and get too complicated, they're usually managed in **super states** and **regions**. A super state is simply a wrapper for a state machine such that it can be used as state in another state machine.

<div align="center"><img src="https://sttp.site/chapters/testing-techniques/img/model-based-testing/examples/phone_super_state.svg"></div>

A super state can be split up in multiple regions which each have their own state machine. These function independently and allow the system to be in multiple states at once. When a state machine enters a super state with multiple regions it enters all the initial states of the individual state machines at once.

<div align="center"><img src="https://sttp.site/chapters/testing-techniques/img/model-based-testing/examples/phone_region.svg"></div>

<br>

Most classes in OOP correspond to their own little state machine and offer two types of methods: **inspection** and **trigger** methods - inspection methods offer information on the values or fields of an object and trigger methods get the class into a new state. A test scenario is a series of trigger method calls which are asserted using inspection methods.

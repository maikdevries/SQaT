## **WEB TESTING**

#### Web testing
**Web testing** is the idea of testing client-server applications that are all over the internet and accessed through your browser. Intertwining JavaScript and HTML on the frontend should be avoided to increase testability, this is usually the biggest challenge.

Testing frontend and backend separately will not cover the entire spectrum and instead **end-to-end tests** should be performed too - these are similar to system tests.

The goal of end-to-end testing is to test the flow through the application as a user might follow it, while integrating the various components of the web application. These can be done manually but many automated tools exist too - the WebDriver API is now a W3C standard.

The audience for a web application is quite diverse and thus **usability testing** and **accessibility testing** are a priority. In addition the load can be quite high and thus **load testing** is a requirement too. Lastly, certain people may have bad intentions and thus **security testing** is of utmost importance.

Usability testing focusses on user-friendliness and making the application pleasant and easy to use. Accessibility testing takes care of how accessible it is to a wider range of people with disabilities.

**Cross-browser testing** aims to ensure that the application works for all supported browsers. Developers may make use of **responsive web design** which makes use of certain browser properties to adjust the application's behaviour (window size) and is something that should be tested too. A big part is **UI component testing** which can easily be achieved by making use of **snapshot testing**.

Snapshot testing renders the DOM multiple times and compares the changes to the first render - the snapshot. The developer can mark changes as expected or unwanted and thus failing the test.

Keep in mind that JavaScript employed in today's web applications is asynchronous, this should be accounted for when writing tests to avoid flaky tests.

To write testable web applications JavaScript and HTML should be put in separate files such that JavaScript code can be tested. In addition code logic and UI should be separated too. At last, elements within the DOM should have clear IDs such that they can be easily found and manipulated.

It is rather tedious to write plain JavaScript and test them using native functionality – it can be done however, but as JavaScript doesn't a testing utility built-in, assertions such as `assertEquals()` would have to be written by the developer. Instead, usually frameworks and the recommended testing framework is used which offers advanced functionality.

> Lots of JavaScript testing code is omitted due to the lack of significance for the exam

Abstraction on top of web applications are called **Page Objects**. Whilst page objects are abstractions of single pages or fragments of pages, **state objects** are individual page objects that are part of a *navigational state machine*. This special type of state machine which describes the flow through a web application has a unique state for each page and possible transitions to and from each page.

In **behaviour-driven design** a system is described through scenarios. Each scenario has a title, pre-conditions (given), the action taken (when) and the post-conditions (then). Each transition requires its own scenario.

```
Given I have arrived in some state
When  I trigger a particular event
Then  the application conducts an action
 And  the application moves to some other state.
```

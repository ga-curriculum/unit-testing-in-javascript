# ![Unit Testing in JavaScript - Concepts](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to understand and explain the importance of unit testing.

## What is Unit Testing?

Unit testing is running tests on small, individual units of code (usually a function or method) to ensure that they run as expected. The idea is that smaller tests give us a very in-depth look at each piece of a larger puzzle, helping to eliminate bugs that can come about from multiple units of code interacting in unexpected or unplanned ways.

## Why use Unit Testing?

As we build web pages and applications, we frequently want to test our code to ensure it functions as intended. Smaller projects often involve logging some values to the console or checking to see if the correct information is displayed on the screen. 

As our codebase expands and complexity rises, however, we increasingly benefit from more robust testing - does the function work as expected, given a range of inputs? What about any edge cases that could introduce problems into our larger codebase? Being able to test for potential issues with each small part of our code means less time wasted overall trying to chase down tricky bugs. 

Let's look at a theoretical example: 

```js
const addTwo = (x,y) => {
  return x + y
}
```

Without thinking about how to test for this in code, let's consider how we'd test this function logically. 

If `x` is the number `7` and `y` is the number `3`, then we would expect the function to return the number `10`. This would be considered a logic check - does the function perform the correct calculations as intended?

Next, we might wonder about edge cases, or what about if the function doesn't receive the expected data? In this case, the `addTwo` function assumes it will receive numbers. What if `x` is 7 but `y` is `'3'`? Or even `'three'`? What if we only receive a single argument, and y is `undefined`? How does our function handle these types of scenarios? 

Currently, it doesn't handle them, which reveals some work we can do to improve the function and guard our larger codebase from potential errors. But even without writing an actual Unit Test, we've uncovered some issues with this code unit!

## Behavioral-Driven Development (BDD) and Test-Driven Development (TDD)

Unit testing is often associated with two different development methodologies: Behavioral-Driven Development (BDD) and Test-Driven Development (TDD).

**Behavioral-Driven Development (BDD)**

- **Use of natural language**. Tests are constructed using natural language such as "should" and "expect". The use of natural language makes **focusing on the behavior of the system** easier.
- **Integration with specifications**. The tests are closely tied to the requirements of the system. This ensures code is written to meet the requirements of the system.
- **Focused on behavior**. Tests are written with the desired behavior of the system in mind rather than the implementation details.

**Test-Driven Development (TDD)**

- **Focused on code design**. Tests are written before the code is written. Developers use a **test-code-refactor** cycle to write the code. Write the tests first, then only write enough code to pass the tests, and then refactor the code without changing the behavior or failing the tests. This ensures that the code is written to meet the requirements of the tests.
- **Implementation Details**. Tests focus on individual units of code and their implementation details. These tests are more granular and focus on method signatures and return values.
- **Incremental Development**. This testing style encourages incremental development, where developers write small, focused tests and code in short iterations.

While both Behavioral-Driven Development (BDD) and Test-Driven Development (TDD) have their own unique approaches, they share the same goal: to ensure that the code meets the requirements of the system and behaves as expected.

> During this module, we will be leaning towards the BDD approach. This is because this is an introductory lesson to be introduced to unit testing. BDD is a more natural way to introduce the concept of unit testing to students. Even though we will be using BDD, you can also apply what you learn here to TDD.

## Introducing Mocha and Chai

Unit testing in JavaScript can be done in several ways. Two of the most popular libraries for this are [Mocha](https://mochajs.org/) and [Chai](https://www.chaijs.com/).

- **Mocha** is a JavaScript test **framework** for Node.js that gives us most of the baseline functionality required to run asynchronous tests.
- **Chai**, meanwhile, is a Behavioral-Driven Development (BDD) and Test-Driven Development (TDD) assertion library for Node.js.

Mocha will provide us with the structure and functionality to run our tests. At the same time, Chai will give us access to several crucial APIs like Assert (for TDD) and Expect/Should (for BDD). We'll primarily focus on Expect/Should syntax, which uses chainable language to create fairly intuitive and readable testing.

> Chai can be paired with most JavaScript testing frameworks, but Chai and Mocha are a very popular and dynamic duo.

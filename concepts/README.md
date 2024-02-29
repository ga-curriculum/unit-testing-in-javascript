# ![Unit Testing in JavaScript - Concepts](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to understand and explain the importance of unit testing.


## What is Unit Testing?

Unit testing is the process of running tests on small, individual units of code (usually a function or method) to make sure that they run as expected. The idea is that smaller tests give us a very in-depth look at each individual piece of a larger puzzle, helping to eliminate bugs that can come about from multiple units of code interacting in unexpected or unplanned ways.

## Why use Unit Testing?

As we build web pages and applications, we frequently find ourselves in the position of wanting to test our code to make sure that it functions as we intended. With smaller projects, this often involves logging some values to the console, or simply checking to see if the correct information is displaying on the screen. 

As our codebase expands and complexity rises, however, we increasingly start to benefit from more robust testing - does the function truly work as expected, given a range of inputs? What about any edge cases that could introduce problems into our larger codebase? Being able to test for potential issues with each small part of our code means less time wasted overall trying to chase down tricky bugs. 

Let's look at a theoretical example: 

```js
const addTwo = (x,y) => {
  return x + y
}
```

Without thinking about how to test for this in code, let's just consider how we'd test this function logically. 

If `x` is the number `7` and `y` is the number `3`, then we would expect the function to return the number `10`. This would be considered a logic check - does the function actually preform the correct calculations as intended?

Next, we might wonder about edge cases, or what about if the function doesn't receive the data we expect? In this case, the `addTwo` function assumes it will receive numbers. What if `x` is 7 but `y` is `'3'`? Or even `'three'`? What if we only receive a single argument, and y is `undefined`? How does our function handle these types of scenarios? 

Currently, it doesn't handle them, which reveals some work we can do to improve the function and guard our larger codebase from potential errors. But even without writing an actual Unit Test, we've already uncovered some issues with this unit of code! 

## Introducing Mocha and Chai

[Mocha](https://mochajs.org/) is a JavaScript test **framework** for Node.js that gives us most of the baseline functionality required to run asynchronous tests. 

[Chai](https://www.chaijs.com/), meanwhile, is a BDD(Behavioral-Driven Development)/TDD(Test-Driven Development) assertion library for Node.js. The Chai library gives us access to several crucial APIs like Assert (for TDD) and Expect/Should (for BDD). We'll primarily focus on Expect/Should syntax, which use chainable language to create fairly intuitive and readable testing. We'll be using Chai to handle assertions in Mocha! 

Note that Chai can be paired with most JavaScript testing frameworks, but Chai and Mocha are a very popular and dynamic duo. 
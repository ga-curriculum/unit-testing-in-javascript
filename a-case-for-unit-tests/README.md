# ![Unit Testing in JavaScript - A Case for Unit Tests](./assets/hero.png)

**Learning Objective:** By the end of this lesson, students will be able to describe a scenario where unit testing prevents unintended changes to code.

## The scenario

Imagine there is a simple function in a large codebase called `add`:

```javascript
const add = (param1, param2) => {
  return param1 + param2;
};
```

It’s designed to do two things:

1. Add two numbers.
2. Concatenate two strings.

```javascript
// For adding two numbers:
const addNumbers = (2, 3) => 2 + 3;  // Returns 5

// For concatenating two strings:
const addStrings = ("hello", "world") => "hello" + "world";  // Returns "helloworld"
```

However, this code currently doesn't have any unit tests to ensure this functionality is consistent across the applications codebase.

## The Problem

One day, a developer decides to use `add` to concatenate a string and a number:

```javascript
console.log(add("1", 1)); // Logs "11"
```

In this case, the function treats the inputs as a string and a number, resulting in string concatenation instead of numerical addition. However, they expected `add` to perform numerical addition outputting `2` instead of `11`. To meet this expectation, they decide to alter the `add` function:

```javascript
const add = (param1, param2) => {
  return parseInt(param1) + parseInt(param2);
};
```

Great! Now `add("1", 1)` correctly logs `2`. However, this change unexpectedly breaks the string concatenation feature that other developers have implemented in other parts of the codebase:

```javascript
console.log(add("Jane ", "Doe")); // No longer works as intended!
```

It's a classic coding conundrum: a fix in one area leads to a breakdown in another.

## The solution: unit testing

If the original author of `add` had written unit tests, they could have tested the function with various inputs and adjusted the functions code to anticipate these scenarios. Let's look at a couple simple unit tests for our `add` function. Open `add.js` in your starter code and take a look at the test functions provided.

These tests help ensure that the function behaves as expected when given specific inputs:

```js
const unitTestExampleOne = () => {
  // Test adding two numbers
  console.log("Test 1: Adding two numbers (2 and 3)");
  console.log(add(2, 3) === 5 ? "Passed" : "Failed");
};

const unitTestExampleTwo = () => {
  // Test adding two non-numeric strings
  console.log('Test 2: Adding two non-numeric strings ("Hello " and "world")');
  console.log(add("Hello ", "world") === "Hello world" ? "Passed" : "Failed");
};

// TODO: write a test for two different data types as inputs

unitTestExampleOne();
unitTestExampleTwo();
```

If you run `add.js` in the terminal you will see the test results logged.

By writing tests _before_ writing your function code, you get to decide how a function should behave and then write the function to behave in that way. 

>  While libraries like [Mocha](https://mochajs.org/) or [Jest](https://jestjs.io/) provide additional functionality and more sophisticated logging for tests, using simple console.log statements is a perfectly valid way to perform basic unit testing. This approach is a great starting point for beginners or in situations where setting up a testing framework is not feasible.

### 🎓 You Do: Create a unit test

 - Create a third unit test to account for two different data types as parameters.

## Test, Code, Refactor

Now that we have a few unit tests, we can adjust our function so that those tests are always passing. 

```javascript
const add = (param1, param2) => {
  // Check if both parameters are numbers
  if (typeof param1 === "number" && typeof param2 === "number") {
    // Both are numbers, so add them
    return param1 + param2;
  }

  //Check is both parameters are strings
  if (typeof param1 === "string" && typeof param2 === "string") {
    // Both are strings, so concatenate them
    return param1 + param2;
  }

  // TODO: Write logic that accounts for parameters that are different data types

  // If no specified condition is met, return a default value or an error message
  return "Invalid input";
};
```

How could we alter our `add` function to account for parameters that are different data types?

# ![Unit Testing in JavaScript - Testing Express Routes](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to write tests for an Express route using Mocha and Chai.

## Our Express Application

For this lesson, we provided some starter code for an Express application. Let's dive into the code and see what has been provided.

**Model**

| User        | ---    |
| ----------- | ------ |
| name        | String |
| email       | String |
| password    | String |
| phoneNumber | String |

**Routes**

| Method | Path     | Description                 |
| ------ | -------- | --------------------------- |
| GET    | `/users` | Returns a list of all users |
| POST   | `/users` | Creates a new user          |

> Note that there is no connection to a database yet. If we were to try to run this application and make requests to these endpoints, we would get an error or would not get any data back.

This application will be the base for our testing. Let's go!

## Test Setup

Before we start writing tests, we need a file to hold these tests in. It is best practice to hold tests in a folder called `test` or `tests`, which is why we've provided one already in the starter code. Inside this folder, create a new file called `app.test.js`.

```bash
touch test/app.test.js
```

Like before, we are going to be using a combination of Mocha and Chai to write our tests. Mocha is our testing framework and we will be using the `expect` syntax from Chai. Let's import the `expect` syntax from Chai into `test/app.test.js`:

```js
// test/app.test.js

// Import the expect method from the chai library
const { expect } = require("chai");
```

We are testing an Express application, so we will need to import the `app.listen` method from our `server.js` file. Take a look at our starter code in `server.js`:

```js
// server.js

// This code is already in your server.js file
const server = app.listen(3000, () => {
  console.log("The express app is ready!");
});

module.exports = server;
```

We are saving the `app.listen` method to a variable called `server` and exporting it. This will allow us to start our server in our test file. Let's import this `server` variable into our `app.test.js` file.

```js
// test/app.test.js

const { expect } = require("chai");
// Import the server variable from the server.js file
const app = require("../server");
```

The benefit of exporting the `app.listen` method is that regardless of how large we scale our application, we can still test all of our routes.

## Before and After Hooks

In testing, particularly when dealing with databases, it's important to set up and clean up before and after running tests. Mocha provides special functions, known as hooks, for these tasks:

- **before:** Executes once before all the tests in a describe block. Use this for setup tasks that need to happen only once, like establishing a database connection.
- **beforeEach:** Runs before each test in a describe block. Ideal for setting up the state for each test individually, such as adding test data to a database.
- **after:** Executes once after all tests in a describe block have run. Use this for cleanup tasks that need to happen only once, such as closing database connections.
- **afterEach:** Runs after each test in a describe block. Helps in cleaning up after each test, like removing test data from the database.

So why are these important? In the practical sense, we need a way to connect to the database, then disconnect from it. In a larger sense, we also need a way to separate any data that we create for the test from any data that we have in our database. We only want to test the data that we create for the test, NOT the data that we have in our database. We are testing the functionality of our code, not the functionality of our database.

Let’s apply these hooks in the context of testing a `User` model in an application that interacts with a database.
Inside our `app.test.js` file, we'll create a `before` and `after` hook. We will use the `before` hook to connect to the database and the `after` hook to disconnect from the database.

Before writing the hooks, make sure your test file imports the necessary libraries:

```js
// test/app.test.js
const { expect } = require("chai");
const app = require("../server");

// Add the needed imports
// Import the dotenv library
const dotenv = require("dotenv");
// Import the .env file vars
dotenv.config();
// Import the mongoose library
const mongoose = require("mongoose");
// Import the User model
const User = require("../models/user");
```

We are going to need `dotenv`, `mongoose`, and the `User` model. We also need to import the `.env` file variables. We'll use the `dotenv` library to do this, which will allow us to import the `.env` file variables into our test file. This is important because we need to connect to the database and we need the `MONGODB_URI` to do this.

> You will need to add an `.env` file to your project and add the `MONGODB_URI` variable to it.

Next, we add our hooks:

```js
before(() => {
  // Connect to the database
});

// test cases will go here

after(() => {
  // Disconnect from the database
});
```

In our `before` hook, we are going to connect to the database and create a new user. You'll notice that the `before` hook takes a `done` callback function. This is because we are working with asynchronous code. We will be using the `done` function to tell Mocha when we are finished with our asynchronous code.

```js
// test/app.test.js

before((done) => {
  // Connect to the database
  mongoose.connect(process.env.MONGODB_URI);

  // Once connected to the database
  mongoose.connection.once("open", async () => {
    try {
      // Create a new user
      await User.create();
      done();
    } catch (error) {
      done(error);
    }
  });
});
```

Inside our `try...catch` block, we are creating a new user. We are using the `await` keyword to wait for the user to be created. Once the user is created, we call the `done` function to tell Mocha that we are done with our asynchronous code. But notice that our `User.create()` method is empty! We will need a mock user for our tests. Let's create a mock user and pass it to the `User.create()` method.

```js
// test/app.test.js

// Add in mock user data above the before hook
const mockUserData = [
  {
    name: "Jane Doe",
    email: "janeDoe@mail.com",
    password: "password123",
    phoneNumber: "1234567890",
  },
];

before((done) => {
  mongoose.connect(process.env.MONGODB_URI);

  mongoose.connection.once("open", async () => {
    try {
      // Pass in the mock user data
      await User.create(mockUserData);
      done();
    } catch (error) {
      done(error);
    }
  });
});
```

No `before` hook is complete without an `after` hook. We need to disconnect from the database after we are done with our tests. We will use the `after` hook to do this. Add the `after` hook to the bottom of the `app.test.js` file.

```js
// test/app.test.js

after((done) => {
  // On application close
  app.close(() => {
    // Drop the database
    mongoose.connection.db.dropDatabase(() => {
      // Close the database connection
      mongoose.connection.close();
    });
    // Tell Mocha we are done
    done();
  });
});
```

In testing our application, it's essential to perform clean-up operations after the tests are completed. This involves shutting down the server using the `app.close` method. Doing so ensures that the server is properly turned off and the resources it was using are freed up. For the database, the process involves two main steps. First, we clear the test database using `mongoose.connection.db.dropDatabase()`. This is like resetting the database, ensuring that each test run begins with a clean state. Following this, we close the database connection with `mongoose.connection.close()`, which is important for preventing open connections that could lead to issues.

We also use the `done` function in our tests. This function is part of Mocha, our testing framework, and it's used to indicate when asynchronous operations, like database interactions, are completed.

Note: Always be cautious with the `dropDatabase()` command for clearing the database. If this command were accidentally used on a production database, it could result in losing all the data stored there. To avoid such a risk, it’s a best practice to use a distinct database solely for testing. By doing this, we ensure that our actual production data is safe and unaffected by any testing activities. **Always use a separate database for testing.**

We have set up the `before` and `after` hooks. We are now ready to write our tests!

## Testing a route

In the starter code, we have been provided a `GET` route that returns a list of all users. Now, we want to write a test for this route to ensure it's working as expected. Before doing so, we need to address how to simulate requests to our server within our tests.

### Supertest for `http` requests

When running the application, we might typically use a tool like nodemon to start our server. However, for testing purposes, particularly when testing routes, we need a different approach. This is where [supertest](https://www.npmjs.com/package/supertest) comes in.

- **What is it?:** supertest is a library designed for testing Node.js HTTP servers. It allows us to make HTTP requests to our server in a testing environment.
- **How it Works:** supertest interacts with our server's `app.listen` method, enabling us to simulate client requests (like GET, POST) without having to run the server.

First, we need to import supertest into our test file. Let's add it to our existing imports in `app.test.js`:

```js
// test/app.test.js

const { expect } = require("chai");
const app = require("../server");
const dotenv = require("dotenv");
dotenv.config();
const mongoose = require("mongoose");
const User = require("../models/user");
// Import request from supertest
const request = require("supertest");
```

We are now ready to write our test!

In this test we want to use the `request` method to build a request to our server. First, the `request` method will need to be passed the `app` variable which is our application's listener. Then we will need to choose which type of request we want to send. We can do that by using the `get` method, which accepts a string that will be the path of the request. We'll write this in between the `before` and `after` hooks.

```js
// test/app.test.js

describe("GET /users", () => {
  it("responds with JSON containing the list of users", (done) => {
    // Use the request method to build a request to our server
    request(app)
      // Choose which type of request we want to send
      .get("/users");
  });
});
```

Now that we are making a GET request to our server, we will need to use the `expect` method to make an assertion. We'll assert that the response is a JSON object and the status code is `200`:

```js
// test/app.test.js

describe("GET /users", () => {
  it("responds with JSON containing the list of users", (done) => {
    request(app)
      .get("/users")
      // Test to ensure that the response is a JSON object
      .expect("Content-Type", /json/)
      // Test to ensure that the status code is 200
      .expect(200);
  });
});
```

Next, we will need to use the `end` method to to see the response and end the request/response cycle for the test:

```js
// test/app.test.js

describe("GET /users", () => {
  it("responds with JSON containing the list of users", (done) => {
    request(app)
      .get("/users")
      .expect("Content-Type", /json/)
      .expect(200)
      .end((err, res) => {
        if (err) {
          done(err); // Notify Mocha about the error
        } else {
          // Test the response body
          expect(res.body.users).to.be.an("array"); // Expect users to be in an array
          res.body.users.forEach((user) => {
            expect(user).to.have.property("name").that.is.a("string");
            expect(user).to.have.property("email").that.is.a("string");
          });
          done(); // Notify Mocha that the test is complete
        }
      });
  });
});
```

- The `end` method will take a callback function which will contain the `err` and `res` parameters.
- We are using an `if` statement to check if there is an error, in which case we will use the `done` function to tell Mocha that we are done with our asynchronous code.
- If there is no error we will use the `expect` method to make assertions about the response body. We are expecting the server to return a JSON object with a list of users.
- These users should have a property of `name` and `email` and they should be strings. We are using the `forEach` method to loop through the `res.body.users` array and make assertions about each user.

Congrats! You have written your first unit test for an express route! Let's run our test to see if it passes.

In the terminal, run the following command:

```bash
npm test
```

Great job! 

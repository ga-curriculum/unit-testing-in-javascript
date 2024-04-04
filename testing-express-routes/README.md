# ![Unit Testing in JavaScript - Testing Express Routes](./assets/tktk-hero.png)

**Learning objective:** By the end of this lesson, students will be able to write tests for an Express application using Mocha and Chai.

## Our Express Application

For this lesson, we provided some starter code for an Express application. Let's dive into the code and see what we have.

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

Before we start writing tests, we need a file to hold these test in. It is best practice to hold tests in a folder called `test` or `tests`, which is why we've provided one already in the starter code. Inside this folder, create a new file called `app.test.js`.

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

We are working with a `User` model for this application, and a lot of the tests that you might have to write will be interacting with a database of some kind. The Mocha framework provide us with some hooks we can use to run code before and after our tests. These hooks are called:

- `before` - runs once before all tests
- `beforeEach` - runs before each test
- `after` - runs once after all tests
- `afterEach` - runs after each test

So why are these important? In the practical sense, we need a way to connect to the database, then disconnect from it. In a larger sense, we also need a way to separate any data that we create for the test from any data that we have in our database. We only want to test the data that we create for the test, NOT the data that we have in our database. We are testing the functionality of our code, not the functionality of our database.

Inside our `app.test.js` file, let's create a `before` and `after` hook. We will use the `before` hook to connect to the database and the `after` hook to disconnect from the database.

First import the needed libraries.

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

For our `before` hook, we are going to connect to the database and create a new user. Notice that the `before` hook takes a `done` callback function. This is because we are working with asynchronous code. We will be using the `done` function in order to tell Mocha when we are done with our asynchronous code.

```js
// test/app.test.js

// Add in the before and after hooks
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

// Add in the after hook
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

We are using the `app.close` method to close the server. We are then using the `mongoose.connection.db.dropDatabase` method to drop the database, using the `mongoose.connection.close` method to close the database connection, then lastly using the `done` function to tell Mocha that we are done with our asynchronous code.

With dropping the database we are ensuring that we are not testing any data that we have in our database. But there is a risk to this - if we use the `dropDatabase` method in a production environment, we will lose all of our data. As a result, we'll need to be careful with this method. Best practice is to never use the database that you are using in production for testing. **Always use a separate database for testing.**

We have set up the `before` and `after` hooks. We are now ready to write our tests!

## Testing a Simple Route

In our app, we have a GET route that returns a list of all users. Next, we're going to write a test for this route. However we will need one more important item before we can write our test.

We have imported our `app.listen` method from our `server.js` file, however we have no way of making requests to this listener. Normally we would use an application runner like `nodemon` to start our server but for tests we will need to use a library called `supertest`. `supertest` will allow us to make requests to our server using `request`. It's already installed, so let's import at the top of our `app.test.js` file.

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

In this test we want to use the `request` method to build a request to our server. First, the `request` method will need to be passed the `app` variable which is our application's listener. Then we will need to choose which type of request we want to send. We can do that by using the `get` method, which accepts a string that will be the path of the request. We'll write this in between the `before` and `after` hook.

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

Lastly, we will need to use the `end` method to end the request:

```js
// test/app.test.js

describe("GET /users", () => {
  it("responds with JSON containing the list of users", (done) => {
    request(app)
      .get("/users")
      .expect("Content-Type", /json/)
      .expect(200)
      // Test the response body
      .end((err, res) => {
        if (err) return done(err);
        expect(res.body.users).to.be.an("array");
        res.body.users.forEach((user) => {
          expect(user).to.have.property("name").that.is.a("string");
          expect(user).to.have.property("email").that.is.a("string");
        });
        // We are done with our asynchronous code
        done();
      });
  });
});
```

The `end` method will take a callback function which will contain the `err` and `res` parameters. We are using an `if` statement to check if there is an error, in which case we will use the `done` function to tell Mocha that we are done with our asynchronous code.

If there is no error we will use the `expect` method to make assertions about the response body. We are expecting the server to return a JSON object with a list of users. These users should have a property of `name` and `email` and they should be strings. We are using the `forEach` method to loop through the `res.body.users` array and make assertions about each user.

We have written our first test for our Express application. We can now run our test to see if it passes. In the terminal, run the following command:

```bash
npm test
```

Mocha and Chai are a very user friendly testing framework and assertion library. We have only scratched the surface of what we can do with these libraries, but our foundational learnings can be taken away and be applied to large applications.

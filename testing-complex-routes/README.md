# ![Unit Testing in JavaScript - Testing Complex Routes](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to write tests for more complex routes in Express.

## Testing More Complex Routes

Since we have already seen how to test a simple route, let's look at how we can test a more complex route. We are still going to be working in the same file, `test/app.test.js`.
 
Let's test a post route. We already have a post route to `users` that we are going to be testing. Just like with our previous tests, we'll use `describe` to group our tests and `it` to declare each test. 

```js
// test/app.test.js

// Set up grouping for tests
describe('POST /users', () => {
    it('adds a new user to the list', (done) => {
        
    });
});
```

For this test we want to add a new user to the list of users. Before we can do that, we'll need a new user to add: 

```js
// test/app.test.js

describe('POST /users', () => {
    it('adds a new user to the list', (done) => {
        // Add in a new mock user
        const newUser = {
            name: 'John Smith',
            email: 'john@example.com',
            password: 'john123',
            phoneNumber: '9876543210'
        };
    });
});
```

With this `newUser` object created, we can now make a request to the server to add the new user. We will be using the same `request` method but this time we will be making a `POST` request. 

```js
// test/app.test.js

describe('POST /users', () => {
    it('adds a new user to the list', (done) => {
        const newUser = {
            name: 'John Smith',
            email: 'john@example.com',
            password: 'john123',
            phoneNumber: '9876543210'
        };
        // Make a POST request to the server
        request(app)
            .post('/users')
    });
});
```

> Remember we want to always pass the `request` method the app listener so that it can make requests to the server.

Now we need a way to send the `newUser` data to the server. Luckily, the `request` method has a method called `send` that we can use to send data to the server. 

```js
// test/app.test.js

describe('POST /users', () => {
    it('adds a new user to the list', (done) => {
        const newUser = {
            name: 'John Smith',
            email: 'john@example.com',
            password: 'john123',
            phoneNumber: '9876543210'
        };
        
        request(app)
            .post('/users')
            // Send the new user data to the server
            .send(newUser)
    });
});
```

After we have sent the data to the server it's time to test. As with our other tests we want to make sure the response is a JSON object and the status code is `200`. 

```js
// test/app.test.js

describe('POST /users', () => {
    it('adds a new user to the list', (done) => {
        const newUser = {
            name: 'John Smith',
            email: 'john@example.com',
            password: 'john123',
            phoneNumber: '9876543210'
        };
        
        request(app)
            .post('/users')
            .send(newUser)
            // Test to ensure that the response is a JSON object
            .expect('Content-Type', /json/)
            // Test to ensure that the status code is 200
            .expect(200)
    });
});
```

Just like with our test for the GET route, we will be using `.end` to end the test. This `end` method will take a callback function that will be used to check the response from the server. But unlike the GET route we will not need to look over the response. 

```js
// test/app.test.js

describe('POST /users', () => {
    it('adds a new user to the list', (done) => {
        const newUser = {
            name: 'John Smith',
            email: 'john@example.com',
            password: 'john123',
            phoneNumber: '9876543210'
        };
        
        request(app)
            .post('/users')
            .send(newUser)
            .expect('Content-Type', /json/)
            .expect(200)
            // Test the response from the server
            .end((err, res) => {
                if (err) return done(err);
                expect(res.body.user).to.be.an('object');
                expect(res.body.user).to.have.property('name', newUser.name);
                expect(res.body.user).to.have.property('email', newUser.email);
                done();
            });
    });
});
```

Now that we have our test for the POST route, we can run our tests to see if they pass. 

```bash
npm test
```


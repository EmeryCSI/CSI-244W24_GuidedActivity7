# Renton Technical College CSI-244

<br />

<div align="center">  
    <img src="logo.jpg" alt="Logo">
    <h3 align="center">Guided Activity 7</h3>
</div>



This repository is a part of CSI-244 at Renton Technical College.

## Guided Activity 7 User Authentication API

### This tutorial is adapted from: 

O, L. (2022, July 8). How to Build an Authentication API with Node.js, MongoDB, and JWT. Medium; Medium. https://medium.com/@olills/how-to-build-a-node-js-api-server-with-jwt-authentication-113fa55708d7

‌

### In this tutorial you will be creating a RESTful API for user Authentication.

1. Clone this repository to your machine.
2. Open the repository in Visual Studio code.
3. Follow the instructions to create a RESTful API using mongoose and MVC

# Part 1: Setting up the Environment

```bash
mkdir screenshots
mkdir userapi
cd userapi
npm init -y
npm install express mongoose cors
```

To enhance the security and flexibility of your application, it's a best practice to move sensitive information, such as your database connection string, into environment variables. This can be achieved using a `.env` file in your Node.js project. Here's how to set it up:

### Step 1: Create a Mongo Collection for this application

1. Navigate to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) log in.
2. Click on collections and create a new database called `userapi`

![Image showing mongodb.com creating a new database](Images/1.png)

3. Create a new collection called `user`

![Image showing mongodb.com creating a new collection](Images/2.png)

### Step 2: Install dotenv Package

First, you'll need to install the `dotenv` package, which will load environment variables from a `.env` file into `process.env` in your application. Run the following command in your project directory:

```powershell
npm install dotenv
```

### Step 3: Create a .env File

1. In the root of your project directory, create a file named `.env`.
2. Open the `.env` file in VS Code.
3. Add your MongoDB connection string as an environment variable in the file. For example:
4. To connect to a specific database include the database name in the connection string. For example:

```
mongodb+srv://<username>:<password>@cluster0.server.mongodb.net/userapi?retryWrites=true&w=majority&appName=Cluster0
```

```env
MONGO_URI=YOUR CONNECTION STRING HERE
```

### Step 4: Ensure .env is in Your .gitignore

To prevent sensitive information from being pushed to your Git repository, make sure your `.env` file is listed in your `.gitignore` file. If you don't have a `.gitignore` file, create one in the root of your project, and add the following lines:

```
.env
node_nodules/
```

By moving your MongoDB connection string into an `.env` file and utilizing the `dotenv` package, you enhance the security and configurability of your application, keeping sensitive information out of your codebase.

# Part 2: Building the Application

Create a new file named server.js in the root folder of userapi.

```powershell
new-item server.js
```

Add a simple console.log statement to server.js verify it is working, and run server.js

```powershell
node server.js
```

Modify server.js to include the following code:

```javascript
const express = require("express");
const app = express();
app.listen(3000, () => console.log("Server is up and running"));
```

### Step 1: Setting up HTTP routes

In the root of the project directory, create a new folder `routes`.

Within the `routes` folder, create a new file `authRoutes.js`.

Inside `authRoutes.js`, create POST routes to enable users to register and login:

```javascript
const router = require("express").Router();
router.post("/register", (req, res) => {
  res.send("Register route");
});
router.post("/login", (req, res) => {
  res.send("Login route");
});
module.exports = router;
```

Here, we create a router with the Express package.

Then, we set up routes for the paths `/register` and `/login`. When a user navigates to the register or login page in their browser window, the browser will make a request to the corresponding route in the server.

For now, the route handler will send back a response with a simple message. We’ll build out the register and login routes as we continue in the tutorial.

At the bottom of the file, export the router with `module.exports` so we can import these routes into the main server file `server.js`.

### Step 2: Adding the routes to the server

Inside `server.js`, import the authentication routes and add middleware:

```javascript
const express = require("express");
const app = express();
// Import routes
const authRoute = require("./routes/authRoutes");
// Middleware
app.use(express.json());
// Route middlewares
app.use("/api/user", authRoute);
app.listen(3000, () => console.log("Server is up and running"));
```

We have added three new pieces to `server.js`:

1. **Import authentication routes** and assign it to a variable, `authRoute`.
2. **Add a bodyparser middleware** with `app.use(express.json())`. This will allow us to "parse" `req.body` and read HTTP POST data from the request.
3. **Finally, add a route middleware** so that the server points to our `authRoutes` file when a user navigates to any URL with the `/api/user/` prefix.

### Step 3: Connecting the server to the database

We will use the Mongoose package to interact with our MongoDB database. We are also going to want to use environment variables so we don’t expose our MongoDB credentials if we push the code to Github.

Import mongoose and dotenv in `server.js`, and connect to the database:

```javascript
const express = require("express");
const app = express();
const mongoose = require("mongoose");
const dotenv = require("dotenv");
// Import routes
const authRoute = require("./routes/authRoutes");
dotenv.config();
// Connect to DB
mongoose.connect(
  process.env.MONGO_URI,
  { useNewUrlParser: true, useUnifiedTopology: true }
);
// Middleware
app.use(express.json());
// Route middlewares
app.use("/api/user", authRoute);
app.listen(3000, () => console.log("Server is up and running"));
```

Here, we require the mongoose and dotenv packages at the top of the file.

We initiate `dotenv.config()` and use `mongoose.connect()` to connect to the database, passing in the connection string via the `MONGO_URI` environment variable.

You might get a message in your console that reads:

`DeprecationWarning: current URL string parser is deprecated, and will be removed in a future version. To use the new parser, pass option { useNewUrlParser: true, useUnifiedTopology: true } to MongoClient.connect.`

We just need to add `{ useNewUrlParser: true, useUnifiedTopology: true }` to get around this issue.

We also add a callback function to log to the console that the application is connected to the database.

### Step 4: Creating the User Model

In the root of the project directory, create a new folder `models`.

Within the `models` folder, create a new file `User.js`.

Inside `User.js`, create a new schema for the user model:

```javascript
const mongoose = require("mongoose");
const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
  },
  email: {
    type: String,
    required: true,
  },
  password: {
    type: String,
    required: true,
  },
  date: {
    type: Date,
    default: Date.now,
  },
});
module.exports = mongoose.model("User", userSchema);
```

### Step 5: Import the User model in authRoutes.js:

In `authRoutes.js`, import the User model

```javascript
const router = require("express").Router();
const User = require("../models/User");
```

### Step 6: Using postman connect to your API and test both the /api/user/register and /api/user/login routes. Take a screenshot of the result and add it to your repository.

# Part 3: Route Handler for Registration

### Step 1: Building the handler

Creating and saving users in `authRoutes.js`, we'll start building out the route handler for account registration. We aim to create a new user instance and save that instance to the database.

```javascript
router.post("/register", async (req, res) => {
  // Create a new user
  const user = new User({
    name: req.body.name,
    email: req.body.email,
    password: req.body.password,
  });
  // Save the user
  try {
    const savedUser = await user.save();
    res.send(savedUser);
  } catch (err) {
    res.status(400).send(err);
  }
});
```

Here, we add `async` to the route because we need some time for the data to be submitted to the database.

In the body of the route, we create a new user instance with data from `req.body`.

Then, in a try-catch block, we create a new variable `savedUser` and call `user.save()`. We send back the `savedUser` as a response.

Now that we have creating and saving users working successfully, there are two important pieces to tackle from here:

1. **Validating user data** such as checking for duplicates and valid email addresses.
2. **Hashing passwords** so we aren’t storing passwords in plaintext.

### Step 2: Validating User Data

We’ll use a data validation library called Joi to create a validation schema.

Install the Joi library and create a new file `validation.js` in the routes directory:

```shell
npm install @hapi/joi
New-Item -Path ".\routes\validation.js" -ItemType File
```

In `validation.js`, require the Joi library then add the following validations for user registration and login data:

```javascript
const Joi = require("@hapi/joi");
const registerValidation = (data) => {
  const schema = Joi.object({
    name: Joi.string().min(2).required(),
    email: Joi.string().min(4).required().email(),
    password: Joi.string().min(6).required(),
  });
  return schema.validate(data);
};
const loginValidation = (data) => {
  const schema = Joi.object({
    email: Joi.string().min(4).required().email(),
    password: Joi.string().min(6).required(),
  });
  return schema.validate(data);
};
module.exports.registerValidation = registerValidation;
module.exports.loginValidation = loginValidation;
```

In this file, we have two schemas, `registerValidation` and `loginValidation`.

We define the data fields and set up basic validations. With the Joi library, we can add multiple validations in a chain.

We pass a data parameter into each validation function. This parameter ultimately represents `req.body`. These functions will be imported in `authRoutes.js`, where we pass in `req.body`.

Finally, we export the validation functions at the bottom of the file.

In `authRoutes.js`, import the validation functions at the top of the file:

```javascript
const { registerValidation, loginValidation } = require("./validation");
```

Use `registerValidation` to validate data in the route handler for `/register`:

```javascript
const { error } = registerValidation(req.body);
if (error) return res.status(400).send(error.details[0].message);
```

Use `loginValidation` to validate data in the route handler for `/login`:

```javascript
const { error } = loginValidation(req.body);
if (error) return res.status(400).send(error.details[0].message);
```

If the validation throws any errors, the JSON object will contain an error message with details. We don’t need the entire object, so if we run into a validation error, we return just the string that contains the error message along with HTTP status 400 for an invalid request.

Here is the full `authRoutes.js` file up to this point:

```javascript
const router = require("express").Router();
const User = require("../models/User");
const { registerValidation, loginValidation } = require("./validation");
router.post("/register", async (req, res) => {
  // Validate data before we create a user
  const { error } = registerValidation(req.body);
  if (error) return res.status(400).send(error.details[0].message);
  // Create a new user
  const user = new User({
    name: req.body.name,
    email: req.body.email,
    password: req.body.password,
  });
  // Save the user
  try {
    const savedUser = await user.save();
    res.send(savedUser);
  } catch (err) {
    res.status(400).send(err);
  }
});
router.post("/login", async (req, res) => {
  // Validate the data
  const { error } = loginValidation(req.body);
  if (error) return res.status(400).send(error.details[0].message);
});
module.exports = router;
```

### Step 3: Checking if the User Already Exists

At this stage, we have a route handler to create a user instance, validate the data, and save the user to the database. We need to add another piece to our application logic: check if the user already exists. We don’t need to register a new user if the user already exists.

Still working in the `/register` route in `authRoutes.js`, after we validate the data and before we save the user, let’s check if the user's email is already in the database.

```javascript
const emailExist = await User.findOne({ email: req.body.email });
if (emailExist) return res.status(400).send("Email already exists");
```

We check to see if there is a user with the email address. If the email is in our database, this user account with this email address has already been created. The return statement will run and we won’t proceed to save the new user.

Here’s the full route handler for `/register`:

```javascript
router.post("/register", async (req, res) => {
  // Validate data before we create a user
  const { error } = registerValidation(req.body);
  if (error) return res.status(400).send(error.details[0].message);
  // Check if the user is already in the database
  const emailExist = await User.findOne({ email: req.body.email });
  if (emailExist) return res.status(400).send("Email already exists");
  // Create a new user
  const user = new User({
    name: req.body.name,
    email: req.body.email,
    password: req.body.password,
  });
  // Save the user
  try {
    const savedUser = await user.save();
    res.send({ user: user._id });
  } catch (err) {
    res.status(400).send(err);
  }
});
```

### Step4: Hashing Passwords

Now that we have our register route up and running and connected to our database, the next step is to hash the password for a new user.

We are currently storing the plaintext password as `req.body.password`. Never store passwords in plaintext. It is a major security issue.

To secure passwords, we will use the bcryptjs package. First, we'll generate a salt. Then, we'll hash the password with the salt.

When a user logs in, bcrypt will compare the password input with the hashed password in the database.

Feel free to read this “Understanding bcrypt” article for more information on how bcrypt works under the hood.

In the terminal window, install the bcrypt package:

```shell
npm install bcrypt
```

Import the bcrypt package at the top of `authRoutes.js`:

```javascript
const bcrypt = require("bcrypt");
```

Generate a salt, then hash the password with the salt:

```javascript
// Hash the password
const salt = await bcrypt.genSalt(10);
const hashedPassword = await bcrypt.hash(req.body.password, salt);
```

First, we create a salt with the bcrypt package. The parameter 10 is used to set the complexity of the string that gets generated.

After we create the salt, we hash the password and assign it to the hashedPassword variable. We pass in two parameters to the hash function: `req.body.password` and the salt we just created.

In the `/register` route handler, replace `req.body.password` with `hashedPassword`:

```javascript
const user = new User({
  name: req.body.name,
  email: req.body.email,
  password: hashedPassword,
});
```

Here is the full route handler for `/register` up to this point:

```javascript
router.post("/register", async (req, res) => {
  // Validate data before we create a user
  const { error } = registerValidation(req.body);
  if (error) return res.status(400).send(error.details[0].message);
  // Check if the user is already in the database
  const emailExist = await User.findOne({ email: req.body.email });
  if (emailExist) return res.status(400).send("Email already exists");
  // Hash the password
  const salt = await bcrypt.genSalt(10);
  const hashedPassword = await bcrypt.hash(req.body.password, salt);
  // Create a new user
  const user = new User({
    name: req.body.name,
    email: req.body.email,
    password: hashedPassword,
  });
  // Save the user
  try {
    const savedUser = await user.save();
    res.send({ user: user._id });
  } catch (err) {
    res.status(400).send(err);
  }
});
```

In the try-catch block, let’s also update the response to send just the user ID with `res.send({user: user._id})`. This way, we aren't sending a response with the entire savedUser details including the hashed password.

### Step 5: Using postman connect register a new user and verify that the user is added to the database. Take a screenshot of the result and add it to your repository.

# Part 4: Building the Route Handler for Login

### Step 1: Validating User Data

When a user attempts to login, we want to follow these steps:

1. Validate the data.
2. If the data is valid, check if the email exists in our database.
3. If the email exists, check if the password is valid.

We have already added validation to the login route using `loginValidation` from `validation.js`.

Below the validation step, add two more steps. Check if the email address exists and if the password is valid:

```javascript
router.post("/login", async (req, res) => {
  // Validate the data
  const { error } = loginValidation(req.body);
  if (error) return res.status(400).send(error.details[0].message);
  // Check if email exists
  const user = await User.findOne({ email: req.body.email });
  if (!user) return res.status(400).send("Email not found");
  // Check if password is valid
  const validPassword = await bcrypt.compare(req.body.password, user.password);
  if (!validPassword) return res.status(400).send("Invalid password");

  res.send("Logged in");
});
```

If all these checks pass, we send back a response `res.send('Logged in!')`. In your app, you may want to redirect the user after successful login. For now, we will just have the server send back a message as we move on to the next step: JSON Web Tokens.

### Step 2: Authenticating Requests with JSON Web Tokens

After a user logs in, we want to be able to allow the user to make other requests associated with their user account. This is where JSON Web Tokens come into play.

HTTP is a stateless protocol. It doesn’t know if a request is coming from a logged-in user or not. So, if a user logs in, they will have to login again for each future request they make. We want a user to be able to make requests to the server without having to login again with every request.

To solve this problem, we can assign a token to the user upon login. The token gets added to the request header for any subsequent requests while the user is logged in. This lets the server "remember" that the user is logged in. We can also secure our API server by ensuring that only logged-in users can access certain routes.

Install the `jsonwebtoken` package:

```shell
npm install jsonwebtoken
```

Import the package at the top of `authRoutes.js`:

```javascript
const jwt = require("jsonwebtoken");
```

Create and assign a token at the bottom of the `/login` route handler:

```javascript
// Create and assign a token
const token = jwt.sign({ _id: user._id }, process.env.TOKEN_SECRET);
res.header("auth-token", token).send(token);
```

Here, we create a variable `token`. The first argument in the `jwt.sign()` function is the user id. The second argument is `TOKEN_SECRET`. This is an environment variable that we will create in a second in `.env`.

Set the token to the response header and give it the name 'auth-token'. This line adds the token to the request header for any other HTTP requests the user makes while logged in.

Now, we have to create the `TOKEN_SECRET` environment variable that we use to sign the token.

Add `TOKEN_SECRET` to the `.env` file:

```
TOKEN_SECRET = kjwoiejfoi2o4ewds
```

This token can be a completely random string. Choose a long string of random characters, or generate a string with [this tool](https://randomkeygen.com/). This is a secret key private to you. It is used to sign the token. The signature on a JWT is used to validate that the token hasn’t been tampered with.

The full `/login` route:

```javascript
router.post("/login", async (req, res) => {
  // Validate the data
  const { error } = loginValidation(req.body);
  if (error) return res.status(400).send(error.details[0].message);
  // Check if email exists
  const user = await User.findOne({ email: req.body.email });
  if (!user) return res.status(400).send("Login credentials not valid");
  // Check if password is valid
  const validPassword = await bcrypt.compare(req.body.password, user.password);
  if (!validPassword)
    return res.status(400).send("Login credentials not valid");
  // Create and assign a token
  const token = jwt.sign({ _id: user._id }, process.env.TOKEN_SECRET);
  res.header("auth-token", token).send(token);
});
```

### Step 3: Accessing Protected Routes

We have now set up the ability to assign a JSON Web Token to a logged-in user.

Let’s create a protected route to see the JSON Web Token in action. When the server gets the request to a protected route, we will first validate their JWT to see if the user may access the route.

We’ll create a middleware function in a separate file, then we can add the middleware to any route that we want only logged-in users to access.

Create a new file in the routes directory, verifyToken.js:

Require the jsonwebtoken package at the top of the file and create the token verification function:

```javascript
const jwt = require("jsonwebtoken");
module.exports = function (req, res, next) {
  const token = req.header("auth-token");
  if (!token) return res.status(401).send("Access denied");
  try {
    const verified = jwt.verify(token, process.env.TOKEN_SECRET);
    req.user = verified;
    next();
  } catch (err) {
    res.status(400).send("Invalid token");
  }
};
```

We grab the authentication token from req.header and assign it to the token variable.

If the token isn’t present, we know the user making the request was never assigned a token, and therefore is not logged in. We send HTTP status 401 for unauthorized access and an ‘Access denied’ message.

If the token is present, we verify the token using our unique TOKEN_SECRET. If it's verified, we get back an id from the JSON Web Token. This id is what gets decoded from the JWT payload. We assign the id to the verified variable, then store it on req.user. We can then use req.user in our routes as needed - such as if we want to get a list of all posts made with this user id.

The next(); line will call the next middleware function in the stack if this current middleware function does not end the request-response cycle. If we don't have this line, we will see an error in our program.

Now to protect a route, we can add this middleware to the route. We will create a basic protected route that simply sends back the user id. Let’s continue with the blogging platform example and create a file for blog post routes.

Create a new file routes/postsRoutes.js:

```javascript
const router = require("express").Router();
const verify = require("./verifyToken");
router.get("/", verify, (req, res) => {
  res.send(req.user);
});
module.exports = router;
```

At the top of the file, we set up our Express router. Then we require the function from verifyToken.js and assign it to the variable verify. This is our middleware function.

We make the route private by adding verify in the route. When a user attempts to access the route, the verify middleware will first check to see if the token exists and is valid.

Import postRoutes into server.js and set up the app.use middleware to point to the route.

Here is our full server.js file:

```javascript
const express = require("express");
const app = express();
const mongoose = require("mongoose");
const dotenv = require("dotenv");
// Import Routes
const authRoute = require("./routes/authRoutes");
const postRoute = require("./routes/postsRoutes");
dotenv.config();
// Connect to DB
mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true }
);
// Middleware
app.use(express.json());
// Route Middlewares
app.use("/api/user", authRoute);
app.use("/api/posts", postRoute);
app.listen(3000, () => console.log("Server is up and running"));
```

### Step 4: Using postman login as the user that you created in the previous step and verify that you can access the protected route. Take a screenshot of the result and add it to your repository.

Summary
We have finished creating a REST API for user registration and authentication!

We built a Node.js server with Express and connected a MongoDB database to the server. We defined our API routes and enabled password hashing and authentication with JSON Web Tokens.

This project can be used as a boilerplate backend starter for any projects that require user account registration and authentication. From here, you can build out more routes and develop a front-end client.

If you have any questions about this assignment please reach out to myself or our TA for this course.

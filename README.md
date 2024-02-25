# Renton Technical College CSI-244

<br />

<div align="center">  
    <img src="logo.jpg" alt="Logo">
    <h3 align="center">Guided Activity 7</h3>
</div>

This repository is a part of CSI-244 at Renton Technical College.

## Guided Activity 7 User Authentication API

1. Clone this repository to your machine.
2. Open the repository in Visual Studio code.
3. Follow the instructions to create a RESTful API using mongoose and MVC

## Setting up the Environment


```bash
mkdir user-api
cd user-api
npm init -y
npm install express mongoose cors
```

## Setting up connection string

To enhance the security and flexibility of your application, it's a best practice to move sensitive information, such as your database connection string, into environment variables. This can be achieved using a `.env` file in your Node.js project. Here's how to set it up:

## Navigate to Mongodb.com, Create a new collection called

### Step 1: Create a Mongo Collection for this application



### Step 1: Install dotenv Package

First, you'll need to install the `dotenv` package, which will load environment variables from a `.env` file into `process.env` in your application. Run the following command in your project directory:

```powershell
npm install dotenv
```

### Step 2: Create a .env File

1. In the root of your project directory, create a file named `.env`.
2. Open the `.env` file in VS Code.
3. Add your MongoDB connection string as an environment variable in the file. For example:

```env
MONGO_URI=YOUR CONNECTION STRING HERE
```

### Step 3: Ensure .env is in Your .gitignore

To prevent sensitive information from being pushed to your Git repository, make sure your `.env` file is listed in your `.gitignore` file. If you don't have a `.gitignore` file, create one in the root of your project, and add the following lines:

```
.env
node_nodules/
```

By moving your MongoDB connection string into an `.env` file and utilizing the `dotenv` package, you enhance the security and configurability of your application, keeping sensitive information out of your codebase.

## Building the Application

Create a new file named server.js in the root folder of my-api, add a simple console statement to verify it is working, and run server.js

```powershell
node server.js
```

If you would like to use nodemon you can install and use that as well.



## Creating the Model

In `models/song.js`, define the schema and model for songs in our application:

```javascript
const mongoose = require('mongoose');
const songSchema = new mongoose.Schema({
  title: String,
  description: String,
});

const Song = mongoose.model('Song', songSchema);

module.exports = Song;
```

## Building the Controller

In `controllers/songController.js`, implement the logic for handling CRUD operations:

```javascript
const Song = require('../models/song');

exports.createSong = async (req, res) => {
  const { title, description } = req.body;
  try {
    const song = new Song({ title, description });
    const savedSong = await song.save();
    res.status(201).json(savedSong);
  } catch (error) {
    res.status(500).json({ error: 'An error occurred' });
  }
};

exports.getAllSongs = async (req, res) => {
  try {
    const songs = await Song.find();
    res.json(songs);
  } catch (error) {
    res.status(500).json({ error: 'An error occurred' });
  }
};

exports.getSong = async (req, res) => {
  const { id } = req.params;
  try {
    const song = await Song.findById(id);
    if (!song) res.status(404).json({ error: 'Song not found' });
    else res.json(song);
  } catch (error) {
    res.status(500).json({ error: 'An error occurred' });
  }
};

exports.updateSong = async (req, res) => {
  const { id } = req.params;
  const { title, description } = req.body;
  try {
    const song = await Song.findByIdAndUpdate(id, { title, description }, { new: true });
    if (!song) res.status(404).json({ error: 'Song not found' });
    else res.json(song);
  } catch (error) {
    res.status(500).json({ error: 'An error occurred' });
  }
};

exports.deleteSong = async (req, res) => {
  const { id } = req.params;
  try {
    const song = await Song.findByIdAndDelete(id);
    if (!song) res.status(404).json({ error: 'Song not found' });
    else res.json({ message: 'Song deleted successfully' });
  } catch (error) {
    res.status(500).json({ error: 'An error occurred' });
  }
};
```

## Setting Up the Routes

Define your application routes in `routes/songRoutes.js`:

```javascript
const express = require('express');
const router = express.Router();
const songController = require('../controllers/songController');

router.post('/songs', songController.createSong);
router.get('/songs', songController.getAllSongs);
router.get('/songs/:id', songController.getSong);
router.put('/songs/:id', songController.updateSong);
router.delete('/songs/:id', songController.deleteSong);

module.exports = router;
```

## Initializing Express and MongoDB

Set up Express and connect to MongoDB in `server.js`:

```javascript
const express = require('express');
const mongoose = require('mongoose');
require('dotenv').config();
const songRoutes = require('./routes/songRoutes');
const cors = require('cors');
const app = express();
const port = process.env.PORT || 3000;

mongoose.connect(process.env.MONGO_URI, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

app.use(cors());
app.use(express.json());
app.use('/api', songRoutes);

app.listen

(port, () => {
  console.log(`Server is running on port ${port}`);
});
```

## Running the Application

Ensure MongoDB is running, then start your application:

```bash
node server.js
```

## Connect to your server using Postman

### Testing Endpoints with Postman and Including Screenshots

For the final part of this tutorial, you will test each of the endpoints with Postman to ensure that your Web API is functioning as expected. You will also capture and include screenshots of these tests to document the process.

#### Step 1: Prepare for Testing

1. **Create a Directory for Screenshots**:
   - In your project repository, create a directory where you can save screenshots of your Postman tests. You can do this from your terminal:

     ```powershell
     mkdir screenshots
     ```

2. **Launch Postman**:
   - Open Postman. If you haven't already installed Postman, download it from [the official site](https://www.postman.com/downloads/) and install it. Once it has installed create a free account.

#### Step 2: Test the `POST /api/songs` Endpoint

You're going to test creating a new song by sending a POST request to your API.

1. **Set Up the Request**:
   - In Postman, create a new request by clicking the "New" button and selecting "HTTP".
   - Click "Save" and name your request "Create New Song" and save it to a collection.
   - Set the request type to `POST` and enter the URL for your `api/songs` endpoint, e.g., `http://localhost:3000/api/songs`.

2. **Include Song Information**:
   - In the "Body" tab of your request, select "raw" and then "JSON" from the dropdown menu.
   - Enter the details of the song you want to create. For example:

     ```json
     {
       "title": "Imagine",
       "description": "A song by John Lennon"
     }
     ```

3. **Send the Request**:
   - Click the "Send" button to make the request to your API.

4. **Capture the Response**:
   - After sending the request, you'll receive a response from your API. If the song is successfully created, you should see a `201` status code and the details of the created song in the response body.
   - Capture a screenshot of the Postman window showing both the request and the response. This will include the setup of your request and the successful response from your server.

  ![postman](./Images/postman.png)

5. **Save the Screenshot**:
   - Save the screenshot to the `screenshots` directory you created. You might name it `create-new-song-postman.png` to clearly indicate what the screenshot represents.

#### Step 3: Repeat for Other Endpoints

Follow a similar process to test other endpoints (`GET`, `PUT`, `DELETE`) of your API. For each test:

- Set up the request in Postman with the correct HTTP method and URL.
- If necessary, include the required information in the request body or URL parameters.
- Send the request and capture the response.
- Take screenshots of both the request and response, and save them to your `screenshots` directory with descriptive names.

By following these steps, you'll not only ensure that your Web API is functioning correctly but also create a visual record of your API's capabilities and how to interact with it. This can be incredibly useful for both your reference and when sharing your project with others.

If you have any questions about this assignment please reach out to myself or our TA for this course.

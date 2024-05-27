1. Frontend Development
UI/UX Design
Design wireframes and mockups using tools like Figma or Adobe XD.
Component Implementation (React.js example)
App.js
import React from 'react';
import './App.css';
import GradeInputForm from './components/GradeInputForm';
import ResultDisplay from './components/ResultDisplay';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <h1>VTU CGPA/SGPA Calculator</h1>
      </header>
      <main>
        <GradeInputForm />
        <ResultDisplay />
      </main>
    </div>
  );
}

export default App;

GradeInputForm.js
import React, { useState } from 'react';

function GradeInputForm() {
  const [grades, setGrades] = useState([{ grade: '', credits: '' }]);

  const handleChange = (index, event) => {
    const values = [...grades];
    values[index][event.target.name] = event.target.value;
    setGrades(values);
  };

  const handleAdd = () => {
    setGrades([...grades, { grade: '', credits: '' }]);
  };

  const handleSubmit = (event) => {
    event.preventDefault();
    // Submit grades to backend
  };

  return (
    <form onSubmit={handleSubmit}>
      {grades.map((input, index) => (
        <div key={index}>
          <input
            name="grade"
            value={input.grade}
            onChange={(event) => handleChange(index, event)}
            placeholder="Grade"
          />
          <input
            name="credits"
            value={input.credits}
            onChange={(event) => handleChange(index, event)}
            placeholder="Credits"
          />
        </div>
      ))}
      <button type="button" onClick={handleAdd}>Add Grade</button>
      <button type="submit">Calculate</button>
    </form>
  );
}

export default GradeInputForm;

ResultDisplay.js
import React from 'react';

function ResultDisplay() {
  return (
    <div>
      <h2>Results</h2>
      <p>CGPA: --</p>
      <p>SGPA: --</p>
    </div>
  );
}

export default ResultDisplay; 

App.css

.App {
  text-align: center;
}

.App-header {
  background-color: #282c34;
  padding: 20px;
  color: white;
}

form {
  margin: 20px;
}

input {
  margin: 5px;
}

button {
  margin: 5px;
}

2. Backend Development
server.js
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const jwt = require('jsonwebtoken');

const app = express();
const PORT = process.env.PORT || 5000;

// Middleware
app.use(bodyParser.json());

// Database connection
mongoose.connect('mongodb://localhost:27017/vtu-calculator', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

// User model
const User = mongoose.model('User', new mongoose.Schema({
  username: String,
  password: String,
  grades: Array,
}));

// Routes
app.post('/api/register', async (req, res) => {
  const { username, password } = req.body;
  const user = new User({ username, password, grades: [] });
  await user.save();
  res.status(201).send('User registered');
});

app.post('/api/login', async (req, res) => {
  const { username, password } = req.body;
  const user = await User.findOne({ username });
  if (user && user.password === password) {
    const token = jwt.sign({ userId: user._id }, 'secret_key');
    res.json({ token });
  } else {
    res.status(401).send('Invalid credentials');
  }
});

app.post('/api/grades', async (req, res) => {
  const { token, grades } = req.body;
  const payload = jwt.verify(token, 'secret_key');
  const user = await User.findById(payload.userId);
  user.grades = grades;
  await user.save();
  res.send('Grades saved');
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});



3. Testing and Debugging
test/server.test.js

const request = require('supertest');
const app = require('../server'); // Assuming you export the app in server.js

describe('API Endpoints', () => {
  it('should register a new user', async () => {
    const res = await request(app)
      .post('/api/register')
      .send({
        username: 'testuser',
        password: 'testpassword',
      });
    expect(res.statusCode).toEqual(201);
  });

  it('should login a user', async () => {
    const res = await request(app)
      .post('/api/login')
      .send({
        username: 'testuser',
        password: 'testpassword',
      });
    expect(res.statusCode).toEqual(200);
    expect(res.body).toHaveProperty('token');
  });
});

4. Documentation
README.md
# VTU CGPA/SGPA Calculator

## Overview
A web application to calculate CGPA and SGPA for VTU students.

## Features
- User registration and login
- Grade input interface
- CGPA/SGPA calculation

## Installation
1. Clone the repository
2. Install dependencies: `npm install`
3. Start the backend server: `node server.js`
4. Start the frontend server: `npm start` in the frontend directory

## API Endpoints
- `POST /api/register`: Register a new user
- `POST /api/login`: Login a user
- `POST /api/grades`: Save user grades

## Technologies Used
- Frontend: React.js
- Backend: Node.js, Express.js
- Database: MongoDB
- Authentication: JWT




System Documentation
1.	System Architecture: Diagram showing the interaction between frontend, backend, and database.
2.	APIs: Detailed description of API endpoints, request, and response formats.
3.	Database Schema: Outline of database collections and their fields.
This code and structure provide a comprehensive foundation for the VTU CGPA/SGPA Calculator Website, covering frontend and backend development, testing, and documentation.



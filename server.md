# ☕ Coffee House Server (Express + MongoDB)

This is the backend server for the Coffee House application built using **Node.js**, **Express**, and connected to **MongoDB Atlas**.

## 📁 Project Structure

coffee-house-server/
├── index.js # Main server entry point
├── .env # Environment variables
├── package.json
└── README.md

## 🔧 Setup Instructions

### 1. Create Project Folder

```Cmd
mkdir coffee-house-server
cd coffee-house-server
npm init -y
npm install express cors dotenv mongodb
```

### 2. Open this project in vs code

```
code .
```

### 3. Add Start Script

In **package.json**, update the **scripts** section:

```
"scripts": {
  "start": "node index.js",
  "test": "echo \"Error: no test specified\" && exit 1"
}
```

### 4. 🌐 Server Setup

Create **index.js** with the following content:

```
require("dotenv").config();
const express = require("express");
const cors = require("cors");
const app = express();
const port = process.env.PORT || 3000;

// Middleware
app.use(cors());
app.use(express.json());

// Root route
app.get("/", (req, res) => {
  res.send("Coffee server is running");
});

// MongoDB connection
const { MongoClient, ServerApiVersion } = require("mongodb");

const uri = `mongodb+srv://${process.env.DB_ADMIN}:${process.env.DB_PASS}@cluster0.glnanjr.mongodb.net/?retryWrites=true&w=majority&appName=Cluster0`;

const client = new MongoClient(uri, {
  serverApi: {
    version: ServerApiVersion.v1,
    strict: true,
    deprecationErrors: true,
  },
});

async function run() {
  try {
    await client.connect();
    await client.db("admin").command({ ping: 1 });
    console.log("Successfully connected to MongoDB!");
  } finally {
    await client.close();
  }
}

run().catch(console.dir);

// Start server
app.listen(port, () => {
  console.log(`☕ Coffee server is running on port ${port}`);
});
```

### 5.🛡️ Environment Variables

Create a **.env** file in the root of your project:

```
DB_ADMIN=your_mongodb_username
DB_PASS=your_mongodb_password
```

Replace **your_mongodb_username** and **your_mongodb_password** with your actual MongoDB Atlas credentials.

## 6. Run The Server

In terminal **nodemon index.js** run this command to start the project

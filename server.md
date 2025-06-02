# ☕ BackEnd Server SetUp (Express + MongoDB)

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
mkdir project-name
cd project-name
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

### CRUD - POST Operation (MongoDB + Express)

```
const coffeesCollections2 = client.db("coffeeDB2").collection("coffees"); //creating bd and collections
// POST route
//backend
    app.post("/coffees", async (req, res) => {
      const coffeeData = req.body;
      const result = await client.db("coffeeDB").collection("coffees").insertOne(coffeeData);
      res.send(result);
      console.log(coffeeData);
    });

    //frontend
    fetch("http://localhost:3000/coffees", {
      method: "POST",
      headers: {
        "content-type": "application/json",
      },
      body: JSON.stringify(coffeeData),
    })
      .then((res) => res.json())
      .then((data) => {
        if (data.insertedId) {
          Swal.fire({
            position: "top",
            icon: "success",
            title: "Coffee Added Successfully",
            showConfirmButton: false,
            timer: 1000,
          });
        }
        console.log("coffee after db ", data);
      });

```

### sending data from dataBase to backend-server using get()

```
// sending data from dataBase to backend-server
    app.get("/coffees", async (req, res) => {
      const cursor = await coffeesCollections2.find().toArray();
      res.send(cursor);
    });
```

### Deleting a single data from UI using delete

```
/// deleting data from ui
    app.delete("/coffees/:id", async (req, res) => {
      const id = req.params.id;
      const query = { _id: new ObjectId(id) };
      const results = await coffeesCollections2.deleteOne(query);
      res.send(results);
    });

    //frontend

Add a onClick handler on the button -
    <Link onClick={() => handleDelete(_id)}className="btn btn-error btn-xs" > Delete</Link>

    const AllCoffeesCard = ({ singleCoffee }) => {
  const { _id, name, price, photoUrl, details, taste, supplier } = singleCoffee;
  const handleDelete = (id) => {
    Swal.fire({
      title: "Are you sure?",
      text: "You won't be able to revert this!",
      icon: "warning",
      showCancelButton: true,
      confirmButtonColor: "#3085d6",
      cancelButtonColor: "#d33",
      confirmButtonText: "Yes, delete it!",
    }).then((result) => {
      if (result.isConfirmed) {
        fetch(`http://localhost:3000/coffees/${id}`, {
          method: "DELETE",
        })
          .then((res) => res.json())
          .then((data) => {
            if (data.deletedCount) {
              Swal.fire({
                title: "Deleted!",
                text: "Your file has been deleted.",
                icon: "success",
              });
            }
            console.log(data);
          });
      }
    });
  };
    }
```

### Find Single Data from DB using id

```
// sending a single data from server
    app.get("/coffees/:id", async (req, res) => {
      const id = req.params.id;
      const query = { _id: new ObjectId(id) };
      const find = await coffeesCollections2.findOne(query);
      res.send(find);
    });

//Frontend
On ViewDetailsButton --->
<Link to={`/details/${_id}`} className="btn btn-primary btn-xs">View</Link>

     {
        path: "/details/:id",
        loader: ({ params }) =>
          fetch(`http://localhost:3000/coffees/${params.id}`),
        Component: ViewCoffeeDetails,
      },
      in View Details Card
      const ViewCoffeeDetails = () => {
       const data = useLoaderData();
         return ( )
         }
```

### app.put - update data using put

```
**backend**
//updating doc
    app.put("/coffees/:id", async (req, res) => {
      const id = req.params.id;
      const filter = { _id: new ObjectId(id) };
      const updatedCoffee = req.body;
      const options = { upsert: true };
      const updatedDoc = {
        $set: updatedCoffee,
      };
      const results = await coffeesCollections2.updateOne(
        filter,
        updatedDoc,
        options
      );
      res.send(results);
    });
    **Frontend**

    Path in router -
    {
        path: "/updateCoffee/:id",
        loader: ({ params }) =>
          fetch(`http://localhost:3000/coffees/${params.id}`),
        Component: UpdateCoffee,
      }
  **onClick on button**
  <Link to={`/updateCoffee/${_id}`} className="btn btn-warning btn-xs">Update</Link>

const UpdateCoffee = () => {
  const data = useLoaderData();
  console.log(data);
  const { _id } = data;
  const handleUpdateCoffee = (e) => {
    e.preventDefault();
    const form = e.target;
    const updatedCoffeeData = Object.fromEntries(new FormData(form));

    //sending data to the server
    fetch(`http://localhost:3000/coffees/${_id}`, {
      method: "PUT",
      headers: { "content-type": "application/json" },
      body: JSON.stringify(updatedCoffeeData),
    })
      .then((res) => res.json())
      .then((data) => {
        if (data.modifiedCount) {
          Swal.fire({
            position: "top",
            icon: "success",
            title: "Your work has been saved",
            showConfirmButton: false,
            timer: 1500,
          });
        }
      });
  };
  retutn (
    <!-- form -->
  )
}
```

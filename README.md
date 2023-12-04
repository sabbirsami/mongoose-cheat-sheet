# Mongoose Cheat Sheet
Mongoose can refer to a JavaScript library used with MongoDB, providing an object data modeling (ODM) layer for Node.js applications. It simplifies interactions with MongoDB by offering a schema-based approach and additional features for managing data.

## Follow these steps to connect your MongoDB database with Mongoose

### 1. Basic Express JS steps:
```JS 
const express = require("express");
const app = express();
const port = process.env.PORT || 5000;

// MIDDLEWARE
app.use(
        cors({
            origin: [
                "http://localhost:5173",
                "https://sabbir-mohammad-sami.web.app/"
            ],
        })
    );
    app.use(express.json());

//APPLICATION ROUTE
app.get("/", (req, res) => {
    res.send("Industry Running...");
});

const main = async () => {
    app.listen(port, () => {
        console.log("Industry running on port: ", { port });
    });
};
main()

```
### 2. connect database using mongoose
```JS 
const express = require("express");
const app = express();
const port = process.env.PORT || 5000;
const { default: mongoose } = require("mongoose");

// MIDDLEWARE
app.use(
        cors({
            origin: [
                "http://localhost:5173",
                "https://sami-industry.web.app/",
                "https://sami-industry.firebaseapp.com",
            ],
        })
    );
    app.use(express.json());

// ROUTE
// <put all of your routes here...>

// APPLICATION ROUTE
app.get("/", (req, res) => {
    res.send("Industry Running...");
});

// CONNECT WITH DB
const connectDB = async () => {
    try {
        const url = <PUT YOUR DATABASE CONNECTION URL LINK>;
        await mongoose.connect(url);
        console.log("Database connect successfully");
    } catch (error) {
        console.log("connection fail", error);
    }
};

const main = async () => {
    await connectDB()
    app.listen(port, () => {
        console.log("Industry running on port: ", { port });
    });
};
main()

```

### 3. Now if you have a gallery section where users can add photo with captions and you want to save this data in your database with imageUrl, caption, uploadDate, uploadedBy. Then you need to create a Schema first. 
#### `Now the question is what is Schema in Mongoose?`
In Mongoose, a schema is a blueprint that defines the structure of documents within a collection in a MongoDB database. You can check and validate what the user is trying to post if it is not what you need then you can throw an error message.

### - Create a Schema for your gallery section post

```JS
// Define the Mongoose schema
const imageSchema = new mongoose.Schema({
  imageUrl: {
    type: String,
    required: true, // if you set (required: true,) then if the user doesn't provide this URL it will not
                    // save data. But if it's not required then you can just remove it.
  },
  caption: {
    type: String,
    maxLength: 150,
  },
  uploadDate: {
    type: Date,
    default: Date.now,
  },
  uploadedBy: {
    type: String,
    required: true,
  },
});

// Create a Mongoose model based on the schema. This model first parameter defines what's going to be your
// database collection name. But remember if you set your database name image then it saves the database
// collection name as images which means it always adds s to your provided model name.

const Image = mongoose.model('Image', imageSchema);

```
#### - Post image
```JS
app.post("/images", async (req, res) => {
    try {
        const newImage = new Image(req.body);
        const result = await newImage.save();
        res.send(result)
    } catch (error) {
        console.error("Error adding image to the gallery:", error.message);
    }
});
```

#### - Get All images
```JS
app.get("/images", async (req, res) => {
    try {
        const result = await Image.find({});
        res.send(result);
    } catch (error) {
        console.error("Error getting image to the gallery:", error.message);
    }
});
```
#### - Update image
```JS
app.put("/images/:id", async (req, res) => {
   try {
        const updatedImage = req.body.image;
        const result = await Image.updateOne(
            { _id: req.params.id },
            {
                $set: { imageUrl: updatedImage },
            }
        );
        if (result.nModified > 0) {
            return res
                .status(200)
                .json({ message: "imageUrl updated successfully" });
        } else {
            return res
                .status(404)
                .json({ error: "imageUrl update fail" });
        }
    } catch (error) {
        console.log(error);
    }
});
```
#### - Delete image
```JS
app.delete("/images/:id", async (req, res) => {
    try {
        const result = await Image.deleteOne({ _id: req.params.id });
        res.send(result);
    } catch (error) {
        console.log(error);
    }
});
```

#### You can put everything part by part in different folders. But don't forget to export it. Here is an example if you put schema in a different folder:
```JS
const mongoose = require('mongoose');

const imageSchema = new mongoose.Schema({
  imageUrl: {
    type: String,
    required: true,
  },
  caption: {
    type: String,
    maxLength: 150,
  },
  uploadDate: {
    type: Date,
    default: Date.now,
  },
  uploadedBy: {
    type: String,
    required: true,
  },
});

const Image = mongoose.model('Image', imageSchema);

module.exports = Image;
```

#### ` ⚠ This is a single-file example for absolute beginners. If you find any bug or any kind of problem don't forget to inform me. If you find it truly helpful don't forget to give it a star ⭐`

## Some additional information:
#### Insert

| Command | Description |
|---------|-------------|
| `insertOne` | Create a new document inside the specified collection |
| `db.users.insertOne({ name: “Sami” })` | Add a new document with the name of Sami into the users collection |
| `insertMany` | Create multi new documents inside a specific collection |
| `db.users.insertMany([{ age: 26 }, { age: 20 }])` | Add two new documents with the age of 26 and 20 into the users collection |

#### Read

| Command | Description |
|---------|-------------|
| `find` | Get all documents |
| `db.users.find()` | Get all users |
| `find(<filterObject>)` | Find all documents that match the filter object |
| `db.users.find({ name: “Sami” })` | Get all users with the name Sami |
| `db.users.find({ “address.street”: “123 Main St” })` | Get all users whose address field has a street field with the value 123 Main St |
| `find(<filterObject>, <selectObject>)` | Find all documents that match the filter object but only return the field specified in the select object |
| `db.users.find({ name: “Sami” }, { name: 1, age: 1 })` | Get all users with the name Sami but only return their name, age, and _id |
| `db.users.find({}, { age: 0 })` | Get all users and return all columns except for age |
| `findOne` | The same as find, but only return the first document that matches the filter object |
| `db.users.findOne({ name: “Sami” })` | Get the first user with the name Sami |
| `countDocuments` | Return the count of the documents that match the filter object passed to it |
| `db.users.countDocuments({ name: “Sami” })` | Get the number of users with the name Sami |

#### Update

| Command | Description |
|---------|-------------|
| `updateOne` | Update the first document that matches the filter object with the data passed into the second parameter which is the update object |
| `db.users.updateOne({ age: 20 }, { $set: { age: 21 } })` | Update the first user with an age of 20 to the age of 21 |
| `updateMany` | Update all documents that match the filter object with the data passed into the second parameter which is the update object |
| `db.users.updateMany({ age: 12 }, { $inc: { age: 3 } })` | Update all users with an age of 12 by adding 3 to their age |
| `replaceOne` | Replace the first document that matches the filter object with the exact object passed as the second parameter. This will completely overwrite the entire object and not just update individual fields |
| `db.users.replaceOne({ age: 12 }, { age: 13 })` | Replace the first user with an age of 12 with an object that has the age of 13 as its only field |

#### Delete

| Command | Description |
|---------|-------------|
| `deleteOne` | Delete the first document that matches the filter object |
| `db.users.deleteOne({ age: 20 })` | Delete the first user with an age of 20 |
| `deleteMany` | Delete all documents that match the filter object |
| `db.users.deleteMany({ age: 12 })` | Delete all users with an age of 12 |

#### Complex Filter Object

| Operator | Description |
|----------|-------------|
| `$eq` | Check for equality |
| `$ne` | Check for not equal |
| `$gt` / `$gte` | Check for greater than and greater than or equal to |
| `$lt` / `$lte` | Check for less than and less than or equal to |
| `$in` | Check if a value is one of many values |
| `$nin` | Check if a value is none of many values |
| `$and` | Check that multiple conditions are all true |
| `$or` | Check that one of multiple conditions is true |
| `$not` | Negate the filter inside of `$not` |
| `$exists` | Check if a field exists |
| `$expr` | Do comparisons between different fields |

#### Complex Update Object

| Operator | Description |
|----------|-------------|
| `$set` | Update only the fields passed to `$set`. This will not affect any fields not passed to `$set` |
| `$inc` | Increment the value of the field by the amount given |
| `$rename` | Rename a field |
| `$unset` | Remove a field |
| `$push` | Add a value to an array field |
| `$pull` | Remove a value from an array field |

#### Read Modifiers

| Modifier | Description |
|----------|-------------|
| `sort` | Sort the results of a find by the given fields |
| `limit` | Only return a set number of documents |
| `skip` | Skip a set number of documents from the beginning |

#### `If you find any kind of error please don't hesitate to reach me.😊 Or if you find it truly helpful don't forget to give it a star ⭐`
<p align="center">
  Contact: 
  <a href="https://sabbir-mohammad-sami.web.app"><strong>Portfolio &rarr;</strong></a>  <a href="https://discord.com/users/993823123122171934"><strong>Discord &rarr;</strong></a>
</p>


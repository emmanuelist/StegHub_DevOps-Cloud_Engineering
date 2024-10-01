# Comprehensive Guide to Building a Book Management System

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Setting Up MongoDB](#setting-up-mongodb)
3. [Setting Up Express](#setting-up-express)
4. [Creating the Book Management Application](#creating-the-book-management-application)
5. [Setting Up Routes](#setting-up-routes)
6. [Deploying on EC2](#deploying-on-ec2)
7. [Testing the Application](#testing-the-application)

---

## 1. Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js**: Download and install Node.js from [nodejs.org](https://nodejs.org/).
- **MongoDB**: You can install MongoDB on your local machine or use a cloud service.
- **Git**: For version control and managing the project files.

---

## 2. Setting Up MongoDB

### 2.1 MongoDB Installation

1. Install MongoDB using the following command:

   ```bash
   sudo apt-get install -y mongodb
   ```

   ![install_mongoDB](https://github.com/user-attachments/assets/c50d54fd-e7b6-4f1e-8c7c-78d278721b6a)

2. Start the MongoDB service:

   ```bash
   sudo service mongodb start
   ```

3. Check the status of MongoDB to ensure it’s running:
   ![start_and_status_of_mongodb](https://github.com/user-attachments/assets/b400369b-e157-4146-be1f-d5e78cc83435)

### 2.2 Setting Up MongoDB Repository

1. Add the MongoDB repository to your system:
   ![MongoDB_7 0_Repo_Setup](https://github.com/user-attachments/assets/20477359-a673-468d-ace9-46f358eef683)

---

## 3. Setting Up Express

### 3.1 Install Express and Body-Parser

1. Create a new directory for your project and initialize npm:

   ```bash
   mkdir book-management
   cd book-management
   npm init -y
   ```

   ![initialize_npm_for_Book_project](https://github.com/user-attachments/assets/7986d771-a1e9-450f-9ca0-9b96393bd8ab)

2. Install Express and Body-Parser:
   ```bash
   npm install express body-parser
   ```
   ![install_express](https://github.com/user-attachments/assets/4edce30a-ae00-4f83-9340-fd0d89f988a0)
   ![install_body-parser](https://github.com/user-attachments/assets/6ee202a7-53b6-40a1-ab42-b3a69e64c850)

---

## 4. Creating the Book Management Application

### 4.1 Create Project Structure

Create the following directory structure:

```
book-management/
├── public/
│   └── index.html
├── routes/
│   └── books.js
├── controllers/
│   └── bookController.js
├── models/
│   └── Book.js
├── server.js
└── package.json
```

### 4.2 HTML File for Frontend

Create an `index.html` file inside the `public` directory:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Book Management</title>
  </head>
  <body>
    <h1>Book Management System</h1>
    <!-- Add your form and other content here -->
  </body>
</html>
```

![indexHTML_public_dir_withContent](https://github.com/user-attachments/assets/c7f907fc-d20e-4c5b-958c-5d53cc207d7d)

### 4.3 Define Book Model

Create a `Book.js` model file in the `models` directory:

```javascript
const mongoose = require("mongoose");

const bookSchema = new mongoose.Schema({
  title: String,
  author: String,
  publishedDate: Date,
});

module.exports = mongoose.model("Book", bookSchema);
```

![bookJS _content](https://github.com/user-attachments/assets/c905079b-a608-4408-96ec-160fec82030a)

### 4.4 Controller Configuration

Create a `bookController.js` in the `controllers` directory:

```javascript
const Book = require("../models/Book");

exports.addBook = async (req, res) => {
  const book = new Book(req.body);
  await book.save();
  res.status(201).send(book);
};
```

![controller_cofig_define_in_scriptJS](https://github.com/user-attachments/assets/d0bc261a-31ac-4bff-b0ca-1e09a2747ab6)

---

## 5. Setting Up Routes

### 5.1 Create Routes for Books

Create a `books.js` file in the `routes` directory:

```javascript
const express = require("express");
const router = express.Router();
const bookController = require("../controllers/bookController");

router.post("/books", bookController.addBook);

module.exports = router;
```

![routesJS_content](https://github.com/user-attachments/assets/82377646-ffb3-4433-a708-a992014d13f9)

### 5.2 Setup Server

Create a `server.js` file:

```javascript
const express = require("express");
const bodyParser = require("body-parser");
const mongoose = require("mongoose");
const bookRoutes = require("./routes/books");

const app = express();
app.use(bodyParser.json());
app.use("/api", bookRoutes);

mongoose.connect("mongodb://localhost:27017/book-management", {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

app.listen(3000, () => {
  console.log("Server is running on http://localhost:3000");
});
```

![serverJS_content](https://github.com/user-attachments/assets/e7ccf571-7dd8-41d4-8b48-af51265758e0)

---

## 6. Deploying on EC2

### 6.1 Launch EC2 Instance

1. Go to your AWS Management Console and launch a new EC2 instance (t3.micro is recommended).
   ![EC2_instance_t3](https://github.com/user-attachments/assets/ae96cd62-1f84-4dac-a217-979a2909edcf)

### 6.2 Configure Security Group

2. Configure the security group to allow incoming traffic on port 3000:
   ![open_tcp_3300_securityGroup](https://github.com/user-attachments/assets/2633a1dc-a9f0-484d-90c1-84994fd5eb4a)

### 6.3 Deploy the Application

3. Clone your project to the EC2 instance and run the server:

   ![Deploy_book_management](https://github.com/user-attachments/assets/ac1420d8-7dd3-42be-96bd-d682e0aee95a)

---

## 7. Testing the Application

1. Access your application via `http://<your-ec2-public-ip>:3000`.
2. Use Postman or any other API client to test the API endpoints:
   - **POST** `/api/books` to add a new book.

### Example Postman Request

- URL: `http://<your-ec2-public-ip>:3000/api/books`
- Method: `POST`
- Body (JSON):
  ```json
  {
    "Name": "Docker Dieep Dive",
    "ISBN": "452",
    "Author": "Emmanuel Paul",
    "Pages": "743"
  }
  ```
  ![Screenshot 2024-10-01 151943](https://github.com/user-attachments/assets/e2a449a8-cb03-4e94-8265-58d19e8a1c89)

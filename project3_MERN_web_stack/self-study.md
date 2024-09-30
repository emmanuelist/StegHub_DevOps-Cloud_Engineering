# Self-Study Project: Modern Web Development Concepts

## Table of Contents

- [Self-Study Project: Modern Web Development Concepts](#self-study-project-modern-web-development-concepts)
	- [Table of Contents](#table-of-contents)
	- [Introduction](#introduction)
	- [Types of Modern Databases](#types-of-modern-databases)
		- [Relational Databases](#relational-databases)
		- [NoSQL Databases](#nosql-databases)
		- [NewSQL Databases](#newsql-databases)
	- [Web Frameworks](#web-frameworks)
		- [Frontend Frameworks](#frontend-frameworks)
		- [Backend Frameworks](#backend-frameworks)
	- [JavaScript Introduction](#javascript-introduction)
		- [Basics of JavaScript](#basics-of-javascript)
		- [JavaScript in the Browser](#javascript-in-the-browser)
	- [RESTful APIs](#restful-apis)
		- [What is REST?](#what-is-rest)
		- [Building a RESTful API](#building-a-restful-api)
	- [CSS](#css)
		- [Introduction to CSS](#introduction-to-css)
		- [CSS Flexbox and Grid](#css-flexbox-and-grid)
	- [Challenges and Solutions: Setting Up a React Application on EC2](#challenges-and-solutions-setting-up-a-react-application-on-ec2)
		- [Challenge 1: Insufficient Memory Resources](#challenge-1-insufficient-memory-resources)
		- [Challenge 2: MySQL Memory Consumption](#challenge-2-mysql-memory-consumption)
		- [Challenge 3: No Swap Space Configured](#challenge-3-no-swap-space-configured)
		- [Result:](#result)
		- [Reflection:](#reflection)

---

## Introduction

This README serves as a comprehensive guide for self-study in key areas of modern web development. It aims to provide foundational knowledge and practical examples across several topics, enhancing both understanding and application in real-world scenarios.

---

## Types of Modern Databases

### Relational Databases

- **Definition**: Databases that store data in structured tables with predefined schemas.
- **Examples**:
  - **MySQL**: Widely used in web applications.
  - **PostgreSQL**: Known for advanced features and reliability.
- **Key Concepts**:
  - **Tables, Rows, Columns**: Data is organized in rows and columns.
  - **SQL (Structured Query Language)**: Used for querying and managing data.
  - **ACID Properties**: Ensures reliable transactions.

**Example SQL Query**:

```sql
SELECT name, age FROM users WHERE age > 18;
```

### NoSQL Databases

- **Definition**: Databases designed for unstructured or semi-structured data, allowing for flexible schemas.
- **Examples**:
  - **MongoDB**: Document-oriented database.
  - **Redis**: Key-value store used for caching.
- **Key Concepts**:
  - **Document Stores**: Data is stored in documents (e.g., JSON).
  - **CAP Theorem**: Consistency, Availability, and Partition Tolerance.

**Example MongoDB Query**:

```javascript
db.users.find({ age: { $gt: 18 } });
```

### NewSQL Databases

- **Definition**: Modern relational databases that provide the scalability of NoSQL while maintaining ACID properties.
- **Examples**:
  - **Google Spanner**: Scalable, globally distributed database.
  - **CockroachDB**: Focuses on resilience and horizontal scaling.
- **Key Concepts**:
  - **Distributed Transactions**: Transactions that can span multiple nodes.

---

## Web Frameworks

### Frontend Frameworks

- **Definition**: Libraries and frameworks used to build the user interface of web applications.
- **Examples**:
  - **React**: A JavaScript library for building user interfaces.
  - **Angular**: A platform for building mobile and desktop web applications.
- **Resources**:
  - [React Documentation](https://reactjs.org/docs/getting-started.html)
  - [Angular Documentation](https://angular.io/docs)

**Example React Component**:

```javascript
function Greeting(props) {
  return <h1>Hello, {props.name}!</h1>;
}
```

### Backend Frameworks

- **Definition**: Frameworks that facilitate server-side development by providing tools for building APIs and handling requests.
- **Examples**:
  - **Express.js**: Fast, unopinionated, minimalist web framework for Node.js.
  - **Django**: High-level Python web framework that encourages rapid development.
- **Resources**:
  - [Express Documentation](https://expressjs.com/)
  - [Django Documentation](https://www.djangoproject.com/start/)

**Example Express.js Route**:

```javascript
const express = require("express");
const app = express();

app.get("/api/users", (req, res) => {
  res.send([{ name: "John" }, { name: "Jane" }]);
});
```

---

## JavaScript Introduction

### Basics of JavaScript

- **Definition**: A high-level, dynamic programming language used for web development.
- **Key Topics**:
  - **Variables**: `let`, `const`, and `var`.
  - **Functions**: Function declarations and arrow functions.
  - **Control Structures**: `if`, `for`, `while`, etc.

**Example JavaScript Function**:

```javascript
function add(a, b) {
  return a + b;
}
```

### JavaScript in the Browser

- **Definition**: Using JavaScript to interact with HTML and CSS in the browser environment.
- **Key Topics**:
  - **DOM Manipulation**: Changing the document structure and styles.
  - **Event Handling**: Responding to user interactions.

**Example DOM Manipulation**:

```javascript
document.getElementById("myElement").innerText = "Hello, World!";
```

---

## RESTful APIs

### What is REST?

- **Definition**: A set of architectural principles for designing networked applications using stateless communication.
- **Key Concepts**:
  - **Resources and URIs**: Each resource is identified by a unique URI.
  - **HTTP Methods**: Use of methods like GET, POST, PUT, DELETE to interact with resources.

### Building a RESTful API

- **Guidelines**:
  - **Designing Endpoints**: Clearly define the resources and actions.
  - **Implementing CRUD Operations**: Create, Read, Update, Delete functionalities.

**Example RESTful API Endpoint**:

```javascript
// GET /api/users
app.get("/api/users", (req, res) => {
  // Logic to retrieve users
});
```

---

## CSS

### Introduction to CSS

- **Definition**: A stylesheet language used to describe the presentation of a document written in HTML.
- **Key Topics**:
  - **Selectors, Properties, Values**: Basics of styling elements.
  - **Box Model**: Understanding margins, borders, padding, and content.

**Example CSS Rule**:

```css
body {
  font-family: Arial, sans-serif;
  background-color: #f0f0f0;
}
```

### CSS Flexbox and Grid

- **Flexbox**: A one-dimensional layout model for aligning items in a row or column.
- **Grid**: A two-dimensional layout model for positioning items in rows and columns.

**Example Flexbox CSS**:

```css
.container {
  display: flex;
  justify-content: space-between;
}
```

**Example Grid CSS**:

```css
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}
```

---

## Challenges and Solutions: Setting Up a React Application on EC2

### Challenge 1: Insufficient Memory Resources

- **Issue**: While attempting to create a new React app using `create-react-app`, I encountered a "JavaScript heap out of memory" error due to limited memory (1GB) on the EC2 t3.micro instance.
- **Solution**:
  - **Increase Node.js Memory Limit**: To address the memory issue, I increased the memory allocation for Node.js by using the following command:
  ```bash
  NODE_OPTIONS="--max-old-space-size=1024" npx create-react-app client
  ```

### Challenge 2: MySQL Memory Consumption

- **Issue**: The MySQL server was consuming a significant amount of memory, further limiting available resources for the React installation.
- **Solution**:
  - **Stop MySQL Service**: I stopped the MySQL service temporarily to free up memory:
  ```bash
  sudo systemctl stop mysql
  ```

### Challenge 3: No Swap Space Configured

- **Issue**: The EC2 instance initially had no swap space, which could lead to performance issues during memory-intensive operations.
- **Solution**:
  - **Create and Enable Swap Space**: I created a swap file to increase the available memory:
  ```bash
  sudo fallocate -l 1G /swapfile
  sudo chmod 600 /swapfile
  sudo mkswap /swapfile
  sudo swapon /swapfile
  ```

### Result:

After implementing the above solutions, I successfully created the React application without memory issues, allowing me to continue my development work effectively.

### Reflection:

This experience underscored the importance of resource management on cloud instances, especially when working with memory-intensive applications. It also highlighted the need to proactively monitor system

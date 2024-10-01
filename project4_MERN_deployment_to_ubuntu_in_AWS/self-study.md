# Self-Study Guide: OSI Model, Load Balancing, HTML + CSS + JavaScript

## Table of Contents

1. **OSI Model**
   - Introduction
   - 7 Layers of OSI Model
   - How the OSI Model Works
   - OSI vs. TCP/IP Model
2. **Load Balancing**
   - What is Load Balancing?
   - Types of Load Balancing Algorithms
   - Why Load Balancing is Important
   - Popular Load Balancers
   - Hands-on with Load Balancing
3. **HTML + CSS + JavaScript**
   - What is HTML?
   - What is CSS?
   - What is JavaScript?
   - Building a Simple Webpage from Scratch
   - JavaScript Interactivity
   - Responsive Design with CSS

---

## 1. OSI Model

### Introduction to OSI Model

The OSI (Open Systems Interconnection) Model is a conceptual framework used to understand and standardize the functions of a network or telecommunication system. It helps visualize how data travels from one computer to another across a network.

### The 7 Layers of the OSI Model

The OSI Model has 7 layers, each with specific roles:

1. **Physical Layer**  
   Deals with the physical connection between devices, such as cables, switches, and the raw transmission of bits.  
   Example: Ethernet cables, hubs.

2. **Data Link Layer**  
   Ensures data transfer between two directly connected nodes is error-free. It deals with MAC addresses and error detection/correction.  
   Example: Network interface cards (NIC), switches.

3. **Network Layer**  
   Manages data routing, switching, and addressing, determining the best path for data to travel across networks.  
   Example: Routers, IP addresses.

4. **Transport Layer**  
   Ensures reliable data transfer with mechanisms like segmentation, flow control, and error recovery. TCP and UDP work at this layer.  
   Example: TCP (Transmission Control Protocol), UDP (User Datagram Protocol).

5. **Session Layer**  
   Manages sessions and connections between applications, establishing, maintaining, and terminating communication sessions.  
   Example: Network sockets, APIs.

6. **Presentation Layer**  
   Translates data between the application layer and the network, dealing with encryption, compression, and data encoding.  
   Example: SSL/TLS encryption.

7. **Application Layer**  
   Provides network services directly to end-user applications. It’s what the user interacts with, like web browsers and email clients.  
   Example: HTTP, FTP, SMTP, DNS.

### How the OSI Model Works

Imagine sending an email. The data is generated at the **Application Layer**, then moves down through each layer, where protocols package, encrypt, and route it. At the **Physical Layer**, it is converted into signals (like electrical pulses or light). On the receiving end, the data goes back up the layers, unwrapping the information until the **Application Layer** presents it to the user.

### OSI vs. TCP/IP Model

The OSI model is more theoretical, while the **TCP/IP model** is used practically on the internet today. The **TCP/IP model** has fewer layers:

- Application (covers OSI’s Application, Presentation, and Session layers)
- Transport
- Internet (Network)
- Network Access (Data Link + Physical)

---

## 2. Load Balancing

### What is Load Balancing?

**Load balancing** is the process of distributing network traffic across multiple servers to ensure no single server becomes overwhelmed. It improves performance, reliability, and scalability of a system.

### Types of Load Balancing Algorithms

1. **Round Robin**  
   Traffic is distributed sequentially across servers. Each server gets an equal share of the traffic.

2. **Least Connections**  
   Sends traffic to the server with the fewest active connections, reducing the risk of overloading.

3. **IP Hash**  
   Uses the client's IP address to determine which server should handle the request, ensuring the same client is consistently routed to the same server.

4. **Weighted Round Robin**  
   Servers with higher capacities are assigned more traffic. Useful for servers with unequal processing power.

5. **Geographic Load Balancing**  
   Routes traffic based on the user's geographical location, reducing latency and ensuring faster service.

### Why Load Balancing is Important

- **Improves availability**: If one server goes down, others can take over.
- **Enhances performance**: By distributing traffic, load balancers prevent a single server from becoming a bottleneck.
- **Enables scalability**: As traffic grows, more servers can be added without downtime.

### Popular Load Balancers

1. **HAProxy**: Open-source, widely used for HTTP and TCP load balancing.
2. **NGINX**: Serves as both a web server and load balancer.
3. **AWS Elastic Load Balancer (ELB)**: Cloud-based solution for balancing traffic in AWS environments.

### Hands-on with Load Balancing

You can simulate load balancing by setting up multiple servers (e.g., via Docker containers) and using tools like **NGINX** or **HAProxy** to distribute traffic between them.

---

## 3. HTML + CSS + JavaScript

### What is HTML?

**HTML (HyperText Markup Language)** is the backbone of web content. It defines the structure of a webpage using tags like `<h1>`, `<p>`, `<div>`, and `<a>`.

### What is CSS?

**CSS (Cascading Style Sheets)** controls the visual style of a webpage. It allows you to style elements, apply layouts, add animations, and make the page responsive.

### What is JavaScript?

**JavaScript** is the programming language that brings interactivity to a webpage. From animations to form validation and API calls, JavaScript enhances the user experience.

### Building a Simple Webpage from Scratch

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Simple Webpage</title>
    <style>
      body {
        font-family: Arial, sans-serif;
        margin: 0;
        padding: 0;
        background-color: #f4f4f4;
      }
      header {
        background-color: #333;
        color: white;
        padding: 10px 0;
        text-align: center;
      }
      main {
        padding: 20px;
      }
      footer {
        background-color: #333;
        color: white;
        text-align: center;
        padding: 10px;
        position: fixed;
        width: 100%;
        bottom: 0;
      }
    </style>
  </head>
  <body>
    <header>
      <h1>Welcome to My Webpage</h1>
    </header>

    <main>
      <p>This is a simple webpage built with HTML, CSS, and JavaScript.</p>
      <button onclick="changeContent()">Click me to change content</button>
    </main>

    <footer>
      <p>&copy; 2024 My Webpage</p>
    </footer>

    <script>
      function changeContent() {
        document.querySelector("main p").textContent =
          "You just changed the content using JavaScript!";
      }
    </script>
  </body>
</html>
```

### JavaScript Interactivity

The button on the webpage changes the text inside the paragraph using JavaScript's DOM manipulation (`document.querySelector()`).

### Responsive Design with CSS

To make your page responsive:

```css
@media (max-width: 600px) {
  main {
    padding: 10px;
  }
}
```

This CSS ensures that when the screen width is less than 600px, the padding in the `main` section reduces, optimizing for mobile devices.

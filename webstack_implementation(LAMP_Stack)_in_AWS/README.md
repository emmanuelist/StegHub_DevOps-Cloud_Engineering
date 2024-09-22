# Project README

## Table of Contents

- [Project README](#project-readme)
	- [Table of Contents](#table-of-contents)
	- [Software Development Life Cycle (SDLC)](#software-development-life-cycle-sdlc)
	- [LAMP Stack](#lamp-stack)
	- [File Permissions Management](#file-permissions-management)
		- [chown](#chown)
		- [chmod](#chmod)
	- [Networking Protocols](#networking-protocols)
		- [TCP](#tcp)
		- [UDP](#udp)
	- [Commonly Used Ports](#commonly-used-ports)

---

## Software Development Life Cycle (SDLC)

The Software Development Life Cycle (SDLC) is a structured approach to software development that facilitates the creation of large software products in a systematic and cost-effective manner. Key reasons for using an SDLC model include:

- **Understanding the Entire Process:** Provides a comprehensive view of the development lifecycle.
- **Structured Approach:** Ensures a systematic method for development.
- **Resource Planning:** Aids in the pre-planning of resources and budget.
- **Progress Tracking:** Helps monitor progress throughout the development phases.

---

## LAMP Stack

The LAMP stack is a software development framework consisting of **Linux**, **Apache**, **MySQL**, and **PHP**. It is commonly used for building and deploying web-based applications, including:

- **Content Management Systems (CMS):** Platforms like WordPress are developed using PHP.
- **E-commerce Platforms:** MySQL provides a robust backend for efficiently handling transactions, extensive product catalogs, and large amounts of user data.
- **Dynamic Websites:** PHP’s backend scripting capabilities allow for real-time content adjustments based on user interactions.
- **Data-Driven Applications:** The stack processes data and complex structures in real-time, supporting various data-driven functionalities.

---

## File Permissions Management

### chown

The `chown` command is used to change the ownership of a file. For example, to check the current owner of a file, you can use:

```bash
ls -l file
```

Output:

```
-rwxrwxr-x  2  emmanuelist steghub   5 May  9 12:49 file
```

In this example, **emmanuelist** is the owner of the file and **steghub** is the group. To change the owner to **samuel** and the group to **sudo**, you would use:

```bash
sudo chown samuel:sudo file
```

Now, the file belongs to **samuel**, and all users in the **sudo** group will have group access.

### chmod

The `chmod` command defines permissions for files and directories. It specifies who can read, write, or execute a file. For example:

```bash
chmod 777 file
```

This command grants read, write, and execute permissions to everyone (owner, group, and others).

---

## Networking Protocols

### TCP

TCP (Transmission Control Protocol) is a connection-oriented protocol that ensures reliable data transmission with error-checking. It establishes a connection between sender and receiver before data is sent.

### UDP

UDP (User Datagram Protocol) is a connectionless protocol that allows faster data transmission without guaranteeing delivery or order. It is suitable for applications where speed is critical and some data loss is acceptable.

---

## Commonly Used Ports

Some commonly used ports in web communications include:

- **HTTP:** Port 80
- **HTTPS:** Port 443
- **IRC:** Port 194
- **SNMP:** Port 161
- **IMAP:** Port 143
- **NTP:** Port 123
- **NNTP:** Port 119
- **POP3:** Port 110
- **DNS:** Port 53
- **SMTP:** Port 25
- **SSH:** Port 22
- **FTP:** Port 21
- **Telnet:** Port 23

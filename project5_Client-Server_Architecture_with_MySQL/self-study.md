# Side-Study on Ping, Traceroute, and Basic SQL Commands

This guide serves as a quick reference to understand and utilize `ping`, `traceroute`, and some basic SQL commands. Each section provides practical usage examples.

## 1. Ping

**Purpose**: `ping` tests network connectivity by sending Internet Control Message Protocol (ICMP) packets to a specific IP address or hostname.

### Usage

```bash
ping [options] <hostname/IP>
```

### Common Options

- **`-c <count>`**: Number of packets to send.
- **`-i <interval>`**: Interval between packets in seconds.
- **`-t <ttl>`**: Time-to-live for each packet (limits hop count).
- **`-s <size>`**: Packet size in bytes.

### Examples

1. **Basic Ping**:

   ```bash
   ping google.com
   ```

   Sends packets to google.com and shows the response time.

2. **Limit to 4 packets**:

   ```bash
   ping -c 4 google.com
   ```

   Sends only 4 packets.

3. **Set packet size**:
   ```bash
   ping -s 64 google.com
   ```
   Sends packets with a size of 64 bytes.

### Key Output Information

- **Reply from**: IP address of the responding host.
- **Time**: Round-trip time for each packet.
- **TTL**: Shows the time-to-live (maximum hops) of the packet.

## 2. Traceroute

**Purpose**: `traceroute` displays the path packets take from your machine to a specified destination, listing each hop and measuring transit delays.

### Usage

```bash
traceroute [options] <hostname/IP>
```

### Common Options

- **`-m <max_ttl>`**: Maximum hops (TTL).
- **`-q <nqueries>`**: Number of queries per hop.
- **`-p <port>`**: Port number to send packets to.

### Examples

1. **Basic Traceroute**:

   ```bash
   traceroute google.com
   ```

   Shows each hop and the round-trip time to reach google.com.

2. **Set max hops**:

   ```bash
   traceroute -m 15 google.com
   ```

   Limits the trace to 15 hops.

3. **Set number of queries per hop**:
   ```bash
   traceroute -q 2 google.com
   ```
   Sends only 2 queries per hop.

### Key Output Information

- **Hop Number**: The number of hops from your machine to the destination.
- **IP Address**: The IP address of each hop.
- **Response Time**: The round-trip time for each query.

## 3. Basic SQL Commands

SQL (Structured Query Language) is used to manage and manipulate databases. Here are some foundational SQL commands to interact with tables.

### Common SQL Commands

1. **Select Data**:

   ```sql
   SELECT column1, column2 FROM table_name;
   ```

   Retrieves specified columns from a table.

2. **Insert Data**:

   ```sql
   INSERT INTO table_name (column1, column2) VALUES (value1, value2);
   ```

   Adds new records to a table.

3. **Update Data**:

   ```sql
   UPDATE table_name SET column1 = value1 WHERE condition;
   ```

   Modifies existing records that meet specified conditions.

4. **Delete Data**:

   ```sql
   DELETE FROM table_name WHERE condition;
   ```

   Removes records that meet specified conditions.

5. **Create Table**:

   ```sql
   CREATE TABLE table_name (
       column1 datatype,
       column2 datatype,
       ...
   );
   ```

   Creates a new table with specified columns and datatypes.

6. **Drop Table**:

   ```sql
   DROP TABLE table_name;
   ```

   Deletes an entire table from the database.

7. **Filter Results with WHERE**:

   ```sql
   SELECT column1 FROM table_name WHERE condition;
   ```

   Filters the results based on specified conditions.

8. **Aggregate Functions**:
   - **COUNT**:
     ```sql
     SELECT COUNT(column1) FROM table_name;
     ```
   - **SUM**:
     ```sql
     SELECT SUM(column1) FROM table_name;
     ```
   - **AVG**:
     ```sql
     SELECT AVG(column1) FROM table_name;
     ```

### Practical Examples

1. **Select all records**:

   ```sql
   SELECT * FROM employees;
   ```

   Displays all columns and rows from the `employees` table.

2. **Insert a new record**:

   ```sql
   INSERT INTO employees (name, position) VALUES ('John Doe', 'Manager');
   ```

3. **Update a record**:

   ```sql
   UPDATE employees SET position = 'Senior Manager' WHERE name = 'John Doe';
   ```

4. **Delete a record**:

   ```sql
   DELETE FROM employees WHERE name = 'John Doe';
   ```

5. **Count records in a table**:
   ```sql
   SELECT COUNT(*) FROM employees;
   ```

---

### Additional Resources

- For more in-depth SQL learning, try resources like [SQLZoo](https://sqlzoo.net/) or [W3Schools](https://www.w3schools.com/sql/).
- For network diagnostics tools, explore `mtr`, a combined traceroute and ping tool for live results.

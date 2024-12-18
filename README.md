
# Client-Server Project Using TCP/UDP Sockets in C and Go

This project demonstrates the implementation of a **Client-Server system** using **TCP/UDP sockets**, written in **C** and **Go**. The system facilitates communication between a client and a database service, with a service-mapper acting as a directory for discovering services.

---

## Project Structure

### 1. **`serviceMap.go`** (Service Mapper)
   - A **UDP server** written in **Go** that listens for service registrations and client requests.
   - **Functionality**:
     - Accepts `PUT` requests from the database server to register IP and TCP port details of the service.
     - Responds to `GET` requests from clients with the registered IP and port.
     - Uses **goroutines** for concurrent message handling.

---

### 2. **`server_tcp.c`** (Database Server)
   - A **TCP server** written in **C** that handles database queries and updates.
   - **Functionality**:
     - Broadcasts its TCP port and IP address to the `serviceMap` via UDP.
     - Accepts TCP connections from clients and handles commands:
       - `qry acctnum`: Retrieves account details.
       - `upd acctnum amount`: Updates the account balance.
       - `quit`: Closes the client connection.
     - Uses **lseek** and **lockf** for atomic file operations on the database (`db24`).
     - Uses **SIGCHLD** signal handling to manage child processes for concurrent client handling.

---

### 3. **`client.go`** (Client)
   - Written in **Go**, the client discovers services via UDP and communicates with the database server via TCP.
   - **Functionality**:
     - Sends a `GET BANK620` UDP broadcast to discover the database server.
     - Establishes a TCP connection with the server.
     - Supports commands:
       - `qry acctnum`: Retrieves account details.
       - `upd acctnum amount`: Updates account balance.
       - `quit`: Disconnects from the server.
     - Displays the server's responses in real-time.

---

### 4. **`makefile.txt`** (Compilation Automation)
   - Automates the build process for the project.
   - **Targets**:
     - `all`: Builds the `server`, `client`, and `serviceMap`.
     - `clean`: Removes compiled binaries.

---

## Requirements

- **Languages**:
  - C (for the server)
  - Go (for the client and service mapper)
- **Tools**:
  - GCC (for compiling the server)
  - Go compiler (for building Go programs)

- **Database File**:
  - A binary file named `db24` containing records in the following format:
    ```c
    struct record {
        int acctnum;
        char name[20];
        char phone[16];
        int age;
    };
    ```

---

## Setup and Execution

### Step 1: Build the project
Run the `make` command to build the binaries:
```bash
make
```

This will generate the following executables:
- `server`: The database server.
- `client`: The client application.
- `serviceMap`: The service mapper.

### Step 2: Run the components

#### 1. Start the Service Mapper
Run the service mapper on one machine (e.g., `spirit`):
```bash
./serviceMap
```

#### 2. Start the Database Server
Run the server on a different machine (e.g., `beethoven`):
```bash
./server
```

- The server will broadcast its IP and port to the service mapper.
- The service mapper will confirm the registration.

#### 3. Start the Client
Run the client on another machine (e.g., `brahms`):
```bash
./client
```

- The client will discover the server's IP and port through the service mapper.
- The client can then send commands to the server.

---

## Command Examples

### From the Client (via TCP):
1. **Query an Account**:
   ```bash
   qry 11111
   ```
   **Response**:
   ```
   MULAN HUA 11111 99.989998 (440)687-2001 25
   ```

2. **Update an Account**:
   - Add balance:
     ```bash
     upd 34567 3.4
     ```
     **Response**:
     ```
     BILL SUN 34567 84.000000
     ```

   - Subtract balance:
     ```bash
     upd 34567 -7.8
     ```
     **Response**:
     ```
     BILL SUN 34567 76.199997
     ```

3. **Quit the Client**:
   ```bash
   quit
   ```

   **Effect**: Disconnects the client from the server.

---

## Debugging Notes

1. The client uses `0.0.0.0` to resolve its local UDP address for broadcasting.
2. Mutex locks are used in the server to prevent race conditions during database updates.
3. The `lseek` function is used to locate specific records in the database for reading or writing.
4. The service mapper and server must be started in the correct order for successful communication.

---

## Clean Up

To remove the compiled binaries, run:
```bash
make clean
```

---

## Status

The project is **fully functional** and has been tested across three machines:
- `spirit`: Service Mapper
- `beethoven`: Database Server
- `brahms`: Client

---

## Contributing

Feel free to submit issues or feature requests via GitHub.

---

## Author

This project was developed as part of a learning exercise to understand **TCP/UDP sockets** and explore **C** and **Go programming**.
```

This README provides a detailed overview of the project, its components, commands, and setup instructions.

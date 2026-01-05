# Epoll-Based Chat Server (C++)

A high-performance, single-threaded TCP chat server built using **Linux epoll** and **non-blocking sockets**.  
The server implements a **custom binary protocol** supporting user login, message delivery, acknowledgements, and offline message buffering.  
This project focuses on **low-level systems programming**, **event-driven I/O**, and **network protocol design**.

---

## 🚀 Features

- ⚡ **Epoll-based event loop** (scales to many concurrent connections)
- 🔌 **Non-blocking TCP sockets**
- 📦 **Custom binary message protocol**
- 👤 User login with unique user IDs
- ✉️ Message delivery with ACK-based reliability
- 📥 Offline message buffering and resend on login
- 🧠 Zero threads, zero external libraries

---

## 🧩 Architecture Overview
```
Client
  |
  | TCP (custom binary protocol)
  v
Epoll Event Loop
  ├── Accept new connections
  ├── Read incoming frames
  ├── Parse protocol messages
  ├── Route messages to recipients
  └── Handle ACKs & pending messages
```

The server uses **epoll** to multiplex all socket I/O inside a single event loop, avoiding blocking operations and thread management overhead.

---

## 📁 Project Structure
```
epoll-server/
├── CMakeLists.txt
├── include/
│   ├── connection.h      # Per-connection state
│   ├── protocol.h        # Message framing & parsing
│   ├── net_utils.h       # Socket utilities
│   └── server_state.h    # Global server state
└── src/
    ├── main.cpp          # Program entry point
    ├── server.cpp        # Epoll loop & core logic
    ├── protocol.cpp      # Protocol implementation
    └── net_utils.cpp     # Non-blocking helpers
```

---

## 📡 Protocol Summary

Each message frame has the following layout:
```
+------------+----------+------------+-------------+
| Length (4) | Type (2) | Msg ID (8) | Payload (...)|
+------------+----------+------------+-------------+
```

### Message Types

| Type | Name  | Description                      |
|------|-------|----------------------------------|
| 1    | LOGIN | User login request               |
| 2    | SEND  | Send message to another user     |
| 3    | MSG   | Server → client message delivery |
| 4    | ACK   | Acknowledgement                  |

- All integers are encoded in **network byte order**
- ACKs ensure reliable delivery
- Messages for offline users are buffered and resent on login

---

## 🛠 Build Instructions

### Requirements

- Linux (epoll-based)
- GCC 11+ or Clang
- CMake ≥ 3.16

### Build
```bash
mkdir build
cd build
cmake ..
make -j
```

### Run
```bash
./server
```

Expected output:
```
epoll server listening on port 9000
```

---

## 📝 Usage Example

### Client Connection
```bash
telnet localhost 9000
```

### Sample Protocol Flow

1. **Client logs in**
```
   LOGIN user_id
```

2. **Client sends message**
```
   SEND recipient_id message 
```

3. **Server delivers message**
```
   MSG to recipient (or buffers if offline)
```

4. **Client acknowledges**
```
   ACK with message_id
```

---

## 🧪 Testing 

For testing purposes, you would manually **construct a ByteBuffer** that follows the protocol frame format and send it directly over the **TCP connection** to the server.

This allows you to:
- Validate message framing and parsing
- Test login, send, and ACK flows without a full client implementation
- Inject malformed or partial frames to verify robustness of the epoll loop

Example testing flow:
1. Build a ByteBuffer in network byte order
2. Populate Length, Type, Msg ID, and Payload fields
3. Write the buffer to the TCP socket
4. Observe server responses and ACK handling


## 📚 Learning Objectives

This project demonstrates:

- **System calls**: `epoll_create`, `epoll_ctl`, `epoll_wait`, `accept`, `recv`, `send`
- **Non-blocking I/O**: Managing partial reads/writes
- **Binary protocol design**: Framing, endianness, state machines
- **Event-driven architecture**: Single-threaded multiplexing
- **Network programming**: TCP socket lifecycle

---

## 🔮 Future Enhancements

- [ ] Room/channel support
- [ ] Authentication & encryption (TLS)
- [ ] Persistent message storage (SQLite)
- [ ] Rate limiting & flood protection
- [ ] Metrics & monitoring (Prometheus)

---


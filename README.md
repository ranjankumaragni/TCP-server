# TCP Chat Server

![C++](https://img.shields.io/badge/C%2B%2B-23-00599C?logo=cplusplus&logoColor=white)
![CMake](https://img.shields.io/badge/CMake-%E2%89%A5%203.16-064F8C?logo=cmake&logoColor=white)
![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20macOS-informational)
![License](https://img.shields.io/badge/license-MIT-green)

A multi-client TCP chat server built from scratch in modern C++ (C++23), handling concurrent connections and message routing at the raw socket level using `select()`.

## Features

- **Multi-client support** — handles multiple simultaneous connections via a `select()`-based event loop (single-threaded, non-blocking I/O)
- **Nickname registration** — clients pick a nickname on connect; duplicates are rejected
- **Live broadcast messaging** — messages are relayed to all other connected clients in real time
- **Join/leave notifications** — the room is notified when a user connects or disconnects
- **Welcome message** — new clients see who else is currently online
- **Graceful disconnect handling** — cleans up sockets and file descriptors on client drop or reset connections
- **Generic, testable design** — `ChatServer` is templated over the socket type and client container (via C++20 concepts), so it isn't hard-wired to raw `int` sockets or `std::list`

## Tech Stack

- C++23 (concepts, ranges, `std::format`, `std::print`)
- POSIX sockets (`sys/socket.h`, `arpa/inet.h`)
- CMake (≥ 3.16) build system

## Prerequisites

- A C++23-capable compiler (e.g. GCC 14+ or Clang 18+)
- CMake ≥ 3.16
- Linux/macOS (uses POSIX socket APIs)

## Build

```bash
git clone https://github.com/ranjankumaragni/TCP-server.git
cd TCP-server
cmake -B build
cmake --build build
```

This produces an executable named `TCPChatCpp` in the `build` directory.

## Run

```bash
./build/TCPChatCpp
```

By default the server listens on `127.0.0.1:3000`.

## Usage

Connect with any TCP client, e.g. `netcat` or `telnet`:

```bash
nc 127.0.0.1 3000
```

1. You'll be prompted to enter a nickname.
2. Once registered, you'll see a welcome message listing other online users.
3. Anything you type is broadcast to all other connected clients.
4. Disconnecting (closing the connection) notifies the rest of the room.

Open multiple terminal sessions to simulate a multi-user chat.

## Project Structure

```
TCP-server/
├── main.cpp         # Server implementation (socket setup, event loop, client/message handling)
├── CMakeLists.txt   # Build configuration
└── demo.jpg         # Demo screenshot
```

## How It Works

The server maintains a single listening socket plus a `master_set` of file descriptors. Each iteration of the event loop calls `select()` to find sockets that are ready to read — either a new incoming connection on the server socket, or incoming data from an already-connected client. New connections are accepted and prompted for a nickname; subsequent messages from a registered client are formatted and broadcast to everyone else.

## License

Add a license of your choice (e.g. MIT) here.

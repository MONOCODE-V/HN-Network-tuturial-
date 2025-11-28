# UDP in Node.js

A simple, minimal tutorial and example that shows how to build a **UDP server** using **Node.js**. This README is ready to paste into a GitHub repository.

---

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Create the UDP Server](#create-the-udp-server)
- [Run the Server](#run-the-server)
- [Test with netcat](#test-with-netcat)
- [UDP Client Example (Node.js)](#udp-client-example-nodejs)
- [Notes & Next Steps](#notes--next-steps)
- [License](#license)

---

## Overview

This repository demonstrates a tiny UDP server implemented in Node.js using the built-in `dgram` module. UDP (User Datagram Protocol) is a connectionless, low-overhead transport protocol commonly [...]

> **Important:** UDP is *unreliable* (no guarantees on delivery, ordering or duplication). Use UDP when low-latency is more important than guaranteed delivery.

---

## Prerequisites

- Node.js (v14+ recommended)
- npm (optional, not required for this example)
- A terminal (macOS / Linux / WSL / Git Bash on Windows)
- `netcat` (`nc`) for quick tests (optional but convenient)

---

## Project Structure

```
udp-server/
├─ index.js        # UDP server implementation
├─ client.js       # (optional) UDP client for testing
└─ README.md       # This file
```

---

## Create the UDP Server

Create a file named `index.js` and paste the following code:

```js
import dgram from "dgram";

// Create a UDP socket (IPv4)
const server = dgram.createSocket("udp4");

// When a message is received
server.on("message", (msg, rinfo) => {
  console.log(`Message: ${msg} from ${rinfo.address}:${rinfo.port}`);

  // Optional: send a response back to the sender
  const response = "Hello from UDP server!";
  server.send(response, rinfo.port, rinfo.address, (err) => {
    if (err) console.error("Send error:", err);
  });
});

// When the server starts listening
server.on("listening", () => {
  const address = server.address();
  console.log(`Server listening at ${address.address}:${address.port}`);
});

// Bind the server to port 5000 (change if needed)
server.bind(5000);
```

> **Notes:**
> - The built-in `dgram` module is used — no external dependencies required.
> - If you prefer CommonJS (older Node), replace the `import` line with: `const dgram = require('dgram');` and run with `node index.js` (ensure package type is correct or use `.cjs`).

---

## Run the Server

Start the server with:

```bash
node index.js
```

Expected output:

```
Server listening at 0.0.0.0:5000
```

---

## Test with netcat

From another terminal, send a UDP message using `netcat`:

```bash
# macOS / Linux
echo "hello" | nc -u 127.0.0.1 5000

# On some platforms you may need to use: nc -u -w1 127.0.0.1 5000
```

You should see on the server terminal:

```
Message: hello from 127.0.0.1:xxxxx
```

If the server sends the optional response, `nc` may print it or exit immediately depending on `nc` flags/implementation.

---

## UDP Client Example (Node.js)

An optional client (`client.js`) to send messages from Node.js:

```js
import dgram from 'dgram';

const client = dgram.createSocket('udp4');
const message = Buffer.from('Hello from client');

client.send(message, 5000, '127.0.0.1', (err) => {
  if (err) console.error(err);
  else console.log('Message sent');
  client.close();
});
```

Run the client with:

```bash
node client.js
```

---

## Notes & Next Steps

- **Ports & Firewalls:** If testing across machines, make sure UDP port 5000 is allowed through firewalls.
- **IPv6:** Use `udp6` or `dgram.createSocket('udp6')` for IPv6.
- **Production use:** For higher-level features (reliability, ordering, retransmission), implement them at the application level or use TCP where appropriate.
- **Further examples:** Add C or Python examples to show lower-level buffer handling and system calls.

---

## License

This project is released under the MIT License. See `LICENSE` for details.

---

Happy hacking! If you'd like me to add CI, Dockerfile, or a Docker image for the UDP server, tell me and I will add it.
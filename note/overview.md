A SocketIO System has 7 Key parts;

- EngineIO
- SocketIO
- EngineIO Parser
- SocketIO Parser
- EngineIO Client
- SocketIO Client
- SocketIO Adapter


## How it works under the hood

The `socket.io-client` library handles the connection process through a series of well-defined steps that leverage the `engine.io-client` library for transport management. Here’s an in-depth look at what happens under the hood when a client establishes a connection to a server using Socket.IO.

### Connection Process

1. **Client Initialization**:
   - When you create a new Socket.IO client instance with `const client = io('https://myhost.com');`, a new `engine.io-client` instance is also created under the hood.
   
2. **Transport Establishment**:
   - The `engine.io-client` instance starts by attempting to establish a connection using the XHR polling transport.
   - It sends a GET request to the server:
     ```plaintext
     GET https://myhost.com/socket.io/?EIO=3&transport=polling&t=ML4jUwU&b64=1
     ```
     - `EIO=3`: Indicates the Engine.IO protocol version.
     - `transport=polling`: Specifies that the client is attempting to establish a polling transport.
     - `t=ML4jUwU&b64=1`: A hashed timestamp for cache-busting purposes.

3. **Server Response**:
   - The server responds with a JSON object containing session details:
     ```json
     {
       "type": "open",
       "data": {
         "sid": "36Yib8-rSutGQYLfAAAD",
         "upgrades": ["websocket"],
         "pingInterval": 25000,
         "pingTimeout": 5000
       }
     }
     ```
     - `sid`: The unique session ID.
     - `upgrades`: List of possible transport upgrades (e.g., WebSocket).
     - `pingInterval` and `pingTimeout`: Parameters for the heartbeat mechanism.

4. **Packet Encoding**:
   - The response content is encoded by the `engine.io-parser`:
     ```plaintext
     '96:0{"sid":"hLOEJXN07AE0GQCNAAAB","upgrades":["websocket"],"pingInterval":25000,"pingTimeout":5000}2:40'
     ```
     - `96`: Length of the first message.
     - `:`: Separator between length and content.
     - `0`: Message type indicating an "open" message.
     - JSON-encoded handshake data follows.
     - `2`: Length of the second message.
     - `4`: Message type indicating a "message" in the Socket.IO protocol.

5. **Packet Decoding**:
   - On the client-side, the encoded content is decoded by the `engine.io-parser`.

6. **Event Emission**:
   - An `open` event is emitted at the `engine.io-client` level, signaling that the connection has been established.
   - A `connect` event is emitted at the `socket.io-client` level, indicating that the Socket.IO connection is ready.

### Upgrade Process

1. **Transport Upgrade**:
   - Once the XHR polling transport's buffers are flushed, the client tests a WebSocket upgrade by sending a probe:
     ```plaintext
     GET wss://myhost.com/socket.io/?EIO=3&transport=websocket&sid=36Yib8-rSutGQYLfAAAD
     ```
     - `EIO=3`: The Engine.IO protocol version.
     - `transport=websocket`: The new transport being tested.
     - `sid=36Yib8-rSutGQYLfAAAD`: The session ID.

2. **Ping-Pong Probe**:
   - The client sends a "ping" packet in a WebSocket frame, encoded as `2probe` by the `engine.io-parser`.
     - `2`: Message type indicating a "ping".
   - The server responds with a "pong" packet, encoded as `3probe`.
     - `3`: Message type indicating a "pong".

3. **Upgrade Completion**:
   - Upon receiving the "pong" packet, the client considers the upgrade successful.
   - All subsequent messages are sent via the new WebSocket transport.

### Summary

The `socket.io-client` manages the connection and transport switching through the following steps:

- **Initialization**: A new `engine.io-client` instance is created.
- **Polling Transport**: The client establishes an initial connection using XHR polling.
- **Server Handshake**: The server provides session details, possible upgrades, and heartbeat parameters.
- **Packet Handling**: Packets are encoded and decoded using the `engine.io-parser`.
- **Event Emission**: `open` and `connect` events signal successful connection establishment.
- **Transport Upgrade**: The client probes for a WebSocket upgrade and switches to it upon successful "ping-pong" communication.

This process ensures that the client maintains a reliable and efficient real-time connection with the server, automatically handling reconnections and transport upgrades as needed.
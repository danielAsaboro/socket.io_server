# There are 3 fundamental parts of a SocketIO Connection

1. SocketIO Adapter
2. SocketIO Parser
3. EngineIO Parser


## EngineIO Parser

The engine.io-parser is a JavaScript library that is responsible for encoding and decoding messages according to the engine.io protocol. This protocol is used for communication between the client and server in the engine.io library, which is a low-level transport library underlying Socket.IO.

### Key Points

#### Purpose:

The engine.io-parser library is essential for converting messages into a format that can be transmitted over the network (encoding) and converting received messages back into a format that can be used by the application (decoding).

#### Shared Library:

The parser is used by both engine.io-client and engine.io:
engine.io-client is the client-side library.
engine.io is the server-side library.

Both the client and the server need to understand the same message format, hence they use the same parser.

#### Protocol Specification:

The specification for the protocol defines how messages should be structured and interpreted. This includes details about the types of messages, how they should be encoded, and the rules for decoding them.
The specification can be found in the engine.io-protocol repository on GitHub: engine.io-protocol specification.


## SocketIO Parser 
The socket.io-parser is a JavaScript library that serves as the encoder and decoder for the Socket.IO protocol, specifically complying with version 3 of the protocol. It is used by both socket.io (the server-side library) and socket.io-client (the client-side library) to ensure consistent communication between the client and server.

### Key Points

#### Purpose:

The socket.io-parser library is designed to encode (serialize) and decode (deserialize) messages that are sent and received using the Socket.IO protocol.

#### Shared Library:

It is utilized by both socket.io on the server side and socket.io-client on the client side. This ensures that both ends of the communication channel understand the message formats and can encode and decode messages correctly.

Protocol Compliance:

The parser complies with version 3 of the Socket.IO protocol. This means it follows the rules and specifications laid out in that version for how messages should be structured, encoded, and decoded.

## SocketIO Adapter

The socket.io-adapter is a core module of the Socket.IO library that provides the default in-memory adapter for Socket.IO. This module facilitates the communication between different Socket.IO server instances and manages the rooms and namespaces functionality. It can also be used as a base class for creating custom adapters, such as those that interface with external systems like Redis.

### Key Points

#### Default In-Memory Adapter:

The socket.io-adapter is the default adapter used by Socket.IO to manage client connections, rooms, and namespaces in memory.
It keeps track of which clients are connected to which rooms and namespaces within a single server instance.
Not Intended for End-User Usage:

This module is generally not intended to be used directly by end-users.
End-users interact with the higher-level Socket.IO API, which internally uses the adapter to manage rooms and namespaces.

#### Interface for Custom Adapters:

The socket.io-adapter can serve as a base class for developing custom adapters.
Developers can extend this class to create adapters that connect Socket.IO with other systems or services, such as socket.io-redis for Redis-based pub/sub functionality.

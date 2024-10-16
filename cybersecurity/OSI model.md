## Overview
7. Application
6. Presentation
5. Session
4. Transport
3. Network Layer
2. Data link
1. Physical

---

## Some definitions
- Protocols: Pre-defined rules and conventions that define the transport, format, handling, and routes of data between devices. 
- Multiplexing: Combining multiple signals or data streams into one while keeping them distinct. Some types are:
	- Time Division
	- Wavelength Division
	- Frequency Division
	- Code Division

---

## Layers
### 7. Application Layer
Topmost layer that handles high level protocols, encoding and dialogue control. 
Its like HTTP requests. 
Handles data exchange and user interaction

### 6. Presentation Layer
Handles translation of the data that is received by the lower levels into an understandable format for the Application Layer. 
Data encryption and decryption is managed by this layer. 
ASCII or Unicode characters translation is done.
Ensures that the data is formatted and encoded in the right format. 
Handles encryption for services, for example SSL ("Secure Sockets Layer") and TLS ("Transport Security Layer") which are used to secure communications over the network.
Data compression aswell. For efficiency.

### 5. Session Layer
Handles establishing, maintaining, coordinating and terminating connections.

### 4. Transport Layer
There are different types of applications depending on the need.
TCP (Transmission Control Protocol)
UDP(User Datagram Protocol)
QUIC(Quick UDP Internet Connections)
The key features of the Transport Layer is to
- Break down huge chunks of data into manageable forms 
- Can identify errors and if any, can request for retransmission.
- Flow control
- Multiplexing
Protocols:
[[Transport Layer]]

### 3. Network Layer
Determines the best path to travel from the source to the destination
Assigns IP address for devices
Handles fragmentation and reassembly
Protocols:
[[Network Layer]]
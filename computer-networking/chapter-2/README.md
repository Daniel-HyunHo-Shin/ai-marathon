# 2.Application Layer

A programmer creates software that will be copied and used on many different users' computers.

However, not everyone's computer runs on the same system. Some users will have Windows 11, while others might have Windows 10.

Given this, it's clear that programmers don’t want to write a code that is responsible for handling the specific details of each system.

Instead, it's better to let the operating system or some software handle these details, particularly those related to the network.

Moreover, it's advisable not to allow programmers to directly intervene in these lower-level operations, as doing so could risk breaking the system.

A programmer doesn't and can't tap into the details of the network. However, a programmer can choose how the application will interact with other systems. So, the overall design is allowed, but the specifics are not.

There are two predominant architectural paradigms: **client-server architecture** and **peer-to-peer architecture**.

**Client-Server Architecture**

Client-server architecture is centralized. In this model, there is a designated computer (the server) that receives and responds to requests from other computers (the clients). You send a request to the server, and it processes and replies to your request.

**Peer-to-Peer Architecture**

In contrast, peer-to-peer architecture is decentralized. In this model, every participant can both send and receive data. Each node acts as both a client and a server simultaneously.

## **2.1.2 Processes Communicating**

Program is a file that computer can reads and run. Process is a ‘running program’.

Programmers write a program. Users run a process.

What we are interested in is how two different processes send messages to each other. To analyze this, we have to clarify who’s sending request and who’s receiving the request.

The one who initiates communication is called the 'client,' and the other process is called the 'server’.

### **The process talks to Socket for network service**

As mentioned earlier, the details of internet communication are managed by software such as the operating system.

The software API that our process interacts with is called a 'socket.' So, when we say we are writing code to send messages to Google, we are actually writing code that sends a message, with Google's address, to the 'socket.'

Here is simple example of socket programming written in Python

```python
import socket

# Create a socket object
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Define the server's address and port
host = '127.0.0.1'  # server's IP address
port = 12345

# Connect to the server
client_socket.connect((host, port))

# Send a message to the server
client_socket.sendall(b"Hello, Server!")

# Receive a response from the server
data = client_socket.recv(1024)
print("Received from server:", data.decode())

# Close the connection
client_socket.close()

```

As I am a flutter programmer, it’s good to try Dart language too.

```dart
// source: https://medium.com/@buddi/establish-a-client-side-tcp-socket-connection-for-data-communication-using-the-dart-io-117e2f76b540

import 'dart:io';

void main() async {
  // Define the server's IP address and port
  final String serverIp = '127.0.0.1'; // Change to the server's IP address
  final int serverPort = 12345; // Change to the server's port

  try {
    // Create a socket connection to the server
    final socket = await Socket.connect(serverIp, serverPort);

    // Send data to the server
    socket.writeln('Hello, server!');

    // Listen for data from the server
    socket.listen(
      (data) {
        print('Received from server: ${String.fromCharCodes(data)}');
      },
      onDone: () {
        print('Server disconnected.');
        socket.destroy();
      },
      onError: (error) {
        print('Error: $error');
        socket.destroy();
      },
    );

    // Close the socket when you're done
    // socket.close();
  } catch (e) {
    print('Error: $e');
  }
}
```

### **Addressing Processes**

So far, we have learned two key tasks that programmers can perform with their programs:

1. Choose a network architecture: client-server or peer-to-peer (P2P).
2. Write code for socket communication.

Now, we introduce one more important concept: defining an address. It’s essential to specify to whom we want to send a message.

When you define two pieces of information must be specified:

1. The server's address = IP address.
2. The server process's name = Port number.

The address of server is called IP address. IP address is a numerical label that is assigned to a device connected to a network. It’s commonly written as ‘dots and numbers’: `192.0.2.111`.

One computer can run multiple programs. You can browse website with chrome while listening to music from spotify.

So you must specify which process you are talking to. Is it chrome browser, or is it spotify app? that address is called port number.

## **2.1.3 Transport Services Available to Applications**

When working with networks, there’s one more decision for programmers to make: choosing the transport protocol. But you might wonder, why not just let the network software handle this automatically?

Well, it comes down to trade-offs. For example, if you want more reliable communication, you’ll have to give up some speed and deal with higher latency.

So, the choice is yours. Want speed? You’ll need to accept that sometimes data won’t make it to the server.

There are four important things to consider when working with networks:

1. Reliable Data Transfer
2. Throughput
3. Timing
4. Security

And when it comes to the internet, we mainly have two options: TCP and UDP protocols.

### **TCP Protocol**

When you want reliablity, you will choose TCP.

TCP improves reliability by testing network before it exchange the data. How does it test? by sending mock request and response. This is called Hand-shaking procedure. After hand shaking procedure, we say **TCP connection** is said to exist between the sockets of the two processes.

TCP also manages if all data sent without error and in the proper order.

TCP also includes a congestion-control mechanism.

<aside>
💡 We don’t write the TCP protocol. We choose TCP protocol.

</aside>

### UDP Protocol

On the other hand, UDP protocol focuses on latency and speed. It just pour all the data the socket hoping it will reach the destination as soon as possible.

Receving order? Don’t care
Reliability? Don’t care

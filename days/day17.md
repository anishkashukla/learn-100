### Testing for WebSockets security vulnerabilities

### WebSockets
````
WebSockets are widely used in modern web applications. 
They are initiated over HTTP and provide long-lived connections with asynchronous communication in both directions.

WebSockets are used for all kinds of purposes, including performing user actions and transmitting sensitive information. 
Virtually any web security vulnerability that arises with regular HTTP can also arise in relation to WebSockets communications.

WebSockets are a bi-directional, full duplex communications protocol initiated over HTTP. They are commonly used in modern web applications for streaming data and other asynchronous traffic.
````
### What is the difference between HTTP and WebSockets ?
````
Most communication between web browsers and web sites uses HTTP. With HTTP, the client sends a request and the server returns a response. 
Typically, the response occurs immediately, and the transaction is complete. Even if the network connection stays open, this will be used for a separate transaction of a request and a response.

Some modern web sites use WebSockets. WebSocket connections are initiated over HTTP and are typically long-lived. 
Messages can be sent in either direction at any time and are not transactional in nature. The connection will normally stay open and idle until either the client or the server is ready to send a message.

WebSockets are particularly useful in situations where low-latency or server-initiated messages are required, such as real-time feeds of financial data.
````
## How are WebSocket connections established ?
````
WebSocket connections are normally created using client-side JavaScript like the following:

var ws = new WebSocket("wss://normal-website.com/chat");

To establish the connection, the browser and server perform a WebSocket handshake over HTTP. The browser issues a WebSocket handshake request like the following:

GET /chat HTTP/1.1
Host: normal-website.com
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: wDqumtseNBJdhkihL6PW7w==
Connection: keep-alive, Upgrade
Cookie: session=KOsEJNuflw4Rd9BDNrVmvwBF9rEijeE2
Upgrade: websocket

If the server accepts the connection, it returns a WebSocket handshake response like the following:

HTTP/1.1 101 Switching Protocols
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Accept: 0FFP+2nmNIf/h+4BP36k9uzrYGk=

At this point, the network connection remains open and can be used to send WebSocket messages in either direction.
````
### What do WebSocket messages look like ?
````
Once a WebSocket connection has been established, messages can be sent asynchronously in either direction by the client or server.

A simple message could be sent from the browser using client-side JavaScript like the following:

ws.send("Peter Wiener");

In principle, WebSocket messages can contain any content or data format. 
In modern applications, it is common for JSON to be used to send structured data within WebSocket messages.

For example, a chat-bot application using WebSockets might send a message like the following:

{"user":"Hal Pline","content":"I wanted to be a Playstation growing up, not a device to answer your inane questions"}
````
### Manipulating WebSocket traffic
````
Finding WebSockets security vulnerabilities generally involves manipulating them in ways that the application doesn't expect. 
You can do this using Burp Suite.

You can use Burp Suite to:

    Intercept and modify WebSocket messages.
    Replay and generate new WebSocket messages.
    Manipulate WebSocket connections.
    Intercepting and modifying WebSocket messages
You can use Burp Proxy to intercept and modify WebSocket messages, as follows:

Configure your browser to use Burp Suite as its proxy server.
Browse to the application function that uses WebSockets. Y
ou can determine that WebSockets are being used by using the application and looking for entries appearing in the WebSockets history tab within Burp Proxy.
In the Intercept tab of Burp Proxy, ensure that interception is turned on.
When a WebSocket message is sent from the browser or server, it will be displayed in the Intercept tab for you to view or modify. 
Press the Forward button to forward the message.
````

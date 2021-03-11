### The HTTP Protocol 

````
HTTP (Hyper Text Transmission Protocol) - Core communications protocol used to access
the World Wide Web (www)
It uses a message based model in which client sends a request message and the server returns a response message.
It is a stateless protocol but uses TCP protocol which is stateful and is used as a transport mechanism.
````
### HTTP Requests

````
All request and response consists of one or more headers, each on a seperate line followed by a mandatory blank line

1st line has 3 components seperated by spaces
1. A verb containing the HTTP method (GET,PUT,POST etc)
2. The requested URL
3. The HTTP version being used
````
#### Headers 
````
Referrer Header - URL from which the request is originated
User-Agent - Used to provide information about the browser
Host - This header specifies the hostname that appeared in the full URL
Cookie - Used to submit additional parameters that the server has issued to the client
````
### HTTP Response 
````
The first line of every HTTP response consists of three items seperated by spaces -
1. The HTTP version being used
2. A numeric status code (200,300,404,503)
3. A textual "reason phrase" describing the status of the response (OK, Moved Temporarily etc)
````
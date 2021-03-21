### What is OAuth ?
````
Auth is a commonly used authorization framework that enables websites and web applications to request limited access to a user's account on another application. 
Crucially, OAuth allows the user to grant this access without exposing their login credentials to the requesting application. This means users can fine-tune which data they want to share rather than having to hand over full control of their account to a third party.

The basic OAuth process is widely used to integrate third-party functionality that requires access to certain data from a user's account. 
For example, an application might use OAuth to request access to your email contacts list so that it can suggest people to connect with. However, the same mechanism is also used to provide third-party authentication services, allowing users to log in with an account that they have with a different website
````
### How OAuth 2.0 works ?
````
OAuth 2.0 was originally developed as a way of sharing access to specific data between applications. 
It works by defining a series of interactions between three distinct parties, namely a client application, a resource owner, and the OAuth service provider.

Client application - The website or web application that wants to access the user's data.

Resource owner - The user whose data the client application wants to access.

OAuth service provider - The website or application that controls the user's data and access to it. They support OAuth by providing an API for interacting with both an authorization server and a resource server.
````
### Oauth Authentication 
````
For OAuth authentication mechanisms, the basic OAuth flows remain largely the same; the main difference is how the 
client application uses the data that it receives. From an end-user perspective, the result of OAuth authentication is 
something that broadly resembles SAML-based single sign-on (SSO). In these materials, we'll focus exclusively on vulnerabilities in this SSO-like use case.

OAuth authentication is generally implemented as follows:

The user chooses the option to log in with their social media account. The client application then uses the 
social media site's OAuth service to request access to some data that it can use to identify the user. This could be the email address that is registered with their account, for example.
After receiving an access token, the client application requests this data from the resource server, typically from a dedicated /userinfo endpoint.
Once it has received the data, the client application uses it in place of a username to log the user in. The access token that it received from the authorization server is often used instead of a traditional password.
````
### How do OAuth authentication vulnerabilities arise ?
````
OAuth authentication vulnerabilities arise partly because the OAuth specification is relatively vague and flexible by design. Although there are a handful of mandatory components required for the basic functionality of each grant type, the vast majority of the implementation is completely optional. This includes many configuration settings that are necessary for keeping users' data secure. In short, there's plenty of opportunity for bad practice to creep in.

One of the other key issues with OAuth is the general lack of built-in security features. The security relies almost entirely on developers using the right combination of configuration options and implementing their own additional security measures on top, such as robust input validation. As you've probably gathered, there's a lot to take in and this is quite easy to get wrong if you're inexperienced with OAuth.

Depending on the grant type, highly sensitive data is also sent via the browser, which presents various opportunities for an attacker to intercept it
````

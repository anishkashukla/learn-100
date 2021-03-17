### What is Authentication ?
````
Authentication is the process of verifying the identity of a given user or client. In other words, it involves 
making sure that they really are who they claim to be.
````
### What is the difference between authentication and authorization ?
````
Authentication is the process of verifying that a user really is who they claim to be, whereas authorization involves verifying 
whether a user is allowed to do something.

In the context of a website or web application, authentication determines whether someone attempting to access the site with the 
username Anishka really is the same person who created the account.

Once Anishka is authenticated, her permissions determine whether or not he is authorized, for example, to access personal 
information about other users or perform actions such as deleting another user's account.
````
### How do authentication vulnerabilities arise ?
````
Most vulnerabilities in authentication mechanisms arise in one of two ways:

* The authentication mechanisms are weak because they fail to adequately protect against brute-force attacks.
* Logic flaws or poor coding in the implementation allow the authentication mechanisms to be bypassed entirely by an attacker. 
This is sometimes referred to as "broken authentication".
````
### Impact of Vulnerable Authentication 
````
The impact of authentication vulnerabilities can be very severe. Once an attacker has either bypassed authentication or has 
brute-forced their way into another user's account, they have access to all the data and functionality that the compromised 
account has. If they are able to compromise a high-privileged account, such as a system administrator, they could take full 
control over the entire application and potentially gain access to internal infrastructure
````
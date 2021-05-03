# day2

| Index | Technique |
| :--- | :--- |
| **1** | Why Vulnerabilities occur ? |
| **2** | Handling User Access |
| **3** | Handling User Input |
| **4** | Boundary Validation |
| **5** | Multistep Validation and Canonicalization |
| **6** | Handling Attackers |
| **7** | Managing the Application |

## Do you ever thought that instead of using SSL and other external services in order to secure the website but still it is vulnerable to different attacks ?

```text
Well, the prime reason for this is because the user is able to give some arbitrary input 
in the web application.
User can give some malicious input such as <script>alert(1)</script> so it is important to handle the user input properly
```

### Handling User Access

```text
Authentication - Proving who you are claiming to be
Session Management - Session should expire
Access Control - Right peope should have access to right resources at the right time for the right reasons
```

### Handling User Input

```text
* Validation 
* Reject Known Bad
* Accept Known Good
* Sanitization 
* Safe Data Handling 
* Semantic Checks 
* Boundary Validation
```

### Boundary Validation

```text
We are doing boundary validation because user input still cannot be trusted because 
we do not know whether the server is receiving the safe or trusted data or not 
Boundary validation is validating at every boundary like -

When input is given -> When it is sent to server -> When it is sent to some services

At every boundary, validating the input is very important in order to secure your web application
```

### Multistep Validation and Canonicalization

```text
Problem is when user-supplied input is manipulated across serveral steps as part of the 
validation logic - It should be handled properly -> Encoding may bypass this vulnerability as multiple encodings can bypass this
```

## We have taken steps to make user input safe and now the other part is Handling Attackers

### Handling Attackers

```text
* Handling errors 
* Maintaining audit logs
* Alerting administrators 
* Reacting to Attacks
```

### Managing the Application

```text
Application should be managed or administered properly in order to make it secure
```

## These are some ways to secure the Web Application


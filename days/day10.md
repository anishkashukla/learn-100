### What are Business Logic Vulnerabilities ?
````
Business logic vulnerabilities are flaws in the design and implementation of an application that allow an attacker to elicit unintended behavior. 
This potentially enables attackers to manipulate legitimate functionality to achieve a malicious goal. 
These flaws are generally the result of failing to anticipate unusual application states that may occur and, consequently, failing to handle them safely.

Logic flaws are often invisible to people who aren't explicitly looking for them as they typically won't be exposed by normal use of the application. 
However, an attacker may be able to exploit behavioral quirks by interacting with the application in ways that developers never intended.

One of the main purposes of business logic is to enforce the rules and constraints that were defined when designing the application or functionality. 
Broadly speaking, the business rules dictate how the application should react when a given scenario occurs. 
This includes preventing users from doing things that will have a negative impact on the business or that simply don't make sense.

Flaws in the logic can allow attackers to circumvent these rules. 
For example, they might be able to complete a transaction without going through the intended purchase workflow. 
In other cases, broken or non-existent validation of user-supplied data might allow users to make arbitrary changes to transaction-critical values or submit nonsensical input. 
By passing unexpected values into server-side logic, an attacker can potentially induce the application to do something that it isn't supposed to.

Logic-based vulnerabilities can be extremely diverse and are often unique to the application and its specific functionality. 
Identifying them often requires a certain amount of human knowledge, such as an understanding of the business domain or what goals an attacker might have in a given context. 
This makes them difficult to detect using automated vulnerability scanners. As a result, logic flaws are a great target for bug bounty hunters and manual testers in general.
````
### How do business logic vulnerabilities arise ?
````
Business logic vulnerabilities often arise because the design and development teams make flawed assumptions about how users will interact with the application. 
These bad assumptions can lead to inadequate validation of user input. 
For example, if the developers assume that users will pass data exclusively via a web browser, 
the application may rely entirely on weak client-side controls to validate input. 
These are easily bypassed by an attacker using an intercepting proxy.

Ultimately, this means that when an attacker deviates from the expected user behavior, the application fails to take appropriate steps to prevent this and, subsequently, fails to handle the situation safely.

Logic flaws are particularly common in overly complicated systems that even the development team themselves do not fully understand. 
To avoid logic flaws, developers need to understand the application as a whole. 
This includes being aware of how different functions can be combined in unexpected ways. Developers working on large code bases may not have an intimate understanding of how all areas of the application work. 
Someone working on one component could make flawed assumptions about how another component works and, as a result, inadvertently introduce serious logic flaws. If the developers do not explicitly document any assumptions that are being made, it is easy for these kinds of vulnerabilities to creep into an application.
````

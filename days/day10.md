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
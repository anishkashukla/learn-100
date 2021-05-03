# day8

## What is Authentication ?

```text
Authentication is the process of verifying the identity of a given user or client. In other words, it involves 
making sure that they really are who they claim to be.
```

## What is the difference between authentication and authorization ?

```text
Authentication is the process of verifying that a user really is who they claim to be, whereas authorization involves verifying 
whether a user is allowed to do something.

In the context of a website or web application, authentication determines whether someone attempting to access the site with the 
username Anishka really is the same person who created the account.

Once Anishka is authenticated, her permissions determine whether or not she is authorized, for example, to access personal 
information about other users or perform actions such as deleting another user's account.
```

## How do authentication vulnerabilities arise ?

```text
Most vulnerabilities in authentication mechanisms arise in one of two ways:

* The authentication mechanisms are weak because they fail to adequately protect against brute-force attacks.
* Logic flaws or poor coding in the implementation allow the authentication mechanisms to be bypassed entirely by an attacker. 
This is sometimes referred to as "broken authentication".
```

## Impact of Vulnerable Authentication

```text
The impact of authentication vulnerabilities can be very severe. Once an attacker has either bypassed authentication or has 
brute-forced their way into another user's account, they have access to all the data and functionality that the compromised 
account has. If they are able to compromise a high-privileged account, such as a system administrator, they could take full 
control over the entire application and potentially gain access to internal infrastructure
```

## Vulnerabilities in Password Based Login

```text
For websites that adopt a password-based login process, users either register for an account themselves or they are 
assigned an account by an administrator. This account is associated with a unique username and a secret password, 
which the user enters in a login form to authenticate themselves.

In this scenario, the mere fact that they know the secret password is taken as sufficient proof of the user's identity. 
Consequently, the security of the website would be compromised if an attacker is able to either obtain or guess the login credentials of another user.

This can be achieved in a variety of ways, as we'll explore below.
```

### Brute-Force attacks

```text
A brute-force attack is when an attacker uses a system of trial and error in an attempt to guess valid user credentials. 
These attacks are typically automated using wordlists of usernames and passwords. Automating this process, especially using 
dedicated tools, potentially enables an attacker to make vast numbers of login attempts at high speed.

Brute-forcing is not always just a case of making completely random guesses at usernames and passwords. By also using basic 
logic or publicly available knowledge, attackers can fine-tune brute-force attacks to make much more educated guesses. This considerably 
increases the efficiency of such attacks. Websites that rely on password-based login as their sole method of authenticating users can be highly 
vulnerable if they do not implement sufficient brute-force protection.
```


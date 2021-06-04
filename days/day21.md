### What is authentication?
````
Authentication is the process of verifying the identity of a given user or client. 
In other words, it involves making sure that they really are who they claim to be. 
At least in part, websites are exposed to anyone who is connected to the internet by design. 
Therefore, robust authentication mechanisms are an integral aspect of effective web security.

There are three authentication factors into which different types of authentication can be categorized:

    * Something you know, such as a password or the answer to a security question.
      These are sometimes referred to as "knowledge factors".
    * Something you have, that is, a physical object like a mobile phone or security token.   These are sometimes referred to as "possession factors".
    * Something you are or do, for example, your biometrics or patterns of behavior. 
      These are sometimes referred to as "inherence factors".

Authentication mechanisms rely on a range of technologies to verify one or more of these factors.
````
### How do authentication vulnerabilities arise?
````
Broadly speaking, most vulnerabilities in authentication mechanisms arise in one of two ways:

The authentication mechanisms are weak because they fail to adequately protect against brute-force attacks.
Logic flaws or poor coding in the implementation allow the authentication mechanisms to be bypassed entirely by an attacker. 
This is sometimes referred to as "broken authentication".
In many areas of web development, logic flaws will simply cause the website to behave unexpectedly, which may or may not be a security issue. 
However, as authentication is so critical to security, the likelihood that flawed authentication logic exposes the website to security issues is clearly elevated.
````
### What is the impact of vulnerable authentication?
````
The impact of authentication vulnerabilities can be very severe. Once an attacker has either bypassed authentication or has brute-forced their way into another user's account, they have access to all the data and functionality that the compromised account has. 
If they are able to compromise a high-privileged account, such as a system administrator, they could take full control over the entire application and potentially gain access to internal infrastructure.

Even compromising a low-privileged account might still grant an attacker access to data that they otherwise shouldn't have, such as commercially sensitive business information. Even if the account does not have access to any sensitive data, it might still allow the attacker to access additional pages, which provide a further attack surface. 
Often, certain high-severity attacks will not be possible from publicly accessible pages, but they may be possible from an internal page.
````
### Vulnerabilities in authentication mechanisms
````
A website's authentication system usually consists of several distinct mechanisms where vulnerabilities may occur. Some vulnerabilities are broadly applicable across all of these contexts, whereas others are more specific to the functionality provided.

We will look more closely at some of the most common vulnerabilities in the following areas:

  * Vulnerabilities in password-based login 
  * Vulnerabilities in multi-factor authentication 
  * Vulnerabilities in other authentication mechanisms 
````
### Vulnerabilities in password-based login
````
For websites that adopt a password-based login process, users either register for an account themselves or they are assigned an account by an administrator. This account is associated with a unique username and a secret password, which the user enters in a login form to authenticate themselves.

In this scenario, the mere fact that they know the secret password is taken as sufficient proof of the user's identity. Consequently, the security of the website would be compromised if an attacker is able to either obtain or guess the login credentials of another user.

This can be achieved in a variety of ways, as we'll explore below.
````
### Brute-force attacks
````
A brute-force attack is when an attacker uses a system of trial and error in an attempt to guess valid user credentials. These attacks are typically automated using wordlists of usernames and passwords. Automating this process, especially using dedicated tools, potentially enables an attacker to make vast numbers of login attempts at high speed.

Brute-forcing is not always just a case of making completely random guesses at usernames and passwords. By also using basic logic or publicly available knowledge, attackers can fine-tune brute-force attacks to make much more educated guesses. This considerably increases the efficiency of such attacks. Websites that rely on password-based login as their sole method of authenticating users can be highly vulnerable if they do not implement sufficient brute-force protection.
````
### Brute-forcing usernames
````
Usernames are especially easy to guess if they conform to a recognizable pattern, such as an email address. For example, it is very common to see business logins in the format firstname.lastname@somecompany.com. However, even if there is no obvious pattern, sometimes even high-privileged accounts are created using predictable usernames, such as admin or administrator.

During auditing, check whether the website discloses potential usernames publicly. For example, are you able to access user profiles without logging in? Even if the actual content of the profiles is hidden, the name used in the profile is sometimes the same as the login username. You should also check HTTP responses to see if any email addresses are disclosed. Occasionally, responses contain emails addresses of high-privileged users like administrators and IT support.
````
### Brute-forcing passwords
````
Passwords can similarly be brute-forced, with the difficulty varying based on the strength of the password. Many websites adopt some form of password policy, which forces users to create high-entropy passwords that are, theoretically at least, harder to crack using brute-force alone. This typically involves enforcing passwords with:

  * A minimum number of characters
  * A mixture of lower and uppercase letters
  * At least one special character
However, while high-entropy passwords are difficult for computers alone to crack, we can use a basic knowledge of human behavior to exploit the vulnerabilities that users unwittingly introduce to this system. Rather than creating a strong password with a random combination of characters, users often take a password that they can remember and try to crowbar it into fitting the password policy. For example, if mypassword is not allowed, users may try something like Mypassword1! or Myp4$$w0rd instead.

In cases where the policy requires users to change their passwords on a regular basis, it is also common for users to just make minor, predictable changes to their preferred password. For example, Mypassword1! becomes Mypassword1? or Mypassword2!.

This knowledge of likely credentials and predictable patterns means that brute-force attacks can often be much more sophisticated, and therefore effective, than simply iterating through every possible combination of characters.
````
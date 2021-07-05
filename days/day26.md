### Access control vulnerabilities and privilege escalation
### What is access control ?
````
Access control (or authorization) is the application of constraints on who (or what) can perform attempted actions or access resources that they have requested. In the context of web applications, access control is dependent on authentication and session management:

Authentication identifies the user and confirms that they are who they say they are.
Session management identifies which subsequent HTTP requests are being made by that same user.
Access control determines whether the user is allowed to carry out the action that they are attempting to perform.
Broken access controls are a commonly encountered and often critical security vulnerability. Design and management of access controls is a complex and dynamic problem that applies business, organizational, and legal constraints to a technical implementation. Access control design decisions have to be made by humans, not technology, and the potential for errors is high.

From a user perspective, access controls can be divided into the following categories:

* Vertical access controls
* Horizontal access controls
* Context-dependent access controls
````
### Vertical access controls
````
Vertical access controls are mechanisms that restrict access to sensitive functionality that is not available to other types of users.

With vertical access controls, different types of users have access to different application functions. For example, an administrator might be able to modify or delete any user's account, while an ordinary user has no access to these actions. Vertical access controls can be more fine-grained implementations of security models designed to enforce business policies such as separation of duties and least privilege.
````
### Horizontal access controls
````
Horizontal access controls are mechanisms that restrict access to resources to the users who are specifically allowed to access those resources.

With horizontal access controls, different users have access to a subset of resources of the same type. For example, a banking application will allow a user to view transactions and make payments from their own accounts, but not the accounts of any other user.
````
### Context-dependent access controls
````
Context-dependent access controls restrict access to functionality and resources based upon the state of the application or the user's interaction with it.

Context-dependent access controls prevent a user performing actions in the wrong order. For example, a retail website might prevent users from modifying the contents of their shopping cart after they have made payment.
````
### Examples of broken access controls
````
Broken access control vulnerabilities exist when a user can in fact access some resource or perform some action that they are not supposed to be able to access.
````
### Vertical privilege escalation
````
If a user can gain access to functionality that they are not permitted to access then this is vertical privilege escalation. For example, if a non-administrative user can in fact gain access to an admin page where they can delete user accounts, then this is vertical privilege escalation.
````
### Unprotected functionality
````
At its most basic, vertical privilege escalation arises where an application does not enforce any protection over sensitive functionality. For example, administrative functions might be linked from an administrator's welcome page but not from a user's welcome page. However, a user might simply be able to access the administrative functions by browsing directly to the relevant admin URL.

For example, a website might host sensitive functionality at the following URL:

https://insecure-website.com/admin

This might in fact be accessible by any user, not only administrative users who have a link to the functionality in their user interface. In some cases, the administrative URL might be disclosed in other locations, such as the robots.txt file:

https://insecure-website.com/robots.txt

Even if the URL isn't disclosed anywhere, an attacker may be able to use a wordlist to brute-force the location of the sensitive functionality.

In some cases, sensitive functionality is not robustly protected but is concealed by giving it a less predictable URL: so called security by obscurity. Merely hiding sensitive functionality does not provide effective access control since users might still discover the obfuscated URL in various ways.

For example, consider an application that hosts administrative functions at the following URL:

https://insecure-website.com/administrator-panel-yb556

This might not be directly guessable by an attacker. However, the application might still leak the URL to users. For example, the URL might be disclosed in JavaScript that constructs the user interface based on the user's role:

<script>
var isAdmin = false;
if (isAdmin) {
  ...
  var adminPanelTag = document.createElement('a');
  adminPanelTag.setAttribute('https://insecure-website.com/administrator-panel-yb556');
  adminPanelTag.innerText = 'Admin panel';
  ...
}
</script>

This script adds a link to the user's UI if they are an admin user. However, the script containing the URL is visible to all users regardless of their role.
````
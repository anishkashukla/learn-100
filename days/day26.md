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
### Parameter-based access control methods
````
Some applications determine the user's access rights or role at login, and then store this information in a user-controllable location, such as a hidden field, cookie, or preset query string parameter. 
The application makes subsequent access control decisions based on the submitted value. For example:

https://insecure-website.com/login/home.jsp?admin=true
https://insecure-website.com/login/home.jsp?role=1

This approach is fundamentally insecure because a user can simply modify the value and gain access to functionality to which they are not authorized, such as administrative functions.
````
### Broken access control resulting from platform misconfiguration
````
Some applications enforce access controls at the platform layer by restricting access to specific URLs and HTTP methods based on the user's role. 
For example an application might configure rules like the following:

DENY: POST, /admin/deleteUser, managers

This rule denies access to the POST method on the URL /admin/deleteUser, for users in the managers group. 
Various things can go wrong in this situation, leading to access control bypasses.

Some application frameworks support various non-standard HTTP headers that can be used to override the URL in the original request, such as X-Original-URL and X-Rewrite-URL. 
If a web site uses rigorous front-end controls to restrict access based on URL, but the application allows the URL to be overridden via a request header, then it might be possible to bypass the access controls using a request like the following:

POST / HTTP/1.1
X-Original-URL: /admin/deleteUser

An alternative attack can arise in relation to the HTTP method used in the request. The front-end controls above restrict access based on the URL and HTTP method. 
Some web sites are tolerant of alternate HTTP request methods when performing an action. 
If an attacker can use the GET (or another) method to perform actions on a restricted URL, then they can circumvent the access control that is implemented at the platform layer.
````
### Horizontal privilege escalation
````
Horizontal privilege escalation arises when a user is able to gain access to resources belonging to another user, instead of their own resources of that type. 
For example, if an employee should only be able to access their own employment and payroll records, but can in fact also access the records of other employees, then this is horizontal privilege escalation.

Horizontal privilege escalation attacks may use similar types of exploit methods to vertical privilege escalation. 
For example, a user might ordinarily access their own account page using a URL like the following:

https://insecure-website.com/myaccount?id=123

Now, if an attacker modifies the id parameter value to that of another user, then the attacker might gain access to another user's account page, with associated data and functions.

In some applications, the exploitable parameter does not have a predictable value. For example, instead of an incrementing number, an application might use globally unique identifiers (GUIDs) to identify users. 
Here, an attacker might be unable to guess or predict the identifier for another user. However, the GUIDs belonging to other users might be disclosed elsewhere in the application where users are referenced, such as user messages or reviews.

In some cases, an application does detect when the user is not permitted to access the resource, and returns a redirect to the login page. 
However, the response containing the redirect might still include some sensitive data belonging to the targeted user, so the attack is still successful.
````
### Insecure direct object references
````
Insecure direct object references (IDOR) are a subcategory of access control vulnerabilities. IDOR arises when an application uses user-supplied input to access objects directly and an attacker can modify the input to obtain unauthorized access. 
It was popularized by its appearance in the OWASP 2007 Top Ten although it is just one example of many implementation mistakes that can lead to access controls being circumvented.
````
### Access control vulnerabilities in multi-step processes
````
Many web sites implement important functions over a series of steps. This is often done when a variety of inputs or options need to be captured, or when the user needs to review and confirm details before the action is performed. 
For example, administrative function to update user details might involve the following steps:

1. Load form containing details for a specific user.
2. Submit changes.
3. Review the changes and confirm.

Sometimes, a web site will implement rigorous access controls over some of these steps, but ignore others. 
For example, suppose access controls are correctly applied to the first and second steps, but not to the third step. 
Effectively, the web site assumes that a user will only reach step 3 if they have already completed the first steps, which are properly controlled. 
Here, an attacker can gain unauthorized access to the function by skipping the first two steps and directly submitting the request for the third step with the required parameters.
````
### Referer-based access control
````
Some websites base access controls on the Referer header submitted in the HTTP request. The Referer header is generally added to requests by browsers to indicate the page from which a request was initiated.

For example, suppose an application robustly enforces access control over the main administrative page at /admin, but for sub-pages such as /admin/deleteUser only inspects the Referer header. 
If the Referer header contains the main /admin URL, then the request is allowed.

In this situation, since the Referer header can be fully controlled by an attacker, they can forge direct requests to sensitive sub-pages, supplying the required Referer header, and so gain unauthorized access.
````
### Location-based access control
````
Some web sites enforce access controls over resources based on the user's geographical location. 
This can apply, for example, to banking applications or media services where state legislation or business restrictions apply. 
These access controls can often be circumvented by the use of web proxies, VPNs, or manipulation of client-side geolocation mechanisms.
````
### How to prevent access control vulnerabilities
````
Access control vulnerabilities can generally be prevented by taking a defense-in-depth approach and applying the following principles:

* Never rely on obfuscation alone for access control.
* Unless a resource is intended to be publicly accessible, deny access by default.
* Wherever possible, use a single application-wide mechanism for enforcing access controls.
* At the code level, make it mandatory for developers to declare the access that is allowed for each resource, and deny access by default.
* Thoroughly audit and test access controls to ensure they are working as designed.
````
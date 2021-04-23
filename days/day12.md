### What is SSRF ?
````
Server-side request forgery (also known as SSRF) is a web security vulnerability that allows an attacker to induce the server-side 
application to make HTTP requests to an arbitrary domain of the attacker's choosing.

In typical SSRF examples, the attacker might cause the server to make a connection back to itself, or to other web-based services within the organization's infrastructure, or to external third-party systems.
````
### What is the impact of SSRF attacks?
````
A successful SSRF attack can often result in unauthorized actions or access to data within the organization, either in the vulnerable application itself or on other back-end systems that the application can communicate with. 
In some situations, the SSRF vulnerability might allow an attacker to perform arbitrary command execution.

An SSRF exploit that causes connections to external third-party systems might result in malicious onward attacks that appear to originate from the organization hosting the vulnerable application, leading to potential legal liabilities and reputational damage.
````
### Common SSRF attacks
````
SSRF attacks often exploit trust relationships to escalate an attack from the vulnerable application and perform unauthorized actions. These trust relationships might exist in relation to the server itself, or in relation to other back-end systems within the same organization.
````
### SSRF attacks against the server itself
````
In an SSRF attack against the server itself, the attacker induces the application to make an HTTP request back to the server that is hosting the application, via its loopback network interface. 
This will typically involve supplying a URL with a hostname like 127.0.0.1 (a reserved IP address that points to the loopback adapter) or localhost (a commonly used name for the same adapter).

For example, consider a shopping application that lets the user view whether an item is in stock in a particular store. 
To provide the stock information, the application must query various back-end REST APIs, dependent on the product and store in question. 
The function is implemented by passing the URL to the relevant back-end API endpoint via a front-end HTTP request. So when a user views the stock status for an item, their browser makes a request like this:

POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1

This causes the server to make a request to the specified URL, retrieve the stock status, and return this to the user.

In this situation, an attacker can modify the request to specify a URL local to the server itself. For example:

POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://localhost/admin

Here, the server will fetch the contents of the /admin URL and return it to the user.

Now of course, the attacker could just visit the /admin URL directly. But the administrative functionality is ordinarily accessible only to suitable authenticated users. So an attacker who simply visits the URL directly won't see anything of interest. 
However, when the request to the /admin URL comes from the local machine itself, the normal access controls are bypassed. 
The application grants full access to the administrative functionality, because the request appears to originate from a trusted location.

Why do applications behave in this way, and implicitly trust requests that come from the local machine? This can arise for various reasons:

The access control check might be implemented in a different component that sits in front of the application server. When a connection is made back to the server itself, the check is bypassed.
For disaster recovery purposes, the application might allow administrative access without logging in, to any user coming from the local machine. 
This provides a way for an administrator to recover the system in the event they lose their credentials. The assumption here is that only a fully trusted user would be coming directly from the server itself.
The administrative interface might be listening on a different port number than the main application, and so might not be reachable directly by users.

These kind of trust relationships, where requests originating from the local machine are handled differently than ordinary requests, is often what makes SSRF into a critical vulnerability.
````
### SSRF attacks against other back-end systems
````
Another type of trust relationship that often arises with server-side request forgery is where the application server is able to interact with other back-end systems that are not directly reachable by users. 
These systems often have non-routable private IP addresses. Since the back-end systems are normally protected by the network topology, they often have a weaker security posture. 
In many cases, internal back-end systems contain sensitive functionality that can be accessed without authentication by anyone who is able to interact with the systems.

In the preceding example, suppose there is an administrative interface at the back-end URL https://192.168.0.68/admin. 
Here, an attacker can exploit the SSRF vulnerability to access the administrative interface by submitting the following request:

POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://192.168.0.68/admin
````
### Circumventing common SSRF defenses
````
It is common to see applications containing SSRF behavior together with defenses aimed at preventing malicious exploitation. Often, these defenses can be circumvented.

SSRF with blacklist-based input filters
Some applications block input containing hostnames like 127.0.0.1 and localhost, or sensitive URLs like /admin. In this situation, you can often circumvent the filter using various techniques:

* Using an alternative IP representation of 127.0.0.1, such as 2130706433, 017700000001, or 127.1.
* Registering your own domain name that resolves to 127.0.0.1. You can use spoofed.burpcollaborator.net for this purpose.
* Obfuscating blocked strings using URL encoding or case variation.
````
### SSRF with whitelist-based input filters
````
Some applications only allow input that matches, begins with, or contains, a whitelist of permitted values. 
In this situation, you can sometimes circumvent the filter by exploiting inconsistencies in URL parsing.

The URL specification contains a number of features that are liable to be overlooked when implementing ad hoc parsing and validation of URLs:

You can embed credentials in a URL before the hostname, using the @ character. For example: https://expected-host@evil-host.
You can use the # character to indicate a URL fragment. For example: https://evil-host#expected-host.
You can leverage the DNS naming hierarchy to place required input into a fully-qualified DNS name that you control. For example: https://expected-host.evil-host.
You can URL-encode characters to confuse the URL-parsing code. This is particularly useful if the code that implements the filter handles URL-encoded characters differently than the code that performs the back-end HTTP request.
You can use combinations of these techniques together.
````
### Bypassing SSRF filters via open redirection

````
It is sometimes possible to circumvent any kind of filter-based defenses by exploiting an open redirection vulnerability.

In the preceding SSRF example, suppose the user-submitted URL is strictly validated to prevent malicious exploitation of the SSRF behavior. However, the application whose URLs are allowed contains an open redirection vulnerability. Provided the API used to make the back-end HTTP request supports redirections, you can construct a URL that satisfies the filter and results in a redirected request to the desired back-end target.

For example, suppose the application contains an open redirection vulnerability in which the following URL:

/product/nextProduct?currentProductId=6&path=http://evil-user.net

returns a redirection to:

http://evil-user.net

You can leverage the open redirection vulnerability to bypass the URL filter, and exploit the SSRF vulnerability as follows:

POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin

This SSRF exploit works because the application first validates that the supplied stockAPI URL is on an allowed domain, which it is. The application then requests the supplied URL, which triggers the open redirection. It follows the redirection, and makes a request to the internal URL of the attacker's choosing
````
### Blind SSRF vulnerabilities
````
Blind SSRF vulnerabilities arise when an application can be induced to issue a back-end HTTP request to a supplied URL, but the response from the back-end request is not returned in the application's front-end response.

Blind SSRF is generally harder to exploit but can sometimes lead to full remote code execution on the server or other back-end components.
````
### What is the impact of blind SSRF vulnerabilities?
````
The impact of blind SSRF vulnerabilities is often lower than fully informed SSRF vulnerabilities because of their one-way nature. They cannot be trivially exploited to retrieve sensitive data from back-end systems, although in some situations they can be exploited to achieve full remote code execution.
````
### Finding hidden attack surface for SSRF vulnerabilities
````
Many server-side request forgery vulnerabilities are relatively easy to spot, because the application's normal traffic involves request parameters containing full URLs. 
Other examples of SSRF are harder to locate.
````
### Partial URLs in requests
````
Sometimes, an application places only a hostname or part of a URL path into request parameters. 
The value submitted is then incorporated server-side into a full URL that is requested. 
If the value is readily recognized as a hostname or URL path, then the potential attack surface might be obvious. 
However, exploitability as full SSRF might be limited since you do not control the entire URL that gets requested.
````
### URLs within data formats
````
Some applications transmit data in formats whose specification allows the inclusion of URLs that might get requested by the data parser for the format. 
An obvious example of this is the XML data format, which has been widely used in web applications to transmit structured data from the client to the server. 
When an application accepts data in XML format and parses it, it might be vulnerable to XXE injection, and in turn be vulnerable to SSRF via XXE. 
````
### SSRF via the Referer header
````
Some applications employ server-side analytics software that tracks visitors. This software often logs the Referer header in requests, since this is of particular interest for tracking incoming links. 
Often the analytics software will actually visit any third-party URL that appears in the Referer header. 
This is typically done to analyze the contents of referring sites, including the anchor text that is used in the incoming links. 
As a result, the Referer header often represents fruitful attack surface for SSRF vulnerabilities. See Blind SSRF vulnerabilities for examples of vulnerabilities involving the Referer header.
````
### Blind SSRF vulnerabilities
````
Blind SSRF vulnerabilities arise when an application can be induced to issue a back-end HTTP request to a supplied URL, but the response from the back-end request is not returned in the application's front-end response.

Blind SSRF is generally harder to exploit but can sometimes lead to full remote code execution on the server or other back-end components.
````
### Finding hidden attack surface for SSRF vulnerabilities
````
Many server-side request forgery vulnerabilities are relatively easy to spot, because the application's normal traffic involves request parameters containing full URLs. 
Other examples of SSRF are harder to locate.
````
### Partial URLs in requests
````
Sometimes, an application places only a hostname or part of a URL path into request parameters. 
The value submitted is then incorporated server-side into a full URL that is requested. 
If the value is readily recognized as a hostname or URL path, then the potential attack surface might be obvious. 
However, exploitability as full SSRF might be limited since you do not control the entire URL that gets requested.
````
### URLs within data formats
````
Some applications transmit data in formats whose specification allows the inclusion of URLs that might get requested by the data parser for the format. 
An obvious example of this is the XML data format, which has been widely used in web applications to transmit structured data from the client to the server. 
When an application accepts data in XML format and parses it, it might be vulnerable to XXE injection, and in turn be vulnerable to SSRF via XXE. 
````
### SSRF via the Referer header
````
Some applications employ server-side analytics software that tracks visitors. This software often logs the Referer header in requests, since this is of particular interest for tracking incoming links. 
Often the analytics software will actually visit any third-party URL that appears in the Referer header. 
This is typically done to analyze the contents of referring sites, including the anchor text that is used in the incoming links. 
As a result, the Referer header often represents fruitful attack surface for SSRF vulnerabilities. 
````
### What is XML external entity injection?
````
XML external entity injection (also known as XXE) is a web security vulnerability that allows an attacker to interfere with an application's processing of XML data. It often allows an attacker to view files on the application server filesystem, and to interact with any back-end or external systems that the application itself can access.

In some situations, an attacker can escalate an XXE attack to compromise the underlying server or other back-end infrastructure, by leveraging the XXE vulnerability to perform server-side request forgery (SSRF) attacks.
````
### How do XXE vulnerabilities arise?
````
Some applications use the XML format to transmit data between the browser and the server. Applications that do this virtually always use a standard library or platform API to process the XML data on the server. XXE vulnerabilities arise because the XML specification contains various potentially dangerous features, and standard parsers support these features even if they are not normally used by the application.

XML external entities are a type of custom XML entity whose defined values are loaded from outside of the DTD in which they are declared. External entities are particularly interesting from a security perspective because they allow an entity to be defined based on the contents of a file path or URL.
````
### What are the types of XXE attacks?
````
There are various types of XXE attacks:

* Exploiting XXE to retrieve files, where an external entity is defined containing the contents of a file, and returned in the application's response.
* Exploiting XXE to perform SSRF attacks, where an external entity is defined based on a URL to a back-end system.
* Exploiting blind XXE exfiltrate data out-of-band, where sensitive data is transmitted from the application server to a system that the attacker controls.
* Exploiting blind XXE to retrieve data via error messages, where the attacker can trigger a parsing error message containing sensitive data.
````
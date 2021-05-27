### Web cache deception

### How does web cache deception work?
````
When your browser makes a request to a website, your connection usually goes through content delivery networks (CDN).

CDNs have edge servers scattered across the world, which store cache local copies of web content to provide faster access to the users in their vicinities, while reducing the load on web servers.

As a rule of thumb, cached items must not contain private or sensitive information. Caching servers only store public, static content – such as images, CSS files, and PDF documents – that are not user-specific.

Requests for all dynamic, user-specific content get routed to the main servers of the website or service a user is interacting with.

This mechanism has been mainly put in place for security reasons because, unlike application servers, caching servers have no safeguards to authenticate users and prevent access to unauthorized data.

But, as Gil showed in 2017, caching servers can be deceived in order to store local copies of sensitive data and serve them to other users via path confusion.

“When an internet user communicates with a web application, the traffic may be proxied and processed by any number of caching technologies on its way, including multiple CDNs and centralized server-side caches, before it finally reaches the origin web server,” Kaan Onarlioglu, security intelligence architect at Akamai and co-author of the paper, told The Daily Swig.

“WCD results from a disagreement between these components on the cacheability of a given object. This disagreement can then be exploited to trick a web cache into storing non-cacheable objects.”

For instance, consider a dynamic, non-cacheable page that contains sensitive user account information, say /account.php.

To get the CDN edge server to store a cached copy of the page, a malicious user might add a suffix to the path to make it look like a static, public asset – /account.php/nonexistent.jpg.

If the victim visits the attacker’s website, or a website that the attacker controls, the attacker can then manipulate the victim’s browser into loading a specific URL on the target website, which will cause their account data to be cached on the CDN.

After that, all the attacker has to do is send a GET request for the forged URL to the edge server and receive a copy of the cached data.
````


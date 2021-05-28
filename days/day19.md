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
### A substantial threat
````
Taking from the list of Alexa’s Top 5,000 websites, the researchers identified 295 websites and 125,000 domains that could be potential targets for WCD attacks. 16 of the 295 websites (5.4%) were later found to have WCD vulnerabilities.

“We observe that the distribution of vulnerable sites is roughly proportional to the number of sites crawled; that is, our data does not suggest that the incidence of WCD vulnerabilities is correlated with site popularity,” the researchers said.

While WCD is theoretically possible on all kinds of caching servers, all instances of vulnerable pages observed in the study are served over a CDN.

“Today, web caches are considered essential Internet infrastructure,” Onarlioglu says. “In particular, CDN technologies (i.e., massively distributed networks of caching proxies) have become ubiquitous.

“When this large potential attack surface is combined with the complexity of identifying and fixing vulnerabilities, site operators are facing a significant challenge in staying ahead of attackers.”

Another notable finding is that, in some cases, site administrators configured their web caches to disregard caching directives response headers and store non-cacheable data.

“The fact that WCD vulnerabilities are relatively easy to exploit leads us to conclude that these vulnerabilities are serious and that this class of vulnerability deserves attention from both site administrators and the security community,” the researchers said.

Web cache deception was ranked second in the 2017 Top 10 Web Hacking Techniques, an annual list compiled by researchers at PortSwigger.
````

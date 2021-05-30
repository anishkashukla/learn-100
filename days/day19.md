### Web cache deception

### About caching
````
Websites often use web cache functionality in order to reduce web server latency and to
provide users with their requested content faster. 
Instead of letting the web server deal with each request over and over, the caching mechanism stores those application files that are frequently retrieved.

Files that are commonly cached are static, public files: style sheets (css), scripts (js), text files (txt), images (png, bmp, gif), and so on. 
These files don't usually contain any sensitive information. As can be found in various best practices articles about web cache configuration, it's recommended to cache all static files that are meant to be public, and
disregard their HTTP caching headers.

There are several ways to implement caching. For example, caching is performed on
browsers: the file is cached, and for a certain period of time, the browser won't ask the web server for the cached file again. This type of caching is NOT relevant for the web cache
deception attack.

Another way to implement caching, that is relevant for this attack, is over a server that
stands between the client and the web server and functions as a caching mechanism. This
type of mechanism can have various forms:

    * CDN (Content Delivery Network) - A distributed network of proxies whose purpose is
        to serve content quickly. The client will be served from a group of proxies, preferably the closest one to him.
    * Load balancer - In addition to their job of balancing the traffic between more than
        one server, load balancers can also cache content in order to reduce the servers' latency.
    * Reverse proxy - A proxy server that retrieves resources from the web server on
        behalf of the client, and can cache some of the application's content.
````
### Servers' reactions
````
The web cache deception attack counts on similar browsers' and web servers' reactions, in
the same way as the RPO attack, explained in two blogs: The Spanner1 and XSS Jigsaw2
.
What happens when a user accesses a URL like
http://www.example.com/home.php/nonexistent.css, where home.php is an actual page,
and nonexistent.css doesn't exist?
In this case, the browser sends a GET request to that URL. The interesting thing to look at is how the web server interprets this request. Depending on the server's technology and
configuration, the web server might return a 200 OK response with the content of the
home.php page, meaning the URL stays the same.
The HTTP response headers will match the home.php page: same caching headers and same
content type.
````
### How does web cache deception work?

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

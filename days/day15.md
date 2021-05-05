### What is web cache poisoning ?
````
Web cache poisoning is an advanced technique whereby an attacker exploits the behavior of a web server and cache so that a harmful HTTP response is served to other users.

Fundamentally, web cache poisoning involves two phases. 
First, the attacker must work out how to elicit a response from the back-end server that inadvertently contains some kind of dangerous payload. 
Once successful, they need to make sure that their response is cached and subsequently served to the intended victims.

A poisoned web cache can potentially be a devastating means of distributing numerous different attacks, exploiting vulnerabilities such as XSS, JavaScript injection, open redirection, and so on
````
### How does a web cache work ?
````
To understand how web cache poisoning vulnerabilities arise, it is important to have a basic understanding of how web caches work.

If a server had to send a new response to every single HTTP request separately, this would likely overload the server, resulting in latency issues and a poor user experience, especially during busy periods. Caching is primarily a means of reducing such issues.

The cache sits between the server and the user, where it saves (caches) the responses to particular requests, usually for a fixed amount of time. 
If another user then sends an equivalent request, the cache simply serves a copy of the cached response directly to the user, without any interaction from the back-end. 
This greatly eases the load on the server by reducing the number of duplicate requests it has to handle.
````
#### Cache keys
````
When the cache receives an HTTP request, it first has to determine whether there is a cached response that it can serve directly, or whether it has to forward the request for handling by the back-end server. 
Caches identify equivalent requests by comparing a predefined subset of the request's components, known collectively as the "cache key". 
Typically, this would contain the request line and Host header. Components of the request that are not included in the cache key are said to be "unkeyed".

If the cache key of an incoming request matches the key of a previous request, then the cache considers them to be equivalent. 
As a result, it will serve a copy of the cached response that was generated for the original request. 
This applies to all subsequent requests with the matching cache key, until the cached response expires.

Crucially, the other components of the request are ignored altogether by the cache. 
````
### What is the impact of a web cache poisoning attack?
````
The impact of web cache poisoning is heavily dependent on two key factors:

    * What exactly the attacker can successfully get cached
        As the poisoned cache is more a means of distribution than a standalone attack, the impact of web cache poisoning is inextricably linked to how harmful the injected payload is. As with most kinds of attack, web cache poisoning can also be used in combination with other attacks to escalate the potential impact even further.
    * The amount of traffic on the affected page
        The poisoned response will only be served to users who visit the affected page while the cache is poisoned. As a result, the impact can range from non-existent to massive depending on whether the page is popular or not. If an attacker managed to poison a cached response on the home page of a major website, for example, the attack could affect thousands of users without any subsequent interaction from the attacker.

Note that the duration of a cache entry doesn't necessarily affect the impact of web cache poisoning. An attack can usually be scripted in such a way that it re-poisons the cache indefinitely
````
### Constructing a web cache poisoning attack
````
Generally speaking, constructing a basic web cache poisoning attack involves the following steps:

    1. Identify and evaluate unkeyed inputs
    2. Elicit a harmful response from the back-end server
    3. Get the response cached
````
#### Identify and evaluate unkeyed inputs
````
Any web cache poisoning attack relies on manipulation of unkeyed inputs, such as headers. Web caches ignore unkeyed inputs when deciding whether to serve a cached response to the user. 
This behavior means that you can use them to inject your payload and elicit a "poisoned" response which, if cached, will be served to all users whose requests have the matching cache key. 
Therefore, the first step when constructing a web cache poisoning attack is identifying unkeyed inputs that are supported by the server.

You can identify unkeyed inputs manually by adding random inputs to requests and observing whether or not they have an effect on the response. 
This can be obvious, such as reflecting the input in the response directly, or triggering an entirely different response. 
However, sometimes the effects are more subtle and require a bit of detective work to figure out. 
You can use tools such as Burp Comparer to compare the response with and without the injected input, but this still involves a significant amount of manual effort.

Param Miner

Fortunately, you can automate the process of identifying unkeyed inputs by adding the Param Miner extension to Burp from the BApp store. 
To use Param Miner, you simply right-click on a request that you want to investigate and click "Guess headers". 
Param Miner then runs in the background, sending requests containing different inputs from its extensive, built-in list of headers. 
If a request containing one of its injected inputs has an effect on the response, Param Miner logs this in Burp, either in the "Issues" pane if you are using Burp Suite Professional, or in the "Output" tab of the extension ("Extender" > "Extensions" > "Param Miner" > "Output") if you are using Burp Suite Community Edition.

For example, in the following screenshot, Param Miner found an unkeyed header X-Forwarded-Host on the home page of the website:

Caution: When testing for unkeyed inputs on a live website, there is a risk of inadvertently causing the cache to serve your generated responses to real users. 
Therefore, it is important to make sure that your requests all have a unique cache key so that they will only be served to you. 
To do this, you can manually add a cache buster (such as a unique parameter) to the request line each time you make a request. 
Alternatively, if you are using Param Miner, there are options for automatically adding a cache buster to every request.
````
#### Elicit a harmful response from the back-end server
````
Once you have identified an unkeyed input, the next step is to evaluate exactly how the website processes it. 
Understanding this is essential to successfully eliciting a harmful response. 

If an input is reflected in the response from the server without being properly sanitized, or is used to dynamically generate other data, then this is a potential entry point for web cache poisoning.
````
#### Get the response cached
````
Manipulating inputs to elicit a harmful response is half the battle, but it doesn't achieve much unless you can cause the response to be cached, which can sometimes be tricky.

Whether or not a response gets cached can depend on all kinds of factors, such as the file extension, content type, route, status code, and response headers. 
You will probably need to devote some time to simply playing around with requests on different pages and studying how the cache behaves. 
Once you work out how to get a response cached that contains your malicious input, you are ready to deliver the exploit to potential victims.
````
### Exploiting web cache poisoning vulnerabilities
````
This basic process can be used to discover and exploit a variety of different web cache poisoning vulnerabilities.

In some cases, web cache poisoning vulnerabilities arise due to general flaws in the design of caches. 
Other times, the way in which a cache is implemented by a specific website can introduce unexpected quirks that can be exploited.
````
### Exploiting cache design flaws
````
Websites are vulnerable to web cache poisoning if they handle unkeyed input in an unsafe way and allow the subsequent HTTP responses to be cached. 
This vulnerability can be used as a delivery method for a variety of different attacks.

Using web cache poisoning to deliver an XSS attack
Perhaps the simplest web cache poisoning vulnerability to exploit is when unkeyed input is reflected in a cacheable response without proper sanitization.

For example, consider the following request and response:

GET /en?region=uk HTTP/1.1
Host: innocent-website.com
X-Forwarded-Host: innocent-website.co.uk

HTTP/1.1 200 OK
Cache-Control: public
<meta property="og:image" content="https://innocent-website.co.uk/cms/social.png" />

Here, the value of the X-Forwarded-Host header is being used to dynamically generate an Open Graph image URL, which is then reflected in the response. 
Crucially for web cache poisoning, the X-Forwarded-Host header is often unkeyed. 
In this example, the cache can potentially be poisoned with a response containing a simple XSS payload:

GET /en?region=uk HTTP/1.1
Host: innocent-website.com
X-Forwarded-Host: a."><script>alert(1)</script>"

HTTP/1.1 200 OK
Cache-Control: public
<meta property="og:image" content="https://a."><script>alert(1)</script>"/cms/social.png" />

If this response was cached, all users who accessed /en?region=uk would be served this XSS payload. 
This example simply causes an alert to appear in the victim's browser, but a real attack could potentially steal passwords and hijack user accounts.
````
### Using web cache poisoning to exploit unsafe handling of resource imports
````
Some websites use unkeyed headers to dynamically generate URLs for importing resources, such as externally hosted JavaScript files. 
In this case, if an attacker changes the value of the appropriate header to a domain that they control, they could potentially manipulate the URL to point to their own malicious JavaScript file instead.

If the response containing this malicious URL is cached, the attacker's JavaScript file would be imported and executed in the browser session of any user whose request has a matching cache key.

GET / HTTP/1.1
Host: innocent-website.com
X-Forwarded-Host: evil-user.net
User-Agent: Mozilla/5.0 Firefox/57.0

HTTP/1.1 200 OK
<script src="https://evil-user.net/static/analytics.js"></script>
````
### Using web cache poisoning to exploit cookie-handling vulnerabilities
````
Cookies are often used to dynamically generate content in a response. A common example might be a cookie that indicates the user's preferred language, which is then used to load the corresponding version of the page:

GET /blog/post.php?mobile=1 HTTP/1.1
Host: innocent-website.com
User-Agent: Mozilla/5.0 Firefox/57.0
Cookie: language=pl;
Connection: close

In this example, the Polish version of a blog post is being requested. Notice that the information about which language version to serve is only contained in the Cookie header. Let's suppose that the cache key contains the request line and the Host header, but not the Cookie header. 
In this case, if the response to this request is cached, then all subsequent users who tried to access this blog post would receive the Polish version as well, regardless of which language they actually selected.

This flawed handling of cookies by the cache can also be exploited using web cache poisoning techniques. In practice, however, this vector is relatively rare in comparison to header-based cache poisoning. 
When cookie-based cache poisoning vulnerabilities exist, they tend to be identified and resolved quickly because legitimate users have accidentally poisoned the cache.
````
### Using multiple headers to exploit web cache poisoning vulnerabilities
````
Some websites are vulnerable to simple web cache poisoning exploits, as demonstrated above. However, others require more sophisticated attacks and only become vulnerable when an attacker is able to craft a request that manipulates multiple unkeyed inputs.

For example, let's say a website requires secure communication using HTTPS. To enforce this, if a request that uses another protocol is received, the website dynamically generates a redirect to itself that does use HTTPS:

GET /random HTTP/1.1
Host: innocent-site.com
X-Forwarded-Proto: http

HTTP/1.1 301 moved permanently
Location: https://innocent-site.com/random

By itself, this behavior isn't necessarily vulnerable. However, by combining this with what we learned earlier about vulnerabilities in dynamically generated URLs, an attacker could potentially exploit this behavior to generate a cacheable response that redirects users to a malicious URL.
````
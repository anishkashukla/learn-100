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
### Exploiting responses that expose too much information
````
Sometimes websites make themselves more vulnerable to web cache poisoning by giving away too much information about themselves and their behavior.
````
### Cache-control directives
````
One of the challenges when constructing a web cache poisoning attack is ensuring that the harmful response gets cached. This can involve a lot of manual trial and error to study how the cache behaves. However, sometimes responses explicitly reveal some of the information an attacker needs to successfully poison the cache.

One such example is when responses contain information about how often the cache is purged or how old the currently cached response is:

HTTP/1.1 200 OK
Via: 1.1 varnish-v4
Age: 174
Cache-Control: public, max-age=1800

Although this doesn't directly lead to web cache poisoning vulnerabilities, it does save a potential attacker some of the manual effort involved because they know exactly when to send their payload to ensure it gets cached.

This knowledge also enables far more subtle attacks. Rather than bombarding the back-end server with requests until one sticks, which could raise suspicions, the attacker can carefully time a single malicious request to poison the cache.
````
### Vary header
````
The rudimentary way that the Vary header is often used can also provide attackers with a helping hand. 
The Vary header specifies a list of additional headers that should be treated as part of the cache key even if they are normally unkeyed. 
It is commonly used to specify that the User-Agent header is keyed, for example, so that if the mobile version of a website is cached, this won't be served to non-mobile users by mistake.

This information can also be used to construct a multi-step attack to target a specific subset of users. 
For example, if the attacker knows that the User-Agent header is part of the cache key, by first identifying the user agent of the intended victims, they could tailor the attack so that only users with that user agent are affected. 
Alternatively, they could work out which user agent was most commonly used to access the site, and tailor the attack to affect the maximum number of users that way.
````
### Using web cache poisoning to exploit DOM-based vulnerabilities
````
If the website unsafely uses unkeyed headers to import files, this can potentially be exploited by an attacker to import a malicious file instead. However, this applies to more than just JavaScript files.

Many websites use JavaScript to fetch and process additional data from the back-end. 
If a script handles data from the server in an unsafe way, this can potentially lead to all kinds of DOM-based vulnerabilities.

For example, an attacker could poison the cache with a response that imports a JSON file containing the following payload:

{"someProperty" : "<svg onload=alert(1)>"}

If the website then passes the value of this property into a sink that supports dynamic code execution, the payload would be executed in the context of the victim's browser session.

If you use web cache poisoning to make a website load malicious JSON data from your server, you may need to grant the website access to the JSON using CORS:

HTTP/1.1 200 OK
Content-Type: application/json
Access-Control-Allow-Origin: *

{
    "malicious json" : "malicious json"
}
````
### Exploiting cache implementation flaws
````
In this, we'll see how you can access a much greater attack surface for web cache poisoning by exploiting quirks in specific implementations of caching systems. 
In particular, we'll look at why flaws in how cache keys are generated can sometimes leave websites vulnerable to cache poisoning via separate vulnerabilities that are traditionally considered unexploitable. 
We'll also show how you can take classic techniques even further to potentially poison application-level caches, often with devastating results.
````
### Cache key flaws
````
Generally speaking, websites take most of their input from the URL path and the query string. 
As a result, this is a well-trodden attack surface for various hacking techniques. However, as the request line is usually part of the cache key, these inputs have traditionally not been considered suitable for cache poisoning. 
Any payload injected via keyed inputs would act as a cache buster, meaning your poisoned cache entry would almost certainly never be served to any other users.

On closer inspection, however, the behavior of individual caching systems is not always as you would expect. 
In practice, many websites and CDNs perform various transformations on keyed components when they are saved in the cache key. This can include:

    * Excluding the query string
    * Filtering out specific query parameters
    * Normalizing input in keyed components

These transformations may introduce a few unexpected quirks. These are primarily based around discrepancies between the data that is written to the cache key and the data that is passed into the application code, even though it all stems from the same input. 
These cache key flaws can be exploited to poison the cache via inputs that may initially appear unusable.

In the case of fully integrated, application-level caches, these quirks can be even more extreme. 
In fact, internal caches can be so unpredictable that it is sometimes difficult to test them at all without inadvertently poisoning the cache for live users.
````
### Cache probing methodology
````
The methodology of probing for cache implementation flaws differs slightly from the classic web cache poisoning methodology. 
These newer techniques rely on flaws in the specific implementation and configuration of the cache, which may vary dramatically from site to site. 
This means that you need a deeper understanding of the target cache and its behavior.

The methodology involves the following steps:

    Identify a suitable cache oracle
    Probe key handling
    Identify an exploitable gadget
````    
### Identify a suitable cache oracle
````
The first step is to identify a suitable "cache oracle" that you can use for testing. A cache oracle is simply a page or endpoint that provides feedback about the cache's behavior. 
This needs to be cacheable and must indicate in some way whether you received a cached response or a response directly from the server. This feedback could take various forms, such as:

    * An HTTP header that explicitly tells you whether you got a cache hit
    * Observable changes to dynamic content
    * Distinct response times

Ideally, the cache oracle will also reflect the entire URL and at least one query parameter in the response. 
This will make it easier to notice parsing discrepancies between the cache and the application, which will be useful for constructing different exploits later.

If you can identify that a specific third-party cache is being used, you can also consult the corresponding documentation. 
This may contain information about how the default cache key is constructed. You might even stumble across some handy tips and tricks, such as features that allow you to see the cache key directly. 
For example, Akamai-based websites may support the header Pragma: akamai-x-get-cache-key, which you can use to display the cache key in the response headers:

GET /?param=1 HTTP/1.1
Host: innocent-website.com
Pragma: akamai-x-get-cache-key

HTTP/1.1 200 OK
X-Cache-Key: innocent-website.com/?param=1
````
### Probe key handling
````
Now let's investigate whether the cache performs any additional processing of your input when generating the cache key. 
You are looking for an additional attack surface hidden within seemingly keyed components.

You should specifically look at any transformation that is taking place. 
Is anything being excluded from a keyed component when it is added to the cache key?
 Common examples are excluding specific query parameters, or even the entire query string, and removing the port from the Host header.

If you're fortunate enough to have direct access to the cache key, you can simply compare the key after injecting different inputs. 
Otherwise, you can use your understanding of the cache oracle to infer whether you received the correct cached response. 
For each case that you want to test, you send two similar requests and compare the responses.

Let's say that our hypothetical cache oracle is the target website's home page. 
This automatically redirects users to a region-specific page. It uses the Host header to dynamically generate the Location header in the response:

GET / HTTP/1.1
Host: vulnerable-website.com

HTTP/1.1 302 Moved Permanently
Location: https://vulnerable-website.com/en
Cache-Status: miss

To test whether the port is excluded from the cache key, we first need to request an arbitrary port and make sure that we receive a fresh response from the server that reflects this input:

GET / HTTP/1.1
Host: vulnerable-website.com:1337

HTTP/1.1 302 Moved Permanently
Location: https://vulnerable-website.com:1337/en
Cache-Status: miss

Next, we'll send another request, but this time we won't specify a port:

GET / HTTP/1.1
Host: vulnerable-website.com

HTTP/1.1 302 Moved Permanently
Location: https://vulnerable-website.com:1337/en
Cache-Status: hit

As you can see, we have been served our cached response even though the Host header in the request does not specify a port. 
This proves that the port is being excluded from the cache key. Importantly, the full header is still passed into the application code and reflected in the response.

In short, although the Host header is keyed, the way it is transformed by the cache allows us to pass a payload into the application while still preserving a "normal" cache key that will be mapped to other users' requests. 
This kind of behavior is the key concept behind all of the exploits that we'll discuss in this section.

You can use a similar approach to investigate any other processing of your input by the cache. Is your input being normalized in any way? How is your input stored? Do you notice any anomalies? We'll cover how to answer these questions later using concrete examples.
````
### Identify an exploitable gadget
````
By now, you should have a relatively solid understanding of how the target website's cache behaves and might have found some interesting flaws in the way the cache key is constructed. 
The final step is to identify a suitable gadget that you can chain with this cache key flaw. 
This is an important skill because the severity of any web cache poisoning attack is heavily dependent on the gadget you are able to exploit.

These gadgets will often be classic client-side vulnerabilities, such as reflected XSS and open redirects. 
By combining these with web cache poisoning, you can massively escalate the severity of these attacks, turning a reflected vulnerability into a stored one. 
Instead of having to induce a victim to visit a specially crafted URL, your payload will automatically be served to anybody who visits the ordinary, perfectly legitimate URL.

Perhaps even more interestingly, these techniques enable you to exploit a number of unclassified vulnerabilities that are often dismissed as "unexploitable" and left unpatched. 
This includes the use of dynamic content in resource files, and exploits requiring malformed requests that a browser would never send.
````
### Exploiting cache key flaws
````
    * Unkeyed port
    * Unkeyed query string 
    * Unkeyed query parameters 
    * Cache parameter cloaking 
    * Normalized cache keys 
    * Cache key injection 
    * Internal cache poisoning 
````
### Unkeyed port
````
The Host header is often part of the cache key and, as such, initially seems an unlikely candidate for injecting any kind of payload. 
However, some caching systems will parse the header and exclude the port from the cache key.

In this case, you can potentially use this header for web cache poisoning. 
For example, consider the case we saw earlier where a redirect URL was dynamically generated based on the Host header. 
This might enable you to construct a denial-of-service attack by simply adding an arbitrary port to the request. 
All users who browsed to the home page would be redirected to a dud port, effectively taking down the home page until the cache expired.

This kind of attack can be escalated further if the website allows you to specify a non-numeric port. You could use this to inject an XSS payload, for example.
````
### Unkeyed query string
````
Like the Host header, the request line is typically keyed. However, one of the most common cache-key transformations is to exclude the entire query string.
````
### Detecting an unkeyed query string
````
If the response explicitly tells you whether you got a cache hit or not, this transformation is relatively simple to spot - but what if it doesn't? 
This has the side-effect of making dynamic pages appear as though they are fully static because it can be hard to know whether you are communicating with the cache or the server.

To identify a dynamic page, you would normally observe how changing a parameter value has an effect on the response. 
But if the query string is unkeyed, most of the time you would still get a cache hit, and therefore an unchanged response, regardless of any parameters you add. 
Clearly, this also makes classic cache-buster query parameters redundant.

Fortunately, there are alternative ways of adding a cache buster, such as adding it to a keyed header that doesn't interfere with the application's behavior. 
Some typical examples include:

Accept-Encoding: gzip, deflate, cachebuster
Accept: */*, text/cachebuster
Cookie: cachebuster=1
Origin: https://cachebuster.vulnerable-website.com

If you use Param Miner, you can also select the options "Add static/dynamic cache buster" and "Include cache busters in headers". 
It will then automatically add a cache buster to commonly keyed headers in any requests that you send using Burp's manual testing tools.

Another approach is to see whether there are any discrepancies between how the cache and the back-end normalize the path of the request. 
As the path is almost guaranteed to be keyed, you can sometimes exploit this to issue requests with different keys that still hit the same endpoint. 
For example, the following entries might all be cached separately but treated as equivalent to GET / on the back-end:

Apache: GET //
Nginx: GET /%2F
PHP: GET /index.php/xyz
.NET GET /(A(xyz)/

This transformation can sometimes mask what would otherwise be glaringly obvious reflected XSS vulnerabilities. 
If penetration testers or automated scanners only receive cached responses without realizing, it can appear as though there is no reflected XSS on the page.
````
### Exploiting an unkeyed query string
````
Excluding the query string from the cache key can actually make these reflected XSS vulnerabilities even more severe.

Usually, such an attack would rely on inducing the victim to visit a maliciously crafted URL. 
However, poisoning the cache via an unkeyed query string would cause the payload to be served to users who visit what would otherwise be a perfectly normal URL. 
This has the potential to impact a far greater number of victims with no further interaction from the attacker.
````
### Unkeyed query parameters
````
So far we've seen that on some websites, the entire query string is excluded from the cache key. 
But some websites only exclude specific query parameters that are not relevant to the back-end application, such as parameters for analytics or serving targeted advertisements. UTM parameters like utm_content are good candidates to check during testing.

Parameters that have been excluded from the cache key are unlikely to have a significant impact on the response. 
The chances are there won't be any useful gadgets that accept input from these parameters. That said, some pages handle the entire URL in a vulnerable manner, making it possible to exploit arbitrary parameters.
````
### Cache parameter cloaking
````
If the cache excludes a harmless parameter from the cache key, and you can't find any exploitable gadgets based on the full URL, you'd be forgiven for thinking that you've reached a dead end. 
However, this is actually where things can get interesting.

If you can work out how the cache parses the URL to identify and remove the unwanted parameters, you might find some interesting quirks. 
Of particular interest are any parsing discrepancies between the cache and the application. This can potentially allow you to sneak arbitrary parameters into the application logic by "cloaking" them in an excluded parameter.

For example, the de facto standard is that a parameter will either be preceded by a question mark (?), if it's the first one in the query string, or an ampersand (&). 
Some poorly written parsing algorithms will treat any ? as the start of a new parameter, regardless of whether it's the first one or not.

Let's assume that the algorithm for excluding parameters from the cache key behaves in this way, but the server's algorithm only accepts the first ? as a delimiter. 
Consider the following request:

GET /?example=123?excluded_param=bad-stuff-here

In this case, the cache would identify two parameters and exclude the second one from the cache key. 
However, the server doesn't accept the second ? as a delimiter and instead only sees one parameter, example, whose value is the entire rest of the query string, including our payload. 
If the value of example is passed into a useful gadget, we have successfully injected our payload without affecting the cache key.
````
### Exploiting parameter parsing quirks
````
Similar parameter cloaking issues can arise in the opposite scenario, where the back-end identifies distinct parameters that the cache does not. 
The Ruby on Rails framework, for example, interprets both ampersands (&) and semicolons (;) as delimiters. 
When used in conjunction with a cache that does not allow this, you can potentially exploit another quirk to override the value of a keyed parameter in the application logic.

Consider the following request:

GET /?keyed_param=abc&excluded_param=123;keyed_param=bad-stuff-here

As the names suggest, keyed_param is included in the cache key, but excluded_param is not. Many caches will only interpret this as two parameters, delimited by the ampersand:

    * keyed_param=abc
    * excluded_param=123;keyed_param=bad-stuff-here

Once the parsing algorithm removes the excluded_param, the cache key will only contain keyed_param=abc. 
On the back-end, however, Ruby on Rails sees the semicolon and splits the query string into three separate parameters:

    * keyed_param=abc
    * excluded_param=123
    * keyed_param=bad-stuff-here

But now there is a duplicate keyed_param. This is where the second quirk comes into play. If there are duplicate parameters, each with different values, Ruby on Rails gives precedence to the final occurrence. 
The end result is that the cache key contains an innocent, expected parameter value, allowing the cached response to be served as normal to other users. 
On the back-end, however, the same parameter has a completely different value, which is our injected payload. It is this second value that will be passed into the gadget and reflected in the poisoned response.

This exploit can be especially powerful if it gives you control over a function that will be executed. 
For example, if a website is using JSONP to make a cross-domain request, this will often contain a callback parameter to execute a given function on the returned data:

GET /jsonp?callback=innocentFunction

In this case, you could use these techniques to override the expected callback function and execute arbitrary JavaScript instead.
````
### Exploiting fat GET support
````
In select cases, the HTTP method may not be keyed. This might allow you to poison the cache with a POST request containing a malicious payload in the body. 
Your payload would then even be served in response to users' GET requests. 
Although this scenario is pretty rare, you can sometimes achieve a similar effect by simply adding a body to a GET request to create a "fat" GET request:

GET /?param=innocent HTTP/1.1
…
param=bad-stuff-here

In this case, the cache key would be based on the request line, but the server-side value of the parameter would be taken from the body.

This is only possible if a website accepts GET requests that have a body, but there are potential workarounds. 
You can sometimes encourage "fat GET" handling by overriding the HTTP method, for example:

GET /?param=innocent HTTP/1.1
Host: innocent-website.com
X-HTTP-Method-Override: POST
…
param=bad-stuff-here

As long as the X-HTTP-Method-Override header is unkeyed, you could submit a pseudo-POST request while preserving a GET cache key derived from the request line.
````
### Exploiting dynamic content in resource imports
````
Imported resource files are typically static but some reflect input from the query string. This is mostly considered harmless because browsers rarely execute these files when viewed directly, and an attacker has no control over the URLs used to load a page's subresources. However, by combining this with web cache poisoning, you can occasionally inject content into the resource file.

For example, consider a page that reflects the current query string in an import statement:

GET /style.css?excluded_param=123);@import… HTTP/1.1

HTTP/1.1 200 OK
…
@import url(/site/home/index.part1.8a6715a2.css?excluded_param=123);@import…

You could exploit this behavior to inject malicious CSS that exfiltrates sensitive information from any pages that import /style.css.

If the page importing the CSS file doesn't specify a doctype, you can maybe even exploit static CSS files. 
Given the right configuration, browsers will simply scour the document looking for CSS and then execute it. 
This means that you can occasionally poison static CSS files by triggering a server error that reflects the excluded query parameter:

GET /style.css?excluded_param=alert(1)%0A{}*{color:red;} HTTP/1.1

HTTP/1.1 200 OK
Content-Type: text/html
…
This request was blocked due to…alert(1){}*{color:red;}
````
### Normalized cache keys
````
Any normalization applied to the cache key can also introduce exploitable behavior. In fact, it can occasionally enable some exploits that would otherwise be almost impossible.

For example, when you find reflected XSS in a parameter, it is often unexploitable in practice. 
This is because modern browsers typically URL-encode the necessary characters when sending the request, and the server doesn't decode them. 
The response that the intended victim receives will merely contain a harmless URL-encoded string.

Some caching implementations normalize keyed input when adding it to the cache key. In this case, both of the following requests would have the same key:

GET /example?param="><test>
GET /example?param=%22%3e%3ctest%3e

This behavior can allow you to exploit these otherwise "unexploitable" XSS vulnerabilities. If you send a malicious request using Burp Repeater, you can poison the cache with an unencoded XSS payload. 
When the victim visits the malicious URL, the payload will still be URL-encoded by their browser; however, once the URL is normalized by the cache, it will have the same cache key as the response containing your unencoded payload.

As a result, the cache will serve the poisoned response and the payload will be executed client-side. You just need to make sure that the cache is poisoned when the victim visits the URL.
````
### Cache key injection
````
Sometimes you may discover a client-side vulnerability in a keyed header. This is also a classic "unexploitable" issue that can sometimes be exploited using cache poisoning.

Keyed components are often bundled together in a string to create the cache key. If the cache doesn't implement proper escaping of the delimiters between the components, you can potentially exploit this behavior to craft two different requests that have the same cache key.

The following example uses double-underscores to delimit different components in the cache key and does not escape them. 
You can exploit this by first poisoning the cache with a request containing your payload in the corresponding keyed header:

GET /path?param=123 HTTP/1.1
Origin: '-alert(1)-'__

HTTP/1.1 200 OK
X-Cache-Key: /path?param=123__Origin='-alert(1)-'__

<script>…'-alert(1)-'…</script>

If you then induce a victim user to visit the following URL, they would be served the poisoned response:

GET /path?param=123__Origin='-alert(1)-'__ HTTP/1.1

HTTP/1.1 200 OK
X-Cache-Key: /path?param=123__Origin='-alert(1)-'__
X-Cache: hit

<script>…'-alert(1)-'…</script>
````
### Poisoning internal caches
````
So far, we've looked at how you can exploit flaws in the way external web caches are implemented to expose an extended attack surface hidden within seemingly keyed components. However, some websites implement caching behavior directly into the application in addition to using a distinct, external component. This can have several advantages, such as avoiding the kind of parsing discrepancies we looked at earlier.

As these integrated caches are purpose-built for the specific application, this also gives developers the freedom to tailor their behavior to a greater degree. As a result, these caches can sometimes behave in unusual ways that you wouldn't typically see from a more standardized, external cache that needs to be compatible with multiple applications. Sometimes, these strange behaviors can also provide an opportunity for some high-severity cache poisoning exploits.

Instead of caching entire responses, some of these caches break the response down into reusable fragments and cache them each separately. For example, a snippet for importing a widely used resource might be stored as a standalone cache entry. Users might then receive a response comprising a mixture of content from the server, as well as several individual fragments from the cache.

As these cached fragments are intended to be reusable across multiple distinct responses, the concept of a cache key doesn't really apply. Every response that contains a given fragment will reuse the same cached fragment, even if the rest of the response is completely different. In a scenario like this, poisoning the cache can have wide-reaching effects, especially if you poison a fragment that is used on every page. As there is no cache key, you would have poisoned every page, for every user, with a single request.

This will often only require you to use basic web cache poisoning techniques, such as manipulating the Host header.`
````
### How to identify internal caches
````
One of the challenges posed by integrated, application-level caches is that they can be difficult to identify and investigate because there is often no user-facing feedback. To identify these caches, you can look for a few tell-tale signs.

For example, if the response reflects a mixture of both input from the last request you sent and input from a previous request, this is a key indicator that the cache is storing fragments rather than entire responses. 
The same applies if your input is reflected in responses on multiple distinct pages, in particular on pages in which you never tried to inject your input.

Other times, the cache's behavior may simply be so unusual that the most logical conclusion is that it must be a unique and specialized internal cache.

When a website implements multiple layers of caching, it can make it difficult to comprehend what is happening behind the scenes and understand how the website's caching system behaves
````
### Testing internal caches safely
````
When testing ordinary web caches, we recommend using a cache buster to prevent your poisoned response from being served to other users. 
However, if an integrated cache has no concept of cache keys, then traditional cache busters are useless. This means that it's very easy to accidentally poison the cache for genuine users.

Therefore, it is important that you do your best to mitigate the potential damage when testing these kinds of vulnerabilities. 
Think carefully about the effect of your injected payload before sending each request. In particular, you should make sure that you only poison the cache using a domain that you control, rather than some arbitrary "evil-user.net". 
This way, you are in control of what happens next if something goes wrong.
````
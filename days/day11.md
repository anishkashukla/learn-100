# day11

## What is information disclosure ?

```text
Information disclosure, also known as information leakage, is when a website unintentionally reveals sensitive information to its users. 
Depending on the context, websites may leak all kinds of information to a potential attacker, including:

Data about other users, such as usernames or financial information
Sensitive commercial or business data
Technical details about the website and its infrastructure
The dangers of leaking sensitive user or business data are fairly obvious, but disclosing technical information can sometimes be just as serious. 
Although some of this information will be of limited use, it can potentially be a starting point for exposing an additional attack surface, which may contain other interesting vulnerabilities. 
The knowledge that you are able to gather could even provide the missing piece of the puzzle when trying to construct complex, high-severity attacks.

Occasionally, sensitive information might be carelessly leaked to users who are simply browsing the website in a normal fashion. 
More commonly, however, an attacker needs to elicit the information disclosure by interacting with the website in unexpected or malicious ways. 
They will then carefully study the website's responses to try and identify interesting behavior.
```

## What are some examples of information disclosure ?

```text
Some basic examples of information disclosure are as follows:

* Revealing the names of hidden directories, their structure, and their contents via a robots.txt file or directory listing
* Providing access to source code files via temporary backups
* Explicitly mentioning database table or column names in error messages
* Unnecessarily exposing highly sensitive information, such as credit card details
* Hard-coding API keys, IP addresses, database credentials, and so on in the source code
* Hinting at the existence or absence of resources, usernames, and so on via subtle           differences in application behavior
```

## How do information disclosure vulnerabilities arise ?

```text
Information disclosure vulnerabilities can arise in countless different ways, but these can broadly be categorized as follows:

Failure to remove internal content from public content. 
For example, developer comments in markup are sometimes visible to users in the production environment.
Insecure configuration of the website and related technologies. 
For example, failing to disable debugging and diagnostic features can sometimes provide attackers with useful tools to help them obtain sensitive information. 
Default configurations can also leave websites vulnerable, for example, by displaying overly verbose error messages.
Flawed design and behavior of the application. 
For example, if a website returns distinct responses when different error states occur, this can also allow attackers to enumerate sensitive data, such as valid user credentials
```

## What is the impact of information disclosure vulenerabilities ?

```text
Information disclosure vulnerabilities can have both a direct and indirect impact depending on the purpose of the website and, therefore, what information an attacker is able to obtain. 
In some cases, the act of disclosing sensitive information alone can have a high impact on the affected parties. For example, an online shop leaking its customers' credit card details is likely to have severe consequences.

On the other hand, leaking technical information, such as the directory structure or which third-party frameworks are being used, may have little to no direct impact. 
However, in the wrong hands, this could be the key information required to construct any number of other exploits. 
The severity in this case depends on what the attacker is able to do with this information.
```

## How to assess the severity of information disclosure vulnerabilities

```text
Although the ultimate impact can potentially be very severe, it is only in specific circumstances that information disclosure is a high-severity issue on its own. 
During testing, the disclosure of technical information in particular is often only of interest if you are able to demonstrate how an attacker could do something harmful with it.

For example, the knowledge that a website is using a particular framework version is of limited use if that version is fully patched. 
However, this information becomes significant when the website is using an old version that contains a known vulnerability. 
In this case, performing a devastating attack could be as simple as applying a publicly documented exploit.

It is important to exercise common sense when you find that potentially sensitive information is being leaked. 
It is likely that minor technical details can be discovered in numerous ways on many of the websites you test. 
Therefore, your main focus should be on the impact and exploitability of the leaked information, not just the presence of information disclosure as a standalone issue. 
The obvious exception to this is when the leaked information is so sensitive that it warrants attention in its own right.
```

## How to test for information disclosure vulnerabilities

```text
Generally speaking, it is important not to develop "tunnel vision" during testing. In other words, you should avoid focussing too narrowly on a particular vulnerability. Sensitive data can be leaked in all kinds of places, so it is important not to miss anything that could be useful later. You will often find sensitive data while testing for something else. A key skill is being able to recognize interesting information whenever and wherever you do come across it.

The following are some examples of high-level techniques and tools that you can use to help identify information disclosure vulnerabilities during testing.

Fuzzing
Using Burp Scanner
Using Burp's engagement tools
Engineering informative responses

We can also look for Information Disclosure Vulnerabilities using Google Dorks and GitHub Recon

Recources for Google Dorks - https://www.exploit-db.com/google-hacking-database
```

## Fuzzing

```text
If you identify interesting parameters, you can try submitting unexpected data types and specially crafted fuzz strings to see what effect this has. 
Pay close attention; although responses sometimes explicitly disclose interesting information, they can also hint at the application's behavior more subtly. 
For example, this could be a slight difference in the time taken to process the request. Even if the content of an error message doesn't disclose anything, sometimes the fact that one error case was encountered instead of another one is useful information in itself.

You can automate much of this process using tools such as Burp Intruder. 
This provides several benefits. Most notably, you can:

Add payload positions to parameters and use pre-built wordlists of fuzz strings to test a high volume of different inputs in quick succession.
Easily identify differences in responses by comparing HTTP status codes, response times, lengths, and so on.
Use grep matching rules to quickly identify occurrences of keywords, such as error, invalid, SELECT, SQL, and so on.
Apply grep extraction rules to extract and compare the content of interesting items within responses.
You can also use the Logger++ extension, available from the BApp store. 
In addition to logging requests and responses from all of Burp's tools, it allows you to define advanced filters for highlighting interesting entries. 
This is just one of the many Burp extensions that can help you find any sensitive data that is leaked by the website.
```

## Using Burp Scanner

```text
Burp Suite Professional users have the benefit of Burp Scanner. 
This provides live scanning features for auditing items while you browse, or you can schedule automated scans to crawl and audit the target site on your behalf. 
Both approaches will automatically flag many information disclosure vulnerabilities for you. 
For example, Burp Scanner will alert you if it finds sensitive information such as private keys, email addresses, and credit card numbers in a response. 
It will also identify any backup files, directory listings, and so on.
```

### Using Burp's engagement tools

```text
Burp provides several engagement tools that you can use to find interesting information in the target website more easily. 
You can access the engagement tools from the context menu - just right-click on any HTTP message, Burp Proxy entry, or item in the site map and go to "Engagement tools".

The following tools are particularly useful in this context
```

### Search

```text
You can use this tool to look for any expression within the selected item. 
You can fine-tune the results using various advanced search options, such as regex search or negative search. 
This is useful for quickly finding occurrences (or absences) of specific keywords of interest.
```

### Find comments

```text
You can use this tool to quickly extract any developer comments found in the selected item. It also provides tabs to instantly access the HTTP request/response cycle in which each comment was found.
```

### Discover content

```text
You can use this tool to identify additional content and functionality that is not linked from the website's visible content. 
This can be useful for finding additional directories and files that won't necessarily appear in the site map automatically.
```

## Engineering informative responses

```text
Verbose error messages can sometimes disclose interesting information while you go about your normal testing workflow. 
However, by studying the way error messages change according to your input, you can take this one step further. In some cases, you will be able to manipulate the website in order to extract arbitrary data via an error message.

There are numerous methods for doing this depending on the particular scenario you encounter. 
One common example is to make the application logic attempt an invalid action on a specific item of data. 
For example, submitting an invalid parameter value might lead to a stack trace or debug response that contains interesting details. 
You can sometimes cause error messages to disclose the value of your desired data in the response.
```

## Common sources of information disclosure

```text
Information disclosure can occur in a wide variety of contexts within a website. The following are some common examples of places where you can look to see if sensitive information is exposed.

Files for web crawlers
Directory listings
Developer comments 
Error messages 
Debugging data 
User account pages 
Backup files 
Insecure configuration 
Version control history
```

## Files for web crawlers

```text
Many websites provide files at /robots.txt and /sitemap.xml to help crawlers navigate their site. 
Among other things, these files often list specific directories that the crawlers should skip, for example, because they may contain sensitive information.

As these files are not usually linked from within the website, they may not immediately appear in Burp's site map. 
However, it is worth trying to navigate to /robots.txt or /sitemap.xml manually to see if you find anything of use.
```

## Directory listings

```text
Web servers can be configured to automatically list the contents of directories that do not have an index page present. 
This can aid an attacker by enabling them to quickly identify the resources at a given path, and proceed directly to analyzing and attacking those resources. 
It particularly increases the exposure of sensitive files within the directory that are not intended to be accessible to users, such as temporary files and crash dumps.

Directory listings themselves are not necessarily a security vulnerability. 
However, if the website also fails to implement proper access control, leaking the existence and location of sensitive resources in this way is clearly an issue.
```

## Developer comments

```text
During development, in-line HTML comments are sometimes added to the markup. These comments are typically stripped before changes are deployed to the production environment. 
However, comments can sometimes be forgotten, missed, or even left in deliberately because someone wasn't fully aware of the security implications. 
Although these comments are not visible on the rendered page, they can easily be accessed using Burp, or even your browser's built-in developer tools.

Occasionally, these comments contain information that is useful to an attacker. 
For example, they might hint at the existence of hidden directories or provide clues about the application logic.
```

## Error messages

```text
One of the most common causes of information disclosure is verbose error messages.
As a general rule, you should pay close attention to all error messages you encounter during auditing.

The content of error messages can reveal information about what input or data type is expected from a given parameter. 
This can help you to narrow down your attack by identifying exploitable parameters. 
It may even just prevent you from wasting time trying to inject payloads that simply won't work.

Verbose error messages can also provide information about different technologies being used by the website. 
For example, they might explicitly name a template engine, database type, or server that the website is using, along with its version number. 
This information can be useful because you can easily search for any documented exploits that may exist for this version. 
Similarly, you can check whether there are any common configuration errors or dangerous default settings that you may be able to exploit. 
Some of these may be highlighted in the official documentation.

You might also discover that the website is using some kind of open-source framework. 
In this case, you can study the publicly available source code, which is an invaluable resource for constructing your own exploits.

Differences between error messages can also reveal different application behavior that is occurring behind the scenes. 
Observing differences in error messages is a crucial aspect of many techniques, such as SQL injection, username enumeration, and so on.
```

## Debugging data

```text
For debugging purposes, many websites generate custom error messages and logs that contain large amounts of information about the application's behavior. 
While this information is useful during development, it is also extremely useful to an attacker if it is leaked in the production environment.

Debug messages can sometimes contain vital information for developing an attack, including:

Values for key session variables that can be manipulated via user input
Hostnames and credentials for back-end components
File and directory names on the server
Keys used to encrypt data transmitted via the client
Debugging information may sometimes be logged in a separate file. If an attacker is able to gain access to this file, it can serve as a useful reference for understanding the application's runtime state. It can also provide several clues as to how they can supply crafted input to manipulate the application state and control the information received.
```

## User account pages

```text
By their very nature, a user's profile or account page usually contains sensitive information, such as the user's email address, phone number, API key, and so on. 
As users normally only have access to their own account page, this does not represent a vulnerability in itself. 
However, some websites contain logic flaws that potentially allow an attacker to leverage these pages in order to view other users' data.

For example, consider a website that determines which user's account page to load based on a user parameter.

GET /user/personal-info?user=carlos

Most websites will take steps to prevent an attacker from simply changing this parameter to access arbitrary users' account pages. 
However, sometimes the logic for loading individual items of data is not as robust.

An attacker may not be able to load another users' account page entirely, but the logic for fetching and rendering the user's registered email address, for example, might not check that the user parameter matches the user that is currently logged in. 
In this case, simply changing the user parameter would allow an attacker to display arbitrary users' email addresses on their own account page.
```

## Source code disclosure via backup files

```text
Obtaining source code access makes it much easier for an attacker to understand the application's behavior and construct high-severity attacks. Sensitive data is sometimes even hard-coded within the source code. Typical examples of this include API keys and credentials for accessing back-end components.

If you can identify that a particular open-source technology is being used, this provides easy access to a limited amount of source code.

Occasionally, it is even possible to cause the website to expose its own source code. When mapping out a website, you might find that some source code files are referenced explicitly. Unfortunately, requesting them does not usually reveal the code itself. When a server handles files with a particular extension, such as .php, it will typically execute the code, rather than simply sending it to the client as text. However, in some situations, you can trick a website into returning the contents of the file instead. For example, text editors often generate temporary backup files while the original file is being edited. These temporary files are usually indicated in some way, such as by appending a tilde (~) to the filename or adding a different file extension. Requesting a code file using a backup file extension can sometimes allow you to read the contents of the file in the response.
Once an attacker has access to the source code, this can be a huge step towards being able to identify and exploit additional vulnerabilities that would otherwise be almost impossible. One such example is insecure deserialization.
```

## Information disclosure due to insecure configuration

```text
Websites are sometimes vulnerable as a result of improper configuration. 
This is especially common due to the widespread use of third-party technologies, whose vast array of configuration options are not necessarily well-understood by those implementing them.

In other cases, developers might forget to disable various debugging options in the production environment. 
For example, the HTTP TRACE method is designed for diagnostic purposes. If enabled, the web server will respond to requests that use the TRACE method by echoing in the response the exact request that was received. 
This behavior is often harmless, but occasionally leads to information disclosure, such as the name of internal authentication headers that may be appended to requests by reverse proxies.
```

## Version control history

```text
Virtually all websites are developed using some form of version control system, such as Git. By default, a Git project stores all of its version control data in a folder called .git. 
Occasionally, websites expose this directory in the production environment. In this case, you might be able to access it by simply browsing to /.git.

While it is often impractical to manually browse the raw file structure and contents, there are various methods for downloading the entire .git directory. 
You can then open it using your local installation of Git to gain access to the website's version control history. 
This may include logs containing committed changes and other interesting information.

This might not give you access to the full source code, but comparing the diff will allow you to read small snippets of code. 
As with any source code, you might also find sensitive data hard-coded within some of the changed lines.
```

## How to prevent information disclosure vulnerabilities

```text
Preventing information disclosure completely is tricky due to the huge variety of ways in which it can occur. However, there are some general best practices that you can follow to minimize the risk of these kinds of vulnerability creeping into your own websites.

* Make sure that everyone involved in producing the website is fully aware of what information is considered sensitive. 
Sometimes seemingly harmless information can be much more useful to an attacker than people realize. 
Highlighting these dangers can help make sure that sensitive information is handled more securely in general by your organization.
* Audit any code for potential information disclosure as part of your QA or build processes. It should be relatively easy to automate some of the associated tasks, such as stripping developer comments.
* Use generic error messages as much as possible. Don't provide attackers with clues about application behavior unnecessarily.
* Double-check that any debugging or diagnostic features are disabled in the production environment.
* Make sure you fully understand the configuration settings, and security implications, of any third-party technology that you implement. 
Take the time to investigate and disable any features and settings that you don't actually need.
```


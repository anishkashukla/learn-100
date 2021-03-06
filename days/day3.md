# day3

| Index | Technique |
| :--- | :--- |
| **1** | What is Recon in Bug Bounty Hunting ? |
| **2** | Why ? |
| **3** | Determing the target |
| **4** | Mapping the target |
| **5** | Making notes |
| **6** | How to get the subdomains of a particular taget ? |
| **7** | How to get the parameters for a particular target ? |
| **8** | Tools for CMS Identification |
| **9** | List of Tools |
| **10** | Resources |

## What is Recon in Bug Bounty Hunting ?

```text
Recon means gathering information about a target. It could  be information related to the subdomains, services used by them etc. 
Sometimes recon can go beyond collecting basic information to understand the system and can also identify information which might 
straight away lead to exploitation, sometimes without actually touching the entity being tested.

Even after having such significance this phase is not given enough importance and most of the tests focus straight away on 
exploitation. The key point here is that exploitation is certainly important but performing a thorough recon could prove very 
helpful in it and also make it easier, faster and stealthier.
```

## Why ?

```text
Find a bigger attack surface 
More bugs
More bounties
```

## Determing the target

```text
Everyone wants to choose a program that has wide scope so that you can can get wider range of vulerabilities within the scope.
Understand which all domains are in scope.
```

## Mapping the target

```text
Read all terms of the program and understand which domains are in scope and which forms of vulnerabilities are considered valid reports.
Recon and gather as much information as you can to effectively map the application
Make notes of everything properly
```

## Making notes

```text
It is extremely important to note information about the target so that you do not miss out on anything. 

There should be a proper methodology for approaching the target.
```

## How to get the subdomains of a particular taget ?

```text
There are different tools and websites available for this purpose like - 

Project Discovery - https://chaos.projectdiscovery.io/#/
Sublister - https://github.com/aboul3la/Sublist3r
Amass - https://github.com/OWASP/Amass
SubBrute - https://github.com/TheRook/subbrute
Knock - https://github.com/guelfoweb/knock

Use different tools and websites and try to collect all the subdomains so that you do no miss out on anything.
```

## How to get the parameters for a particular target ?

```text
Some tools for getting the parameters are -

ParamSpider - https://github.com/devanshbatham/ParamSpider
Arjun - https://github.com/s0md3v/Arjun

Using it, you wil be able to get a lot of parameters and will help you in hunting the bugs.
```

## Some Tools for CMS Identificatin

```text
Wappalyzer - https://www.wappalyzer.com/
BuiltWith - https://builtwith.com/
WhatWeb - https://github.com/urbanadventurer/WhatWeb

You can also install the browser extension for Wappalyzer and BuiltWith
```

## List of Tools

```text
https://pentest-tools.com/
https://virustotal.com/
https://www.shodan.io/
https://crt.sh/?q=%25taregt.com
https://dnsdumpster.com/
https://censys.io
http://dnsgoodies.com
https://bitbucket.org/LaNMaSteR53/recon-ng
https://github.com/michenriksen/aquatone
https://github.com/aboul3la/Sublist3r
https://github.com/rbsec/dnscan
https://github.com/Cleveridge/cleveridge-subdomain-scanner
https://github.com/yasinS/sandcastle
https://digi.ninja/projects/bucket_finder.php
https://github.com/jobertabma/relative-url-extractor
https://web.archive.org/
https://gist.github.com/mhmdiaa/2742c5e147d49a804b408bfed3d32d07
https://gist.github.com/mhmdiaa/adf6bff70142e5091792841d4b372050
http://viewdns.info/reversewhois/?q=
https://www.punkspider.org -  Global web application vulnerability search engine
```

## Resources

```text
https://github.com/JakobTheDev/bug-bounty
https://www.bugcrowd.com/resources/webinars/github-recon-and-sensitive-data-exposure/
https://infosecwriteups.com/guide-to-basic-recon-bug-bounties-recon-728c5242a115
https://www.offensity.com/de/blog/just-another-recon-guide-pentesters-and-bug-bounty-hunters/
https://blog.usejournal.com/
web-application-security-bug-bounty-methodology-reconnaissance-vulnerabilities-reporting-635073cddcf2
https://youtu.be/uKWu6yhnhbQ
https://youtu.be/ZnugWiOULmw
https://youtu.be/amihlWTtkMA
https://youtu.be/p4JgIu1mceI
```

## Will keep updating it so keep checking it regularly :smile: :v:


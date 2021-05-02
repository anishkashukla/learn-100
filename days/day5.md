# day5

| Index | Technique |
| :--- | :--- |
| **1** | What is Shodan ? |
| **2** | How to use Shodan ? |
| **3** | Command Line Interface |
| **4** | Resources |
| **5** | Other information |

## What is Shodan ?

```text
Shodan is a search engine that lets the user find specific types of computers connected to the internet using a variety 
of filters. 
Some have also described it as a search engine of service banners, which are 
metadata that the server sends back to the client.
You can find information of all the interconnected devices, it could be any 
device like webcams, refrigerator, etc that were crawled by the Shodan crawler.
```

## How to use Shodan ?

```text
There are different queries that you can use in order to get better results.
Syntax - filtername: value
For ex, we can use queries including -
data: the main response from the service itself
ip_str: IP address of the device
port: port number of the service
org: the organization that owns this IP space
location.country_code: the country where the device is located
```

## Command Line Interface

```text
You can also use Shodan in command line 

Installation - 

In a virtual python environment
easy_install shodan or pip3 install shodan
Once you have installed CLI tool, setup your API token
shodan init <Your API Key>

Help -

shodan -h

Search -

shodan search -h for help
```

## Resources

[ShodanQueries-github-repo](https://github.com/jakejarvis/awesome-shodan-queries)

## Other information

```text
Download data
Generate reports
Shodan Maps
Shodan Images
```


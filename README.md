
# SecOps automation

A few nodejs, python, go tools 

Goals:

	1. To help with quick prototyping
	2. Ease automation efforts 
	3. And a simpler programming style that allows for repurposing. 
	

## sslScan.js

A node script that's essentially a tiny client leveraging the ssllabs api to perform a TLS scan of a given endpoint. It takes 2 command line arguments:

	1. a hostname
	2. and an optional 'checkcache' parameter with the value 'cache'

 Installation: 
 
 	1. Download the script from this repo
	2. Get these dependencies 'querystringify', 'request', async-polling via npm. 
	
Usage: 

	node sslScan.js 'your hostName' and the optional 'checkcache=cache' argument


Notes: 

This script has 2 functions 

	1. newScan: performs a new TLS scan asynchronously i.e. if you dont use the optional 'checkcache=cache' flag, 
		a. A newScan calls on the ssllabs api to perform a new scan. 
		b. A new scan could take anywhere between 3-15 mins, depending on a bunch of different factors. 
		c. A new scan will keep polling for progress (no progress bar) during this time.
		d. Upon completion, a JSON object will be returned. 
	2. fromCache: to fetch a previously scanned result from cache asynchronously, use the 'checkcache=cache' flag. 
		a. If the result is not in cache, an error is returned - 'HTTP error at Cached Result retrieval time'
		b. If the ssllabs api is unavailable or unresponsive, an 'HTTP error at API availability check time' is returned.  
		b. Please refer to the ssllabs API for more info - https://github.com/ssllabs/ssllabs-scan/blob/master/ssllabs-api-docs.md

Lambda: 

	1. This script can be easily adapted to run in a serverless environment with very little modification. 
	2. Unfortunately, I cant show you the exact piece of serverless code. 
	3. But here's a working link that should give you an idea - https://e4jtf0bv84.execute-api.ap-south-1.amazonaws.com/prod_QA/-sslscan?hostname=www.google.com

A screenshot of the resultant JSON object:

![sslscan js readme 4](https://user-images.githubusercontent.com/20253082/39177024-dc580820-47cb-11e8-9d03-73dc17ce6651.png)


## dns_tunneling_test.js

A test script to emulate a dns tunneling client written in node. The objective is to show how a few lines of js can be used to exfiltrate data via dns. During the post exploitation phase, DNS has turned out to be the strongest candidate for data exflitration, as majority of enterprises lack approriate security (monitoring & preventative) controls. It takes a single command line argument:

	1. a 140 character payload 
	2. An example payload - say you want to drop some shellcode without setting off a traditional FW. It's possible.

 Installation: 
 
 	1. Download the script from this repo
	2. Get these dependencies 'dgram', 'dns-packet', 'base64', 'utf8' via npm. 
	
Usage: 

	node dns_tunneling_test.js 'payload' 

Notes:  

	1. The test script uses 'dns.com' as its tld as I didn't want to spin up my own dns server and host my own domain.  
	2. So if you want to go beyond the quick test case here. You'll need your own domain (or dns server) to which you can send these queries.
	3. ### Note: the response returned is all garbled as DNS messages are case insensitive. Decoding a case insensitive message gives you garbage. To work around this, you'll need a dns server (like most attacker controlled domains) that preserves case.  
	3. Some info on the client's operation:
		a. Takes the payload, chunks it into dns labels of 63 bytes each to create a fully qualified domain name
		b. Encodes to base64
		c. Makes a dns query
		d. The response is logged to the console. In this case your payload is simply reflected by 'dns.com' 
Lambda: 

	1. This script can be easily adapted to run in a serverless environment with very little modification. 
	2. Unfortunately, I cant show you the exact piece of serverless code. 

A command line screenshot:

![dns_tunneling_screenshot](https://user-images.githubusercontent.com/20253082/39226271-ef4b808a-486e-11e8-80de-2caf74d8ad5f.png)

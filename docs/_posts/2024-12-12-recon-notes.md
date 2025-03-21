---
layout: post
title:  Recon Notes
date:   2024-12-12 16:07:44
categories: recon
post_id: 8
---

Often times I find myself trying to remember the name of a useful tool I saw somewhere on the internet, or some blogpost that I read a while ago. This is a collection of tools, techniques, and resources that I find useful for recon. This notes will InshaAllah be continuously updated.

### Robots.txt

This is not very common anymore but sometimes you can find interesting stuff in robots.txt file of a website. It is a file that tells search engine bots which pages to index and which not to index. It can also contain links to sitemaps and other resources.

### Detect 404 pages differentials

Not found/404 pages can sometimes leak information about the server type, version, web technologies, etc. It can also be used to find hidden directories and files, by comparing the response of a known 404 page with the response of a page that doesn't exist, any change in the response can be an indicator of something worth investigating, hence the term "differential". It's important to develop extensive tooling around this concept to automate the process. Also to make a fingerprinting database of different web servers and technologies based on their 404 responses.

### Security TXT files

This is also not very common but sometimes you can find security.txt files in the .well-known directory of a website. This file contains information about the security policies of the website, like how to report a vulnerability, etc. It can be a foothold for further recon.

### Directory listings

It's important to analyze every directory for potential directory listings, sometimes they can contain sensitive information like source code, configuration files, etc.

### Directory brute-forcing

Performing informed directory brute-forcing can be very useful in finding hidden directories and files. It's important to use a good wordlist and also to use tools that can perform recursive brute-forcing. Curating a good wordlist and understanding the target's technology stack can be very helpful in this process.

### VHOST brute-forcing

Playing around with the host header can sometimes reveal more information about the target or reveal more targets. More on vhost discovery later

### TLS Certificates

TLS Certificates contain alternate names and can be source of information

### Load balancing

Sometimes multiple backends can be serving request for a single web application, it can be useful to send the same request times to see if the response changes, this can be an indicator of multiple backends.

### TXT Records

TXT records can contain sometimes contain useful information so it's worth looking into.

### Public assets directories

Public assets directories can sometimes contain sensitive information like API keys, etc. It's important to look into them.

## Tools

## **ASN Enumeration**

ASN do not provide the complete picture of a target due to advent of cloud services and CDNs.

### [bgp.he.net](https://bgp.he.net/)

Website but really reliable and accurate.

### [ASNLookup](https://github.com/yassineaboukir/Asnlookup)

python script that leverages asnlookup.com free api

### [Amass](https://github.com/owasp-amass/amass)

Created by OWASP, Amass has in-depth attack surface discovery capability.

- [Intigriti on Amass](https://blog.intigriti.com/hacking-tools/hacker-tools-amass-hunting-for-subdomains)

## **Recon Tools**

### ***Subdomain Enumeration***

- ### [aiodnsbrute](https://github.com/blark/aiodnsbrute)

- ### [amass](https://github.com/owasp-amass/amass)

- ### [hosthunter](https://github.com/SpiderLabs/HostHunter)

- ### [mksub](https://github.com/trickest/mksub)

- ### [massdns](https://github.com/blechschmidt/massdns)

- ### [sublister](https://github.com/aboul3la/Sublist3r)

- ### [Subdomainizer](https://github.com/nsonaniya2010/SubDomainizer)

- ### [Subfinder](https://github.com/projectdiscovery/subfinder)

- ### [Subdominator](https://github.com/RevoltSecurities/Subdominator)

- ### [vhostscan](https://github.com/codingo/VHostScan)

### ***Guessing & Bruteforcing***

- ### [shuffleDNS](https://github.com/projectdiscovery/shuffledns)

- ### [altDNS](https://github.com/infosec-au/altdns)

### ***Crawling***

- ### [GoSpider](https://github.com/jaeles-project/gospider)

- ### [Hakrawler](https://github.com/hakluke/hakrawler)

## **Wordlists**

- ### [trickest wordlists](https://github.com/trickest/wordlists)

- ### [SecLists](https://github.com/danielmiessler/SecLists)

- ### [FuzzDB](https://github.com/fuzzdb-project/fuzzdb)

- ### [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)

## **Wordlist Generation**

- ### [CeWL](https://github.com/digininja/CeWL)

- ### [WL](https://github.com/s0md3v/wl)

- ### [Cook](https://github.com/giteshnxtlvl/cook)

---
layout: post
title:  Recon Notes
date:   2024-12-12 16:07:44
categories: recon
post_id: 8
---

## Notes for learning recon and keeping track of tools and resources

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

## Tools

### [Amass](https://github.com/owasp-amass/amass)

Created by OWASP, Amass has in-depth attack surface discovery capability.

- [Intigriti on Amass](https://blog.intigriti.com/hacking-tools/hacker-tools-amass-hunting-for-subdomains)


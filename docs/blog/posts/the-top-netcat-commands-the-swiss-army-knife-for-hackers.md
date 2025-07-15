---
slug: "the-top-netcat-commands-the-swiss-army-knife-for-hackers"
title: |
  The Top Netcat Commands: The Swiss Army Knife for Hackers"
date: 2022-03-19
tags: ['aws']
---

Netcat is a network service for reading and writing network connections using either TCP or UDP. It has the ability to create almost any kind of network connections and has many interesting capabilities. Hence, it is commonly termed as "the Swiss army knife of networking".

<!-- more -->




Here are a few examples of common Netcat commands:


1. Listen for incoming connections on port 8080: nc -l 8080


2. Connect to a remote host on port 80: nc [example.com](http://example.com) 80


3. Redirect incoming connections on port 8080 to port 80 on [example.com](http://example.com): nc -l 8080 | nc [example.com](http://example.com) 80


4. Send a file "test.txt" to a remote host on port 1234: nc [example.com](http://example.com) 1234 < test.txt


5. Receive a file from a remote host on port 1234 and save it as "received.txt": nc -l 1234 > received.txt


6. Transfer a file "test.txt" between two hosts: Host A: nc -l 1234 < test.txt Host B: nc [host A's IP] 1234 > test.txt


7. Port scan a host to see which ports are open: nc -v -n -z [example.com](http://example.com) 1-1000





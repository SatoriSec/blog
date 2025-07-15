---
slug: the-top-10-nmap-commands-you-need-to-know
title: The Top 10 Nmap Commands You Need to Know
date: 2021-01-10
tags: ['aws']
---

Nmap (Network Mapper) is a free and open-source network scanning tool that is used to discover hosts and services on a computer network, and to determine what devices are running on a network. Here are some common Nmap commands with examples:

<!-- more -->




1. `nmap -sP <target IP or range>`: This command pings all the hosts in the specified target IP or IP range to check if they are online.


Example: `nmap -sP 192.168.1.1/24`


1. `nmap -p <port range> <target IP or range>`: This command scans the specified target IP or range for the specified port range.


Example: `nmap -p 80-443 192.168.1.1/24`


1. `nmap -O <target IP or range>`: This command attempts to determine the operating system of the hosts in the specified target IP or range.


Example: `nmap -O 192.168.1.1/24`


1. `nmap -sV <target IP or range>`: This command attempts to determine the version of the services running on the specified target IP or range.


Example: `nmap -sV 192.168.1.1/24`


1. `nmap --script <script name> <target IP or range>`: This command runs the specified Nmap script on the specified target IP or range. Nmap has a large number of scripts available for tasks such as vulnerability scanning, brute force attacks, and discovery of network services.


Example: `nmap --script vuln 192.168.1.1/24`


1. `nmap -A <target IP or range>`: This command enables OS detection, version detection, script scanning, and traceroute for the specified target IP or range.


Example: `nmap -A 192.168.1.1/24`


1. `nmap -T<0-5> <target IP or range>`: This command specifies the timing template to use for the scan. The options range from T0 (paranoid) to T5 (insane), with higher values resulting in faster scans but potentially less accurate results.


Example: `nmap -T4 192.168.1.1/24`


Above are just a few examples of the many Nmap commands that are available. For more information and a complete list of options, you can refer to the Nmap documentation or use the `--help` option.



# TryHackMe-Pickle-Rick
This is a writeup for Pickle Rick TryHackMe machine!  
https://tryhackme.com/room/picklerick

## Scanning & Enumeration

To begin with this machine, we will perform a nmap scan on our target:

```
nmap -sV -sC -T5 10.10.122.153
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-04-26 01:01 EDT
Nmap scan report for 10.10.122.153
Host is up (0.16s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 79:d6:1c:ca:5e:5c:3f:14:83:d4:21:07:b1:01:76:c1 (RSA)
|   256 61:2e:22:78:78:b9:d7:7e:c9:77:2d:d3:29:07:07:da (ECDSA)
|_  256 07:64:71:80:17:56:a6:79:61:bd:d4:09:f3:85:15:4d (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Rick is sup4r cool
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

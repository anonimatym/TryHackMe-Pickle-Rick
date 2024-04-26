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
## Service Enumeration
### Port 22 - SSH
### Port 80 - HTTP

Now we'll proceed to visit the website located at port 80
![image_2024-04-26_001448881](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/028603ac-1f72-4fd7-8b5a-83b00aa96054)
This is the first thing we see on the website, if we take a view on the page source we can find this
![image_2024-04-26_001731557](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/01135460-5b08-40c8-bfb3-cae0ffbea415)
Looks like we found an username, next, we will proceed to find hidden directories that may have been hidden from us
## Directory Fuzzing
## Using Gobuster
**Gobuster** is a tool used to brute-force:
* URIs (directories and files) in web sites.
* DNS subdomains (with wildcard support).
* Virtual Host names on target web servers.
* Open Amazon S3 buckets.
* Open Google Cloud buckets.
* TFTP servers
#### If you wish to install Gobuster, here is the link to the official github repository: https://github.com/OJ/gobuster
```
gobuster dir -u 'http://10.10.122.153/' -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 200 --no-error -x php,sh,txt,cgi,html,css,js,py
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.122.153/
[+] Method:                  GET
[+] Threads:                 200
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php,sh,
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/login.php            (Status: 200) [Size: 882]
/.php                 (Status: 403) [Size: 278]
/.                    (Status: 200) [Size: 1062]
/assets               (Status: 301) [Size: 315] [--> http://10.10.122.153/assets/]
/portal.php           (Status: 302) [Size: 0] [--> /login.php]
```
This are some directory gobuster could find for us, we could be interested in **/login.php** so we will navigate into it
![image_2024-04-26_003529817](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/bbb9309b-3f13-431f-9c04-31f441a966f2)
We will find a login page, we already have the username but we lack the password, so we can keep with the directory fuzzing
At some point, we will encounter a file called **robots.txt**, most websites will have a robots.txt file which basically tells the sitee what it is allowed and not allowed to index.
If we check the robots.txt file we will encounter
![image_2024-04-26_003953275](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/f874ffe2-2943-4d26-991e-d1a19f75a39b)
This could be the password for our **/login.php** so lets try
![image_2024-04-26_004100740](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/a0f5cb3e-e8eb-40fd-949e-1416b55078db)
And we were right, it was the password for our **/login.php**

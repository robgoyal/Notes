# Website
Promotional page for a templating website.

![[Pasted image 20220209182649.png]]

Server header indicates that the website is hosted by nginx.

![[Pasted image 20220209182634.png]]

![[Pasted image 20220209182813.png]]

Application is built using Vue and contains a reference to an API hosted at api-prod. Unfortunately, we cannot reference this even if we added an entry for horizontall.htb. We need to add the same entry for api-prod.horizontall.htb.

`10.129.138.23   horizontall.htb api-prod.horizontall.htb

Browsing to /reviews on the website, we get 

![[Pasted image 20220209183105.png]]

Let's use gobuster to enumerate more of these endpoints

```
┌──(voldemort㉿voldemort)-[~/Documents/hacking/htb/machines]
└─$ gobuster dir -u http://api-prod.horizontall.htb -w /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt -t 15
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://api-prod.horizontall.htb
[+] Method:                  GET
[+] Threads:                 15
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/02/09 18:31:51 Starting gobuster in directory enumeration mode
===============================================================
/admin                (Status: 200) [Size: 854]
/Admin                (Status: 200) [Size: 854]
/users                (Status: 403) [Size: 60] 
/reviews              (Status: 200) [Size: 507]
/.                    (Status: 200) [Size: 413]
/ADMIN                (Status: 200) [Size: 854]
/Users                (Status: 403) [Size: 60] 
/Reviews              (Status: 200) [Size: 507]
```

We're forbidden from accessing `/users` so let's check out `/admin`.

Browsing there, we're presented with

![[Pasted image 20220209183327.png]]

Look's like Strapi is a CMS found here. https://strapi.cms blah blah blah

At first I tried to find the version but I couldn't find anything in the page source or the related JS applicationf iles. Let's move on to some potential vulnerabiliites. 

## Vulernabilities
Quick Google search for "Strapi CMS vulnerabilities" led to the following [exploit](https://www.exploit-db.com/exploits/50239). 

Searching through the exploit code, we come across the following block

```py
def check_version():
    global url
    print("[+] Checking Strapi CMS Version running")
    version = requests.get(f"{url}/admin/init").text
    version = json.loads(version)
    version = version["data"]["strapiVersion"]
    if version == "3.0.0-beta.17.4":
        print("[+] Seems like the exploit will work!!!\n[+] Executing exploit\n\n")
    else:
        print("[-] Version mismatch trying the exploit anyway")
```

Browsing to `http://api-prod.horizontall.htb/admin/init`, it looks like the CMS is vulnerable to this RCE exploit.

![[Pasted image 20220209184623.png]]
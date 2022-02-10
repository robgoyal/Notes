# Domain
Occasionally, a target will redirect you to its domain if you specified the IP address. Since the domain name cannot be resolved on root/authoritative DNS servers, you'll have to add the entry to your /etc/hosts file with the IP address. 

![[Pasted image 20220209175729.png]]

The above request to 10.129.138.23 redirects us to horizontall.htb but our host does not know where this doman is supposed to point to. However, the hosted site will redirect us until the [[Virtual Hosts|Host]] Request header is horizontall.htb. 

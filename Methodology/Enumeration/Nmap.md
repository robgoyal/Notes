# Nmap

Versatile tool to perform host discovery, ping sweeps, service and version scanning, and more. 

Without root privileges, nmap cannot craft it's own packets using the kernel and needs to use the OS to send packets. IF nmap is run with root privileges, it can use scanning modes such as TCP SYN scan or TCP ACK scans. 

[[#Practical Usage]]


## Scanning
Nmap completes its scans in several stages. 

1. Host Discovery
2. Service Detection
3. Version Scanning

### Host Discovery

Two types of host discovery scans:
1. Privileged scan
2. Unprivileged scan

With an **unprivileged scan**, nmap sends the following packets by default for host discovery.
1. TCP connect to **port 443**
2. TCP connect to **port 80**

With a **privileged scan**, nmap sends the following packets by default for host discovery.
1. ARP request (**only sent if on the same subnet; ignores remaining packets**)
2. TCP SYN packet to **port 443**
3. TCP ACK packet to **port 80**
4. ICMP echo request
5. ICMP timestamp request


If ANY of the packets return a response, nmap will consider the host to be alive and proceed with a service detection scan. 

To skip host discovery and proceed with service scanning, use the `-Pn` flag.

There are improved  [[#Improved Host Discovery | host discovery]] techniques to identify active hosts.

### Service Scanning
To skip service scanning, use the `-sn` flag. From here, we'll ignore the host discovery stage and skip over it in all examples.

Basic scan.

```
$ nmap -Pn <host>
```

By default, if you don't specify any ports, nmap performs a scan of the most frequent 1000 ports. 

#### Types of Service Scans
##### TCP Connect Scan
Default scan when nmap is invoked without root privileges. A connect scan will perform the full TCP connection.

```
$ nmap -Pn -sT -p <port list> <host>
```

To force a TCP Connect scan, use the `-sT` flag.

##### TCP Syn Scan
Default scan nmap is invoked with root privileges. A syn scan will send a RST if an ACK is received not performing the full scan.

```
$ sudo nmap -Pn -sS -p <port list> <host>
```

To force a TCP SYN scan, use the `-sS` flag.

**Advantage of SYN scans is that since there isn't a full TCP connect, the connection is not logged.**

##### UDP Scan
```
$ sudo nmap -Pn -sU -p <port list> <host>
```

UDP scans are unique since they may not respond unless a correctly-formed payload is received for that service. For common ports, nmap will send protocol-specific payloads but for other ports, the packet is empty.

Another challenge for UDP is that some OS' limit ICMP port unreachable messages to one per second (sent when a port is closed).

### Version Detection
Nmap associates the port with the expected service based on information in the `nmap-services` file. However, it's possible that the service deployed on port 80 (e.g) is not a web server but an SSH server. 

```
$ sudo nmap -Pn -sV <host>
```

Without version detection, nmap will state it's a web server. With version detection, nmap will send probes to inquire if it is in fact HTTP as well as the application name, version number, hostname device type, etc. 

Nmap will use probes specified in `nmap-services-probes` file.

### NSE
NSE executes scripts associated with a port or a service with a wide range of categories. Some categories are safe and interact with the service normally while other scripts can be used for vulnerability exploitation. 

To run the default scripts which consist of potentially intrusive scripts.

```
$ sudo nmap -sC <host>
```

To run a category of scripts, directory, filename, expression

```
$ sudo nmap --script "default or safe" <host>
$ sudo nmap --script "default and safe" <host>
$ sudo nmap --script "http-*" <host>
$ sudo nmap --script "not intrusive" <host>
$ sudo nmap --script "(default or safe or instrusive) and not http-*" <host>
```

Some scripts require arguments which can be specified using `--script-args <n1>=<v1`

These scripts are located in script.db but can also be located in depth at https://nmap.org/nsedoc/.


## General Options
### Verbose
To enable verbose mode, use the `-v` flag. This will also display open ports as nmap discovers them.

### OS Detection
Use the `-O` flag to perform OS detection but this is not always accurate.

### Specifying Ports
To scan port X, run this command

```
$ nmap -p X <host>
```

To scan a range of ports X-Y, run this command

```
$ nmap -p X-Y <host>
```

To scan all ports, run this command

```
$ nmap -p- <host>
```

### Specifying Hosts
Specify a single host

```
$ sudo nmap 192.168.0.1
```

Specify hosts in a subnet

```
$ sudo nmap 192.168.0.1/24
```

Specify a range of hosts

```
$ sudo nmap 192.168.0.1-25
$ sudo nmap 192.168.0.1,2
$ sudo nmap 192.168.0,1.1-25 => 192.168.0.1-25, 192.168.1.1-25
```

Specify hosts by input list

```
$ sudo nmap -iL hosts.txt
```

### Output Formats
Output by XML format

```
$ sudo nmap -oX scan.xml <host>
```

Output by greppable nmap format

```
$ sudo nmap -oG scan.gnmap <host>
```

Output by standard nmap output

```
$ sudo nmap -oN scan.nmap <host>
```

Output by all of the above (**don't need to specify extension**)

```
$ sudo nmap -oA scan <host>
```

### Adjusting Speed
Use the `--min-rate=<num>` flag to adjust the minimum number of packets sent per second. **Note**: This can potentially crash your target or be an opsec concern by being flagged in logs or IDS systems. 

Conversely, there is a `--max-rate=<num>` which limits the maximum number of packets sent.

By default, nmap has a dynamic timing mechanism to determine the appropriate speed for a network. Only use these options if you're sure that this won't crash the service or affect the accuracy if a network cannot support a faster scanning rate.

### Timing 
Use the `-T#` flag to specify the timing profile. 

- T0 (Paranoid)
- T1 (Sneaky)
- T2 (Polite)
- T3 (Normal)
- T4 (Aggressive)
- T5 (Insane)

T4 should be the typical timing template if on an ethernet connection. 

Notes:
- T3 to T5 consists of parallel port scans
- T5 can lead to loss of accuracy and crash systems

### Specify Hosts
## Practical Usage
### Improved Host Discovery
Typically useful when performing a network sweep and not an individual host.

```
$ sudo nmap -PS21,22,23,25,53,80,110,111,135,137,139,143,443,445,502,993,995,1433,1434,1723,3306,3389,5900,8080 -PE -PP -sn <hosts>
```

* Skip service scanning
* Send TCP SYN packets to all ports in the specified port list
* ICMP echo request
* ICMP timestamp request

Uses highly common ports to determine if a host is up rather than port 80 and 443 by default.

### Service Scanning
```
$ sudo nmap -p- -T4 --min-rate=1000 -oA tcp_all_ports <host>
```

### Version Scanning
Once you've identified the appropriate ports that are up and accessible, perform a version scan with some default scripts. 

```
$ sudo nmap -p <port list> -sC -sV -O -T4 --min-rate=1000 -oA tcp_subset_ports_sc-sv <host>
```


# References
https://nmap.org/nsedoc/


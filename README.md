# craft
### Write up for the craft machine from hackthebox

I fully explored a couple of rabbitholes that led nowhere; and that's okay as I am learning what rabbitholes look like (maybe... feel like?) , and learning to trust my instincts developing my skillset.

nmaps
```
nmap -p- -sT --min-rate=10000 -oN alltcp.nmap 10.10.10.110
Starting Nmap 7.70 ( https://nmap.org ) at 2019-07-27 12:12 CDT
Warning: 10.10.10.110 giving up on port because retransmission cap hit (10).
Nmap scan report for craft.htb (10.10.10.110)
Host is up (0.059s latency).
Not shown: 65304 closed ports, 228 filtered ports
PORT     STATE SERVICE
22/tcp   open  ssh
443/tcp  open  https
6022/tcp open  x11
```
```
nmap -sV -sC -p 22,443,6022 -oN found.nmap 10.10.10.110
Starting Nmap 7.70 ( https://nmap.org ) at 2019-07-27 12:16 CDT
Nmap scan report for craft.htb (10.10.10.110)
Host is up (0.056s latency).

PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 7.4p1 Debian 10+deb9u5 (protocol 2.0)
| ssh-hostkey: 
|   2048 bd:e7:6c:22:81:7a:db:3e:c0:f0:73:1d:f3:af:77:65 (RSA)
|   256 82:b5:f9:d1:95:3b:6d:80:0f:35:91:86:2d:b3:d7:66 (ECDSA)
|_  256 28:3b:26:18:ec:df:b3:36:85:9c:27:54:8d:8c:e1:33 (ED25519)
443/tcp  open  ssl/http nginx 1.15.8
|_http-server-header: nginx/1.15.8
|_http-title: About
| ssl-cert: Subject: commonName=craft.htb/organizationName=Craft/stateOrProvinceName=NY/countryName=US
| Not valid before: 2019-02-06T02:25:47
|_Not valid after:  2020-06-20T02:25:47
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|_  http/1.1
| tls-nextprotoneg: 
|_  http/1.1
6022/tcp open  ssh      (protocol 2.0)
| fingerprint-strings: 
|   NULL: 
|_    SSH-2.0-Go
| ssh-hostkey: 
|_  2048 5b:cc:bf:f1:a1:8f:72:b0:c0:fb:df:a3:01:dc:a6:fb (RSA)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port6022-TCP:V=7.70%I=7%D=7/27%Time=5D3C86ED%P=x86_64-pc-linux-gnu%r(NU
SF:LL,C,"SSH-2\.0-Go\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 45.37 seconds
```
### Lessons Learned
- learned a lot about modules, packages, and `import` in python. This was nice.
- I read almost every bit of code in this project looking for ways to leak data. It helped out a ton.
- I am starting to develop a good methodology for note-taking. A bit of refining is needed, but I like how I can organizedata in cherrytree.

# craft.htb
### Write up for the craft machine from hackthebox
<img width="302" alt="Screen Shot 2019-08-09 at 9 20 32 AM" src="https://user-images.githubusercontent.com/46615118/62785902-283dc980-ba87-11e9-9973-13ed7359cdf5.png">

This has been my favorite box so far, mainly due to it's realism. The path to root was logical once you understood how the app worked and how to interact with it in order to get the output that you desired. I tested local proofs of concept with modifications to some of the scripts on the machine in order to progress towards root.

We start off by enumerating the hell out of everything that we're given and find a [Gogs](https://gogs.io) repo that leaks credentials via commit history. We also find sloppy coding practices and are able to exploit them with the creds we found in order to get a reverse shell. We are jailed, but can upload our own modified versions of scripts we find on the box in order to steal more creds. Logging back into Gogs with one of the new creds we find a private repo with ssh keys and app info that allows us to own root ... if you read the app documentation.

### Scan and Basic Recon

I ran an nmap scan on all TCP/UDP ports. The UDP scan came back empty. The TCP scan showed
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
I then ran a safe script and version scan on the above ports, and it returned the following
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
We see https, ssh, and a weird ssh thing I have never seen before on `6022/tcp`. There is also hostname info in the `443/tcp` results; I add `craft.htb` to `/etc/hosts` before enumerating further. From the cert on 443 we get a potential user, `admin@craft.htb`.

I started gobusting/dirb'ing `https://10.10.10.110/` at this point but it kept throwing errors. I assume it's my fault, but before attemting to fix, I visit `https://10.10.10.110/` in a browser and found the following:

![Screenshot from 2019-07-27 12-14-22](https://user-images.githubusercontent.com/46615118/62792345-af456e80-ba94-11e9-82a7-f3a89d926a46.png)

Clicking the links for api and the git icon return `404 Not Found` errors. Hovering over the links we find 2 new subdomains: `api.craft.htb` and `gogs.craft.htb`. Adding those to `/etc/hosts` gets me past the 404 errors.



### Lessons Learned
- learned a lot about modules, packages, and `import` in python. This was nice.
- I read almost every bit of code in this project looking for ways to leak data. It helped out a ton.
- I am starting to develop a good methodology for note-taking. A bit of refining is needed, but I like how I can organizedata in cherrytree.

<img width="540" alt="Screen Shot 2019-08-09 at 9 18 23 AM" src="https://user-images.githubusercontent.com/46615118/62785900-283dc980-ba87-11e9-8fec-4ace2403efbf.png">
<img width="540" alt="Screen Shot 2019-08-09 at 9 19 04 AM" src="https://user-images.githubusercontent.com/46615118/62785901-283dc980-ba87-11e9-815c-fdb39dbce1af.png">


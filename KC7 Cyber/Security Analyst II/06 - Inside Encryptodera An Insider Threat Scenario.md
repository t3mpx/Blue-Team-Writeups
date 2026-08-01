---
tags:
  - kc7 
urls: https://kc7cyber.com/challenges/145
---
---

## SECTION 1: OFFENSIVE ODOR

`1. What is Barry's role at the company?`
```
// Gets information about specified employee
Employees
| where name == "Barry Shmelly"
```

ANSWER: StackOverflow Copy Paster

`2. What is Barry's email address?`
```
SAME QUERY AS NUMBER 1
```

ANSWER: barry_shmelly@encryptoderafinancial.com

`3. What was the subject of the interesting email (the one on January 16th) that Barry sent?`
```
// Gets emails sent by specified sender in between the specified timestamp
Email
| where sender == "barry_shmelly@encryptoderafinancial.com"
| where timestamp between (datetime(2024-01-16) .. datetime(2024-01-17))
```

ANSWER: I'm not coming in today. I'm sick of this place. We're all getting laid off anyway.

`4. What was the role of the employees that received Barry's email?`
```
// Gets the roles of the specified employees
Employees
| where email_addr in ("christopher_naylor@encryptoderafinancial.com", "michelle_collins@encryptoderafinancial.com", "jarrod_rodriguez@encryptoderafinancial.com")
| project role
```

ANSWER: Social Media Manager

`5. What was the role of the recipient of that email?`
```
// Gets email that has specified subject
Email
| where sender == "barry_shmelly@encryptoderafinancial.com"
| where subject == "YOU ARE A GREEDY PIG!!!! WHAT IS WRONG WITH YOU?????"
```

Use the email to get information from the Employees table:
```
// Gets information about the employee that has the specified email
Employees
| where email_addr == "les_goh@encryptoderafinancial.com"
```

ANSWER: Chief Executive Officer

`6. What's Barry's IP address? (Paste the full IP address )`
```
// Gets information about the specified employee
Employees
| where name == "Barry Shmelly"
```

ANSWER: 10.10.0.1

`7. What was the complete URL that Barry was browsing on his computer regarding Cybersecurity Insiders on the afternoon of December 26th?(Paste the full url)`
```
// Gets outbound connections from specified source IP between dates
OutboundNetworkEvents
| where timestamp between (datetime(2023-12-26) .. datetime(2023-12-27))
| where src_ip == "10.10.0.1"
```

ANSWER: https://www.cybersecurity-insiders.com/safe-ways-to-transfer-sensitive-files

`8. What website did he visit first on January 15th? (Paste the full URL)`
```
// Gets outbound connections from specified source IP between dates
OutboundNetworkEvents
| where timestamp between (datetime(2024-01-15) .. datetime(2024-01-16))
| where src_ip == "10.10.0.1"
| take 1
```

ANSWER: https://www.7-zip.org/a/7z2002-x64.exe

`9. Could you provide the full URL for the website Barry searched for USB Flash Drives?`
```
// Gets outbound connections from specified source IP where the URL mentions USB
OutboundNetworkEvents
| where src_ip == "10.10.0.1"
| where url contains "USB"
```

ANSWER: https://www.wikihow.com/Use-a-USB-Flash-Drive

`10. What "secret" document on business transactions did Barry download?`
```
// Gets files created on specified host after specified date
FileCreationEvents
| where hostname == "IGOY-DESKTOP"
| where timestamp > datetime(2024-01-15)
```

ANSWER: SECRET_MergersAndAcquisitions_Strategy2025.docx

`11. What document (docx) did Barry download about salaries?`
```
SAME QUERY AS NUMBER 10
```

ANSWER: ExecutiveSalaryNegotiations.docx

`12. What document (zip) did Barry download to get this?
```
SAME QUERY AS NUMBER 10
```

ANSWER: Encryptodera_Proprietary_Algorithms.zip

`13. Do you know the password he used to zip the files?`
```
// Gets processes that mention 7z in the specified computer
ProcessEvents
| where hostname == "IGOY-DESKTOP"
| where timestamp > datetime(2024-01-15)
| where parent_process_name contains "7z" or process_commandline contains "7z" or process_name contains "7z"
```

ANSWER: securePass123

`14. What is the name of the drive on which Barry stored the final files?`
```
// Get processes mentioning a drive letter other than C using regex searching from D-Z using @ to not have to escape backslashes
let drive_letters = @"[D-Z]:\\";
ProcessEvents
| where hostname == "IGOY-DESKTOP"
| where timestamp > datetime(2024-01-15)
| where process_commandline matches regex drive_letters
```

ANSWER: SchmellyDrive

`15. Type gotheem to take credit`

ANSWER: gotheem

## SECTION 2: CRYPTO CONQUEST

`1. What is the filename of this note?`

ANSWER: YOU_GOT_CRYTOED_SO_GIMME_CRYPTO.txt

`2. What kind of attack is this?`

ANSWER: Ransomware

`3. On how many machines was this .txt file seen?`
```
// Gets how many machines have the specified file
FileCreationEvents
| where filename == "YOU_GOT_CRYTOED_SO_GIMME_CRYPTO.txt"
| distinct hostname
| count
```

ANSWER: 306

`4. What time was the ransom note first seen?`
```
// Gets machines that have the specified file orderding from earliest to lastest
FileCreationEvents
| where filename == "YOU_GOT_CRYTOED_SO_GIMME_CRYPTO.txt"
| order by timestamp asc
```

ANSWER: 2024-02-17 02:34:54+00:00

`5. What is the hostname of the system where the ransom note was first seen?`
```
SAME QUERY AS NUMBER 4
```

ANSWER: UL8R-MACHINE

`6. How many files were encrypted on this machine?`
First grab the extension of the encrypted files:
```
// Gets files created in specified host after specified time
FileCreationEvents
| where hostname == "UL8R-MACHINE"
| where timestamp > datetime(2024-02-17)
```

Then look for files ending in that extension:
```
// Gets files ending in the specified extension
FileCreationEvents
| where hostname == "UL8R-MACHINE"
| where filename endswith "umadbro"
```

ANSWER: 50

`7. What is the extension that was used on the encrypted files?`
```
// Gets files created in specified host after specified time
FileCreationEvents
| where hostname == "UL8R-MACHINE"
| where timestamp > datetime(2024-02-17)
```

ANSWER: umadbro

`8. What command was run that references the ransomware extension?`
```
// Gets processes that have the exact "umadbro" word
ProcessEvents
| where hostname == "UL8R-MACHINE"
| where process_commandline has "umadbro"
```

ANSWER: start /b C:\\\\ProgramData\\\\files_go_byebye.exe -encrypt -target C:\\\\Users\\\\ -ext .umadbro

`9. When did files_go_byebye.exe appear on this machine?`
```
// Gets file information from specified filename
FileCreationEvents
| where hostname == "UL8R-MACHINE"
| where filename == "files_go_byebye.exe"
```

ANSWER: 2024-02-17 02:30:50+00:00

`10. How many commands were run on UL8R-MACHINE during this timeframe?`
```
// Gets processes between timestamps
ProcessEvents
| where hostname == "UL8R-MACHINE"
| where timestamp between (datetime("2024-02-16") .. datetime("2024-02-18"))
```

ANSWER: 23

`11. What domain does the encoded PowerShell reference?`
```
SAME QUERY AS NUMBER 10
```

Look for the encoded powershell command

ANSWER: notification-finance-services.com

`12. What command is run right before the base64-encoded PowerShell?`

ANSWER: gpupdate /force

`13. How many devices ran the gpupdate /force command?`
```
// Gets how many computers ran the specified command
ProcessEvents
| where process_commandline == "gpupdate /force"
| distinct hostname
| count
```

ANSWER: 306

`14. How many machines at Encryptodera ran "systeminfo"?`
```
// Gets how many computers ran the specified command
ProcessEvents
| where process_commandline == "systeminfo"
| distinct hostname
| count
```

ANSWER: 8

`15. What was the timestamp for the first time the command was run?`
```
SAME QUERY AS NUMBER 14
```

ANSWER: 2024-02-02 03:32:36+00:00

`16. How many days elapsed between when the attackers ran discovery commands and when the ransomware attack started?`

ANSWER: 15

`17. What is the hostname of the device on which the attackers first ran systeminfo?`
```
SAME QUERY AS NUMBER 15
```

ANSWER: 41QI-LAPTOP

`18. What was the full commandline used by the threat actor when running nltest /dclist? (paste the full commandline)`
```
// Gets information about specified command ran
ProcessEvents
| where hostname == "41QI-LAPTOP"
| where process_commandline contains "nltest /dclist"
```

ANSWER: cmd.exe /C nltest /dclist:encryptoderafinancial.com

`19. What is the full name of the .xlsx.exe file on 41QI-LAPTOP?`
```
// Gets files ending with the specified extension
FileCreationEvents
| where hostname == "41QI-LAPTOP"
| where filename endswith ".xlsx.exe"
```

ANSWER: Company_Financials_Q1_2024_Review.xlsx.exe

`20. What file shows up a few seconds after the .xlsx.exe file?`
```
// Gets files created after specified timestamp
FileCreationEvents
| where hostname == "41QI-LAPTOP"
| where timestamp > datetime(2024-02-01 08:50:12)
```

ANSWER: screenconnect_client.exe

`21. How many devices does screenconnect_client.exe appear on?`
```
// Gets hostnames that have the specified file
FileCreationEvents
| where filename == "screenconnect_client.exe"
| distinct hostname
```

ANSWER: 3

`22. Check the Email logs to see if the .xlsx.exe file was sent in a link. What email address was used to send this file?`
```
// Gets emails that mention the specified word in their link
Email
| where link contains "Company_Financials_Q1_2024_Review.xlsx.exe"
```

ANSWER: barry_shmelly@encryptoderafinancial.com

`23. How many unusual emails were sent by Barry?`
```
// Gets emails sent by the specified sender after specified timestamp
Email
| where sender == "barry_shmelly@encryptoderafinancial.com"
| where timestamp > datetime(2024-02-01)
```

ANSWER: 9

`24. Type got it once you've made a note of these recipients.`

ANSWER : got it

`25. What IP was used to sign into Barry's account on February 1st?`
```
//Gets authentication evens from specified user in specified date
AuthenticationEvents
| where username == "bashmelly"
| where timestamp == datetime(2024-02-01)
```

ANSWER: 143.38.175.105

`26. How many other accounts did that IP log into?`
```
// Gets authentication events from specified IP
AuthenticationEvents
| where src_ip == "143.38.175.105"
```

ANSWER: 0

`27. How many IPs logged in to all 8 devices where the attacker ran systeminfo?`
```
// Creates a temporary variable holding the hostnames that ran "systeminfo" then looks for authentication events grouping them by IP source
let hosts = ProcessEvents
| where process_commandline has "systeminfo"
| distinct hostname;
AuthenticationEvents
| where hostname in (hosts)
| summarize dcount(hostname) by src_ip
| order by dcount_hostname desc
```

ANSWER: 2

`28. What is the role of the employee who this IP address belongs to?`
```
// Gets information about the employee with the specified IP
What is the role of the employee who this IP address belongs to?
```

ANSWER: System Administrator

`29. How many successful logins were made from this IP?`
```
// Gets total successful logins from specified IP
AuthenticationEvents
| where src_ip == "10.10.0.138"
| where result == "Successful Login"
| count
```

ANSWER: 554

`30. What is the hostname of the server the attackers logged into?`
```
// Gets what servers the specified IP logged
AuthenticationEvents
| where src_ip == "10.10.0.138"
| where result == "Successful Login"
| distinct hostname
```

ANSWER: DOMAIN_CONTROLLER_SERVER

`31. Type f in chat to pay respect and continue to the next section!`

ANSWER: f

## SECTION 3: F IN THE CHAT

`1. What username was used to log into the DOMAIN_CONTROLLER_SERVER?`
```
// Gets information about authentication events from specified IP and specified host
AuthenticationEvents
| where src_ip == "10.10.0.138"
| where result == "Successful Login"
| where hostname == "DOMAIN_CONTROLLER_SERVER"
```

ANSWER: lihenry_domain_admin

`2. What laptop did the lihenry_domain_admin account sign into? (Enter the hostname)`
```
// Gets authentication events from specified user
AuthenticationEvents
| where username == "lihenry_domain_admin"
```

ANSWER: GJ95-LAPTOP

`3. What is the MITRE ATT&CK ID for mimikatz?`

Search for "MITRE ATT&CK ID mimikatz" on Google.

ANSWER: S0002

`4. Did the threat actor run mimikatz on this device? If so, enter the command line the attacker ran. If not, enter no.`
```
// Gets if specified hostname has any process that mentions "mimikatz"
ProcessEvents
| where hostname == "GJ95-LAPTOP"
| where process_commandline contains "mimikatz"
```

ANSWER: `totally_not_mimikatz.exe \"sekurlsa::logonpasswords\"`

`5. Who does this device belong to? (Enter the employee's name)`

Get the username from the number 4 query.

Get the name:
```
// Gets the name of provided username
Employees
| where username == "vaorozco"
| project name
```

ANSWER: Valerie Orozco

`6. Was Valerie Orozco targeted in the phishing emails sent from Barry Shmelly?`

ANSWER: yes

`7. What is the name of the file that was sent to Valerie in the phishing email?`
```
// Gets emails sent from specified sender to specified recipient
Email
| where recipient == "valerie_orozco@encryptoderafinancial.com"
| where sender == "barry_shmelly@encryptoderafinancial.com"
```

ANSWER: Employee_Contact_List_Updated_March_2024.docx.exe

`8. Did Valerie click the link? If so, enter the timestamp when she clicked the link. If not, enter 'no'`
```
// Gets outbound requests from specified source IP to specified URL
OutboundNetworkEvents
| where src_ip == "10.10.0.18"
| where url contains "Employee_Contact_List_Updated_March_2024.docx.exe"
```

ANSWER: No

`9. How many different user accounts logged into Valerie's machine?`
```
// Gets unique users that authenticated against the machine
AuthenticationEvents
| where hostname == "GJ95-LAPTOP"
| distinct username
```

ANSWER: 3

`10. How many unique hosts did this user account attempt to log into?`
```
// Gets unique computers that a user authenticated against
AuthenticationEvents
| where username == "systadmi_local_admin"
| distinct hostname
```

`11. Which user NOT in an IT role was improperly using the systadmi_local_admin credentials? (This is likely a sign of compromise)`
```
// Gets hosts that have the remote access tool screenconnects and looks for authentications from the user "systadmi" then gets the employees that used it
let hosts = FileCreationEvents
| where filename has "screenconnect"
| distinct hostname;
AuthenticationEvents
| where hostname in (hosts)
| where username has "systadmi"
| where result has "Successful"
| join (
    Employees 
    | project ip_addr,role,email_addr,name
) on $left.src_ip==$right.ip_addr
| project SourceIpName=name, a="who is a", SourceIpUserRole=role, b="logged onto",hostname, c="using", username, d="at",timestamp
```

ANSWER: Robin Kirby

`12. When was Robin phished by Barry Shmelly's account?`
```
// Gets emails sent from specified sender to specified recipient
Email
| where recipient == "robin_kirby@encryptoderafinancial.com"
| where sender == "barry_shmelly@encryptoderafinancial.com"
```

ANSWER: 2024-02-01T03:59:30Z

`13. Type letsgo to move on!`

ANSWER: letsgo

## SECTION 4: A NETWORK MYSTERY

`1. Which IP address received the largest amount of data on Feb 5th?`
```
// Sums the numbers of bytes from each destination IP and creates a new column to hold the values (https://stackoverflow.com/questions/67611844/how-to-use-sum-within-summarize-in-a-kql-query)
NetworkFlow
| where timestamp between (datetime(2024-02-05) .. datetime(2024-02-06)) 
| summarize total_bytes = sum(bytes) by dest_ip
| order by total_bytes desc
```

ANSWER: 182.56.23.121

`2. How many bytes of data were sent to that IP on the 5th?`

ANSWER: 12716

`3. When was data first sent to this IP? (paste the full timestamp)`
```
// Gets the first instance of data sent to specified IP
NetworkFlow
| where dest_ip == "182.56.23.121"
| order by timestamp asc
```

ANSWER: 2024-01-21T13:28:33Z

`4. On how many distinct days have we sent data to this IP?`
```
// Gets unique timestamps of data sent to sepcified IP
NetworkFlow
| where dest_ip == "182.56.23.121"
| distinct timestamp
```

ANSWER: 27

`5. What service is used for the port to which this data is being transferred?`

ANSWER: FTP

`6. What is the total amount of data transferred to this IP address?`
```
// Sums the number of bytes from all the connections
NetworkFlow
| where dest_ip == "182.56.23.121"
| summarize total_bytes = sum(bytes) by dest_ip
| order by total_bytes desc
```

ANSWER: 208138

`7. How many distinct employees have sent data to this IP address?`
```
// Gets unique source IPs  that contacted the specified address
NetworkFlow
| where dest_ip == "182.56.23.121"
| distinct src_ip
```

ANSWER: 1

`8. Whose name is linked to that IP address? Provide the employee's name.`
```
// Gets the employee's information based on the IP that contacted the specified IP
NetworkFlow
| where dest_ip == "182.56.23.121"
| join ( Employees )
on $left.src_ip == $right.ip_addr
```

ANSWER: Jane Smith

`9. What is that employee's role?`
```
SAME QUERY AS NUMBER 8
```

ANSWER: Cryto Bruh (Blockchain Contractor)

`10. We see her looking for the location of the company's __ __ __ __ (4 words)`
```
// Gets inbound connections from specified IP
InboundNetworkEvents
| where src_ip == "10.10.0.2"
| where url contains "encryptodera"
```

ANSWER: cold storage crypto wallets

`11. Who was Jane having a suspicious conversation with? (email address)`
```
// Gets emails where sender is specified sender
Email
| where sender == "jane_smith@encryptoderafinancial.com"
| where timestamp > datetime(2024-01-19)
```

ANSWER: elboss@westealurcrypto.com

`12. What IP address did the boss man provide to help with smuggling the data?`
```
// Gets emails where sender is specified sender
Email
| where sender == "elboss@westealurcrypto.com"
| where timestamp > datetime(2024-01-19)
```

ANSWER: 182.56.23.121

`13. What is the name of the data exfil tool Jane downloads to help with her operation?`
```
// Gets files created in specified computer
FileCreationEvents
| where hostname == "GOTI-LAPTOP"
| where timestamp > datetime(2024-01-19)
```

ANSWER: ftp_client.exe

`14. What is the name of the crypto theft tool Jane downloads to help with her operation?`
```
SAME QUERY AS NUMBER 14
```

ANSWER: crypto_stealer.exe

`15. To what path does Jane point her data exfiltration tool?`
```
// Gets process events in specified computer
ProcessEvents
| where hostname == "GOTI-LAPTOP"
| where timestamp > datetime(2024-01-21)
```

Grab the Base-64 and decode it.

ANSWER:  C:\\Users\\jasmith\\ToTheMoon\\;tothemoon

`16. At what tempo does she set the tool to run? (one word)`
```
SAME QUERY AS NUMBER 15
```

ANSWER: daily

`17. What password does Jane use for the tool?`
```
SAME QUERY AS NUMBER 15
```

ANSWER: Ugot2muchCRYTOw3llt4k3it0FFurH4ND5
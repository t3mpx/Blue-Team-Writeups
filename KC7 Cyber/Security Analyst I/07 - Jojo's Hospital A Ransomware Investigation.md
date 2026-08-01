---
tags:
  - kc7
urls: https://kc7cyber.com/challenges/195
---
---

## SECTION 1: CRYPTO - BUT THE BAD KIND

`1. How many hours did the hackers give the hospital to pay the ransom?`

ANSWER: 72 hours

`2. What was the name of the ransomware group?`

ANSWER: Lock Byte

`3. The slogan was: we spend your money, so ____`

ANSWER: you don't have to

`4. How much did the hackers ask the patients to pay?`

ANSWER: $10,000

`5. What very important unique identifier number did the ransomware operators threaten to release?`

ANSWER: social security number

`6. How many total files were encrypted at the hospital?`
```
// Gets files endin with `.encrypted`
FileCreationEvents
| where filename endswith ".encrypted"
| count
```

ANSWER: 6420

`7. How many unique hostnames had files encrypted on them?`
```
// Gets unique computers that have encrypted files on them
FileCreationEvents
| where filename endswith ".encrypted"
| distinct hostname
| count
```

ANSWER: 321

`8. What was the Sha256 hash of this ransom file?`
```
// Gets information about the specified file
FileCreationEvents
| where filename == "We_Have_Your_Data_Pay_Up.txt"
```

ANSWER: 97c348e95c8a8aeb8808f76434d73a92bbcb6b4586788365762b22624990b018

`9. What was the full path of this ransom file?`
```
SAME QUERY AS NUMBER 8
```

ANSWER: C:\Users\andavis\Documents\We_Have_Your_Data_Pay_Up.txt

`10. On how many hosts (machines) was this ransom file seen?`
```
// Gets unique computers that have the ransom note
FileCreationEvents
| where filename == "We_Have_Your_Data_Pay_Up.txt"
| count
```

ANSWER: 1

`11. What hostname was the ransom note seen on?`
```
SAME QUERY AS NUMBER 8
```

ANSWER: AMFB-MACHINE

`12. What is the name of the employee whose host has the ransom note?`
```
// Gets information about the owner of the specified hostname
Employees
| where hostname == "AMFB-MACHINE"
```

ANSWER: Anthony Davis

`13. Run the query above. How many process events were executed on Anthony's machine during this time period?`
```
// Gets a list of processes run between the specified dates
ProcessEvents
| where hostname == "AMFB-MACHINE"
| where timestamp between (datetime(2024-06-17) ..  datetime(2024-06-18))
```

ANSWER: 14

`14. What was the name of the ransomer file mentioned?`
```
SAME QUERY AS NUMBER 13
```

ANSWER: lockbyte_ransomer.exe

`15. When the attackers copied the ransomer file to the network share, what new name did they give it?`
```
SAME QUERY AS NUMBER 13
```

ANSWER: spread_ransomware.exe

`16. What tool did the attackers use to steal the data? This will be a .exe file`
```
SAME QUERY AS NUMBER 13
```

ANSWER: patient_data_exporter.exe

`17. What information did the attackers put into patient_data_1.zip? Provide the full path of the network share \\something\like\this`
```
SAME QUERY AS NUMBER 13
```

ANSWER: `\\jojos-hospital-server\important_data\patient_records`

`18. What information did the attackers put into patient_data_2.zip? Provide the full path of the network share \\something\like\this
```
SAME QUERY AS NUMBER 13
```

ANSWER: `\\jojos-hospital-server\important_data\archive\patient-records`

`19. What information did the attackers put into patient_data_3.zip? Provide the full path of the network share \\something\like\this`
```
SAME QUERY AS NUMBER 13
```

ANSWER: `\\jojos-hospital-server\important_data\old-patient-data`

`20. What domain (e.g. abcd.com) did the attackers send the stolen data to?`
```
SAME QUERY AS NUMBER 13
```

ANSWER: secure-health-access.com

`21. What command did they use to clear their tracks? Copy and paste the full command.`
```
SAME QUERY AS NUMBER 13
```

ANSWER: cmd.exe /c del C:\Users\andavis\Documents\patient_data_*.zip

`22. What domain was the patient data exporter file downloaded from?`
```
// Gets information about who accessed an URL
OutboundNetworkEvents
| where url has "patient_data_exporter.exe"
```

ANSWER: secure-health-access.com

`23. When was the patient data exporter file downloaded? (copy and paste the exact timestamp)`
```
SAME QUERY AS NUMBER 22
```

ANSWER: 2024-06-17T14:22:29Z

`24. How many distinct IPs does the domain secure-health-access.com resolve to?`
```
// Gets the total number of unique IPs a domain resolves to
PassiveDns
| where domain == "secure-health-access.com"
| distinct 
```

ANSWER: 2

`25. Which one of these IPs ends with the digit 1?`
```
SAME QUERY AS NUMBER 24
```

ANSWER: 203.0.113.1

`26. Which one of these IPs ends with the digit 2?`
```
SAME QUERY AS NUMBER 24
```

ANSWER: 203.0.113.2

`27. What additional domain name is associated with these IP addresses?`
```
// Creates a temporary variable holding the IPs of the domain and gets other domains sharing the IPs
let unique_ip = PassiveDns
| where domain == "secure-health-access.com"
| distinct ip;
PassiveDns
| where ip in (unique_ip)
| distinct domain
```

ANSWER: emr-help.net

`28. How many requests did the hackers make to our website from these IPs?`
```
// Creates a temporary variable holding the IPs of the domain and counts the requests made 
let unique_ip = PassiveDns
| where domain == "secure-health-access.com"
| distinct ip;
InboundNetworkEvents
| where src_ip in (unique_ip)
| count
```

ANSWER: 37

`29. The hackers were curious about how to bypass ___ at Jojo's hospital.`
```
// Creates a temporary variable holding the IPs of the domain and looks for instances of the "bypass" word in URLs
let unique_ip = PassiveDns
| where domain == "secure-health-access.com"
| distinct ip;
InboundNetworkEvents
| where src_ip in (unique_ip)
| where url has "bypass"
```

ANSWER: security

`30. What was the first web request the hackers made using the term patient? (hint: it was a search). Paste the full url.`
```
// Creates a temporary variable holding the IPs of the domain and looks for instances of the "patient" word in URLs
let unique_ip = PassiveDns
| where domain == "secure-health-access.com"
| distinct ip;
InboundNetworkEvents
| where src_ip in (unique_ip)
| where url has "patient"
```

ANSWER: https://jojoshospital.org/search=JoJo%27s+Hospital+patient+records

`31. When did this login occur?`
```
// Creates a temporary variable holding the IPs of the domain and looks for authentication events from the IPs
let unique_ip = PassiveDns
| where domain == "secure-health-access.com"
| distinct ip;
AuthenticationEvents
| where src_ip in (unique_ip)
```

ANSWER: 2024-05-20T00:00:00Z

`32. Which IP address did the actors use for the login?`
```
SAME QUERY AS NUMBER 31
```

ANSWER: 203.0.113.1

`33. Whose account did the hackers login to? (provide a first and last name)`
```
// Gets an employee's information based on the username
Employees
| where username == "andavis"
```

ANSWER: Anthony Davis

`34. Enter yikes to finish with this section`

ANSWER: yikes

## SECTION 2: SHARKS IN THE HOSPITAL WATER

`1. Whose credentials did the hackers use to access the hospital's network? (Enter first and last name)`

ANSWER: Anthony Davis

`2. What was the domain name observed in the sponsored search result?`

ANSWER: raisinkanes.com

`3. What is the legitimate domain for Raising Cane's?`

ANSWER: raisingcanes.com

`4. How many web requests do we see going to the fake raisinkanes domain?`
```
// Gets web requests to the specified domain
OutboundNetworkEvents
| where url contains "raisinkanes.com"
| count
```

ANSWER: 26

`5. How many unique employees were seen browsing to the fake raisinkanes domains? (hint distinct the src_ip)`
```
// Gets uniques employees accesing the URL
OutboundNetworkEvents
| where url contains "raisinkanes.com"
| distinct src_ip
| count
```

ANSWER: 24

`6. Which of the malicious domains used for redirection starts with the word "nothing"?`
```
// Gets web requests to domains starting with specified string
OutboundNetworkEvents
| where url startswith "https://nothing"
```

ANSWER: nothing-to-see-here.net

`7. Which of the malicious domains used for redirection starts with the word "totally"?`
```
// Gets web requests to domains starting with specified string
OutboundNetworkEvents
| where url startswith "https://totally"
```

ANSWER: totally-legit-domain.com

`8. What is the name of the docx file they are redirected to?`
```
SAME QUERY AS NUMBER 7
```

ANSWER: Raisin_Kane_Promo_Offer.docx

`9. What is the name of the pdf file they are redirected to?`
```
SAME QUERY AS NUMBER 7
```

ANSWER: Raisin_Kane_Free_Meal_Voucher.pdf

`10. What is the hostname of the first person to download the suspicious docx file?`
```
// Gets information related to the file in a host
FileCreationEvents
| where filename == "Raisin_Kane_Promo_Offer.docx"
```

ANSWER: RQJQ-MACHINE

`11. When did this download occur? (copy and paste the timestamp)`
```
SAME QUERY AS NUMBER 10
```

ANSWER: 2024-05-01 09:56:50+00:00

`12. What was the Sha256 hash of the file?`
```
SAME QUERY AS NUMBER 10
```

ANSWER: bd886046266b909a8ca5f19f16e5606baf73194a70632c81fdc44ef39ba29712

`13. Which browser was used to download this file? (look at the process_name)`
```
SAME QUERY AS NUMBER 10
```

ANSWER: chrome

`14. What was the name of the malicious file dropped by the attackers?`
```
// Gets files created in specified host
FileCreationEvents
| where hostname == "RQJQ-MACHINE"
```

ANSWER: cobaltstrike.exe

`15. Which command (process_commandline) shows the execution of the Raisin_Kane_Promo_Offer.docx file? (copy and paste the whole command)`
```
// Gets processes run in between timestamps
ProcessEvents
| where hostname == "RQJQ-MACHINE"
| where timestamp between (datetime(2024-05-01) .. datetime(2024-05-02))
```

ANSWER: "C:\Program Files\Microsoft Office\Office16\WINWORD.EXE" "C:\Users\evbrowne\Downloads\Raisin_Kane_Promo_Offer.docx"

`16. What IP address do the hackers connect to using cobalt strike?`
```
SAME QUERY AS NUMBER 15
```

ANSWER: 93.238.22.122

`17. Over what port do the hackers connect to that IP address?`
```
SAME QUERY AS NUMBER 15
```

ANSWER: 50050

`18. What was the first discovery command issued by the hackers? (hint: it has to do with a system)`
```
// Gets processes run in between timestamps
ProcessEvents
| where hostname == "RQJQ-MACHINE"
| where timestamp between (datetime(2024-05-02) .. datetime(2024-05-04))
```

ANSWER: systeminfo

`19. How many of these short discovery commands did the attackers run?`
```
SAME QUERY AS NUMBER 18
```

ANSWER: 6

`20. What is Anthony Davis' hostname?`
```
// Gets the hostname of specified employee
Employees
| where name == "Anthony Davis"
| project hostname
```

ANSWER: AMFB-MACHINE

`21. When did the attackers connect to their IP address using cobalt strike on Anthony Davis' machine?`
```
// Gets processes containing cobaltstrike in between timestamps
ProcessEvents
| where hostname == "AMFB-MACHINE"
| where process_commandline contains "cobaltstrike"
```

ANSWER: 2024-05-14 12:24:45+00:00

`22. What was the name of this scanning tool?`
```
// Gets processes run in between timestamps
ProcessEvents
| where hostname == "AMFB-MACHINE"
| where timestamp between (datetime(2024-05-14) .. datetime(2024-05-17))
```

ANSWER: advanced-ip-scanner.exe

`23. What was the name of the file the attackers exfiltrated to learn about the network? (hint: ___.pdf)`
```
SAME QUERY AS NUMBER 22
```

ANSWER: network_diagrams.pdf

`24. What was the name of the file the attackers took that would have contained usernames and passwords?`
```
SAME QUERY AS NUMBER 22
```

ANSWER: credentials.txt

`25. What was the name of this zip file?`
```
SAME QUERY AS NUMBER 22
```

ANSWER: important_network_info.zip

`26. Which domain did the attackers send the zip to?`
```
SAME QUERY AS NUMBER 22
```

ANSWER: nothing-to-see-here.net

`27. You did an amazing job! Enter yay to finish`

ANSWER: yay
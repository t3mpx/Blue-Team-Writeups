---
tags:
  - kc7 
urls: https://kc7cyber.com/challenges/830
---
---

## Section 1: 💪 Tables, Ladders


`1. Enter KQL ftw to continue.`

ANSWER: KQL ftw

`2. Enter uncover the clues to continue.`

ANSWER: uncover the clues

`3. Enter show me the data to continue.`

ANSWER: show me the data

`4. Enter table it up to contine.`

ANSWER: table it up

`5. Which table would tell us if an employee received a suspicious email?`

ANSWER: Email

`6. Which table would tell us if they went to their bank website to see how much money they had?`

ANSWER: OutboundNetworkEvents

`7. Which table would tell us if an employee saved a malicious file on his computer?`

ANSWER: FileCreationEvents

`8. Which table would tell us if that employee opened that malicious file that was saved on his computer?`

ANSWER: ProcessEvents

`9. Which table would you look in to see if that malicious file was detected on an employee's machine?`

ANSWER: SecurityAlerts

## Section 2: 🤼‍♂️ Rows & Columns: Tag Team Champs of Data!

`1. Enter row row row to continue.`

ANSWER: row row row

`2. How many rows of data did that query return?`
```
// Gets the 10 first employees
Employees
| take 10
```

ANSWER: 10

`3. What column contains information about an employee's job title?`
```
SAME QUERY AS NUMBER 2
```

ANSWER: role

`4. How many pharmacists work at our company?`
```
// Gets all employees with the "pharmacist" role
Employees
| where role == "Pharmacist"
```

ANSWER: 19

## Section 3: 💥 Commands & Operators: The Finishing Moves

`1. Do a take 10 on a table of your choice and enter when in doubt take 10.`
```
// Gets the 10 first employees
Employees
| take 10
```

ANSWER: when in doubt take 10

`2. How many employees work at our company?`
```
// Counts all the entries in the employee's table
Employees
| count
```

ANSWER: 321

`3. How many distinct employee roles do we have at our company?`
```
// Counts unique employee's roles
Employees
| distinct role
| count
```

ANSWER: 23

`4. How many Radiologists with the name Richard do we have at our company?`
```
// Counts employees named Richard with the role "Radiologist"
Employees
| where role == "Radiologist"
| where name has "Richard"
| count
```

ANSWER: 2

`5. What is Noemi's email address?`
```
// Gets information related to the employee "Noemi Tep"
Employees
| where name == "Noemi Tep"
```

ANSWER: noemi_tep@jojoshospital.org

`6. What is the name of the exact role?`
```
// Gets roles that contain the word "security"
Employees
| where role contains "security"
```

ANSWER: Security Officer

`7. How many emails did we receive that have the word health in the subject?`
```
// Gets email that have the exact "health" word in the subject
Email
| where subject has "health"
```

ANSWER: 613

`8. Who is the sender for the last external email received by Noemi?`
```
// Filters out senders that contain the word "jojoshospital" in their mail
Email
| where recipient == "noemi_tep@jojoshospital.org"
| where sender !contains "jojoshospital"
```

ANSWER: skirmishes.converts@yandex.com

`9. How many failed login attempts were there from this IP address?`
```
// Gets failed logins from the specified IP
AuthenticationEvents
| where src_ip == "10.10.0.144" and result == "Failed Login"
```

ANSWER: 59

`10. How many results do we get in AuthenticationEvents for these two IP addresses?`
```
// Gets login events from the specified IPs
AuthenticationEvents
| where src_ip == "10.10.0.144" or src_ip == "10.10.0.86"
```

ANSWER: 131

`11. How many failed login attempts were from these IP addresses?`
```
// Gets failed logins from the specified IPs
AuthenticationEvents
| where src_ip in ("10.10.0.144", "10.10.0.86", "10.10.0.86", "10.10.0.20", "10.10.0.109") and result == "Failed Login"
```

ANSWER: 169

`12. How many distinct websites(urls) did Nancy Roberts visit?`

Grab the IP:
```
Employees
| where name == "Nancy Roberts" 
```

```
// Gets visited URLs by specified IP
OutboundNetworkEvents
| where src_ip == "10.10.0.30"
| distinct url
```

ANSWER: 80

`13. Enter //noted to continue.`

ANSWER: //noted

`14. Enter rhymes with junk to continue.`

ANSWER: rhymes with junk

`15. How many websites did employees with the first name Mary visit?`
```
// Creates a temporary variable holding the IP of the specified employee to check for visited URLs
let mary_ips = 
Employees
| where name has "Mary"
| distinct ip_addr;
OutboundNetworkEvents
| where src_ip in (mary_ips)
```

ANSWER: 668

`16. How many authentication attempts did we see to the accounts of employees with the first name Mary?`
```
// Creates a temporary variable holding the username of employees named Mary to check for authentication attempts
let mary_auth_attempts = Employees
| where name has "Mary"
| distinct username;
AuthenticationEvents
| where username in (mary_auth_attempts)
| count
```

`17. Enter threat actors fear me to complete this module.`

ANSWER: threat actors fear me


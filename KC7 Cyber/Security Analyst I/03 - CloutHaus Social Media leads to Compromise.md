---
tags:
  - kc7
urls: https://kc7cyber.com/challenges/1360
---
---

## SECTION 1: GOT CLOUT? 🤳

`1. Based on Afomiya’s Instagram profile, what is the email address she uses for brand deals?`

ANSWER: afomiya.storm@gmail.com

`2. Which of the following signs should Afomiya look for to determine if an email offering a brand deal is a phishing attempt?`

ANSWER: E

`3. What technique is the threat actor using to manipulate her into revealing personal information that could compromise her email or Instagram account?`

ANSWER: Social Engineering

`4. What answer did the attacker enter to try and bypass the security questions? Enter one of the answers the attacker submitted.`

ANSWER: Lalibela

`5. What security measure saved Afomiya's email account from being hacked, despite the threat actor having access to her security question answers?`

ANSWER: MFA

`6. According to CloutHaus internal employee logs, what is Afomiya’s designated professional email?
```
// Get information related to the specified employee
Employees
| where name contains "afomiya"
```

ANSWER: afomiya_storm@clouthaus.com

`7. What is Afomiya’s role with CloutHaus?`
```
// Gets an employee's role
Employees
| where name contains "afomiya"
| distinct role
```

ANSWER: Influencer Partner

`8. Based on the CloutHaus employee table, what is the status of Multi-Factor Authentication (MFA) for Afomiya’s account?`
```
// Gets and employee's MFA status
Employees
| where name contains "afomiya"
| distinct mfa_enabled
```

ANSWER: false

`9. What is the sender’s email address in the email Afomiya received from "Dior"?`
```
// Gets emails received by the specific recipient that contain "Dior" in either the subject or the link
Email
| where recipient == "afomiya_storm@clouthaus.com"
| where subject contains "Dior" or links contains "Dior"
```

ANSWER: collabs@dior-partners.com

`10. What is the subject line of the email Afomiya received from "Dior"?`
```
SAME QUERY AS NUMBER 9
```

ANSWER:  [EXTERNAL] Exclusive Partnership Opportunity with Dior

`11. What is the link provided in the email?`
```
SAME QUERY AS NUMBER 9
```

ANSWER: https://super-brand-offer.com/login

`12. When did Afomiya click on the link? Paste the entire timestamp.`
```
// Gets outbound network connections related to the URL
OutboundNetworkEvents
| where url contains "https://super-brand-offer.com/login"
```

ANSWER:  2025-04-03T11:20:00Z

`13. What username did she enter?`
```
SAME QUERY AS NUMBER 12
```

ANSWER: afstorm

`14. What is the IP address associated with the domain?`
```
// Gets the IP of the specified domain
PassiveDns
| where domain contains "super-brand-offer.com"
```

ANSWER: 198.51.100.12

`15. How many distinct domains are linked to the suspicious IP address?`
```
// Gets distinct domains associated to an IP
PassiveDns
| where ip contains "198.51.100.12"
| distinct domain
```

ANSWER: 3

`16. Enter: I have MFA setup for my Instagram!`

ANSWER: I have MFA setup for my Instagram!

`17. What are the followers really investing in: a great deal or a phishing scam?`

ANSWER: phishing scam

`18. Based on the images showing the apartment view and amenities from Afomiya’s Instagram post, use a reverse image search to identify the name of the apartment building.`

Copy and paste the image in https://picdetective.com/ and you'll get the name.

ANSWER: City Center Apartments

`19. ENTER: Unlocking trouble with a photo!`

ANSWER: Unlocking trouble with a photo!

`20. ❓ What should you never reuse across different sites to protect your accounts?`

ANSWER: passwords

`21. ENTER: Be the hunter, not the hunted!`

ANSWER: Be the hunter, not the hunted!

## SECTION 2: INSIDE THE CLOUT ...

`1. What IP address was used to gain access?`
```
// Get authentication events from specific user after given timestamp
AuthenticationEvents
| where username == "afstorm"
| where timestamp > datetime(2025-04-03T11:20:00Z)
```

ANSWER: 182.45.67.89

`2. What domains are associated with this IP? (enter one)`
```
// Gets domains associated with given IP
PassiveDns
| where ip == "182.45.67.89"
| distinct domain
```

`3. What part of the User-Agent string indicates the suspicious browser and operating system? (Submit either the browser name/version or the operating system name/version.)`
```
SAME QUERY AS NUMBER 1
```

Look at the user-agent field

ANSWER: Windows NT 5.2

`4. What country did the login originate from?`

Use https://www.maxmind.com/en/geoip-web-services-demo to search for the IP location.

ANSWER: China

`5. According to the attacker's web search history on the site, what were they trying to hack?`
```
// Gets incoming activity from the given IP
InboundNetworkEvents
| where src_ip == "182.45.67.89"
```

Look throught the URLs

ANSWER: location

`6. According to another search log, what kind of personal info were they sneakily trying to uncover (and pretending to ask “for a friend”)?`
```
SAME QUERY AS NUMBER 5
```

Look throught the URLs

ANSWER: home address

`7. What kind of account or app does that log suggest they were targeting?`
```
SAME QUERY AS NUMBER 5
```

Look throught the URLs

ANSWER: Venmo

`8. Based on another search, what shady and fake event were they pretending to plan as a way to lure Afomiya?`
```
SAME QUERY AS NUMBER 5
```

Look throught the URLs

ANSWER: fake birthday party

`9. What external email address received messages forwarded from Afomiya’s account?`
```
// Gets emails sent from specific sender to recipients that dont have the domain "clouthaus"
Email
| where sender == "afomiya_storm@clouthaus.com"
| where recipient !contains "clouthaus"
| where timestamp > datetime(2025-04-03T11:20:00Z)
```

ANSWER: noreply@influencer-deals.net

`10. Which forwarded email contained Afomiya’s payment details or direct deposit form?`
```
// Gets emails sent from sender to recipient
Email
| where sender == "afomiya_storm@clouthaus.com"
| where recipient == "noreply@influencer-deals.net"
```

ANSWER: [EXTERNAL] [FORWARD] Afomiya's payment details – direct deposit form

`11. What forwarded email subject included a passport scan?`
```
SAME QUERY AS NUMBER 10
```

ANSWER:  [EXTERNAL] [FORWARD] Afomiya's passport scan – confidential

`12. Which forwarded email subject contained either Afomiya’s bank statement or year-end tax documents?`
```
SAME QUERY AS NUMBER 10
```

ANSWER: [EXTERNAL] [FORWARD] Re: Tax documents – year-end summary

`13. Based on everything you’ve discovered, what do you think the attacker’s true objective was?`

ANSWER: ✅ All of the above - it’s a cyber buffet and the attacker’s hungry


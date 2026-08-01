---
tags:
  - kc7
urls: https://kc7cyber.com/challenges/1664
---
---

## SECTION 1: WELCOME TO THE CHAOS

`1. Type definitely ready to continue.`

ANSWER: definitely ready

`2. Type no pressure to continue.`

ANSWER: no pressure

`3. Type let's learn to continue.`

ANSWER: let's learn

## SECTION 2: THE NETWORK MAP

`1. What does IP stand for?`

ANSWER: Internet Protocol

`2. How many octets are in an IPv4 address?`

ANSWER: 4

`3. Is 10.10.16.5 a public or private IP address?`

ANSWER: private

`4. Is 8.8.8.8 public or private?`

ANSWER: public

`5. Public or private?`

ANSWER: private

`6. Public or private?`

ANSWER: private

`7. Public or private?`

ANSWER: public

`8. Is 185.174.137.42 public or private?`

ANSWER: public

`9. How many VLANs (network zones) does Whiskers & Wonders have?`
```
//  Gets total numbers of devices in vlans and orders them
DeviceInfo
| summarize device_count=count() by vlan
| order by device_count desc
```

ANSWER: 5

`10. Which VLAN has the most devices?`
```
SAME QUERY AS NUMBER 9
```

ANSWER: workstations

`11. What is the IP address of the DonationPortal?`
```
// Gets information related to devices in the dmz vlan
DeviceInfo
| where vlan == "dmz"
| project name, ip_address, host_type
```

ANSWER: 10.10.0.3

`12. What is the IP address of MailServer01?
```
// Gets information related to devices in the servers vlan
DeviceInfo
| where vlan == "servers"
| project name, ip_address
```

ANSWER: 10.10.1.2

`13. What device is in the restricted zone?`
```
// Gets information related to the devices in the restricted vlan
DeviceInfo
| where vlan == "restricted"
| project name, ip_address, host_type
```

ANSWER: DomainController

`14. What VLAN do IPs starting with 10.10.1 belong to?`

ANSWER: servers

`15. What IP range are workstations in? (format: 10.10.X)`

ANSWER: 10.10.16

`16. What is the IP address of the DNS-Server?`
```
// Gets information about the DNS-Server device
DeviceInfo
| where name == "DNS-Server"
| project name, ip_address, vlan
```

ANSWER: 10.10.1.7

`17. What table would show you DNS lookups?`

ANSWER: DnsEvents

`18. What zone contains the Domain Controller?`

ANSWER: Restricted

`19. Type time to dig deeper to continue.`

ANSWER: time to dig deeper

## SECTION 3: FOLLOWING THE TRAIL

`1. What is the maximum port number?`

ANSWER: 65535

`2. What port number is used for HTTPS?`

ANSWER: 443

`3. What port number is used for DNS?`

ANSWER: 53

`4. What port number is used for SSH?`

ANSWER: 22

`5. What port number is used for RDP?`

ANSWER: 3389

`6. What destination port has the most traffic?`
```
// Gets the 5 most common destination ports
NetworkFlow
| summarize traffic=count() by dest_port
| order by traffic desc
| take 5
```

ANSWER: 25

`7. What does SMTP stand for?`

ANSWER: Simple Mail Transfer Protocol

`8. What does TCP stand for?`

ANSWER: Transmission Control Protocol

`9. What does UDP stand for?'

ANSWER: User Datagram Protocol

`10. What protocol does DNS primarily use?`

ANSWER: UDP

`11. What protocol carries the majority of network traffic on this network?`
```
// Gets network traffic by protocol
NetworkFlow
| summarize count() by protocol
| order by count_ desc
```

ANSWER: TCP

`12. Type totally normal to continue.`

ANSWER: totally normal

`13. What does DNS stand for?`

ANSWER: Domain Name System

`14. What column contains the domain name that was looked up?`
```
// Gets DNS events
```

ANSWER: query_name

`15. What type of DNS record maps a domain to an IPv4 address?`

ANSWER: A

`16. What does TTL stand for?`

ANSWER: Time To Live

`17. Type following the breadcrumbs to continue.`

ANSWER: following the breadcrumbs

`18. What IP address did docs.google.com resolve to?`
```
// Gets DNS event related to specified domain
DnsEvents
| where query_name == "docs.google.com"
| where query_type == "A"
| project timestamp, client_ip, query_name, resolved_ips
| take 1
```

ANSWER: 152.52.146.99

`19. What protocol was this DNS query using?`

ANSWER: UDP

`20. What destination port was used for the HTTPS connection?`
```
// Gets information about a connection
NetworkFlow
| where src_ip == "10.10.16.14"
| where dest_ip == "152.52.146.99"
| project timestamp, src_ip, dest_ip, dest_port, protocol
| take 1
```

ANSWER: 443

`21. What HTTP status code was returned?`
```
// Gets information about a connection to the specified domain
ProxyEvents
| where src_ip == "10.10.16.14"
| where domain == "docs.google.com"
| where timestamp between (datetime(2025-06-03 08:50:00) .. datetime(2025-06-03 09:00:00))
| project timestamp, src_ip, url, status_code
| take 1
```

ANSER: 200

`22. What happens first when browsing to a website: the DNS lookup or the TCP connection?`

ANSWER: DNS lookup

`23. What column contains the full URL?`
```
// Gets proxy events
ProxyEvents
| take 5
| project src_ip, url, method, status_code
```

ANSWER: url

`24. What status code indicates a successful request?`

ANSWER: 200

`25. What HTTP method is used to submit form data?`

ANSWER: POST

`26. Type time to investigate to continue.`

ANSWER: 26

## SECTION 4: THE PICTURE EMERGES

`1. What domain resolves to 185.174.137.42?`
```
//Gets the domains of an IP
DnsEvents
| where resolved_ips has "185.174.137.42"
| distinct query_name
```

ANSWER: update-cdn-service.xyz

`2. How many distinct internal IPs queried this domain?`
```
//Gets the IPs of a domain
DnsEvents
| where query_name contains "update-cdn-service.xyz"
| distinct client_ip
```

ANSWER: 3

`3. Is update-cdn-service.xyz an internal domain or an external domain?`
```
// Gets client connections to the domain
DnsEvents
| where query_name contains "update-cdn-service.xyz"
| summarize count() by client_ip
| order by count_ desc
```

ANSWER: external

`4. What destination port is this traffic using?`
```
// Gets connection to specified IP
NetworkFlow
| where dest_ip == "185.174.137.42"
| project timestamp, src_ip, dest_ip, dest_port, protocol, bytes
| take 10
```

ANSWER: 443

`5. What protocol normally runs on port 443?`

ANSWER: HTTPS

`6. What URL path appears repeatedly in these requests?`
```
// Gets proxy events of the specified domain
ProxyEvents
| where domain == "update-cdn-service.xyz"
| project timestamp, src_ip, url, method, status_code
| order by timestamp asc
```

ANSWER: /api/v1

`7. What does C2 stand for?`

ANSWER: command and control

`8. What is the term for malware regularly checking in with its C2 server?`

ANSWER: beaconing

`9. Type all good here to continue.`

ANSWER: all good here

`10. What domain in the sender address is impersonating the sanctuary?`
```
// Gets email that have the specified domain but don't end in @whiskersandwonders.org
Email
| where sender !endswith "@whiskersandwonders.org"
| where sender contains "whiskersandwonders"
| project timestamp, sender, recipient, subject
```

ANSWER: whiskersandwonders-hr.com

`11. What was the subject line of the phishing email?`
```
SAME QUERY AS NUMBER 10
```

ANSWER: [EXTERNAL] Important: Employee Benefits Update Required

`12. How many employees received the phishing email?`
```
// Gets unique emails that recieved a mail from specified domain
Email
| where sender contains "whiskersandwonders-hr.com"
| distinct recipient
```

ANSWER: 3

`13. Did all three phishing recipients become compromised? (yes/no)`
```
// Get phishing recipients
Email
| where sender contains "whiskersandwonders-hr.com"
| distinct recipient

// Compare to C2 DNS queries
DnsEvents
| where query_name contains "update-cdn-service.xyz"
| distinct client_ip
```

ANSWER: yes

`14. Type jamie was right to continue.`

ANSWER: jamie was right

`15. What date was the first phishing email sent? (copy and paste)`
```
// Gets information about emails sent by specified recipient
Email
| where sender contains "whiskersandwonders-hr.com"
| project timestamp, recipient
| order by timestamp asc
```

ANSWER: 2025-06-02 16:00:00+00:00

`16. Approximately how many hours between the phishing email and the first C2 beacon?`
```
// Gets the first query of each ip to the specified domain
DnsEvents
| where query_name contains "update-cdn-service.xyz"
| summarize first_query=min(timestamp) by client_ip
| order by first_query asc
```

ANSWER: 7

`17. How many requests did the victim computers make to the malicious domain?`
```
// Gets requests to specified domain
ProxyEvents
| where domain == "update-cdn-service.xyz"
| project timestamp, src_ip
| order by timestamp asc
```

ANSWER: 6

`18. What is the C2 domain?`

ANSWER:  update-cdn-service.xyz

`19. What is the C2 IP address?`
```
// Gets the IP of the domain
DnsEvents 
| where query_name == "update-cdn-service.xyz" 
| distinct tostring(resolved_ips)
```

ANSWER: 185.174.137.42

`20. What is the phishing sender domain?`

ANSWER: whiskersandwonders-hr.com

`21. What destination port is the C2 traffic using?`
```
// Gets the port of specified IP
NetworkFlow
| where src_ip == "185.174.137.42"
```

ANSWER: 443

`22. What is the name of the compromised Development Officer?`
```
// Gets the IP of the specified domain and matches it against the IP of the Employees table
DnsEvents
| where query_name contains "update-cdn-service.xyz"
| distinct client_ip
| join kind=inner (Employees | project ip_addr, name, username, role) on $left.client_ip == $right.ip_addr
| project name, username, role
```

ANSWER: Alex Rivera

`23. What role do two of the compromised users share?`
```
SAME QUERY AS NUMBER 22
```

ANSWER: Adoption Coordinator

`24. Type they want the money to continue.`

ANSWER: they want the money

`25. What domain should be blocked at DNS?`

ANSWER: update-cdn-service.xyz

`26. What IP address should be blocked at the firewall?`

ANSWER: 185.174.137.42

`27. What URL path pattern should be blocked at the proxy?`

ANSWER: /api/v1

`28. What table should you query to detect C2 domain lookups?

ANSWER: DnsEvents

`29. What table should you query to detect network connections to the C2 IP?`

ANSWER: NetworkFlow

`30. What table should you query to detect the /api/v1/status beaconing pattern?`

ANSWER: ProxyEvents

`31. Type almost there to continue.`

ANSWER: almost there

`32. Approximately how many days has the C2 been active?`
```
// Gets first and last time a client ip made requests to the IPs of the specified domain
let c2_domain = "update-cdn-service.xyz";
let compromised_ips = DnsEvents
| where query_name contains c2_domain
| distinct client_ip;
DnsEvents
| where client_ip in (compromised_ips)
| where query_name contains c2_domain
| summarize
    first_beacon = min(timestamp),
    last_beacon = max(timestamp),
    total_queries = count()
  by client_ip
```

ANSWER: 5

`33. Type thank you jamie to complete the investigation.`

ANSWER: thank you jamie
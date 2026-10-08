# Network Traffic Analysis: DNS Service Disruption Incident Report

## 📋 Scenario Overview
As a Cybersecurity trainee, I investigated a network connectivity issue where multiple users reported an inability to access the client website **www.yummyrecipesforme.com**, (Sample website only) receiving a "destination port unreachable" error. 

To diagnose the problem, I analyzed a traffic capture utilizing **tcpdump** to inspect the underlying network protocol interactions and determine the root cause of the service failure.

---

## 🔍 Log Analysis & Data Interpretation
Below is the captured network log showing the interaction between the host machine (`192.51.100.15`) and the DNS Server (`203.0.113.2`):

### Network Traffic Log (Provided by Google)
<img width="902" height="448" alt="tcpdump log sample" src="https://github.com/user-attachments/assets/b1ace764-6be5-4d9e-b1a9-56da03633240" />

### Key Observations from the Log:
* **Timestamp:** `13:24:32.192571` (1:24 PM).
* **Source IP:** `192.51.100.15` (Client Browser/Host machine).
* **Destination IP:** `203.0.113.2.domain` (Target DNS Server on Port 53).
* **Outbound Request:** The host sent an initial query via **UDP** requesting an A record (`A?`) to resolve the domain name `www.yummyrecipesforme.com`.
* **Inbound Response:** The host received an **ICMP error packet** from the DNS server stating: `udp port 53 unreachable`. 
* **Persistence:** The log reflects that the host attempted the request two additional times, receiving the identical delivery failure each time.

---

## 🛡️ Incident Report: Network Traffic Analysis

## Summary of the problem found in the tcpdump log
As part of the DNS protocol, the UDP protocol was used to contact the DNS server to retrieve the IP address for the domain name of yummyrecipesforme.com. The ICMP protocol was used to respond with an error message, indicating issues contacting the DNS server. The UDP message going from your browser to the DNS server is shown in the first two lines of every log event. The ICMP error response from the DNS server to your browser is displayed in the third and fourth lines of every log event with the error message, “udp port 53 unreachable.” Since port 53 is associated with DNS protocol traffic, we know this is an issue with the DNS server. Issues with performing the DNS protocol are further evident because the plus sign after the query identification number 35084 indicates flags with the UDP message and the “A?” symbol indicates flags with performing DNS protocol operations. Due to the ICMP error response message about port 53, it is highly likely that the DNS server is not responding. This assumption is further supported by the flags associated with the outgoing UDP message and domain name retrieval.

## Analysis of the data and cause of the incident
The incident occurred today at 1:24 p.m. Customers notified the organization that they received the message “destination port unreachable” when they attempted to visit the website yummyrecipesforme.com. The cybersecurity team providing IT services to their client organization are currently investigating the issue so customers can access the website again. In our investigation into the issue, we conducted packet sniffing tests using tcpdump. In the resulting log file, we found that DNS port 53 was unreachable. The next step is to identify whether the DNS server is down or traffic to port 53 is blocked by the firewall. The DNS server might be down due to a successful Denial of Service attack or a misconfiguration. 

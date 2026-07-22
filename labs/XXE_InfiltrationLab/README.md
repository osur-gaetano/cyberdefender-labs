# XXE Infiltration Lab

## Objective
Link to lab: [XXE Infiltration](https://cyberdefenders.org/blueteam-ctf-challenges/xxe-infiltration/)

Through the XXE Infiltration lab, I aim to analyze a PCAP capture in Wireshark/Brim to trace how an attacker exploited an XML External Entity (XXE) vulnerability, identify the compromised credentials and files, and map the observed activity to the MITRE ATT&CK framework.

### Scenario
An automated alert has detected unusual XML data being processed by the server, which suggests a potential XXE (XML External Entity) Injection attack. This raises concerns about the integrity of the company's customer data and internal systems, prompting an immediate investigation.

Analyze the provided PCAP file using the network analysis tools available to you. The goal is to identify how the attacker gained access and what actions they took.

### Skills Learned
- Analyzing network traffic (PCAP) for signs of XXE exploitation.
- Identifying vulnerable web application endpoints from HTTP traffic.
- Extracting compromised credentials and sensitive file contents from packet captures.
- Reconstructing an attacker's timeline from Reconnaissance through Exfiltration.
- Mapping adversarial tactics and techniques to MITRE ATT&CK.

### Tools Used
- **Wireshark** – for inspecting and filtering packet capture data.
- **Brim (Zui)** – for querying and pivoting through large PCAP datasets efficiently.
- **tshark** – for command-line packet filtering and field extraction.
- **MITRE ATT&CK** – for mapping observed techniques to adversary tactics.

## Steps
The lab is accompanied by a PCAP file (`XXEInfiltration.pcap`) containing the captured network traffic of the incident. I begin by opening the file in Wireshark to get an overview of the traffic.


#### Q1. Knowing which open ports have been discovered by the attacker allows us to understand which services are exposed and potentially targeted. Which ports were found open on the server?
I start by filtering for completed TCP handshakes to identify which ports the attacker successfully connected to.


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

<img width="1239" height="650" alt="highest port number" src="https://github.com/user-attachments/assets/7ecef5ba-6f29-413c-b33d-3dff1c3b7901" />

`Port 3306`

#### Q2. By identifying the vulnerable PHP script, security teams can directly address and mitigate the vulnerability. What's the complete URI of the PHP script vulnerable to XXE Injection?
I started by filtering POST methods in Wireshark `http.request.method="POST"`, since a GET request wouldn't carry an XML payload in its body, and XXE attacks require the attacker to submit XML data to the server.

<img width="703" height="370" alt="Q2" src="https://github.com/user-attachments/assets/1ee863fb-02dc-44da-a46f-4d8935983143" />

`Vulnerable URI: /review/upload.php `


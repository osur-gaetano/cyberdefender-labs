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

#### Q3. To construct the attack timeline and determine the initial point of compromise, what's the name of the first malicious XML file uploaded by the attacker?

Going through the filtered results, I looked for a POST request with a Content-Type of application/xml (or text/xml), since that indicates the server is expecting to parse XML input. I then right-clicked the packet and followed the HTTP Stream to view the full request and response.

Inside the stream, the request body contained a <!DOCTYPE> declaration with a <!ENTITY> referencing an external SYSTEM file path, confirming this was an XXE payload. The destination URI in the request line pointed to the vulnerable script.

<img width="681" height="628" alt="Q3" src="https://github.com/user-attachments/assets/c2325cbe-5c77-469b-8c3c-e941d74f0521" />

`Malicious File: TheGreatGatsby.xml `

#### Q4. Understanding which sensitive files were accessed helps evaluate the breach's potential impact. What's the name of the web app configuration file the attacker read?

Still working with the `http.request.method=="POST"` filter, I went through the subsequent malicious XML payloads sent to the vulnerable script, since the attacker would need to modify the <!ENTITY> declaration each time to target a different local file.

I followed the HTTP Stream on each of these requests to inspect the SYSTEM entity reference inside the <!DOCTYPE> declaration, which points to the local file path the attacker is asking the server's XML parser to read and return.

Looking at the corresponding HTTP response for that stream, I could see the contents of the requested file reflected back in the server's reply, confirming successful exfiltration of a configuration file rather than just a probing attempt.

<img width="679" height="708" alt="Q4" src="https://github.com/user-attachments/assets/93595c71-ec25-4c6f-89c2-c09719676626" />

`Configuration File: config.php`

#### Q5. To assess the scope of the breach, what is the password for the compromised database user?

Having already seen a configuration file get exfiltrated in Q4, I suspected it contained database credentials, so I pivoted to look for the attacker actually using them. I filtered Wireshark for MySQL login requests using `mysql.login_request`, since that isolates the packets where a client authenticates to the MySQL server.

I then right-clicked the relevant login request packet and followed the TCP Stream to view the full authentication exchange between the client and the MySQL server.

Within the stream, the login request packet contains the username and password used to authenticate, confirming these were the same credentials leaked from the configuration file read via the XXE vulnerability.

<img width="757" height="739" alt="Q5" src="https://github.com/user-attachments/assets/bd6ca341-7571-430f-b4c2-5154a62f018d" />

`Password: Winter2024 `

#### Q6. Following the database user compromise, what's the UTC timestamp of the attacker's first connection with the MySQL server in an attempt to utilize the compromised credentials?

Building on Q5, I kept the `mysql.login_request` filter active in Wireshark, since it isolates every login attempt made against the database server rather than just the one I'd already inspected.

I sorted the filtered results by the Time column and took the earliest entry, since this represents the attacker's first attempt to authenticate with the credentials obtained from the configuration file in Q4.

Wireshark's default time display is relative to the start of the capture, so I changed it to `View > Time Display Format > Date and Time of Day (UTC)` to get an absolute timestamp I could report.

<img width="715" height="803" alt="Q6" src="https://github.com/user-attachments/assets/75dfaad0-e12b-4bb2-be94-9d81c53cf01f" />

`Timestamp (UTC): 2024-05-31 12:08 `

#### Q7. To eliminate the threat and prevent further unauthorized access, can you identify the name of the web shell that the attacker uploaded for remote code execution and persistence?

I continued using the `http.request.method=="POST"` filter from the earlier questions, since a web shell has to be delivered to the server the same way the malicious XML payloads were — through a POST request carrying the file in the body.

Scrolling through the filtered requests, I looked for a POST request whose endpoint pointed to a file upload handler rather than the vulnerable XXE script from Q2, and whose payload had a scripting extension such as .php, since that's what would let the attacker execute commands on the server rather than just read files.

I opened that packet directly to inspect the request line and body, which showed both the upload endpoint the file was sent to and the filename of the uploaded script, confirming this was the web shell the attacker planted for persistence and remote code execution.

<img width="718" height="721" alt="Q7" src="https://github.com/user-attachments/assets/514dab13-994f-46c1-9ccd-cf6c7fcbd8b2" />

`Answer: booking.php`

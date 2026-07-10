# OSKI Lab

## Objective
[](https://cyberdefenders.org/blueteam-ctf-challenges/oski/)
The OSKI lab aims to analyze a sandbox report using Any.Run to identify Stealc malware behavior, extract configuration details, and map observed tactics to MITRE ATT&CK.


### Scenario
The accountant at the company received an email titled "Urgent New Order" from a client late in the afternoon. When he attempted to access the attached invoice, he discovered it contained false order information. Subsequently, the SIEM solution generated an alert regarding downloading a potentially malicious file. Upon initial investigation, it was found that the PPT file might be responsible for this download. Could you please conduct a detailed examination of this file?

### Skills Learned
- Analyze malware behavior.
- Threat Intelligence.
- Map adversarial tactics to MITRE ATT&CK.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used
- Online Malware Analysis tool (VirusTotal) for analyzing suspicious files, URLs, domains, and IP addresses for malware.
- Malware analysis sandbox (ANY.RUN) to safely execute and analyze the suspicious file, links, and phishing attempts in real-time.

## Steps
The lab is accompanied by a file with a hash and an ANY.RUN report for the particular malware under investigation, which I downloaded. For this investigation, I begin with a hash.

<img width="770" height="398" alt="image" src="https://github.com/user-attachments/assets/ec38a7ae-fd8e-4b4b-abc4-1843a2651987" />

`File hash: 12c1842c3ccafe7408c23ebf292ee3d9`

#### Q1. Determining the creation time of the malware can provide insights into its origin. What was the time of malware creation?
I visit [VirusTotal](https://virustotal.com/) website, and search for the hash

<img width="649" height="481" alt="image" src="https://github.com/user-attachments/assets/29f2e45e-4573-41b0-b0a5-3a9408fea2ee" />

VirusTotal flags this particular hash as malicious

<img width="1189" height="578" alt="image" src="https://github.com/user-attachments/assets/a69a5d38-3eec-42de-9ccc-d08acf7125ad" />


After receiving the hash search results, I navigate to the 'Details' tab, looking at the 'History' section, I can view the different timelines of this particular malware

<img width="1164" height="504" alt="image" src="https://github.com/user-attachments/assets/ed64c3dc-6cc6-4745-a2df-555c79fe1d5a" />

`Creation Date: 2022-09-28 17:40`

#### Q2. Identifying the command and control (C2) server that the malware communicates with can help trace back to the attacker. Which C2 server does the malware in the PPT file communicate with?
When identifying the C2 server, I look at network activities for outbound connections to URLs or IP addresses. In VirusTotal, I navigate to the **'Relations' Tab**, then **'Contacted URLs' section**. This section contains a list of IP addresses and URLs that had communication initiated by a particular malware

<img width="1196" height="557" alt="image" src="https://github.com/user-attachments/assets/91f795b5-1bd5-403f-8023-af70d0d0c64d" />

`The C2 server: http://171.22.28.221/5c06c05b7b34e8e6.php`


#### Q3. Identifying the initial actions of the malware post-infection can provide insights into its primary objectives. What is the first library that the malware requests post-infection?
Since I am investigating the activities of the malware when it is executed. I navigate to the **'Behavior'** Tab, then go to the **' Files Dropped'** section, since I am interested in a library that was downloaded

<img width="1193" height="572" alt="image" src="https://github.com/user-attachments/assets/3787baeb-d6ee-4aa1-b4fb-d3b755e8cf6e" />

`First Library requested: sqlite3.dll`

#### Q4. By examining the provided [Any.run](https://any.run/report/a040a0af8697e30506218103074c7d6ea77a84ba3ac1ee5efae20f15530a19bb/d55e2294-5377-4a45-b393-f5a8b20f7d44) report, what RC4 key is used by the malware to decrypt its base64-encoded string?
The Any.Run report has a **Malware Configuration** section, from there I can view the different configs that the malware uses for it to be executed successfully on the infected machine. From that section, I can get the **RC4** key and other details. I can also see the **C2 server** that I initially got from **VirusTotal**

<img width="1112" height="398" alt="image" src="https://github.com/user-attachments/assets/30efcd77-4532-4134-8e77-9f19e49cbd53" />

Another way to get the Malware configuration is to click on the `full report` link just before Malware Configuration

<img width="1122" height="218" alt="image" src="https://github.com/user-attachments/assets/3cb63347-1b23-4599-bf3c-b8d03ee8abf5" />

Then click on the `CFG` button

<img width="958" height="619" alt="image" src="https://github.com/user-attachments/assets/43323dfc-d19e-4d52-b9ab-fac2ba56b45e" />

From the window that pops up, I can also view **RC4** key

<img width="1440" height="745" alt="image" src="https://github.com/user-attachments/assets/770ee8ba-e338-459b-8216-053f366216a7" />


`RC4 Key: 5329514621441247975720749009`

#### Q5. By examining the MITRE ATT&CK techniques displayed in the Any.run sandbox report, identify the main MITRE technique (not sub-techniques) the malware uses to steal the user’s password.
From the Any.Run Analysis with MITRE ATT&CK. I went to the **Process** tab, on clicking VPN.exe, there are process details, and from the list, I can view a variety of listed information. 
The detail important for in this case is `T1555.003`

<img width="575" height="517" alt="image" src="https://github.com/user-attachments/assets/10a60ec9-f60a-4e51-bf27-1ef7a008194b" />

With this information, I head to [MITRE ATT&CK](https://attack.mitre.org/) and search for `T1555.003`. I can view it as a subtechnique of `T155.003`

<img width="1405" height="736" alt="image" src="https://github.com/user-attachments/assets/27e7803a-477c-4451-9127-e0ee83c62f02" />

Clicking on `T155`, I get to the parent technique, which also lists some other sub-techniques

<img width="1440" height="660" alt="image" src="https://github.com/user-attachments/assets/0073e476-5592-4273-a7f0-252681d1d576" />

`MITRE Technique: T1555`

#### Q6. By examining the child processes displayed in the Any.run sandbox report, which directory does the malware target for the deletion of all DLL files?
Still, while at the **Processes** Tab, I click on the **Cmd.exe** process. The **command-line** section contains the information about the command that was run.

<img width="496" height="746" alt="image" src="https://github.com/user-attachments/assets/2c18f6ce-4a08-41cc-870c-6a4154a96024" />

The command waits 5 seconds, then deletes VPN.exe from the temp folder, deletes all .dll files in C:\ProgramData, then exits, a self-cleanup/self-delete routine to erase its own traces after execution.

`C:\ProgramData`

`Note: This pattern (delayed self-deletion of an exe + wiping DLLs in ProgramData) is a common technique used by malicious software to cover its tracks, so if you encountered this in a suspicious context, treat it as a red flag.`


#### Q7. Understanding the malware's behavior post-data exfiltration can give insights into its evasion techniques. By analyzing the child processes, after successfully exfiltrating the user's data, how many seconds does it take for the malware to self-delete?

`cmd.exe` has a child process `timeout.exe`. Looking at the `command` section I see arguments for the command it `\t 5`
<img width="499" height="708" alt="image" src="https://github.com/user-attachments/assets/5c538617-213a-4b69-8d65-74f92ebbbe15" />

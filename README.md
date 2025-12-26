<h1>Detection & Incident Response with Atomic Red Team</h1>


<h2>Description</h2>
Project consists of configuring a Windows Server with a Splunk Universal Forwarder to simulate endpoint telemetry for threat detection. Sysmon was installed to enhance event logging, and the Splunk instance was configured to receive and index logs in real time. Atomic Red Team test T1059.001 (Command and Scripting Interpreter: PowerShell) was executed to simulate adversary behavior. The resulting activity was identified in Splunk, where a detection rule was created, triggering automated notifications for response action.
<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b> 
- <b>SPL</b>
- <b>Slack</b>
  
<h2>Environments Used </h2>

- <b>Windows Server 2022</b> (21H2)

<h2>Program walk-through:</h2>

<p align="center">
To begin I configured by Splunk instance to receive data from my Windows Server, by setting it to listen on port 9997: <br/>
<img src="https://github.com/user-attachments/assets/ae5bf145-2834-4342-9689-363f110fbf02" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
I then created a new index and labeled it "wineventlog:  <br/>
<img src="https://github.com/user-attachments/assets/c1e12e02-aa80-4fa7-8e68-873d04bffed7" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
From there, I navigated to the Splunk web page to download the Splunk Universal Forwarder for my system and begin installation: <br/>
<img src="https://github.com/user-attachments/assets/8b75ee1e-6e7a-4794-997c-a4d89ad5bbb8" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
I specified my installation options, created my credentials for my Admin account,entered the IP of my deployment server as well as the default port and completed installation:  <br/>
<img src="https://github.com/user-attachments/assets/135fdef1-15dc-4388-9c3c-bd00fa008c3b" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
In my Splunk instance I chose the data ingestion method as forwarder, and configured. Below you can the name, the index and forwarder selected, as well as the event log to pull from:  <br/>
<img src="https://github.com/user-attachments/assets/732dfb62-1998-4219-9db2-c80395627fd0" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
Before navigating back to my Windows Server, I installed the following add-ons within my Splunk instance. The Windows add-on includes predefined inputs to collect data from the Windows systems:  <br/>
<img src="https://github.com/user-attachments/assets/5eae3dca-afb2-4cb6-aafd-057424bfbee3" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
The Sysmon add-on provides continuous Sysmon-to-Splunk integration for real-time streaming of detections, events, incidents, and audit data:  <br/>
<img src="https://github.com/user-attachments/assets/56259400-9049-4c30-823c-7b101e64954f" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
Navigated to MITRE webpage, found the specific Powershell technique I'd be executing and made note of commands to look for:  <br/>
<img src="https://github.com/user-attachments/assets/cd945569-2875-4154-b067-4f0e5c3ba0a7" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
Installing Sysmon and accepting license on my Windows Server:  <br/>
<img src="https://github.com/user-attachments/assets/2d5c818b-611b-4ade-b795-38939b84c2dd" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
Creating inputs.conf file:  <br/>
<img src="https://github.com/user-attachments/assets/d63ccbc2-7b8f-4302-9585-1c7025bb669b" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
Output.conf file:  <br/>
<img src="https://github.com/user-attachments/assets/b4c30393-d01d-4614-b99d-1425867dfd00" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
Turned off virus protection for the duration of this lab to avoid interference with the simulated attack techniques:  <br/>
<img src="https://github.com/user-attachments/assets/33c0813c-3faa-4cff-89ab-e78b265956e9" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
I then navigated to the Atomic Red Team web page and installed the execution framework. Once installed I ran show details for T1059.001 to gather more information:  <br/>
<img src="https://github.com/user-attachments/assets/38b80a8c-76d6-448f-9d10-e355b0c40f7e" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
Running the atomic tests 1-3:  <br/>
<img src="https://github.com/user-attachments/assets/c0081369-13ef-4759-a2b6-72a129dc879a" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
Inside Splunk, I navigated to the wineventlog index and began to investigate the data that came through:  <br/>
<img src="https://github.com/user-attachments/assets/aea13e50-fa74-464b-8ab9-029b6271b17c" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
There were specific commands that I wanted to find so I ran the search command to see what results it would bring back. I navigated to the command field and found the simulated Powershell attack:  <br/>
<img src="https://github.com/user-attachments/assets/6b6d6aba-ebe6-411d-b0fd-8cb0289f7a95" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
I created the following SPL query, searching the command line for the following: IEX, DownloadString, and Mimikatz and putting that information in a table by the host, user, parent image(process that launched the executable), command line and image(executable that was launched):  <br/>
<img src="https://github.com/user-attachments/assets/55dda9c6-0ef2-407f-a52c-3f2530f15bb4" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
From the table I found critical information such as Bloodhound, Sharphound and Invoke-Mimikatz-Dumpcreds. So I built an alert to test if my query could detect this attack:  <br/>
<img src="https://github.com/user-attachments/assets/32ceaf8f-9b5d-4679-a678-4b9b13c2222b" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
I ran the Atomic Test again and my alert triggered successfully:  <br/>
<img src="https://github.com/user-attachments/assets/249457eb-ff4f-45a6-b935-1ca4e3654380" height="80%" width="80%" alt="Splunk"/>
<br />
<br />
With the successful trigger of my alert I also recieved Slack notifications confirming that end-to-end functionality for response automation:  <br/>
<img src="https://github.com/user-attachments/assets/9edba1f9-dca9-49b8-b3c6-909d2840e24a" height="80%" width="80%" alt="Splunk"/>
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>

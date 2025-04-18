# Anomalous-Behavior-Discovery-and-Mitigation

<img width="589" alt="Screen Shot 2025-04-18 at 5 38 30 PM" src="https://github.com/user-attachments/assets/9c89c5d7-a3ef-4c4d-b5ac-438af66d1c0b" />

# Wireshark Pcap file analysis

We are provided with a traffic.pcap file to investigate and potentially anomalous traffic and find any external IP Addresses that interacted with the target system.
After careful investigation I have discovered an external IP Address 75.30.5.55 utilizing port 1337 (ELITE) which is not a commonly used port.

<img width="1680" alt="Screen Shot 2025-04-18 at 5 47 32 PM" src="https://github.com/user-attachments/assets/fec121b3-4c95-4e02-8177-6f2397b43616" />

After following TCP Stream to look at the data that has been transferred to the attacker I have discovered what looked like PII, including: Names, Addresses, Phone Numbers, Email Addresses and Social Security Numbers.

<img width="1649" alt="Screen Shot 2025-04-18 at 6 20 47 PM" src="https://github.com/user-attachments/assets/16d7f172-6078-48eb-b044-87541183179d" />

# Let's investigate this on the target machine utilizing Windows Server 2019

I was given access to Windows Server and first thing was to check for all connections Listening or Established on the machines. I opened up Powershell and utilized 'netstat -an' and have discovered an established connection with the same destination IP address and port as we discovered in out Wireshark investigation.

<img width="1075" alt="Screen Shot 2025-04-18 at 5 52 22 PM" src="https://github.com/user-attachments/assets/21e2e546-a52e-4c49-82f1-57a86e5479b7" />

Next thing I decided to do was check the security logs for any unauthorized events, such unauthorized user creation. We navigated to Computer Management and started checking for security logs for Event ID 4720 which indicates that a new user account has been created. Unfortunately I couldn't find anything. Then I saw the "saved logs" and voila we filtered for the Event ID and we got the user created event with username "Adm1nistrator".(Super Sketchy)

<img width="985" alt="Screen Shot 2025-04-18 at 6 19 54 PM" src="https://github.com/user-attachments/assets/c572b09d-67b2-418b-8d9e-dc0b854dce5d" />

# Let's find the malicious process/application running

I opened up Task Manager and started investigating.

First, I didn't find anything that stood out, but after navigating to the "Details" tab, I saw the WinT0Ols.exe process running.(Again Super Sketchy)

<img width="669" alt="Screen Shot 2025-04-18 at 6 24 23 PM" src="https://github.com/user-attachments/assets/69a8478f-9c07-4240-98d9-796223ead504" />


So I checked the properties tab, saw it's location was in the Downloads Folder. 
Now we have our unauthorized user and the malicious process.

Time for eradication and recovery.

# Eradication

Using Task Manager we navigate to WinT0Ols.exe and click "End Task" to terminate and remove the application.

<img width="669" alt="Screen Shot 2025-04-18 at 6 24 23 PM" src="https://github.com/user-attachments/assets/2ded66be-1dea-4e93-b8c1-4d25fc2c4947" />

One more thing we have to do is disable the "Adm1nistrator" account.

Lets get back to the "Computer Management" tab and go to "Local Users and Groups", find the account we want to disable > properties and select "Account is Disabled".


<img width="414" alt="Screen Shot 2025-04-18 at 6 27 50 PM" src="https://github.com/user-attachments/assets/b14260a7-4288-4917-b45e-4ff5b7bf8dc1" />

Next thing is we have to monitor the Task Manager to make sure the WinT0Ols.exe process doesn't come back, also a good idea is to block the malicious IP in the firewall and integrate an IDS/IPS to prevent this breach from happening again as well as disable any unused ports and make sure our Antivirus is enabled and up to date, but that was not within the scope of this lab.


Thank you, hope you enjoyed.


 



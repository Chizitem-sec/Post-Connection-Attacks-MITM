# Post-Connection-Attacks Info Gathering & MITM 

## Objective

The objective is to gather as much information as possible on the devices connected to the target network, like MAC addresses, IP addresses, their operating system, open ports and services that are running on those ports. The second objective is to perform a MITM attack (Man in the middle) in a sanbox environment. This places the attack linux computer in the middle of the connection between the windows virtual machine and the accesspoint. The goal is to impersonate the target machine so the access point thinks the attack linux is the windows machine and the windows machine thinks the attack linux is the access point using ARP spoofing. 

### Skills Learned

- Using netdiscover & zenmap GUI to discover targets connected to a network.
- Reading ARP tables.
- Use of the bettercap module to create ARP spoofing attacks, run DNS spoofing, and hsts hijacking (downgrading https to http).
- Writing custom spoofing scripts and caplets.
- Proficiency in the use of network analysing tools like wireshark.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.

### Tools Used

- VMware Fusion
- Kali linux and Windows 11 machines
- Network scanning tools like Netdiscover and Zenmap.
- ARP spoofing tool module BETTERCAP, used for network probing, https bypass, dns spoofing, and code injection.
- Wireshark for analysing packets and captured traffic.
  
## Steps

- STEP 1 Information Gathering

In this step, I used Zenmap GUI to perform a network scan on the target range 192.168.0.1/24. This instructed Zenmap to identify active IP addresses from 192.168.0.1 to 192.168.0.255 using the Quick Scan Plus option, which provided detailed information about devices connected. The diagram below illustrates the scan results

N/B: In this case I used my home network instead of the virtual NAT network, to show more information.
  
*Ref 1: Zenmap GUI* <img width="1470" height="956" alt="Screenshot 2025-08-19 at 7 49 39 PM" src="https://github.com/user-attachments/assets/a2684f25-4cd5-4262-b48a-007805789364" />




- STEP 2 Launching Bettercap

In this step, the Bettercap module was launched to begin configuration for the ARP spoofing attack. The command used was "bettercap -iface eth0", where the "-iface eth0" flag specifies the network interface to listen on and execute the ARP spoofing attack. 

N/B: eth0 is the interface for the virtual NAT network the two virtual machines are connected to.
  
*Ref 2: Bettercap Launch* <img width="1470" height="956" alt="Screenshot 2025-08-21 at 4 05 24 PM" src="https://github.com/user-attachments/assets/ba7c1719-1433-4c65-a9a9-d9ec56252b89" />




- STEP 3 Probing the Network For Hosts 

In this step, the "net.probe on" command was executed to probe the network for active hosts, followed by the net.show command to display the discovered hosts in a structured tabular format. As seen in the diagram above the access point has an IP of 172.16.108.2 and the target windows has an IP of 172.16.108.133


  
*Ref 3: Network Probing* <img width="1470" height="956" alt="Screenshot 2025-08-21 at 5 43 50 PM" src="https://github.com/user-attachments/assets/086cf74d-4989-4cca-bc85-023c7b02064f" />

*Ref 4: Discovered Hosts* <img width="1457" height="333" alt="Screenshot 2025-08-19 at 9 01 09 PM" src="https://github.com/user-attachments/assets/c554924a-d9fb-4b0f-8de8-a33f4be7b7f7" />




- STEP 4 Configuring the ARP Spoofing module in bettercap

I set the parameters of the arp.spoof module to the following:

"set arp.spoof.fullduplex true". If this is set to true, both the target and the gateway (access point) will be attacked. 
"set arp.spoof.targets 172.16.108.133". The target is set to the IP of the target windows computer.


*Ref 5: ARP Spoof Configuration* <img width="1465" height="36" alt="Screenshot 2025-08-19 at 9 09 26 PM" src="https://github.com/user-attachments/assets/da682356-af96-49c1-9524-3399b9e9914e" />

*Ref 6: ARP Spoof Configuration* <img width="1463" height="31" alt="Screenshot 2025-08-19 at 9 10 42 PM" src="https://github.com/user-attachments/assets/73a652ff-67a9-4840-bb7e-b003021d0366" />




- STEP 5 ARP Spoofing is Turned On

In this step, ARP spoofing was initiated using the "arp.spoof on" command. As shown in the target Windows terminal (Ref 8 diagram), the gateway MAC address at 172.16.108.2 was replaced with the MAC address of the attack Linux machine at 172.16.108.132. This confirmed the success of the ARP spoofing attack, establishing the linux machine as the man-in-the-middle.


*Ref 7: ARP Spoof On* <img width="1470" height="956" alt="Screenshot 2025-08-19 at 9 11 50 PM" src="https://github.com/user-attachments/assets/e5b821c9-972e-45c2-be05-74bb409ba3c5" />

*Ref 8: ARP Spoofing Successful* <img width="1470" height="956" alt="Screenshot 2025-08-19 at 9 14 06 PM" src="https://github.com/user-attachments/assets/825d3e47-284e-423c-8f8c-27caaa302f36" />




- STEP 6 Packet Sniffing Initiated

Next, packet sniffing was initiated using "net.sniff" to capture and analyze network traffic between the target and the gateway on the attack linux.


*Ref 9: Packet Sniffing Initiated* <img width="1470" height="246" alt="Screenshot 2025-08-19 at 9 20 17 PM" src="https://github.com/user-attachments/assets/70ebf254-4e41-417b-8e44-7eb9a1b70921" />


N/B: This step proved that the MITM attack worked succesfully but i was not done yet. I still has to make the process faster and cleaner and also to downgrade the https connection, and enable dns spoofing.




- STEP 7 Writing a Custom Spoofing Script 

In this step, I created a custom spoofing script/caplet using the text editor on the Linux machine and executed it in Bettercap using the "-caplet" flag. This allowed the script to automate the spoofing process, removing the need to manually execute each step.

*Ref 10: Custom Spoofing Script* <img width="1470" height="956" alt="Screenshot 2025-08-20 at 3 36 13 PM" src="https://github.com/user-attachments/assets/5042dbdc-21e6-48a4-b274-b5cf1b30d3b9" />


*Ref 11: Using "-caplet"* <img width="1470" height="956" alt="Screenshot 2025-08-20 at 3 37 35 PM" src="https://github.com/user-attachments/assets/6569addb-a184-4034-adc6-e82d3c8ca00d" />


*Ref 12: Running Bettercap Using Caplet* <img width="699" height="803" alt="Screenshot 2025-08-20 at 3 40 19 PM" src="https://github.com/user-attachments/assets/647c4834-e7cf-4f29-b600-c1395b679851" />




- STEP 7 Downgrading HTTPS CConnection to HTTP 

I found and modified Bettercap’s hstshijack caplet to improve its functionality and extend support to additional websites such as Netflix, Stack Overflow, and LinkedIn. After the modifications, I executed the hstshijack command and enabled SSL stripping to intercept and manipulate HTTPS traffic.


*Ref 13: Finding the PAth to Beetercaps HSTSHIJACK Caplet* <img width="1470" height="956" alt="Screenshot 2025-08-20 at 3 50 51 PM" src="https://github.com/user-attachments/assets/4b031a8b-0c22-4e2f-a16f-93f5ebf72e81" />


*Ref 14: Modifying the HSTSHIJACK Caplet* <img width="1470" height="956" alt="Screenshot 2025-08-21 at 7 13 28 PM" src="https://github.com/user-attachments/assets/b1399e80-3d88-4176-a024-eddff3f5cacb" />

*Ref 15: Running HSTSHIJACK Caplet* <img width="1470" height="956" alt="Screenshot 2025-08-20 at 3 51 55 PM" src="https://github.com/user-attachments/assets/36fe96c8-1280-476c-92f1-a1612cd70b59" />




- STEP 8  Proof of Successful SSL Striping

After executing the hstshijack caplet, i proceeded to the windows target machine and searched spoofed sites like, stackoverflow.com and netflix.com. THese sites where successfully downgraded to http connections as seen in (Ref 16, 17 & 18).


*Ref 16: Successful Downgrade LinkedIn* <img width="1470" height="956" alt="Screenshot 2025-08-20 at 4 19 14 PM" src="https://github.com/user-attachments/assets/e7d39f14-a16a-4a0c-8d84-7681e6256ce9" />


*Ref 17: Successful Downgrade Stackoverflow* <img width="1470" height="956" alt="Screenshot 2025-08-20 at 4 29 56 PM" src="https://github.com/user-attachments/assets/e32873de-79a8-4d7c-9298-a383fb57d28b" />



*Ref 18: Successful Downdrade Netflix* <img width="1470" height="956" alt="Screenshot 2025-08-20 at 4 37 25 PM" src="https://github.com/user-attachments/assets/6c8619fc-15db-423d-85f6-88e00192891a" />





- STEP 9  Using Wireshark to Analyse Captured Packets

In this step, Wireshark was launched after executing the hstshijack caplet to capture network packets. A HTTP filter was then applied to isolate and analyze only the captured HTTP traffic.

*Ref 19: Captured packets* <img width="1470" height="956" alt="Screenshot 2025-08-20 at 8 01 55 PM" src="https://github.com/user-attachments/assets/1199c738-c512-4580-9657-9e53ea64e513" />



*Ref 20: Filtering HTTP Pakets* <img width="1470" height="956" alt="Screenshot 2025-08-20 at 8 02 06 PM" src="https://github.com/user-attachments/assets/0bc9fc8e-e92c-47af-afad-11294d4bf9a6" />




- STEP 10  Analysing Captured packets

I analyzed the captured packets by isolating HTTP POST requests, which are typically used to transmit form data such as login credentials. From this traffic, I was able to identify plaintext usernames and passwords entered on the target websites, confirming the success of the interception. 


*Ref 21: Captured Password & Username* <img width="1470" height="956" alt="Screenshot 2025-08-20 at 8 06 01 PM" src="https://github.com/user-attachments/assets/ddd0f31a-df1b-4324-b739-4b959a43875e" />





### Detection

Applications like XArp can be used to detect ARP spoofing attacks. XArp monitors network traffic for suspicious ARP activity and notifies the user when a potential spoofing attempt is detected. This allows network administrators and users to respond promptly to mitigate man-in-the-middle attacks and protect sensitive data.

Download link - https://softfamous.com/xarp/





### Mitigation

To prevent MITM attacks, I recommend using a VPN with HTTPS enforcement tools like HTTPS Everywhere. This combination encrypts network traffic and ensures secure connections, providing a double layer of protection against interception.


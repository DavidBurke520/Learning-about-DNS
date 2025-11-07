<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1> Learning DNS</h1>
This tutorial is a basic overview of DNS servers and the ipconfig /flushdns command. It is sometimes required when a computer isn’t connecting to a server. We can use the virtual machines we created previously. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- DNS (Dynamic Name System)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)

<h2>Actions and Observations</h2>

<p>
<img width="1919" height="1079" alt="1" src="https://github.com/user-attachments/assets/aad19bf1-dbb4-4bc4-aff4-c38106343a3c" />
</p>
<p>
Start both virtual machines and log in to DC-1 and Client-1 simultaneously in separate windows. From Client-1, we will run PowerShell ISE as Admin and try to ping “mainframe”. It will fail because it can’t find “mainframe” in the Local Cache, the Local Host File, or the DNS Server. It doesn’t have an IP Address stored for the name “mainframe.” 
To see the Local Cache, type “ipconfig /displayDNS” in PowerShell. It will show a long list of stored IP Addresses. Unfortunately, “mainframe” is not stored in the Local Cache.
</p>
<br />

<p>
<img width="1918" height="1079" alt="2" src="https://github.com/user-attachments/assets/f7af4a89-fb21-4706-ad5f-185bab8c34a3" />
</p>
<p>
You can also check the Local Host File. To do this, run Notepad as Admin. Go to File and select Open. For file type, select All files. 
Go to This PC→Windows(C:)→Windows→System32→drivers→etc→hosts. This will show the Local Host File. Unfortunately, “mainframe” is not stored in the Local Host File.
</p>
<br />

<p>
<img width="1919" height="1079" alt="3" src="https://github.com/user-attachments/assets/1b98e22d-2137-4e82-890a-db67d944a146" />
</p>
<p>
Last, you can check the DNS server. In PowerShell, type “nslookup mainframe”. It will say it can't find /mainframe: Non-existent domain. Unfortunately, “mainframe” is not stored in the DNS Server.
</p>
<br />

<p>
<img width="1917" height="1079" alt="4" src="https://github.com/user-attachments/assets/103b2e65-1f56-4932-8e22-edcc67bf2c85" />
</p>
<p>
To solve this, we can create an A record for “mainframe” on DC-1 and point it to DC-1’s Private IP address. Go to the DNS Server or DC-1, click Start, open Administrative Tools, and select DNS. 
Double-Click Dc-1→Forward lookup zones→mydomain.com. On the right, you will notice Names and IP Addresses.
</p>
<br />

<p>
<img width="1917" height="1079" alt="5" src="https://github.com/user-attachments/assets/b775abb2-d9f0-45d2-ba69-c5312d7127a6" />
</p>
<p>
Find the DC-1 private IP Address, open PowerShell, and type ipconfig. Take note of the IPv4 Address.
</p>
<br />

<p>
<img width="1916" height="1079" alt="6" src="https://github.com/user-attachments/assets/64ec4ae1-fc06-4af6-9802-607340ce6b60" />
</p>

<p>
<img width="1912" height="1079" alt="7" src="https://github.com/user-attachments/assets/e6014aeb-736f-403f-984e-34d60f363e34" />
</p>
<p>
Now, create a record with the hostname "Mainframe" pointing to 10.0.1.4. In DNS Manager, Right-Click and select New Host (A or AAA). Name it “mainframe” and input the IPv4 Address from above. Check the box named “Create associated pointer (PTR) record. This will allow a reverse lookup of 10.0.1.4 to Hostname Mainframe. Click Add host.
</p>
<br />

<p>
<img width="1919" height="1079" alt="8" src="https://github.com/user-attachments/assets/4fd9ef70-6484-4453-8c7f-27c7dde525f3" />
</p>
<p>
Return to Client-1 and try to ping “mainframe” again. It now finds “mainframe at IP Address 10.0.1.4. What we did in the previous step allowed us to find “mainframe” on the DNS Server.
</p>
<br />

<p>
<img width="1918" height="1079" alt="9" src="https://github.com/user-attachments/assets/d33bb39c-21ac-4d74-917e-079071ce6fe8" />
</p>
<p>
Now, let's simulate that mainframe's IP address changes on the DNS server after the Local DNS Cache has stored it. On DC-1, in the DNS Manager, change the IP address we just set for Mainframe to 8.8.8.8. On Client-1, ping mainframe again, and it will still say it's on 10.0.1.4. We need to flush the Local DNS Cache by typing “ipconfig /flushdns”. Now ping mainframe and notice it shows 8.8.8.8. 
</p>
<br />

<p>
<img width="1916" height="1079" alt="10" src="https://github.com/user-attachments/assets/2e6e3b88-e310-4e2f-ac8e-319e62e77bf6" />
</p>
<p>
Go back to DC-1 and create a CNAME record that points the host “serach” to “www.google.com”. Right-click inside DNS Manager and select New Alias (CNAME). Name it “Search” and enter “www.google.com” in the Fully qualified domain name. Then click OK.
</p>
<br />

<p>
<img width="1915" height="1078" alt="11" src="https://github.com/user-attachments/assets/9773d373-b599-4cd9-b7d8-de14668f1cba" />
</p>
<p>
Back on Client-1, we can ping “search” and take note that it goes to www.google.com. Now type “nslookup search” and notice the results of the CNAME, it shows IP Address 10.0.1.4.
</p>
<br />

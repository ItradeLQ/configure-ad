<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>

Welcome back! This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Setup Resources In Azure
- Ensure Connection between Client and Domain Controller
- Install Active Directory and Admin Creation
- Create X-Amount of Client Users using PowerShell Script

<h2>Setup Resources in Azure</h2>

1. Create the Domain Controller VM (Windows Server 2022) named “DC-1"
   Take note of the Resource Group and Virtual Network (Vnet) that get created at this time
2. Set Domain Controller’s Network Interface Card (NIC) Private IP address to be static
DC-1 > Networking > NIC > IP Configurations

<a href="https://imgur.com/FPO2IYk"><img src="https://i.imgur.com/FPO2IYk.png" title="source: imgur.com" /></a>

<a href="https://imgur.com/XGvwlCc"><img src="https://i.imgur.com/XGvwlCc.png" title="source: imgur.com" /></a>

3. Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in the DC-1 step.
4. Ensure that both VMs are in the same Vnet [you can check the topology with Network Watcher]
Here is an illustration of what we are doing: 

<a href="https://imgur.com/8pnNkjj"><img src="https://i.imgur.com/8pnNkjj.png" title="source: imgur.com" /></a>


<a href="https://imgur.com/2URx1hb"><img src="https://i.imgur.com/2URx1hb.png" title="source: imgur.com" /></a>

<h2>Ensure Connection between Client and Domain Controller<h2>

1. Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping)

<a href="https://imgur.com/1eG0bfT"><img src="https://i.imgur.com/1eG0bfT.png" title="source: imgur.com" /></a>

Notice how, we are getting a "request timed out." Let us fix that. 

2. Login to the Domain Controller and enable ICMPv4 on the local Windows Firewall. Keep client-1 instance open. 

- Start Menu -> Windows Defender Firewall with Advanced Security programme -> Inbound Rules -> Sort by Protocol -> 

- Enable "Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In) Private and Domain Profiles. 2 Inbound Rules.

<a href="https://imgur.com/xYKRbfw"><img src="https://i.imgur.com/xYKRbfw.png" title="source: imgur.com" /></a>


3. Return to Client-1 to see the ping succeed

<a href="https://imgur.com/9WQydZ5"><img src="https://i.imgur.com/9WQydZ5.png" title="source: imgur.com" /></a>

ICMP is what is used to ping the VM, hence why traffic resumes when it is enabled.

<h2>Install Active Directory<h2>


1. Login to DC-1 and install Active Directory Domain Services

- Server Manager > "Add Roles and Features" > Check "Active Directory Domain Services"

<a href="https://imgur.com/N3ssKOS"><img src="https://i.imgur.com/N3ssKOS.png" title="source: imgur.com" /></a>

2. Promote as a DC: Setup a new forest as mydomain.com (can be anything, just remember what it is)

<a href="https://imgur.com/rxf1BoP"><img src="https://i.imgur.com/rxf1BoP.png" title="source: imgur.com" /></a>

![2023-01-18 09 38 10 coursecareers com 78e39ae4181d](https://user-images.githubusercontent.com/109401839/213215738-c6379380-e5b8-438b-95a8-6906a16ff339.jpg)

3. Restart and then log back into DC-1 as user: mydomain.com\labuser which is the Fully Qualified Domain Name (FQDN) to the DC-1 Sever.

<a href="https://imgur.com/ulqvlWB"><img src="https://i.imgur.com/ulqvlWB.png" title="source: imgur.com" /></a>

<a href="https://imgur.com/gMUnmgV"><img src="https://i.imgur.com/gMUnmgV.png" title="source: imgur.com" /></a>

4. In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES"

<a href="https://imgur.com/2kKLszL"><img src="https://i.imgur.com/2kKLszL.png" title="source: imgur.com" /></a>

5. Create a new Organisational Unit (OU) named “_ADMINS"

<a href="https://imgur.com/r90qNqo"><img src="https://i.imgur.com/r90qNqo.png" title="source: imgur.com" /></a>

6. Create a new employee named “Jane Doe” (same password) with the username of “jane_admin”
7. Add jane_admin to the “Domain Admins” Security Group

<a href="https://imgur.com/XaLDemD"><img src="https://i.imgur.com/XaLDemD.png" title="source: imgur.com" /></a>

8. Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin"
9. User jane_admin as your admin account from now on

<h2>Join Client-1 to your domain (mydomain.com)<h2>

<a href="https://imgur.com/2URx1hb"><img src="https://i.imgur.com/2URx1hb.png" title="source: imgur.com" /></a>

1. From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address
2. From the Azure Portal, restart Client-1, which will flush DNS cache
3. Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart). Run Powershell to ensure that the DNS of Client-1 now points to the private IP address of DC-1 and you're not getting an error when joining Client-1 to the domain.
4. Client-1 will prompt to restart to join the Domain.
   
<a href="https://imgur.com/d7BbzBz"><img src="https://i.imgur.com/d7BbzBz.png" title="source: imgur.com" /></a>

<a href="https://imgur.com/6R2Rcwc"><img src="https://i.imgur.com/6R2Rcwc.png" title="source: imgur.com" /></a>

<a href="https://imgur.com/AWXg6hz"><img src="https://i.imgur.com/AWXg6hz.png" title="source: imgur.com" /></a>

<a href="https://imgur.com/UlB5k9H"><img src="https://i.imgur.com/UlB5k9H.png" title="source: imgur.com" /></a>

<H2>Setup Remote Desktop for non-administrative users on Client-1<H2>

1. Log into Client-1 as mydomain.com\jane_admin and open system properties
2. Click “Remote Desktop”
3. Allow “domain users” access to remote desktop

<a href="https://imgur.com/lyyCc33"><img src="https://i.imgur.com/lyyCc33.png" title="source: imgur.com" /></a>

4. You can now log into Client-1 as a normal, non-administrative user now
5. Normally you’d want to do this with Group Policy that allows you to change MANY systems at once

<H2>Create additional users and attempt to log into Client-1 with one of the users<H2>

1. Login to DC-1 as jane_admin
2. Open PowerShell_ISE as an administrator
3. Create a new File and paste the contents of the [script] below:


> '''Function generate-random-name() {
>    $consonants = @('b','c','d','f','g','h','j','k','l','m','n','p','q','r','s','t','v','w','x','z')
>    $vowels = @('a','e','i','o','u','y')
>    $nameLength = Get-Random -Minimum 3 -Maximum 7
>    $count = 0
>    $name = ""
>
>    while ($count -lt $nameLength) {
>        if ($($count % 2) -eq 0) {
>            $name += $consonants[$(Get-Random -Minimum 0 -Maximum $($consonants.Count - 1))]
>        }
>        else {
>            $name += $vowels[$(Get-Random -Minimum 0 -Maximum $($vowels.Count - 1))]
>        }
>        $count++
>    }
>
>    return $name
>
> }
>
> $count = 1
> while ($count -lt $NUMBER_OF_ACCOUNTS_TO_CREATE) {
>    $fisrtName = generate-random-name
>    $lastName = generate-random-name
>    $username = $fisrtName + '.' + $lastName
>    $password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force
>
>    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
>    
>    New-AdUser -AccountPassword $password `
>               -GivenName $firstName `
>               -Surname $lastName `
>               -DisplayName $username `
>               -Name $username `
>               -EmployeeID $username `
>               -PasswordNeverExpires $true `
>               -Path "ou=_EMPLOYEES,$(([ADSI]`"").distinguishedName)" `
>               -Enabled $true
>    $count++
> }''' 

[Code Source](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)

4. Run the script and observe the accounts being created
   
<a href="https://imgur.com/Vh3mwmn"><img src="https://i.imgur.com/Vh3mwmn.png" title="source: imgur.com" /></a>

5. When finished, open Active Directory Users and Control (ADUC) and observe the accounts in the appropriate OU
   
6. Attempt to log into Client-1 with one of the accounts (take note of the password in the script)

<a href="https://imgur.com/ViJtkIb"><img src="https://i.imgur.com/ViJtkIb.png" title="source: imgur.com" /></a>


We've reached the end of this exciting tutorial! In the [next tutorial](https://github.com/ItradeLQ/azure-network-protocols), the focus will be reviewing various network traffic to and from Azure Virtual Machines with Wireshark and experimenting with Network Security Groups (NSGs).

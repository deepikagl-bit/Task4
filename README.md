README.md
# Task 4: Setup and Use a Firewall (Windows / Linux)

**Objective:** Configure and test basic firewall rules to allow or block traffic.  
**Tools:** UFW (Uncomplicated Firewall) for Linux, Windows Firewall (PowerShell / netsh) for Windows.  
**Deliverables:** Commands/configuration, test steps, and screenshots showing rules applied.

---

## Repo structure


.
├─ README.md
├─ linux/
│ ├─ ufw_commands.sh
│ ├─ ufw_rules_output.txt
│ └─ screenshots/
│ ├─ ufw_status.png
│ └─ ufw_test.png
├─ windows/
│ ├─ powershell_commands.ps1
│ ├─ netsh_rules_output.txt
│ └─ screenshots/
│ ├─ wf_rules.png
│ └─ wf_test.png
├─ interview_answers.md
└─ LICENSE


---

## How to use
1. Choose your OS directory (`linux/` or `windows/`).
2. Run the commands (instructions in each file). For Linux, you will need `sudo`.
3. Save screenshots into the `screenshots/` folder (names above) and commit them.
4. After completion, paste this repository link into the provided submission Google Form:
   `https://forms.gle/8Gm83s53KbyXs3Ne9`

---

## Notes & safety
- Do not expose remote services to the public Internet during testing.
- If you open or block ports, revert the test rules at the end (commands provided).

📄 interview_answers.md
# Interview Questions — Answers

**1. What is a firewall?**  
A firewall is a network security device or software that monitors and controls incoming and outgoing network traffic based on an applied rule set. It enforces a security policy to allow, deny, or log traffic between networks or hosts.

**2. Difference between stateful and stateless firewall?**  
- *Stateless firewall* filters packets based solely on packet headers (IP, port, protocol) and treats each packet independently.  
- *Stateful firewall* keeps track of connection state (e.g., TCP handshake) and uses state information to make more informed decisions, allowing return traffic from established connections automatically.

**3. What are inbound and outbound rules?**  
- *Inbound rules* control traffic coming into a host or network.  
- *Outbound rules* control traffic leaving a host or network.

**4. How does UFW simplify firewall management?**  
UFW (Uncomplicated Firewall) provides a simplified command-line interface for managing iptables rules. It abstracts complex iptables commands into simple allow/deny commands, supports named applications, and enables easy rule management for common tasks.

**5. Why block port 23 (Telnet)?**  
Telnet transmits data (including credentials) in plaintext and lacks modern authentication and encryption. Blocking port 23 prevents insecure remote login and reduces risk of credential interception and unauthorized access.

**6. What are common firewall mistakes?**  
- Blocking management ports (e.g., SSH) without an alternate access method.  
- Leaving unnecessary ports open.  
- Misconfigured rules order (on devices where order matters).  
- Relying only on firewall without host hardening or monitoring.  
- Not logging or monitoring firewall events.

**7. How does a firewall improve network security?**  
A firewall enforces network access policies, reduces attack surface by limiting services/ports, prevents unauthorized access, and can log suspicious traffic for further analysis.

**8. What is NAT in firewalls?**  
NAT (Network Address Translation) translates private IP addresses to a public IP address for outgoing traffic. Firewalls often integrate NAT to allow internal hosts to access external networks while hiding internal addressing.

📄 COMMIT_INSTRUCTIONS.txt
1) Create directories:
   mkdir -p linux/screenshots windows/screenshots

2) Copy the provided files into the repo structure.

3) Take screenshots:
   - Run 'sudo ufw status verbose' and take a screenshot -> linux/screenshots/ufw_status.png
   - Run the test telnet/nc attempt and capture -> linux/screenshots/ufw_test.png
   - On Windows, use Windows Firewall GUI or PowerShell and capture -> windows/screenshots/wf_rules.png and wf_test.png

4) Commit:
   git add .
   git commit -m "Task 4: UFW and Windows Firewall configuration, commands, outputs, and interview answers"
   git push origin main

5) Submit the repo link on the Google Form:
   https://forms.gle/8Gm83s53KbyXs3Ne9

📄 linux/ufw_commands.sh
#!/bin/bash
# ufw_commands.sh
# Run these commands on an Ubuntu/Debian-based Linux with sudo.

set -e

echo "1) Check UFW status and existing rules"
sudo ufw status numbered

echo
echo "2) Enable UFW if disabled (careful: this will enforce default rules)"
# Uncomment next line only if you are ready to enable it.
# sudo ufw enable

echo
echo "3) Example: Block inbound Telnet (port 23)"
sudo ufw deny 23/tcp comment 'Block Telnet for Task 4'

echo
echo "4) Example: Allow SSH (port 22) so you don't lock yourself out"
sudo ufw allow 22/tcp comment 'Allow SSH'

echo
echo "5) Check rules after changes"
sudo ufw status verbose

echo
echo "6) Test connectivity (on same host or remote host):"
echo " - From the same machine, try: nc -zv localhost 23"
echo " - From another host: nc -zv <target-ip> 23"
echo
echo "7) Remove the Telnet block rule (cleanup)"
sudo ufw delete deny 23/tcp

echo
echo "8) OPTIONAL: Reset UFW to default (removes all user rules)"
# sudo ufw reset

echo "Done. Save screenshots of 'sudo ufw status verbose' as linux/screenshots/ufw_status.png"

📄 linux/ufw_rules_output.txt
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
23/tcp                     DENY        Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
23/tcp (v6)                DENY        Anywhere (v6)

📄 windows/powershell_commands.ps1
# powershell_commands.ps1
# Run as Administrator PowerShell

Write-Host "1) List current inbound rules (filtered example)"
Get-NetFirewallRule -Direction Inbound | Select-Object -First 50 | Format-Table -AutoSize

Write-Host "2) Block inbound Telnet (port 23) - create a rule to block TCP port 23"
New-NetFirewallRule -DisplayName "Block Telnet - Task4" -Direction Inbound -Action Block -Protocol TCP -LocalPort 23 -Description "Block Telnet for Task 4"

Write-Host "3) Allow SSH (port 22) if required (on Windows with SSH server installed)"
New-NetFirewallRule -DisplayName "Allow SSH - Task4" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 22 -Description "Allow SSH for remote admin"

Write-Host "4) View the rules you just created"
Get-NetFirewallRule -DisplayName "Block Telnet - Task4" | Format-Table -AutoSize
Get-NetFirewallRule -DisplayName "Allow SSH - Task4" | Format-Table -AutoSize

Write-Host "5) Test from another machine: telnet <windows-ip> 23  (should fail)."
Write-Host "   Or use PowerShell Test-NetConnection -ComputerName <IP> -Port 23"

Write-Host "6) Remove the Block Telnet rule (cleanup)"
Remove-NetFirewallRule -DisplayName "Block Telnet - Task4"
Write-Host "Cleanup done."

📄 windows/netsh_rules_output.txt
# Example netsh advfirewall firewall show rule name=all output snippet:
Rule Name: Block Telnet - Task4
----------------------------------------------------------------------
Enabled: Yes
Direction: In
Profiles: Domain,Private,Public
Action: Block
Protocol: TCP
LocalPort: 23
Edge traversal: No

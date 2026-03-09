# Brute Force Investigation

A SOC investigation simulating and detecting an SSH brute force 
attack using Hydra and Splunk.

## Attack Summary

| Field | Value |
|---|---|
| Attack Type | SSH Brute Force |
| Tool Used | Hydra v9.6 |
| Target | Windows 11 VM (10.0.2.15) |
| Attacker | Kali Linux |
| Protocol | SSH Port 22 |
| Detection Method | Splunk EventCode 4625 analysis |

## Lab Environment
```
Kali Linux (Attacker)
        ↓  [Hydra SSH brute force]
Windows 11 VM (Victim)
        ↓  [Generates EventCode 4625]
Splunk Universal Forwarder
        ↓  [Ships logs]
Splunk SIEM
        ↓  [Detection query fires]
SOC Analyst (You)
```

## Detection Query
```splunk
index=main EventCode=4625
| bin _time span=1m
| stats count by _time, Account_Name
| where count > 3
```

## Key Findings

| Finding | Detail |
|---|---|
| Failed logins | 50+ in 1 minute |
| Accounts targeted | user, FakeUser, WINDEV2407EVAL$ |
| Failure reasons | Bad password, Account lockout |
| Attack process | sshd.exe |
| Access gained | No |

## Project Structure

| Folder | Contents |
|---|---|
| Attack-Simulation | Hydra commands and attack setup |
| Investigation | Splunk queries and analysis |
| Incident-Report | Full SOC incident report |
| Screenshots | Evidence screenshots |

## Investigation Status

✅ Attack simulated using Hydra  
✅ 30+ EventCode 4625 detected in Splunk  
✅ Account lockout triggered and logged  
✅ Brute force pattern confirmed  
✅ Incident report completed  
```
Name:
```
Attack-Simulation/attack-details.md

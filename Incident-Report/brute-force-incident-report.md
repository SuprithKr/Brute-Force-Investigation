# Incident Report — Brute Force Attack
**Date:** 2026-03-09  
**Analyst:** Suprith Kr  
**Severity:** High  
**Status:** Resolved  

---

## 1. Executive Summary

A brute force attack was detected against the Windows 11 victim 
machine (WinDev2407Eval) on 2026-03-09. The attacker used Hydra 
to systematically attempt SSH logins using a password list. 
The attack generated 50+ failed login attempts within a single 
minute, triggering account lockouts. The attack was detected 
through Splunk log analysis using Windows Security EventCode 4625.

---

## 2. Attack Details

| Field | Value |
|---|---|
| Attack Type | SSH Brute Force |
| Tool Used | Hydra v9.6 |
| Target Machine | WinDev2407Eval (10.0.2.15) |
| Attacker Machine | Kali Linux (10.0.2.x) |
| Protocol | SSH (Port 22) |
| Attack Time | 2026-03-09 14:07:00 |
| Total Failures | 50+ in 1 minute |

---

## 3. Log Evidence

### EventCode 4625 — Failed Logon Events

| Account | Logon Type | Failure Reason | Count |
|---|---|---|---|
| user | 8 (Network Cleartext) | Unknown username or bad password | 10 |
| user | 8 (Network Cleartext) | Account locked out | 12 |
| WINDEV2407EVAL$ | 8 | Account locked out | 12 |
| FakeUser | 8 | Unknown username or bad password | 2 |

### Key Event Fields
```
EventCode:       4625
LogName:         Security
Computer:        WinDev2407Eval
Failure Reason:  Account locked out
Logon Process:   sshd
Caller Process:  C:\Windows\System32\OpenSSH\sshd.exe
Keywords:        Audit Failure
```

---

## 4. Detection Logic

### Splunk Query Used
```
index=main EventCode=4625
| bin _time span=1m
| stats count by _time, Account_Name
| where count > 3
```

### Result
```
2026-03-09 14:07:00   WINDEV2407EVAL$   28 failures
2026-03-09 14:07:00   user              22 failures
```

50 failed attempts in 1 minute = confirmed brute force pattern.

---

## 5. Timeline

| Time | Event |
|---|---|
| 14:07:00 | Hydra begins SSH brute force from Kali |
| 14:07:00 | Windows logs first EventCode 4625 |
| 14:07:01 | Account lockout triggered after repeated failures |
| 14:07:21 | 50+ failures recorded in Splunk |
| 14:07:22 | Attack ends — Hydra completes password list |

---

## 6. Impact Assessment

| Item | Detail |
|---|---|
| Account Compromised | No — attack did not succeed |
| Account Locked Out | Yes — user account locked |
| Data Exfiltrated | No |
| Persistence Established | No |

---

## 7. Mitigation Recommendations

1. **Disable SSH if not required** — reduces attack surface
2. **Implement account lockout policy** — already triggered, 
   confirm policy is set to lock after 5 attempts
3. **Block failed login IPs** — implement fail2ban or 
   Windows Firewall rules after repeated failures
4. **Use SSH key authentication** — disable password-based 
   SSH login entirely
5. **Enable alerting in Splunk** — create alert for 
   EventCode 4625 count > 10 in 1 minute

---

## 8. Splunk Alert Rule

To automatically detect future brute force attacks:
```
index=main EventCode=4625
| bin _time span=1m
| stats count by _time, Account_Name
| where count > 10
```

Set alert to trigger when results > 0.

---

## 9. Conclusion

The brute force attack was successfully simulated and detected 
using Splunk log analysis. The detection query identified 50 
failed login attempts in a single minute targeting the SSH 
service. No unauthorized access was gained. The account lockout 
policy partially mitigated the attack by locking the targeted 
account.

This investigation demonstrates the SOC workflow for brute 
force detection: attack simulation → log collection → 
SIEM analysis → incident reporting.

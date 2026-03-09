# Splunk Investigation Queries — Brute Force

## Query 1 — Find All Failed Logins
```
index=main EventCode=4625
```
Purpose: Shows all failed login attempts

## Query 2 — Count Failures by Account
```
index=main EventCode=4625 
| stats count by Account_Name, Logon_Type, Failure_Reason
```
Purpose: Identifies which accounts were targeted

## Query 3 — Attack Timeline
```
index=main EventCode=4625 
| timechart count span=1m
```
Purpose: Shows when the attack occurred

## Query 4 — Brute Force Detection Rule
```
index=main EventCode=4625
| bin _time span=1m
| stats count by _time, Account_Name
| where count > 3
```
Purpose: Detects brute force pattern — 
more than 3 failures per minute per account

## Results

| Query | Finding |
|---|---|
| All failures | 30 EventCode 4625 events |
| By account | user (22), WINDEV2407EVAL$ (28) |
| Timeline | Spike at 14:07:00 |
| Detection rule | 50 failures in 1 minute confirmed |

09-4625-events-detected.png
10-attack-timeline.png
11-failure-stats.png
12-full-event-detail-1.png
13-full-event-detail-2.png
14-brute-force-detection-query.png

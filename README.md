# Windows and Active Directory Security Lab

This repository contains my Windows and Active Directory security practice for a Junior SOC Analyst / SOC L1 role.

The goal of this lab is to practice Windows logs, processes, network activity, authentication events and basic Active Directory security analysis.

## Environment

- Windows 11
- Windows Event Viewer
- CMD
- Windows Security Log
- Local Windows host
- Active Directory training scenarios

## Windows process analysis

I practiced working with Windows processes and PID information.

Commands used:

```cmd
whoami
hostname
tasklist
netstat -ano
```

I analyzed:

- process names
- PID
- local and remote addresses
- listening ports
- established connections
- process and network correlation

Example:

```text
Process
↓
PID
↓
Network connection
```

Important lessons:

```text
Process name does not prove legitimacy.
External connection does not automatically mean C2.
Port 443 does not prove HTTPS.
```

## Network activity

I used:

```cmd
netstat -ano
```

to analyze TCP connections and listening ports.

I practiced the difference between:

```text
LISTENING
ESTABLISHED
```

Important lessons:

```text
LISTENING means a process is waiting for connections.
ESTABLISHED means a TCP connection exists.
0.0.0.0 means listening on all IPv4 interfaces.
127.0.0.1 means localhost.
```

## Windows Security Log

I used Windows Event Viewer to analyze Security events.

Path:

```text
Event Viewer
↓
Windows Logs
↓
Security
```

I analyzed real logon events such as:

```text
4624
4625
```

## Event ID 4624

```text
4624
→ Successful Logon
```

I practiced different Logon Types.

Examples:

```text
Type 5
→ Service

Type 7
→ Unlock

Type 10
→ RemoteInteractive / RDP

Type 11
→ CachedInteractive
```

Important lessons:

```text
Successful Logon does not mean administrator access.
SYSTEM activity is not automatically malicious.
Account used does not prove the physical person.
```

## Event ID 4625

```text
4625
→ Failed Logon
```

I analyzed fields such as:

- Logon Type
- Account Name
- Account Domain
- Process
- Source Network Address
- authentication information

Important lessons:

```text
One failed logon does not mean brute force.
127.0.0.1 is localhost.
Failed logon needs context and timeline.
```

## Subject and account context

During log analysis I practiced the difference between:

```text
Subject
→ account or process context that generated the action

New Logon / Target Account
→ account related to the new session or action
```

Important lesson:

```text
Account context does not prove which physical person performed the action.
```

## Windows Services

I practiced basic Windows service analysis.

Commands used:

```cmd
sc query EventLog
sc qc EventLog
tasklist /svc
```

I analyzed:

- service state
- startup type
- binary path
- service account
- process and PID

Example:

```text
Service: EventLog
State: RUNNING
Startup type: AUTO_START
Process: svchost.exe
```

Important lessons:

```text
AUTO_START does not mean the service is currently running.
RUNNING does not mean the service is safe.
Binary path does not prove execution by itself.
```

## Active Directory fundamentals

I studied basic Active Directory concepts:

- Domain
- Domain Controller
- Domain User
- Computer Account
- Groups
- Domain Admins
- Organizational Units
- Group Policy

Example:

```text
CORP\ivan
→ domain account

PC-15\ivan
→ local account

PC-15$
→ computer account
```

Important lesson:

```text
Local account and domain account are different identities.
```

## Kerberos

I studied the basic Kerberos authentication flow.

```text
User
↓
AS-REQ
↓
KDC
↓
AS-REP
↓
TGT

TGT
↓
TGS-REQ
↓
KDC
↓
TGS-REP
↓
Service Ticket
↓
Service
```

Important concepts:

```text
KDC
TGT
Service Ticket
SPN
```

I also studied these Windows events:

```text
4768
→ TGT request

4769
→ Service Ticket request

4771
→ Kerberos pre-authentication failed
```

Important lesson:

```text
4771 does not automatically mean brute force.
```

## NTLM

I studied NTLM at a basic level.

```text
Kerberos
→ ticket-based authentication

NTLM
→ challenge-response authentication
```

Important lesson:

```text
NTLM activity does not automatically mean an attack.
```

## Active Directory security events

I practiced analyzing training scenarios with account and group changes.

Events studied:

```text
4720
→ User Account Created

4726
→ User Account Deleted

4722
→ User Account Enabled

4725
→ User Account Disabled

4728
→ Member Added to Global Security Group

4729
→ Member Removed from Global Security Group

4732
→ Member Added to Local Security Group

4733
→ Member Removed from Local Security Group

4740
→ Account Locked Out

4723
→ Password Change

4724
→ Password Reset
```

Important lessons:

```text
Account Created does not mean Account Used.
Account Lockout does not prove brute force.
Privileged group change does not automatically mean attack.
```

## Privileged activity

I practiced analyzing scenarios such as:

```text
New account
↓
Added to Domain Admins
↓
Kerberos activity
↓
Access to service
↓
Removed from Domain Admins
↓
Account deleted
```

During analysis I check:

- who performed the action
- which account was changed
- which group was involved
- source host
- timeline
- baseline
- activity after the change

## Investigation method

I separate investigation information into three parts.

### FACT

Information directly confirmed by logs.

Example:

```text
CORP\alex was added to Domain Admins.
```

### HYPOTHESIS

A possible explanation that requires more evidence.

Example:

```text
The administrator account may have been compromised.
```

### ASSESSMENT

Current analytical evaluation.

Examples:

```text
Benign
Suspicious
Confirmed Malicious
```

## Key lessons

- Successful logon does not prove admin access.
- Account used does not prove the physical person.
- One failed logon does not prove brute force.
- SYSTEM activity is not automatically malicious.
- Process name does not prove legitimacy.
- Port 443 does not prove HTTPS.
- External connection does not automatically mean C2.
- Account Created does not mean Account Used.
- Privileged group change requires context.
- Timeline and baseline are important during investigation.

## Tools

- Windows Event Viewer
- Windows Security Log
- CMD
- `tasklist`
- `netstat`
- `sc`
- `tasklist /svc`

## Goal

The goal of this repository is to improve my Windows and Active Directory security analysis skills for a Junior SOC Analyst / SOC L1 role.

# Case 01 — Windows Logon Event Investigation

## Scenario

This case is based on hands-on analysis of Windows Security Log events in Event Viewer.

Personal account information was removed or replaced with generic names for portfolio use.

The goal was to analyze successful and failed logon events and understand what can and cannot be concluded from them.

## Tools

- Windows Event Viewer
- Windows Security Log
- CMD

## Event ID 4624 — Successful Logon

A Windows Security event with Event ID `4624` was analyzed.

```text
Event ID: 4624
Result: Successful Logon
Logon Type: 5
Account: SYSTEM
```

Logon Type `5` means:

```text
Service
```

This event showed that a Windows service created a logon session in the SYSTEM context.

### FACTS

- Event ID `4624` was recorded.
- The logon was successful.
- Logon Type was `5`.
- The account context was `SYSTEM`.

### Important conclusions

```text
SYSTEM activity does not automatically mean malicious activity.
Successful Logon does not prove privilege escalation.
```

## Event ID 4624 — Unlock

Another successful logon event was analyzed.

```text
Event ID: 4624
Logon Type: 7
```

Logon Type `7` means:

```text
Unlock
```

This event was related to unlocking an existing Windows session.

During analysis I compared:

```text
Subject
```

and:

```text
New Logon
```

These fields can describe different security contexts.

### Important conclusion

```text
Account used does not prove which physical person performed the action.
```

## Event ID 4625 — Failed Logon

A failed logon event was also analyzed.

```text
Event ID: 4625
Result: Failed Logon
Logon Type: 2
Source Network Address: 127.0.0.1
Caller Process: C:\Windows\System32\svchost.exe
```

Logon Type `2` means:

```text
Interactive
```

The source address was:

```text
127.0.0.1
```

This means:

```text
localhost
```

The event did not show evidence of an external source.

## FACTS

- Event ID `4625` was recorded.
- The logon attempt failed.
- Logon Type was `2`.
- Source address was `127.0.0.1`.
- The caller process was `svchost.exe`.

## HYPOTHESES

Possible explanations could include:

- a local Windows component attempted authentication
- a local application used invalid credentials
- saved credentials may have been incorrect

More evidence would be required to determine the exact cause.

## ASSESSMENT

```text
Needs Context / Not Confirmed Malicious
```

One failed authentication event from localhost is not enough to classify the activity as brute force or malicious.

## Investigation questions

During analysis I would check:

- Were there more failed logons before or after this event?
- Was there a successful logon after the failures?
- Which account was targeted?
- Was the source always localhost?
- Is this behavior normal for the host?
- Are there related process or service events?

## Key lessons

- `4624` means Successful Logon.
- `4625` means Failed Logon.
- Logon Type is important for understanding the event.
- Logon Type `5` is related to services.
- Logon Type `7` is related to session unlock.
- Logon Type `2` is an interactive logon.
- `127.0.0.1` means localhost.
- One failed logon does not prove brute force.
- SYSTEM activity is not automatically malicious.
- Account context does not prove the physical person.
- Event analysis should use timeline, context and correlation.

## Result

This lab helped me practice reading Windows Security Log events and separating:

```text
FACT
HYPOTHESIS
ASSESSMENT
```

instead of making conclusions from a single Event ID.

# Case 02 — Active Directory Privileged Account Investigation

## Scenario

This is a training Active Directory investigation scenario.

The goal was to analyze a timeline with:

- new account creation
- privileged group membership
- Kerberos activity
- access to a Domain Controller service
- privilege removal
- account deletion

## Environment

```text
Domain: CORP.LOCAL
Domain Controller: DC01
Workstation: PC-77
Account: CORP\svc-backup
```

Additional context:

```text
CORP\svc-backup did not exist before this activity.

CORP\helpdesk-admin normally creates standard user accounts.

PC-77 is normally used by a Sales employee.

This activity is not part of the normal baseline.
```

## Timeline

### 18:00 — Account created

```text
Event ID: 4720
New Account: CORP\svc-backup
Subject: CORP\helpdesk-admin
```

Event ID `4720` means:

```text
User Account Created
```

### 18:03 — Added to Domain Admins

```text
Event ID: 4728
Member: CORP\svc-backup
Group: Domain Admins
Subject: CORP\helpdesk-admin
```

Event ID `4728` means:

```text
Member Added to Global Security Group
```

`Domain Admins` is a privileged Active Directory group.

### 18:05 — Kerberos TGT request

```text
Event ID: 4768
Account: CORP\svc-backup
Client: PC-77
```

Event ID `4768` is related to:

```text
Kerberos TGT request
```

The result/status fields should also be checked before concluding that the request was successful.

### 18:06 — Service Ticket request

```text
Event ID: 4769
Account: CORP\svc-backup
Client: PC-77
Service: cifs/DC01
```

Event ID `4769` is related to:

```text
Kerberos Service Ticket request
```

The SPN:

```text
cifs/DC01
```

refers to the SMB/CIFS service on `DC01`.

### 18:15 — Removed from Domain Admins

```text
Event ID: 4729
Member: CORP\svc-backup
Group: Domain Admins
```

Event ID `4729` means:

```text
Member Removed from Global Security Group
```

### 18:17 — Account deleted

```text
Event ID: 4726
Account: CORP\svc-backup
```

Event ID `4726` means:

```text
User Account Deleted
```

## Timeline summary

```text
Account created
↓
Added to Domain Admins
↓
Kerberos TGT request
↓
Service Ticket request for cifs/DC01
↓
Removed from Domain Admins
↓
Account deleted
```

## FACTS

- `CORP\svc-backup` was created.
- The creation was performed in the context of `CORP\helpdesk-admin`.
- `CORP\svc-backup` was added to `Domain Admins`.
- Kerberos activity for `CORP\svc-backup` was recorded from `PC-77`.
- A Service Ticket request for `cifs/DC01` was recorded.
- The account was later removed from `Domain Admins`.
- The account was later deleted.
- This sequence was outside the expected baseline.

## HYPOTHESES

Possible explanations:

- `CORP\helpdesk-admin` may have been compromised.
- `CORP\svc-backup` may have been created as a temporary privileged account.
- The activity may also be a legitimate administrative task that was not part of the normal baseline.

More evidence is required before making a final conclusion.

## ASSESSMENT

```text
Suspicious
```

The activity is suspicious because several unusual events happened in a short timeline:

```text
new account
+
privileged group membership
+
Kerberos activity from an unusual workstation
+
Service Ticket request for a Domain Controller service
+
privilege removal
+
account deletion
```

This is not enough to classify the activity as Confirmed Malicious.

## Next investigation steps

I would check:

- status/result fields in events `4768` and `4769`
- successful logons for `CORP\svc-backup`
- source host for account and group changes
- activity of `CORP\helpdesk-admin`
- process activity on `PC-77`
- file and network activity after authentication
- access to `DC01`
- additional Active Directory changes
- Group Policy changes
- approved change requests or administrator tickets
- normal baseline for both accounts

## Important lessons

- Account Created does not mean Account Used.
- Privileged group membership does not automatically mean an attack.
- A Kerberos request does not automatically prove successful authentication.
- Account context does not prove the physical person.
- Timeline is more useful than an isolated Event ID.
- Baseline helps identify unusual behavior.
- Suspicious does not mean Confirmed Malicious.
- Related events should not be treated as causal without evidence.

## Result

This case helped me practice Active Directory timeline analysis and privileged activity investigation using:

```text
FACT
HYPOTHESIS
ASSESSMENT
```

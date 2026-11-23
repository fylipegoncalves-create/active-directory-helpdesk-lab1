# Active Directory and IT Helpdesk Lab

A virtualized enterprise Windows environment built to simulate Tier 1/Tier 2 Helpdesk operations, Active Directory administration, Group Policy management and ticket workflows in Jira.


## Lab Architecture

**Domain:** `corp.local`
**Domain Controller (`DC01`):** Windows Server 2022 (AD DS, DNS, DHCP, File Services)
**Client (`CLIENT01`):** Windows 11 Enterprise (joined the domain)
**Ticketing System:* Jira Service Management 


###Organizational Structure
**OUs:** `CorpUsers` (`IT`, `Sales`), `CorpComputers`, `CorpGroups`
**Security Groups:** `SG_IT`, `SG_Sales`
**GPOs:** `Block_Control_Panel` (Linked to `Sales`), Default Domain Account Lockout Policy (3 invalid attempts)

---

## Resolved Tickets


### Ticket 1: Domain Account Lockout (`gabriel.costa`)
**Problem:** User gabriel.costa can’t sign in to CLIENT01. “The referenced account is currently locked out and may not be logged on to.” (Repeated failed password attempts)
**Root Cause:** Account exceeded the domain lockout threshold.
**Fix:** Unlocked gabriel.costa in Active Directory Users and Computers and verified login on CLIENT01.
**Screenshots:**
 -Error: ![Account Lockout Error](screenshots/01-lockout-error)
 -Fix: ![Account Unlocked in ADUC](screenshots/01-lockout-fix)
 -Jira Ticket: ![Resolved Lockout Ticket](screenshots/01-lockout-resolved)

---

### Ticket 2: Expired Domain Password (`filipe.goncalves`)
**Problem:** User filipe.goncalves is blocked from signing in to CLIENT01. “The user’s password must be changed before signing in.”
**Root Cause:** Domain password expired (flagged for mandatory reset at logon)
**Fix:** Verified user identity, reset account in Active Directory Users and Computers and confirmed login on CLIENT01
**Screenshots:**
 -Error: ![Account Lockout Error](screenshots/02-expired-password-error)
 -Fix: ![Account Unlocked in ADUC](screenshots/02-expired-password-fix)
 -Jira Ticket: ![Resolved Lockout Ticket](screenshots/02-expired-password-res

---

### Ticket 3: DNS Resolution Failure (`CLIENT01`)
**Problem:** User reports network drives and domain resources unreachable on client01. Running nslookup corp.local fails with DNS request timed out.
**Root Cause:** CLIENT01 network adapter was configured with an invalid preferred DNS server IP.
**Fix:** Reverted IPv4 properties to Obtain DNS server automatically, flushed local DNS cache (ipconfig /flushdns) and verified corp.local resolution through (nslookup corp.local). 
**Screenshots:**
 -Error: ![Account Lockout Error](screenshots/03-dns-failure-error)
 -Fix: ![Account Unlocked in ADUC](screenshots/03-dns-failure-fix)
 -Jira Ticket: ![Resolved Lockout Ticket](screenshots/03-dns-failure-res)

---

### Ticket 4: NFTS Folder Permission Denied (`\\DC01\SalesData`)
**Problem:** User requires access to \\DC01\SalesData but receives “You do not have permission to access \\DC01\SalesData. Contact your network administrator to request access.” (NTFS Access Denied)
**Root Cause:** NTFS permissions on C:\Shares\SalesData restrict access exclusively to member of SG_Sales, and the requesting user was not a member of that security group.
**Fix:** Added user to SG_Sales in Active Directory Users and Computers (rather than granting SG_IT broad access. User reauthenticated and confirmed access.
**Screenshots:**
 -Error: ![Account Lockout Error](screenshots/04-nfts-permission-error)
 -Fix: ![Account Unlocked in ADUC](screenshots/04-nfts-permission-fix)
 -Jira Ticket: ![Resolved Lockout Ticket](screenshots/04-nfts-permission-resolved)
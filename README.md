<p align="center">
<img src="https://i.imgur.com/Ucqw15T.jpeg" alt="Active Directory" width=500 height=300/> 
</p>

<h1>Account Lockouts, Group Policy, and Event Log Observation (Part 4)</h1>
<p>In this final Active Directory lab section, we’ll learn how to simulate account lockouts, configure lockout policies via Group Policy, enable/disable accounts, and monitor events through Windows Event Viewer. </p>

<h1>🧠 Overview</h1>
<p>This is essential for real-world IT administration, as it covers both security best practices and user account management.</p>

<h2>Environments and Technologies to use</h2>

- Microsoft Azure (Virtual Machines)
- Microsoft RD Client (Remote Desktop)

<h2>Operating Systems to use</h2>

- macOS Sonoma ***(if you own Macbook Air M1 or M2; it does not matter what type of macOS you own)***
- Windows 10 or Windows 11 Home or Pro ***(if you own either of them)***

-----

### Part 1: Simulate an Account Lockout

1.	Log into the `Domain Controller (DC-1)` as `mydomain.com\jane_admin`.
2.	Open `Active Directory Users and Computers (ADUC)`.
3.	Navigate to `_EMPLOYEES` OU and select a test user (e.g., testuser3).

-----

### Part 2: Trigger an Account Lockout (Before GPO)

1.	RDP into `Client-1` as a local admin.
2.	Log out or switch user.
3.	Attempt to log in as `mydomain.com\testuser3` with the wrong password 10 times.


***⚠️ You’ll notice no lockout occurs yet — this is because lockout policies are not enabled by default.***

-----

### Part 3: Configure Group Policy for Account Lockouts

1.	On `DC-1`, open `Group Policy Management` (`Tools` > `Group Policy Management`).
2.	Expand your domain (e.g., `mydomain.com`).
3.	Right-click `Default Domain Policy` → `Edit`.

-----

### Part 4: Set the Account Lockout Threshold

1.	In the `Group Policy Editor`, navigate to:
     - Computer `Configuration` > `Policies` > `Windows Settings` > `Security Settings` > `Account Policies` > `Account Lockout Policy`
2.	Configure:
      - Account lockout threshold → 5 invalid attempts
      - Account lockout duration → 15 minutes
      - Reset account lockout counter after → 15 minutes

-----

### Part 5:  Apply Group Policy

1. In Command Prompt on `DC-1`, run: `gpupdate /force`
2. Now, retry logging in as testuser3 on `Client-1` with the wrong password 6 times.

***✅ You should see: “The account is currently locked out and may not be logged on to.”***

-----

### Part 6: Unlock & Reset the Account

1.	In `ADUC` on `DC-1`, right-click testuser3 → `Properties`.
2.	Check `Unlock account` (if visible) and click `Apply`.
3.	Reset the password to: `Cyberlab123!`
4.	Uncheck `"User must change password at next login”`.

-----

### Part 7: Test Login

1. Log in to `Client-1` as:
   - Username: `mydomain.com\testuser3`
   - Password: `Cyberlab123!`
  
***✅ Login should now work.***

-----

### Part 8: Disable and Re-Enable a User Account
	
1.	In `ADUC` on `DC-1`, right-click testuser3 → `Disable Account`.
2.	On `Client-1`, try logging in — you’ll see:
    - `“Your account has been disabled. Please see your system administrator.”`
3.	Re-enable the account in `ADUC`, then log in again successfully.

-----

### Part 9: Observe Security Event Logs

1. On `DC-1`:
    - Open `Event Viewer` → `Windows Logs` > `Security`
    - Look for:
	  - 4740 → Account locked out
      - 4625 → Failed login attempt
      - 4725 → Account disabled
      - 4722 → Account enabled
      - 4723/4724 → Password changed/reset

2. On Client-1:
   - Similar 4625 failed login events will appear for incorrect credentials or RDP attempts.
  
-----

<h2>Why This Matters</h2>

- Security Compliance: Prevents brute-force attacks.
- Troubleshooting: Event logs help identify the cause of account issues.
- User Management: Admins can quickly restore access or disable compromised accounts.

-----

### Conclusion

You’ve now:

   - Simulated bad logins & account lockouts
   - Configured Group Policy lockout settings
   - Reset and unlocked accounts
   - Disabled/re-enabled user accounts
   - Reviewed event logs for security tracking

<p>This wraps up our four-part Active Directory series in Azure. But keep your VMs running because we’ll explore two more advanced topic, which is Network File Sharing Permission and understanding DNS.</p>

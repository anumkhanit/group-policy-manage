<p align="center">
<img src="https://i.imgur.com/Ucqw15T.jpeg" alt="Active Directory" width=500 height=300/> 
</p>

<h1>Active Directory in Azure</h1>
<p>Account Lockouts, Group Policy, Enabling/Disabling Accounts, and Log Observation</p>

<h2>Environments and Technologies to use</h2>

- Microsoft Azure (Virtual Machines)
- Microsoft RD Client (Remote Desktop)

<h2>Operating Systems to use</h2>

- macOS Sonoma ***(if you own Macbook Air M1 or M2; it does not matter what type of macOS you own)***
- Windows 10 or Windows 11 Home or Pro ***(if you own either of them)***

-----

### Part 1: Simulate an Account Lockout

 - Choose a Test User
   - Log into `DC-1` as `mydomain.com\jane_admin`
	 - Open `Active Directory Users and Computers (ADUC)`
	 - Navigate to `_EMPLOYEES OU` and pick a user (e.g., testuser3)

-----

### Part 2: Trigger an Account Lockout (Before GPO)

 - RDP into `Client-1` as a `local admin` or `jane_admin`
 - Log out or Switch User
 - Try logging in as `mydomain.com\testuser3` with a wrong password
 - Do this 10 times

***⚠️ Nothing happens yet because account lockout policy is not enabled.***

-----

### Part 3: Configure Group Policy to Lock Accounts After Failed Attempts

 - Edit Default Domain Policy
    - Go back to `DC-1`
	  - Open Group Policy Management
	  - Go to: `Tools` > `Group Policy Management`
	- Expand your domain (e.g., mydomain.com)
	- Right-click `Default Domain Policy` > `Edit`

-----

### Part 4: Set the Account Lockout Threshold

- In the Group Policy Editor:
	- Navigate to: `Computer Configuration` > `Policies` > `Windows Settings` > `Security Settings` > `Account Policies` > `Account Lockout Policy`
- Set the following:

  - Setting > Value 
    - Account lockout threshold > 5 invalid attempts
    - Account lockout duration > 15 minutes (or your choice)
    - Reset account lockout counter after > 15 minutes
- Close the editor.

-----

### Part 5: Force Group Policy to Apply

- Still on `DC-1`, open `Command Prompt` and run: `gpupdate /force`

***This applies the policy immediately.***

- Lock the Account Again (Now GPO Is Active)
	 - Go back to `Client-1`
   - Attempt to log in as testuser3 again
	 - Enter the wrong password 6 times

***You should now see a message like: “The account is currently locked out and may not be logged on to.”***

-----

### Part 6: Unlock & Reset the Account

- On `DC-1`, go back to `ADUC`
- Right-click on testuser3 > `Properties`
- Under the Account tab:
   - Check the `Unlock account` box (if shown)
	 - Click `Apply`
- Now, reset the password:
  - Right-click the user > `Reset Password`
	- Set it to: `Cyberlab123!`
  - Uncheck `User must change password at next login`

-----

### Part 7: Test the Login Again

- Return to `Client-1`
- Try logging in as testuser3 again
  - Username: `mydomain.com\testuser3`
	- Password: `Cyberlab123!`

***You should now successfully log in.***

-----

### Part 8: Disable and Re-Enable a User Account

- Disable the User
	- Back in `ADUC` on `DC-1`
	- Right-click testuser3 > `Disable Account`
	  - Icon turns into a small down arrow

-----

### Part 9: Attempt Login While Disabled

- On `Client-1`, try logging in as testuser3 again

***You should now see: "Your account has been disabled. Please see your system administrator.”***

***This confirms the account was correctly disabled.***

-----

### Part 10: Re-Enable the User

- Back on `DC-1`, in `ADUC`
- Right-click testuser3 > `Enable Account`
- Try logging in again on `Client-1` — it should work!

-----

### Part 11: Observe Event Logs

- Observe Logs on `DC-1`
	- On `DC-1`, open `Event Viewer`
	- Go to: `Windows Logs` > `Security`
- Look for event `ID 4740`
  - This indicates an account was locked out
- Also check for:
  - Event `ID 4625` – Failed login attempt
	- Event `ID 4725` – Account was disabled
  - Event `ID 4722` – Account was enabled
	- Event `ID 4723/4724` – Password changes

-----

### Part 12: Observe Logs on `Client-1`

- On `Client-1`, open `Event Viewer`
  - Go to: `Windows Logs` > `Security`
- Look for similar `4625` (logon failures) or failed RDP attempts

***These logs help admins troubleshoot login issues and security incidents.***

-----

### Conclusion

<p>Cograts on reaching to this last (and tough part) of Active Directory! What you've learned so far is that you learned about simulated bad logins, what triggered and allowed a lockout, you configured GPO, set a lockout threshold, unlocked/reset account, recovered user access, disabled/enabled user, managed account status, reviewed logs, and tracked events across systems</p>

<p>This is a complete four-part of deploying Active Directory within Azure, but don't delete the VMs yet! We still have a few lessons to go over while you have active directory in your machines.</p>

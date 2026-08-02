# Lab 2: Group Policy Management
 
## Overview
<img width="328" height="365" alt="GPM" src="https://github.com/user-attachments/assets/8054842c-a51f-41b0-b4ea-edf3e2c35ab2" />

The goal of this lab was to build on the Active Directory environment from Lab 1 by creating and applying Group Policy Objects (GPOs) to enforce security settings and manage the user experience across the domain.

## Group Policy Concepts

Group Policy Management was already installed as a feature back in Lab 1, so I accessed it directly from the Tools dropdown in Server Manager rather than installing anything new.

### Computer Configuration vs. User Configuration
<img width="907" height="449" alt="Config Types" src="https://github.com/user-attachments/assets/c04f9844-c6ea-4e60-bdb8-fd518767fc0c" />
Every GPO is split into two configuration types:

- **Computer Configuration** applies to the machine itself, regardless of who logs into it. These settings don't change from user to user since they're tied to the computer, not the account. This matters when you want a setting enforced no matter who's sitting at that machine (e.g., password requirements, USB restrictions).
- **User Configuration** applies to the user account, following that user to any computer they log into (and applies to any new users created later, as long as they fall under the GPO's scope). This matters when the setting should follow the person rather than the machine (e.g., desktop wallpaper, mapped drives).

Knowing which one to pick matters because it determines *what* the policy actually targets, computer or person, which changes how consistently and where the setting shows up across the domain.

### Policies vs. Preferences

Within each configuration type, settings fall into one of two categories:

- **Policies** are enforced by AD and cannot be changed by the end user. They're used to restrict choices and are managed entirely by admins. Examples: password policies, account lockout policies, software restriction policies, audit policies, user rights assignments, and folder redirection.
- **Preferences** are defaults set by the admin, but the user is free to change them afterward. They're used to save users setup time without locking them into a permanent restriction. Examples: mapped network drives, printers, desktop shortcuts, scheduled tasks, power options, and Start menu shortcuts.

The distinction matters because it's the difference between something you're *guaranteeing* (a policy) and something you're just *convenient-defaulting* (a preference). Picking the wrong one either over-restricts users unnecessarily or fails to actually enforce something that should be locked down.

## Steps Taken

### Activity 1: Password Policy GPO

**Purpose:** Enforce strong passwords across the domain to reduce the risk of weak or easily guessed credentials.

1. In the Group Policy Management window, right-clicked the domain (x.local) and selected **Create a GPO in this domain, and link it here...**
2. Named the new GPO "Password Policy" so it's clear what it does at a glance.
3. Right-clicked the new GPO and selected **Edit** to open the Group Policy Management Editor.
4. Configured this under **Computer Configuration** since it's the computer that prompts for a password at login, and selected **Policies** since this should be enforced by admins and never modified by the user.
5. Navigated to **Windows Settings > Security Settings > Account Policies > Password Policy**.
6. Opened **Minimum password length**, checked "Define this policy setting," set it to 12 characters, and clicked Apply.
7. Opened **Password must meet complexity requirements**, checked "Define this policy setting," and selected Enabled. (The Explain tab lists exactly what the default complexity rules require.)
8. Opened **Maximum password age**, checked "Define this policy setting," and set it to 90 days so users are prompted to change their password every quarter. Minimum password age adjusted automatically to stay below the maximum.

### Activity 2: Drive Mapping GPO

**Purpose:** Automatically map network drives for users at login instead of having them set it up manually.

1. Right-clicked the domain again and selected **Create a GPO in this domain, and link it here...**
2. Named the new GPO "Drive Mapping."
3. Right-clicked the new GPO and selected **Edit**.
4. Configured this under **User Configuration** since it's the user who needs the drive, and selected **Preferences** since users should still be free to add their own drive mappings later without being restricted to just this one.
5. Went to **Windows Settings > Drive Maps**, right-clicked, selected **New > Mapped Drive**, entered the network share path, chose a drive letter, and clicked Apply.

### Activity 3: Desktop Wallpaper GPO

**Purpose:** Set a consistent default desktop wallpaper across the domain.

1. Right-clicked the domain again and selected **Create a GPO in this domain, and link it here...**
2. Named the new GPO "Desktop Wallpaper."
3. Right-clicked the new GPO and selected **Edit**.
4. Configured this under **User Configuration** since it should apply whenever a user logs in, and selected **Policies** since users shouldn't be able to change it afterward.
5. Went to **Policies > Administrative Templates: Policy definitions > Desktop > Desktop Wallpaper**.
6. Selected **Enabled**, entered the wallpaper file path, set the wallpaper style to "Fill," and clicked Apply.

### Activity 4: Restrict Access to Control Panel

**Purpose:** Prevent users from accessing Control Panel and PC Settings to reduce accidental or unauthorized system changes.

1. Right-clicked the domain again and selected **Create a GPO in this domain, and link it here...**
2. Named the new GPO "Restrict Control Panel."
3. Right-clicked the new GPO and selected **Edit**.
4. Configured this under **User Configuration** since it applies to the user while logged in, and selected **Policies** since this restriction shouldn't be user-editable.
5. Went to **Policies > Administrative Templates: Policy definitions > Control Panel**.
6. Selected **Prohibit access to Control Panel and PC settings**, set it to Enabled, and clicked Apply. Every user under this GPO now has restricted access to Control Panel.

### Activity 5: Disable USB Storage

**Purpose:** Block USB storage devices to reduce the risk of data exfiltration or malware introduced through removable media.

1. Right-clicked the domain again and selected **Create a GPO in this domain, and link it here...**
2. Named the new GPO "Disable USB Devices."
3. Right-clicked the new GPO and selected **Edit**.
4. Configured this under **Computer Configuration** since it applies to the machine itself, and selected **Policies** since this restriction shouldn't be user-editable.
5. Went to **Policies > Administrative Templates: Policy definitions > System > Removable Storage Access**.
6. Double-clicked **All Removable Storage classes: Deny all access**, set it to Enabled, and clicked Apply.

### Activity 6: Account Lockout Policy

**Purpose:** Lock accounts out after repeated failed login attempts to prevent brute-force password attacks.

1. Right-clicked the domain again and selected **Create a GPO in this domain, and link it here...**
2. Named the new GPO "Account Lockout."
3. Right-clicked the new GPO and selected **Edit**.
4. Configured this under **Computer Configuration** since it applies to the machine itself, and selected **Policies** since this restriction shouldn't be user-editable.
5. Went to **Windows Settings > Security Settings > Account Policies > Account Lockout Policy**.
6. Configured the three account lockout settings:
   - **Account lockout threshold:** 5 invalid login attempts, a common baseline that blocks brute-force attempts without locking users out from the occasional honest typo.
   - **Account lockout duration:** 15 minutes, long enough to meaningfully slow down repeated attack attempts while not requiring an admin to manually unlock every account.
   - **Reset account lockout counter after:** 15 minutes, so failed attempts don't accumulate indefinitely, only within the same 15-minute window as the lockout duration.

## Lessons Learned

This lab was my first real hands-on experience with Group Policy, and it made a lot more sense once I actually started building GPOs instead of just reading about them! The biggest thing that clicked was the difference between Computer Configuration and User Configuration, and between Policies and Preferences. Before this lab I would have assumed those were basically the same thing, but choosing the wrong one changes whether a setting follows the device or the person, and whether the user can override it or not.

Each activity also gave me a reason to think about *why* a setting matters, not just how to configure it:

- The **Password Policy GPO** matters because weak passwords are still one of the most common ways accounts get compromised. Enforcing length, complexity, and a maximum age at the domain level means I'm not relying on individual users to make good password decisions on their own.
- The **Drive Mapping GPO** matters less for security and more for convenience and consistency. It's a good example of a Preference instead of a Policy, since it saves users a manual step without locking them out of managing their own drives afterward.
- The **Desktop Wallpaper GPO** is a small change, but it's a good demonstration of how GPOs can enforce a consistent look and feel across an organization, which matters for branding and also makes it obvious at a glance if a machine isn't following domain policy.
- The **Restrict Control Panel GPO** matters because it reduces the chance of a user (accidentally or otherwise) making system-level changes they shouldn't, which cuts down on help desk tickets and reduces the attack surface on end-user machines.
- The **Disable USB Storage GPO** matters from a data security standpoint. USB drives are a common way for data to leave an organization or for malware to get in, so blocking removable storage at the domain level closes off that risk without relying on users to just "not do that."
- The **Account Lockout Policy GPO** matters because it directly defends against brute-force login attempts. Without it, someone could keep guessing passwords indefinitely! Setting the threshold, duration, and reset counter together means failed attempts are still fielded gracefully for real users who mistype a password, without giving an attacker unlimited attempts.

Overall, this lab made it clear that Group Policy isn't just about enforcing restrictions, it's about deciding deliberately where each setting should live (computer vs. user) and how much control to hand back to the end user (policy vs. preference). That distinction is something I'll keep applying as I build out more GPOs in future labs!

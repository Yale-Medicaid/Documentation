# Embedding Onboarding

Most information related to embedded systems will be tracked within the embedded portal (virtual machine). Below, we track how to establish a VM connection and debug any administrative issues.

| Support Team | Contact Info | When to use |
| --- | --- | --- |
| DSS IT Support | 860-424-4949; DSS-ITS-Support@ct.gov | Locked out of portal, password issues, questions about online IT tickets, ... |
| DSS Security | While in-person, ask to call the 2nd floor security to fix badge access | Issues accessing Hartford Central Office in-person |

## Azure Virtual Desktop (AVD)

The Connecticut DSS embedded portal is based out of an AVD instance. You must go through the following process to obtain online access:

1. Have a team manager submit a DSS request for credentials
2. Install the "Microsoft Authenticator" application on your phone for 2-factor authentication
3. Install "Microsoft Remote Desktop" from the App Store
    - Upon launch, click the "Microsoft Remote Desktop > Preferences..." button on the upper right console and unclick "Enable optimizations for Microsoft Teams"
    - Go to "Connections > Add Workspace" and use the following URL: https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery
    - Use your state credentials (@ct.gov) to log in, including 2-factor authentication
4. Reset your password
    - Review password requirements in the section below
    - Directly from the VM display (Ctrl+Alt+Delete, or Ctrl+Opt+Delete on Mac)
    - Alternatively, try going to office.com in the browser, logging in with your state credentials, and selecting "Change password" from the settings icon
5. In the AVD File Explorer, go to the 'DSS-Yale-Share/' folder in the shared network drive: `\\stavddss001.privatelink.file.core.windows.net\dss-yale-share-001`
    - You can put the link directly in the File Explorer file path (top-middle section).

## DSS Password Requirements

The following info is specific to your DSS log-in account. Other credentials (e.g., database passwords) are managed separately.

Connecticut DSS security requires the following password requirements:

- Must be at least 9 characters long
- Must contain at least one character in 3 of the following groups of characters:
    - Uppercase letters (A-Z)
    - Lowercase letters (a-z)
    - Numbers (0-9)
    - Symbols (!@#$%^&*)
- Cannot match any of your previous 24 passwords
- Avoid using single words that can be found in the dictionary (e.g., "Wintertime1!")
- You cannot change your password more often than once in a 7-day period unless overridden by the helpdesk

## Oracle Password Change

**If you are locked out, reach out to DSS IT and request Gainwell support to unlock your MDW credentials.**

Changing your Oracle database password for the Medicaid Data Warehouse (MDW) can be done within any SQL editor, such as DBeaver. However, this requires setting the connection using *only* your username and excluding the `[YALE]` suffix, which is used to connect to the shared schema.

For example, the following workflow may be performed in DBeaver with an existing connection:

- Right-click on the Oracle connection
- Click "edit connection," changing the username to exclude the `[YALE]` suffix
- Save the edited connection, then right-click on the connection again (don't click "edit connection" this time)
- Select "Security" > "Change user password." Click save
- Right-click on the connection, click "edit connnection," update the password, and reset the username to include `[YALE]` again
- Test connection and save updated credentials

Your updated password should persist all Oracle connections, regardless of other connections (e.g., RStudio or Python). Make sure you keep a secure record of your passwords for tracking.


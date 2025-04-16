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
    - Directly from the VM display (Ctrl+Alt+Delete, or Ctrl+Opt+Delete on Mac)
    - Alternatively, try going to office.com in the browser, logging in with your state credentials, and selecting "Change password" from the settings icon



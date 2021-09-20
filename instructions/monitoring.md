# Azure AD monitoring

## Prerequisites
Make sure your Azure Pass is set up and ready. If you haven't done it yet, please see [Configure your Azure Pass](azurePass.md).

You will have also needed to finish the first part of this lab [Day Zero AzureAD and Azure setup](dayZero.md) to be able to continue with this excercise. 

## Azure AD Monitoring

In the last part of the lab, we configured two break-glass accounts. Those accounts are for special situations only and should not be used to perform daily work. Ideally, their passwords should be split, and both halves should be stored in different vaults. A process should govern its usage, which provides audibility but also allows operators to take quick action.
However, we need to monitor them and send out an alert when they are used. 

Azure AD has a built-in event log, but it has a limited data retention time (90 days) and doesn't allow us to configure alerts. But we do have an excellent solution for that - we'll collect data to a dedicated data lake.

1) Deploy a Log Analytics Workspace
- In your new subscripion Create a new Resource Group.
- Use whatever name you like, I will refer to it as the "Monitoring-RG".
- Deploy a Log Analytics Workspace to your Monitoring-RG.
- Use whatever name you like, I will refer to it as the "Core-LA".

2) Confiuge Log Collection for Azure AD
- Go to the Azure AD tab in the portal
- Scroll down to find "Diagnostic Settings" under "Monitoring"
- Configure a new setting that captures all data to your newly created Log Analytics Workspace "Core-LAW"

3) Configure Alerting
- Go to the Monitor tab in the portal
- Under Alerts, configure a new Alert Rule
- Use "Core-LAW" as the scope
- Use Custom Log Search as signal
- Use the query provided below
- Configure an Action Group which sends you an email when a new sign-in from either of the two emergency accounts is logged

Query:

`SigninLogs
| project UserId
| where UserId == "<replace with a valid ID>" or UserId ==  "<replace with a valid ID>"
`

4) Test your new Alert Rule
- Log in with one of the two break glass accounts
- See if you receive an email
# Day Zero AzureAD and Azure setup

## Prerequisites

Make sure your Azure Pass is set up and ready. If you haven't done it yet, please see [Configure your Azure Pass](azurePass.md).

## Azure AD, Management Groups and Subscriptions

If you've followed the prerequisites, you now have a fresh Azure AD tenant with a single subscription. That's enough to get going and have some fun. We will first make sure that your directory is set up to resemble something worthy of production. But first, take a look around.

1) If you're not already logged into the Azure Portal, please do that. Be sure to use the new Microsoft Account which you created while redeeming the Azure Pass.

2) Find Azure Active Directory in the Portal Menu and take a look around. Explore users, groups, applications... Take your time.

    You will probably notice that the user account which was created by default looks somewhat strange. It is an external account, even though it is marked as a Member account rather than a Guest. Your brand new Microsoft account was used to give you access to the Azure AD tenant. Accounts native to AAD are also called work or school accounts and do not have the #EXT# part in their UPN.

3) Create a new, local, named account for yourself in this new Azure AD tenant. Make that user has Global Administrator permissions in the directory.

4) Open a new private windows and go to <https://portal.azure.com>.

   - Log in with your newly created account.
   - Go to Azure Active Directory and verify that you have Global Admin permissions.
   - If yes, close your previous window. We won't be needing it any more.

5) Customize your portal experience. I recommend a "Docked" menu and a dark theme, but make it suit your preferences. It should feel cosy.

Using our corporate identity, which in real life scenarios is backed by an AD sync or Okta, is very comfortable, but things do go wrong from time to time. And when they do go wrong, they tend to do that in groups or even cohorts. That is why we always create emergency "break-glass" accounts that can be used in times of turbulence. That will be our next step.

6) Create two new accounts in your AAD tenant.
   - Be sure to give both global administrator permissions.
   - Use names that are easy to recognize, like emergency1 and emergency2.
   - Once created, be sure to rotate the default passwords which Azure generated upon creation. Use VERY strong passwords. The maximum length is now 256 characters, but 128 should be enough.

Now that we have our accounts set up, we're well on our way to get our Directory ready. But as we are, even with strong passwords, we're still not secure enough to host production systems. We need to enforce MFA at least, but while we're at it, we might as well add a bit more controls.

7) Enable a free trial of the premium features on your Azure tenant.
   - Check the Licenses tab under Azure Active Directory
   - Go for Premium P2.
   - Remember to assign the license to your user (make sure to set the 'Usage Location' attribute for each user you want to assign the license).

8) Create a new Conditional Access policy. Check the Security tab under Azure Active Directory.
**Tip** You'll first need to disable Azure Security defaults, it might be worth starting with that. Check the Properties tab of Azure AD in the Azure portal to do that.
   - Enforce MFA for All users, but be sure to exclude your emergency accounts
   - Restrict Access to Trusted Locations
   - Add the public IP addresses of the the SBP office and your home as trusted locations.
   - Enforce the policy for Microsoft Azure Management

9) Test your policy. Log out, close the browser window and log in again.

Before moving forward, let's create a security group to make our life easier. In a customer case, we probably wouldn't use dynamic assignments as they might introduce a vulnerability, but for the sake of learning, we will.

10) Create a new security group with dynamic user assignment.
**Tip** pay attention to irreversible decisions, the so-called one-way doors
    - Create a new group called CCOE Members
    - Use dynamic assignment based on department (Department == CCOE)
    - Set the department of your user to CCOE
    - Wait for a little and check the results

Now we will use that group to configure Just-in-Time and Just-Enough-Access for our Azure AD. Especially that we already have trial licenses.

11)  Ensure that every member of the CCOE group has a P2 license.
     - Assign the Azure A2 P2 license to the CCOE group.

12) Configure Privileged Identity Management (PIM) for Azure AD
    - Navigate to PIM in the Azure portal
    - Go to Azure AD Roles and Assign Eligibility
    - Assign Global Administrator eligibility to the CCOE Group. Use permanent eligibility.
    - Be sure to clean up active assignments - only emergency accounts should have the Global Admin permanently active.

From now you'll need to activate your Global Administrator role whenever you want to configure your directory. It's probably a good idea to give that a go.

13) Test PIM for Azure AD
    - Log out and back in
    - Try to activate your role

Since we're already configuring Privileged Identity Management, we might as well include Azure Resources. But first, it might be a good idea to enable Management Groups.

14) Enable Management Groups in your tenant
    - Go to Management Groups in the Azure Portal and start using them
    - Pick any id/name for your first group. I went for "AzurePassSubscriptions."

15) Move Your Azure Subscription to the new Management Group

You probably don't see any Azure Subscriptions. But why?
Try giving yourself "User Access Administrator" permissions in Azure AD and check again.

16) Onboard your Management Group to PIM
    - Navigate to PIM and go to Azure Resources
    - Discover Resources to find your Management Group
    - Assign Owner role eligibility to the CCOE Group.
    - Make sure that your emergency accounts have permanent Owner permissions on the Management Group.
    - Remember to remove your User Access Administrator permissions!

With that process complete, you have a solid baseline for your Azure AD directory configuration. We can now add some [Azure AD monitoring](monitoring.md).

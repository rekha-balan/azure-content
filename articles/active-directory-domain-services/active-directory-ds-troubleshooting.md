<properties
	pageTitle="Azure Active Directory Domain Services: Troubleshooting Guide | Microsoft Azure"
	description="Troubleshooting guide for Azure AD Domain Services"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2016"
	ms.author="maheshu"/>

# Azure AD Domain Services - Troubleshooting guide
This article provides troubleshooting hints for issues you may encounter when setting up or administering Azure Active Directory (AD) Domain Services.


### You are unable to enable Azure AD Domain Services for your Azure AD directory
If you try to enable Azure AD Domain Services for your directory and it fails or gets toggled back to 'Disabled', perform the following troubleshooting steps:

- Ensure that you do not have an existing domain with the same domain name available on that virtual network. For instance, assume you have a domain called 'contoso.com' already available on the selected virtual network. Later, you try to enable an Azure AD Domain Services managed domain with the same domain name (that is, 'contoso.com') on that virtual network. You encounter a failure when trying to enable Azure AD Domain Services. This failure is due to name conflicts for the domain name on that virtual network. In this situation, you must use a different name to set up your Azure AD Domain Services managed domain. Alternately, you can de-provision the existing domain and then proceed to enable Azure AD Domain Services.

- Check to see if you have an application with the name 'Azure AD Domain Services Sync' in your Azure AD directory. If this application exists, you need to delete it and then re-enable Azure AD Domain Services. Perform the following steps to check for the presence of the application and to delete it, if the application exists:

  1. Navigate to the **Azure classic portal** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. Select the **Active Directory** node on the left pane.
  3. Select the Azure AD tenant (directory) for which you would like to enable Azure AD Domain Services.
  4. Navigate to the **Applications** tab.
  5. Select the **Applications my company owns** option in the dropdown.
  6. Check for an application called **Azure AD Domain Services Sync**. If the application exists, proceed to delete it.
  7. Once you have deleted the application, try to enable Azure AD Domain Services once again.


### Users are unable to sign in to the Azure AD Domain Services managed domain
If one or more users in your Azure AD tenant are unable to sign in to the newly created managed domain, perform the following troubleshooting steps:

- Try to sign in using the UPN format (for example, 'joeuser@contoso.com') instead of the SAMAccountName format ('CONTOSO\joeuser'). Sometimes, the SAMAccountName may be automatically generated for users whose UPN prefix is overly long or is the same as another user on the managed domain. The UPN format is guaranteed to be unique within an Azure AD tenant.

> [AZURE.NOTE] We recommend using the UPN format to sign in to the Azure AD Domain Services managed domain.

- Ensure that you have [enabled password synchronization](active-directory-ds-getting-started-password-sync.md) in accordance with the steps outlined in the Getting Started guide.

- **External accounts:** Ensure that the affected user account is not an external account in the Azure AD tenant. Examples of external accounts include Microsoft accounts (for example, 'joe@live.com') or user accounts from an external Azure AD directory. Since Azure AD Domain Services does not have credentials for such user accounts, these users cannot sign in to the managed domain.

- **Overly long UPN prefix:** Ensure the affected user account's UPN prefix (first part of the UPN) in your Azure AD tenant is fewer than 20 characters long. For instance, for the UPN 'joereallylongnameuser@contoso.com', the prefix ('joereallylongnameuser') exceeds 20 characters and this account is not available in the managed domain.

- **Duplicate UPN prefix:** Ensure other user accounts in your Azure AD tenant do not have the same UPN prefix (the first part of the UPN) as the affected user account. For instance, if you have two user accounts 'joeuser@finance.contoso.com' and 'joeuser@engineering.contoso.com', both users encounter issues signing in to the managed domain. This issue could also happen if one of the user accounts is an external account (for example, 'joeuser@live.com'). We are working on a fix for this issue.

- **Synced accounts:** If the affected user accounts are synchronized from an on-premises directory, verify that:
    - You have deployed or updated to the [latest recommended release of Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).

    - You have configured Azure AD Connect to [perform a full synchronization](active-directory-ds-getting-started-password-sync.md).

    - Depending on the size of your directory, it may take a while for user accounts and credential hashes to be available in Azure AD Domain Services. Ensure you wait long enough before retrying authentication (depending on the size of your directory - a few hours to a day or two for large directories).

    - If the issue persists after verifying the preceding steps, try restarting the Microsoft Azure AD Sync Service. From your sync machine, launch a command prompt and execute the following commands:
      1. net stop 'Microsoft Azure AD Sync'
      2. net start 'Microsoft Azure AD Sync'

- **Cloud-only accounts**: If the affected user account is a cloud-only user account, ensure that the user has changed their password after you enabled Azure AD Domain Services. This step causes the credential hashes required for Azure AD Domain Services to be generated.


### Contact Us
Contact the Azure Active Directory Domain Services product team to [share feedback or for support] (active-directory-ds-contact-us.md).

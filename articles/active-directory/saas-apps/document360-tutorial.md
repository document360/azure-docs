---
title: Azure Active Directory SSO integration with Document360
description: Learn how to configure Single Sign-On (SSO) between Azure Active Directory (AD) and Document360.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2023
ms.author: jeedes

---

## Azure Active Directory (AD) SSO integration with Document360
> **Note**
> *Azure Active Directory = Azure AD*
> *Single Sign-On = SSO*

This article teaches you how to integrate Document360 with Azure AD. Document360 is an online self-service knowledge base software. When you integrate Document360 with Azure AD, you can:

* Control in Azure AD who has access to Document360.
* Enable your users to be automatically signed in to Document360 with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

You configure and test Azure AD single sign-on for Document360 in a test environment. Document360 supports **Service Provider (SP)** and **Identity Provider (IdP)** initiated SSO.

> **Note**
> *Identifier of this application is a fixed string value, so only one instance can be configured in one tenant.*

## Prerequisites

To integrate Azure AD with Document360, you need the following:

* An Azure AD user account. If you don't already have one, you can [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* One of the following roles: Global Administrator, Cloud Application Administrator, Application Administrator, or owner of the service principal.
* An Azure AD subscription. If you don't have a subscription, you can [get a free account](https://azure.microsoft.com/free/).
* Document360 subscription with SSO enabled. If you don't have a subscription, you can [Sign up for a new account](https://document360.com/signup/)

## Add application and assign a test user

Before configuring SSO, add the Document360 application from the Azure AD gallery. You need a test user account to assign to the application and test the SSO configuration.

### Add Document360 from the Azure AD gallery

Add Document360 from the Azure AD application gallery to configure SSO with Document360. For more information on adding an application from the gallery, see the [Quickstart: Add application from the gallery](../manage-apps/add-application-portal.md).

### Create and assign Azure AD test user

Follow the guidelines in the [create and assign a user account](../manage-apps/add-application-portal-assign-users.md) article to create a test user account in the Azure portal called B.Simon.

Alternatively, you can use the [Enterprise App Configuration Wizard](https://portal.office.com/AdminPortal/home?Q=Docs#/azureadappintegration). In this wizard, you can add an application to your tenant, add users/groups to the app, and assign roles. The wizard also provides a link to the single sign-on configuration pane in the Azure portal. [Learn more about Microsoft 365 wizards.](/microsoft-365/admin/misc/azure-ad-setup-guides). 

## Configure Azure AD SSO

Complete the following steps to enable Azure AD single sign-on in the Azure portal.

1. In the Azure portal, on the **Document360** application integration page, find the **Manage** section and select **single sign-on**.
2. On the **Select a single sign-on method** page, select **SAML**.
3. On the **Set up single sign-on with SAML** page, select the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Screenshot shows how to edit Basic SAML Configuration.](common/edit-urls.png "Basic Configuration")

4. Perform the following steps in the **Basic SAML Configuration** section. Choose any one of the Identifier, Reply URL, and Sign-on URLs based on your Datacenter region

    a. In the **Identifier** textbox, type/copy & paste one of the following URLs:

    | **Identifier** |
    |-----------|
    | `https://identity.document360.io/saml` |
    | **(or)** |
    | `https://identity.us.document360.io/saml` |

    b. In the **Reply URL** textbox, type/copy & paste a URL using one of the following patterns:

    | **Reply URL** |
    | ----------|
    | `https://identity.document360.io/signin-saml-<ID>` |
    | **(or)** |
    | `https://identity.us.document360.io/signin-saml-<ID>` |  

5. If you wish to configure the application in **SP** initiated mode, then perform the following step:

    In the **Sign on URL** textbox, type/copy & paste one of the following URLs:

    | **Sign on URL** |
    |-----------| 
    | `https://identity.document360.io ` |
    | **(or)** |
    | `https://identity.us.document360.io` |

    > [!NOTE]
    > Refer to the patterns in the Azure portal's **Basic SAML Configuration** section.

6. On the **Set-up single sign-on with SAML** page, in the **SAML Signing Certificate** section, find **Certificate (Raw)** and select **Download** to download the certificate and save it on your computer.

    ![Screenshot shows the Certificate download link.](common/certificateraw.png "Certificate")

7. On the **Set up Document360** section, copy the appropriate URL(s) based on your requirement.

	![Screenshot shows to copy configuration appropriate URL.](common/copy-configuration-urls.png "Metadata")

## Configure Document360 SSO

To configure SSO on the **Document360** portal, you need to navigate to **Setting** → **Users & Security** → **SAML/OpenID** → **SAML**

![Accessing SAML SSO in the Document360 portal](https://cdn.document360.io/860f9f88-412e-4570-8222-d5bf2f4b7dd1/Images/Documentation/85_Screenshot-SAML_Accessing_the_SAML_page.png){height="" width=""}

Click on the Edit icon in **SAML basic configuration** on the Document360 portal side and paste the values from the Azure AD portal based on the below-mentioned field associations.


| Document360 portal fields | Azure AD portal values |
| --- | --- |
| Email domains | Domains of emails you have under active directory |
| Sign On URL | Login URL |
| Entity ID | Azure AD identifier |
| Sign Out URL | Logout URL |
| SAML certificate | Download Certificate (Base64) from Azure AD side and upload in Document360 |

Click the **Save** button when you're done with the values.


### Create Document360 test user

In this section, you create a test user (For example, Britta Simon) at Document360. 

![Adding a team account in Document360](https://cdn.document360.io/860f9f88-412e-4570-8222-d5bf2f4b7dd1/Images/Documentation/87_Screenshot-Team_accounts_adding_new_team_account.png){height="" width=""}

1. Navigate to the Document360 portal
2. Go to **Settings → Users & Security → Team accounts & groups  → Team accounts**
3. Click the **+ New** button and select **New team account**
4. A **New team account** blade appears
5. Type in the required details, specify the roles and follow the module steps to add a user to Document360. 

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with the following options. 

#### SP initiated:

* Click on **Test this application** in Azure portal. This will redirect to the Document360 Sign-on URL, where you can initiate the login flow.  

* Go to Document360 Sign-on URL directly and initiate the login flow.

#### IDP initiated:

* Click on **Test this application** in the Azure portal, and you should be automatically signed in to the Document360 for which you set up the SSO. 

You can also use Microsoft My Apps to test the application in any mode. When you click the Document360 tile in the My Apps if configured in SP mode, you will be redirected to the application sign-on page for initiating the login flow. If configured in IDP mode, you should be automatically signed in to the Document360 for which you set up the SSO. 

For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Additional resources

* [What is single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Plan a single sign-on deployment](../manage-apps/plan-sso-deployment.md).

## Next steps

Once you configure Document360, you can enforce session control, which protects the exfiltration and infiltration of your organization's sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).

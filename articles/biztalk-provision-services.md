<properties urlDisplayName="Provision BizTalk Services in management portal" pageTitle="Create BizTalk Services in management portal | Azure" metaKeywords="Get started Azure biztalk services, provision, Azure unstructured data" description="Learn how to provision a BizTalk service in the Azure Management Portal, as well as create an optional SQL database server and Storage account." metaCanonical="http://www.windowsazure.com/en-us/manage/services/biztalk-services/provisioning-biztalk-service" services="biztalk-services" documentationCenter="" title="BizTalk Services: Provisioning Using Azure Management Portal" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# Create a BizTalk Service using Azure management portal

This topic lists the steps to create an Azure BizTalk Service in the Azure Management Portal. Specifically:

-   [Create a BizTalk Service][Create a BizTalk Service]
-   [Post-Provisioning Steps][Post-Provisioning Steps]
-   [Requirements Explained][Requirements Explained]
-   [Hybrid Connections - New!][Hybrid Connections - New!]

<div class="dev-callout"> 
<b>Tip</b> 
<p>To log into the Azure Management Portal, you need an Azure account and Azure subscription. If you don't have an account, you can create a free trial account within a few minutes. See <a href="http://go.microsoft.com/fwlink/p/?LinkID=239738">Azure Free Trial</a>.</p> 
</div>

## <a name="BizTalk"></a>Create a BizTalk Service

Depending on the Edition you choose, not all BizTalk Service settings may be available.

1.  Log in to the [Azure Management Portal][Azure Management Portal].
2.  In the bottom navigation pane, select **NEW**:
    ![Select the New button][Select the New button]

3.  Select **APP SERVICES** \> **BIZTALK SERVICE** \> **CUSTOM CREATE**:
    ![Select BizTalk Service and select Custom Create][Select BizTalk Service and select Custom Create]

4.  Enter the BizTalk Service settings.

    |--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **BizTalk Service Name** | You can enter any name but be specific. Some examples include:                                                                                                                                                                                                
                                 *mycompany*.biztalk.windows.net                                                                                                                                                                                                                               
                                 *mycompanymyapplication*.biztalk.windows.net                                                                                                                                                                                                                  
                                 *myapplication*.biztalk.windows.net                                                                                                                                                                                                                           
                                 ".biztalk.windows.net" is automatically added to the name you enter. This creates a URL that is used to access your BizTalk Service, like **https://*myapplication*.biztalk.windows.net**.                                                                    |
    | **Edition**              | If you are in the testing/development phase, choose **Developer**. If you are in the production phase, use the [BizTalk Services: Editions Chart][BizTalk Services: Editions Chart] to determine if **Premium**, **Standard**, or **Basic** is the correct choice for your business scenario. |
    | **Region**               | Select the geographic region to host your BizTalk Service.                                                                                                                                                                                                    |
    | **Domain URL**           | **Optional**. By default, the domain URL is *YourBizTalkServiceName*.biztalk.windows.net. A custom domain can also be entered. For example, if your domain is *contoso*, you can enter:                                                                       
                                 *MyCompany*.contoso.com                                                                                                                                                                                                                                       
                                 *MyCompanyMyApplication*.contoso.com                                                                                                                                                                                                                          
                                 *MyApplication*.contoso.com                                                                                                                                                                                                                                   
                                 *YourBizTalkServiceName*.contoso.com                                                                                                                                                                                                                          |

    Select the NEXT arrow.

5.  Enter the Storage and Database Settings:

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Monitoring/Archiving Storage Account</strong></td>
    <td align="left">Select an existing storage account or select create a new storage account.<br /><br /> If you create a new Storage account, enter the <strong>Storage Account Name</strong>.</td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Tracking Database</strong></td>
    <td align="left">If you use an existing Azure SQL Database, it cannot be used by another BizTalk Service. You need the login name and password entered when that Azure SQL Database Server was created.<br /><br />
    <div class="dev-callout"> 
<b>Tip</b> 
<p>Create the Tracking database and Monitoring/Archiving Storage Account in the same region as the BizTalk Service.</p> 
</div></td>
    </tr>
    </tbody>
    </table>

    Select the NEXT arrow.

6.  Enter the Database settings:

    |---------------------------|----------------------------------------------------------------------------------------------------------------------------|
    | **Name**                  | Available when **Create a new SQL Database instance** is selected in the previous screen.                                  
                                  Enter a SQL Database name to be used by your BizTalk Service.                                                              |
    | **Server**                | Available when **Create a new SQL Database instance** is selected in the previous screen.                                  
                                  Select an existing SQL Database Server or create a new SQL Database server.                                                |
    | **Server Login Name**     | Enter the login user name.                                                                                                 |
    | **Server Login Password** | Enter the login password.                                                                                                  |
    | **Region**                | Available when **Create a new SQL Database instance** is selected. Select the geographic region to host your SQL Database. |

Select the check mark to complete the wizard. The progress icon displays:
![Progress icon displays when complete][Progress icon displays when complete]

When complete, the Azure BizTalk Service is created and ready for your applications. The default settings are sufficient. If you want to change the default settings, select **BIZTALK SERVICES** in the left navigation pane, and select your BizTalk Service. Additional settings are displayed in the [Dashboard, Monitor, and Scale tabs][Dashboard, Monitor, and Scale tabs] at the top.

Depending on the state of the BizTalk Service, there are some operations that cannot be completed. For a list of these operations, go to [BizTalk Services State Chart][BizTalk Services State Chart].

## <a name="PostProv"></a>Post-Provisioning Steps

-   [Install the certificate on a local computer][Install the certificate on a local computer]
-   [Add a production-ready certificate][Add a production-ready certificate]
-   [Get the Access Control namespace][Get the Access Control namespace]

#### <a name="InstallCert"></a>Install the certificate on a local computer

As part of BizTalk Service provisioning, a self-signed certificate is created and associated with your BizTalk Service subscription. You must download this certificate and install it on computers from where you either deploy BizTalk Service applications or send messages to a BizTalk Service endpoint.

1.  Log in to the [Azure Management Portal][Azure Management Portal].
2.  Click **BIZTALK SERVICES** in the left navigation pane and then select your BizTalk Service subscription.
3.  Click the **Dashboard** tab.
4.  Click **Download SSL Certificate**.
    ![Modify SSL Certificate][Modify SSL Certificate]
5.  Double-click the certificate and run through the wizard to install the certificate. Make sure you install the certificate under the **Trusted Root Certificate Authorities** store.

#### <a name="AddCert"></a>Add a production-ready certificate

The self-signed certificate that is automatically created while provisioning BizTalk Services is intended for use in development environments only. For production scenarios, you must replace it with a production-ready certificate.

1.  On the **Dashboard** tab, click **Update SSL Certificate**.
2.  Browse to your private SSL certificate (*CertificateName*.pfx) that includes your BizTalk Service name, enter the password, and select the check mark.

#### <a name="ACS"></a>Get the Access Control namespace

1.  Log in to the [Azure Management Portal][Azure Management Portal].
2.  Select **BIZTALK SERVICES** in the left navigation pane and then select your BizTalk Service.
3.  In the task bar, select **Connection Information**:
    ![Select Connection Information][Select Connection Information]

4.  Copy the Access Control values.

When you deploy a BizTalk Service project from Visual Studio, you enter this Access Control namespace. The Access Control Namespace is automatically created for your BizTalk service.

The Access Control values can be used with any application. When Azure BizTalk Services is created, this Access Control namespace controls the authentication with your BizTalk Service deployment. If you want to change the subscription or manage the namespace, select **ACTIVE DIRECTORY** in the left navigation pane and then select your namespace. The task bar lists your options.

Clicking **Manage** opens the Access Control Management Portal. In the Access Control Management Portal, the BizTalk Service uses **Service identities**:
![ACS Service Identities in the Access Control Management Portal][ACS Service Identities in the Access Control Management Portal]

The Access Control service identity is a set of credentials that allow applications or clients to authenticate directly with Access Control and receive a token.

**Important**
The BizTalk Service uses **Owner** for the default service identity and the **Password** value. If you use the Symmetric Key value instead of the Password value, the following error may occur:

*Could not connect to the Access Control Management Service account with the specified credentials*

[Managing Your ACS Namespace][Managing Your ACS Namespace] lists some guidelines and recommendations.

## <a name="Requirements"></a>Requirements Explained

These Requirements do not apply to the Free Edition.

<table border="1">

<tr bgcolor="FAF9F9">

<td>
**What you need**

</td>

<td>
**Why you need it**

</td>

</tr>

<tr>

<td>
Azure Subscription

</td>

<td>
The subscription determines who can log into the Azure Management Portal. The Account holder creates the subscription at [Azure Subscriptions][Azure Subscriptions].
The Azure account can have multiple subscriptions and can be managed by anyone who is permitted. For example, your Azure account holder creates a subscription named *BizTalkServiceSubscription* and gives the BizTalk Administrators within your company (e.g. <ContosoBTSAdmins@live.com>) access to this subscription. In this scenario, the BizTalk Administrators log into the Azure Management Portal and have full Administrator rights to all the hosted services in the subscription, including Azure BizTalk Services. The BizTalk Administrators are not the Azure account holders and therefore don't have access to any billing information.
[Manage Subscriptions and Storage Accounts in the Azure Management Portal][Manage Subscriptions and Storage Accounts in the Azure Management Portal] provides more information.

</td>

</tr>

<tr>

<td>
Azure SQL Database

</td>

<td>
Stores the tables, views, and stored procedures used by the BizTalk Service, including the Tracking data.
When you create a BizTalk Service, you can use an existing Azure SQL Server, Azure SQL Database, or automatically create a new Server or Database.
The SQL Database scale is automatically configured. Typically, the default scale is sufficient for a BizTalk Service. Changing the scale impacts pricing. See [Accounts and Billing in Azure SQL Database][Accounts and Billing in Azure SQL Database]
**Notes**

-   When you create a new Azure SQL Server and Database, Azure Services is automatically enabled. The BizTalk Service requires Azure Services be enabled.
-   If you create a new Azure SQL Database on an existing Azure SQL Server, the firewall rules of the Server are not changed. As a result, it's possible other Azure Services are not allowed access to the Server's databases.

</td>

</tr>

<tr>

<td>
Azure Access Control namespace

</td>

<td>
Authenticates with Azure BizTalk Services. When you deploy a BizTalk Service project from Visual Studio, you enter this Access Control Namespace. When you create a BizTalk Service, the Access Control Namespace is automatically created.

</td>

</tr>
</p>
<tr>
<td>
Azure Storage Account

</td>
<td>
Gives access to tables, blobs, and queues used by your BizTalk Service to save the following:

-   Log files that monitor the BizTalk Service. The monitoring output is also displayed in Monitoring tab in the Azure Management Portal.
-   When creating an X12 or AS2 agreement between partners, you can enable the Archiving feature to store message properties. This data is saved in the Storage Account.

 When you create a BizTalk Service, you can use an existing Storage Account or automatically create a new Storage Account.
 The default Storage settings are sufficient for a BizTalk Service.
 When you create a Storage account, a Primary Key and Secondary Key are automatically created. These Keys control access to your Storage Account. The BizTalk Service automatically uses the Primary Key.
 See [Storage][Storage] for more information.

</td>
</tr>
<tr>
<td>
SSL private certificate

</td>
<td>
When an Azure BizTalk Service is created, an HTTPS URL that includes your BizTalk Service name is also created. This URL is automatically configured to use a self-signed development-only certificate. For production, you need a private SSL certificate.
 **Important SSL Certificate Information**

-   The certificate expiration date must be less than 5 years.
-   All private certificates require a password. Know this password and as a best practice, share this password with your administrators.
-   Self-signed certificates are used in a test/development environment. When using self-signed certificates, import the certificate to your Personal certificate store and the Trusted Root Certification Authorities certificate store.

When sending the production certificate request to your certification authority, give the follwoing certificate properties:

-   **Enhanced Key Usage**: At a minimum, Azure BizTalk Services requires Server Authentication.
-   **Common Name**: Enter the fully qualified domain name (FQDN) of your Azure BizTalk Service URL. See [Create a BizTalk Service][Create a BizTalk Service] in this topic.

 A new or different certificate can be added after the BizTalk Service is created.

</td>
</tr>
</table>
## <a name="HC"></a>Hybrid Connections

When you create an Azure BizTalk Service, the **Hybrid Connections** tab is available:

![Hybrid Connections Tab][Hybrid Connections Tab]

Hybrid Connections are used to connect an Azure website or Azure mobile service to any on-premises resource that uses a static TCP port, such as SQL Server, MySQL, HTTP Web APIs, Mobile Services, and most custom Web Services. Hybrid Connections and the BizTalk Adapter Service are different. The BizTalk Adapter Service is used to connect Azure BizTalk Services to an on-premises Line of Business (LOB) system.

See [Hybrid Connections][Hybrid Connections] to learn more, including creating and managing Hybrid Connections.

## Next

Now that a BizTalk Service is created, familiarize yourself with the different [BizTalk Services: Dashboard, Monitor and Scale tabs][Dashboard, Monitor, and Scale tabs]. Your BizTalk Service is ready for your applications. To start creating applications, go to [Azure BizTalk Services][Azure BizTalk Services].

## See Also

-   [BizTalk Services: Editions Chart][BizTalk Services: Editions Chart]
-   [BizTalk Services: State Chart][BizTalk Services State Chart]
-   [BizTalk Services: Backup and Restore][BizTalk Services: Backup and Restore]
-   [BizTalk Services: Throttling][BizTalk Services: Throttling]
-   [BizTalk Services: Issuer Name and Issuer Key][BizTalk Services: Issuer Name and Issuer Key]
-   [How do I Start Using the Azure BizTalk Services SDK][How do I Start Using the Azure BizTalk Services SDK]
-   [Hybrid Connections][Hybrid Connections]

  [Create a BizTalk Service]: #BizTalk
  [Post-Provisioning Steps]: #PostProv
  [Requirements Explained]: #Requirements
  [Hybrid Connections - New!]: #HC
  [Azure Free Trial]: http://go.microsoft.com/fwlink/p/?LinkID=239738
  [Azure Management Portal]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [Select the New button]: ./media/biztalk-provision-services/WABS_New.png
  [Select BizTalk Service and select Custom Create]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
  [BizTalk Services: Editions Chart]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Progress icon displays when complete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
  [Dashboard, Monitor, and Scale tabs]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk Services State Chart]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Install the certificate on a local computer]: #InstallCert
  [Add a production-ready certificate]: #AddCert
  [Get the Access Control namespace]: #ACS
  [Modify SSL Certificate]: ./media/biztalk-provision-services/WABS_QuickGlance.png
  [Select Connection Information]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
  [ACS Service Identities in the Access Control Management Portal]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
  [Managing Your ACS Namespace]: http://go.microsoft.com/fwlink/p/?LinkID=285670
  [Azure Subscriptions]: https://account.windowsazure.com/Subscriptions
  [Manage Subscriptions and Storage Accounts in the Azure Management Portal]: http://go.microsoft.com/fwlink/p/?LinkID=267577
  [Accounts and Billing in Azure SQL Database]: http://go.microsoft.com/fwlink/p/?LinkID=234930
  [Storage]: http://go.microsoft.com/fwlink/p/?LinkID=285671
  [Hybrid Connections Tab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
  [Hybrid Connections]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Azure BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=235197
  [BizTalk Services: Backup and Restore]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [BizTalk Services: Throttling]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [BizTalk Services: Issuer Name and Issuer Key]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [How do I Start Using the Azure BizTalk Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=302335

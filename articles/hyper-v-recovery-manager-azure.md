<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Getting Started with Azure Site Recovery: On-Premises to Azure Protection" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in on-premises VMM clouds to Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to Azure Protection" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# Getting Started with Azure Site Recovery: On-Premises to Azure Protection

<div class="dev-callout">

Use Azure Site Recovery to orchestrate protection for virtual machines on on-premises Hyper-V host servers located in VMM clouds. You can configure:

-   **On-premises to Azure protection**—Replicate on-premise virtual machines to Azure. You configure and enable protection settings in Azure Site Recovery vaults. Virtual machine data replicates from an on-premises Hyper-V server to Azure storage.
-   **On-premises to on-premises protection**—Replicate on-premise virtual machines to another on-premises site. You configure and enable protection settings in Azure Site Recovery vaults. Virtual machine data replicates from one on-premises Hyper-V server to another. Learn about this scenario in [Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection][Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection].

## <span id="about"></span></a>About this tutorial

Use this tutorial to set up a quick proof-of-concept for Azure Site Recovery in an on-premises to Azure deployment. It uses the quickest path and default settings where possible. You'll create an Azure Site Recovery vault, install the Azure Site Recovery Provider in the source VMM server, install the Azure Recovery Services Agent on Hyper-V host servers in the VMM clouds, configure cloud protection settings, enable protection for virtual machines, and test your deployment.

If you want information about a full deployment see:

-   [Plan for Azure Site Recovery Deployment][Plan for Azure Site Recovery Deployment]—Describes the planning steps you'll need before a starting a full deployment.
-   [Deploy Azure Site Recovery: On-Premises to Azure Protection][Deploy Azure Site Recovery: On-Premises to Azure Protection]—Provides step-by-step instructions for a full deployment.

If you run into problems during this tutorial, review the wiki article [Azure Site Recovery: Common Error Scenarios and Resolutions][Azure Site Recovery: Common Error Scenarios and Resolutions], or post your questions on the [Azure Recovery Services Forum][Azure Recovery Services Forum].

</div>

## <span id="before"></span></a>Before you begin

<div class="dev-callout">

Before you start this tutorial check the prerequisites.

### <span id="HVRMPrereq"></span></a>Prerequisites

-   **Azure account**—You'll need an Azure account. If you don't have one, see [Azure free trial][Azure free trial]. Get pricing information at [Azure Site Recovery Manager Pricing Details][Azure Site Recovery Manager Pricing Details].
-   **Azure storage account**—You'll need an Azure storage account to store data replicated to Azure. The account needs geo-replication enabled. It should be in the same region as the Azure Site Recovery service, and be associated with the same subscription. To learn more about setting up Azure storage, see [Introduction to Microsoft Azure Storage][Introduction to Microsoft Azure Storage].
-   **VMM server**—A VMM server running on System Center 2012 R2.
-   **VMM clouds**—At least one cloud on the VMM server.The cloud should contain:
    -   One or more VMM host groups
    -   One or more Hyper-V host servers or clusters in each host group.
    -   One or more virtual machines located on the source Hyper-V server in the cloud. The virtual machines should be generation 1.
-   **Virtual machine**—You'll need virtual machines that comply with Azure requirements. See [Prerequisites and support][Prerequisites and support] in the Planning guide.
-   For a full list of virtual machine support requirements for failover to Azure, read

## <span id="tutorial"></span></a>Tutorial steps

After verifying the prerequisites, do the following:

-   [Step 1: Create a vault][Step 1: Create a vault]—Create an Azure Site Recovery vault.
-   [Step 2: Install the Provider application][Step 2: Install the Provider application]—Generate a registration key, and run the Microsoft Azure Site Recovery Provider application on the VMM server. This installs the Provider and registers the VMM server in the vault.
-   [Step 3: Add an Azure storage account][Step 3: Add an Azure storage account]—If you don't have an account create one.
-   [Step 4: Install the Agent application][Step 4: Install the Agent application]—Install the Microsoft Azure Recovery Services agent on each Hyper-V host.
-   [Step 5: Configure cloud protection][Step 5: Configure cloud protection]—Configure protection settings for VMM clouds.
-   [Step 6: Configure network mapping][Step 6: Configure network mapping]—You can optionally configure network mapping to map source VM networks to target Azure networks.
-   [Step 7: Enable protection for virtual machines][Step 7: Enable protection for virtual machines]—Enable protection for virtual machines located in protected VMM clouds.
-   [Step 8: Test the deployment][Step 8: Test the deployment]—You can run a test failover for a single virtual machine, or you can create a recovery plan and run a test failover for the plan.

<a name="vault"></a>

## Step 1: Create a vault

</p>
1.  Sign in to the [Management Portal][Management Portal].

2.  Expand **Data Services**, expand **Recovery Services**, and click **Site Recovery Vault**.

3.  Click **Create New** and then click **Quick Create**.

4.  In **Name**, enter a friendly name to identify the vault.

5.  In **Region**, select the geographic region for the vault. Available geographic regions include East Asia, West Europe, West US, East US, North Europe, Southeast Asia.
6.  Click **Create vault**.

    ![New Vault][New Vault]

Check the status bar to confirm that the vault was successfully created. The vault will be listed as **Active** on the main Recovery Services page.

<a name="download"></a>

## Step 2: Generate a registration key and install the Azure Site Recovery Provider

</p>
1.  In the **Recovery Services** page, click the vault to open the Quick Start page. Quick Start can also be opened at any time using the icon.

    ![Quick Start Icon][Quick Start Icon]

2.  In the dropdown list, select **Between an on-premises Hyper-V site and Microsoft Azure**.
3.  In **Prepare VMM Servers**, click **Generate registration key** file. The key is valid for 5 days after it's generated. Copy the file to the VMM server. You'll need it when you set up the Provider.

    ![Registration key][Registration key]

4.  On the **Quick Start** page, in **Prepare VMM servers**, click **Download Microsoft Azure Site Recovery Provider for installation on VMM servers** to obtain the latest version of the Provider installation file.

5.  Run this file on the source VMM server.

6.  In **Pre-requirements Check** select to stop the VMM service to begin Provider setup. The service stops and will restart automatically when setup finishes.

    ![Prerequisites][Prerequisites]

7.  In **Microsoft Update** you can opt in for updates. With this setting enabled Provider updates will be installed according to your Microsoft Update policy.

    ![Microsoft Updates][Microsoft Updates]

After the Provider is installed continue setup to register the server in the vault.

1.  In **Internet Connection** specify how the Provider running on the VMM server connects to the Internet. Select **Use default system proxy settings** to use the default Internet connection settings configured on the server.

    ![Internet Settings][Internet Settings]

2.  In **Registration Key**, select that you downloaded from Azure Site Recovery and copied to the VMM server.
3.  In **Vault name**, verify the name of the vault in which the server will be registered.
4.  In **Server name**, specify a friendly name to identify the VMM server in the vault.

    ![Server registration][Server registration]

5.  In **Initial cloud metadata** sync select whether you want to synchronize metadata for all clouds on the VMM server with the vault. This action only needs to happen once on each server. If you don't want to synchronize all clouds, you can leave this setting unchecked and synchronize each cloud individually in the cloud properties in the VMM console.

6.  In **Data Encryption** you specify a location to save an SSL certificate that’s automatically generated for data encryption. This certificate is used if you enable data encryption for a cloud protected by Azure in the Azure Site Recovery portal. Keep this certificate safe. When you run a failover to Azure you’ll select it in order to decrypt encrypted data.
    This option isn’t relevant if you’re replicating from one on-premises site to another.

    ![Server registration][1]

7.  Click **Register** to complete the process. After registration, metadata from the VMM server is retrieved by Azure Site Recovery. The server is displayed on the ed on the **Resources** tab on the **Servers** page in the vault.

## <span id="storage"></span></a>Step 3: Create an Azure storage account

If you don't have an Azure storage account click **Add an Azure Storage Account**. The account should have geo-replication enabled. It must in the same region as the Azure Site Recovery service, and be associated with the same subscription.

Use this tutorial to set up a quick proof-of-concept for Azure Site Recovery in an on-premises to Azure deployment. It uses the quickest path and default settings where possible. You'll create an Azure Site Recovery vault, install the Azure Site Recovery Provider in the source VMM server, install the Azure Recovery Services Agent on Hyper-V host servers in the VMM clouds, configure cloud protection settings, enable protection for virtual machines, and test your deployment.

![Storage account][Storage account]

## <span id="agent"></span></a>Step 4: Install the Azure Recovery Services Agent on Hyper-V hosts

Install the Azure Recovery Services agent on each Hyper-V host server located in the VMM clouds you want to protect.

1.  On the Quick Start page, click **Download Azure Site Recovery Services Agent and install on hosts** to obtain the latest version of the agent installation file.

    ![Install Recovery Services Agent][Install Recovery Services Agent]

2.  Run the installation file on each Hyper-V host server that's located in VMM clouds you want to protect.
3.  On the **Prerequisites Check** page click **Next**. Any missing prerequisites will be automatically installed.

    ![Prerequisites Recovery Services Agent][Prerequisites Recovery Services Agent]

4.  On the **Installation Settings** page, specify where you want to install the Agent and select the cache location in which backup metadata will be installed. Then click **Install**.

## <span id="clouds"></span></a>Step 5: Configure cloud protection settings

After VMM servers are registered, you can configure cloud protection settings. You enabled the option **Synchronize cloud data with the vault** when you installed the Provider so all clouds on the VMM server will appear in the **Protected Items** tab in the vault.

![Published Cloud][Published Cloud]

1.  On the Quick Start page, click **Set up protection for VMM clouds**.

2.  On the **Protected Items** tab, click on the cloud you want to configure and go to the **Configuration** tab.
3.  In **Target**, select **Microsoft Azure**.
4.  In **Storage Account**, select the Azure storage you want to use to store Azure virtual machines.
5.  Set **Encrypt stored data** to **Off**. This setting specifies that data should be encrypted replicated between the on-premises site and Azure.
6.  In **Copy frequency** leave the default setting. This value specifies how frequently data should be synchronized between source and target locations.
7.  In **Retain recovery points for**, leave the default setting. With a default value of zero only the latest recovery point for a primary virtual machine is stored on a replica host server.
8.  In **Frequency of application-consistent snapshots**, leave the default setting. This value specifies how often to create snapshots. Snapshots use Volume Shadow Copy Service (VSS) to ensure that applications are in a consistent state when the snapshot is taken. If you do set a value, make sure it's less than the number of additional recovery points you configure.
9.  In **Replication start time**, specify when initial replication of data to Azure should start. The timezone on the Hyper-V host server will be used. We recommend that you schedule the initial replication during off-peak hours.

    ![Cloud replication settings][Cloud replication settings]

After you save the settings a job will be created and can be monitored on the **Jobs** tab. All Hyper-V host servers in the VMM source cloud will be configured for replication. After saving, cloud settings can be modified on the **Configure** tab. To modify the target location or target storage you'll need to remove the cloud configuration, and then reconfigure the cloud. Note that if you change the storage account the change is only applied for virtual machines that are enabled for protection after the storage account has been modified. Existing virtual machines are not migrated to the new storage account.

## <span id="networkmapping"></span></a>Step 6: Configure network mapping

This tutorial describes the simplest path to deploy Azure Site Recovery in a test environment. If you do want to configure network mapping as part of this tutorial, read [Prepare for network mapping][Prepare for network mapping] in the Planning Guide. To configure mapping follow the steps to [Configure network mapping][Configure network mapping] in the deployment guide.

## <span id="virtualmachines"></span></a>Step 7: Enable protection for virtual machines

After servers, clouds, and networks are configured correctly, you can enable protection for virtual machines in the cloud. Note the following:

-   Virtual machines must meet Azure requirements. Check these in [Prerequisites and support][Prerequisites and support] in the Planning guide.
-   To enable protection the operating system and operating system disk properties must be set for the virtual machine. When you create a virtual machine in VMM using a virtual machine template you can set the property. You can also set these properties for existing virtual machines on the \*\*General\*\* and \*\*Hardware Configuration\*\* tabs of the virtual machine properties. If you don't set these properties in VMM you'll be able to configure them in the Azure Site Recovery portal.

![Create virtual machine][Create virtual machine]

![Modify virtual machine properties][Modify virtual machine properties]

1.  To enable protection, on the **Virtual Machines** tab in the cloud in which the virtual machine is located, click **Enable protection** and then select **Add virtual machines**
2.  From the list of virtual machines in the cloud, select the one you want to protect.

    ![Enable virtual machine protection][Enable virtual machine protection]

3.  Verify the virtual machine properties and modify as required.

    ![Verify virtual machines][Verify virtual machines]

Track progress of the Enable Protection action in the \*\*Jobs\*\* tab, including the initial replication. After the Finalize Protection job runs the virtual machine is ready for failover. After protection is enabled and virtual machines are replicated, you’ll be able to view them in Azure.

![Virtual machine protection job][Virtual machine protection job]

## <span id="test"></span></a>Step 8: Test the deployment

To test your deployment you can run a test failover for a single virtual machine, or create a recovery plan consisting of multiple virtual machines and run a test failover for the plan. Test failover simulates your failover and recovery mechanism in an isolated network. Note the following:

-   If you want to connect to the virtual machine in Azure using Remote Desktop after the failover, enable Remote Desktop Connection on the virtual machine before you run the test failover.
-   After failover you'll use a public IP address to connect to the virtual machine in Azure using Remote Desktop. If you want to do this, ensure you don't have any domain policies that prevent you from connecting to a virtual machine using a public address.

-   For instructions on creating a recovery plan see [Create and customize recovery plans: On-Premises to Azure][Create and customize recovery plans: On-Premises to Azure].
-   For instructions on running a test failover see [Test an on-premises to Azure deployment][Test an on-premises to Azure deployment].

</p>
### <span id="runtest"></span></a>Monitor activity

You can use the **Jobs** tab and **Dashboard** to view and monitor the main jobs performed by the Azure Site Recovery vault, including configuring protection for a cloud, enabling and disabling protection for a virtual machine, running a failover (planned, unplanned, or test), and committing an unplanned failover.

From the **Jobs** tab you view jobs, drill down into job details and errors, run job queries to retrieve jobs that match specific criteria, export jobs to Excel, and restart failed jobs.

From the **Dashboard** you can download the latest versions of Provider and Agent installation files, get configuration information for the vault, see the number of virtual machines that have protection managed by the vault, see recent jobs, manage the vault certificate, and resynchronize virtual machines.

For more information about interacting with jobs and the dashboard, see the [Operations and Monitoring Guide][Operations and Monitoring Guide].

## <span id="next"></span></a>Next steps

-   To plan and deploy Azure Site Recovery in a full production environment, see [Planning Guide for Azure Site Recovery][Plan for Azure Site Recovery Deployment] and [Deployment Guide for Azure Site Recovery][Deployment Guide for Azure Site Recovery].
-   For questions, visit the [Azure Recovery Services Forum][Azure Recovery Services Forum].

</div>

  [Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Plan for Azure Site Recovery Deployment]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Deploy Azure Site Recovery: On-Premises to Azure Protection]: http://go.microsoft.com/fwlink/?LinkId=402679
  [Azure Site Recovery: Common Error Scenarios and Resolutions]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Azure Recovery Services Forum]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Azure free trial]: http://aka.ms/try-azure
  [Azure Site Recovery Manager Pricing Details]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Introduction to Microsoft Azure Storage]: http://go.microsoft.com/fwlink/?LinkId=398704
  [Prerequisites and support]: http://go.microsoft.com/fwlink/?LinkId=402602
  [Step 1: Create a vault]: #vault
  [Step 2: Install the Provider application]: #download
  [Step 3: Add an Azure storage account]: #storage
  [Step 4: Install the Agent application]: #agent
  [Step 5: Configure cloud protection]: #clouds
  [Step 6: Configure network mapping]: #NetworkMapping
  [Step 7: Enable protection for virtual machines]: #virtualmachines
  [Step 8: Test the deployment]: #test
  [Management Portal]: https://manage.windowsazure.com
  [New Vault]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Quick Start Icon]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Registration key]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png
  [Prerequisites]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft Updates]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Internet Settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Server registration]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Storage account]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png
  [Install Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png
  [Prerequisites Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [Published Cloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Cloud replication settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png
  [Prepare for network mapping]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Configure network mapping]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Create virtual machine]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png
  [Modify virtual machine properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png
  [Enable virtual machine protection]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png
  [Verify virtual machines]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png
  [Virtual machine protection job]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Create and customize recovery plans: On-Premises to Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Test an on-premises to Azure deployment]: http://go.microsoft.com/fwlink/?LinkId=511494
  [Operations and Monitoring Guide]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Deployment Guide for Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321295

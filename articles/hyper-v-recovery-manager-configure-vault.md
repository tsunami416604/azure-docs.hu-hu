<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in on-premises VMM clouds to another on-premises site." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection

<div class="dev-callout">

Use Azure Site Recovery to orchestrate protection for virtual machines on on-premises Hyper-V host servers located in VMM clouds. You can configure:

-   **On-premises to on-premises protection**—Replicate on-premise virtual machines to another on-premises site. You configure and enable protection settings in Azure Site Recovery vaults. Virtual machine data replicates from one on-premises Hyper-V server to another. Learn about this scenario in [Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection][Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection].
-   **On-premises to Azure protection**—Replicate on-premise virtual machines to Azure. You configure and enable protection settings in Azure Site Recovery vaults. Virtual machine data replicates from an on-premises Hyper-V server to Azure storage. Learn about this scenario in [Getting Started with Azure Site Recovery: On-Premises to Azure Protection][Getting Started with Azure Site Recovery: On-Premises to Azure Protection]

## <span id="about"></span></a>About this tutorial

Use this tutorial to set up a quick proof-of-concept for Azure Site Recovery in an on-premises to Azure deployment. It uses the quickest path and default settings where possible. You'll create an Azure Site Recovery vault, install the Azure Site Recovery Provider in the source VMM server, configure cloud protection settings, enable protection for virtual machines, and test your deployment.

If you want information about a full deployment see:

-   [Plan for Azure Site Recovery Deployment][Plan for Azure Site Recovery Deployment]—Describes the planning steps you should complete before a starting a full deployment.
-   [Deploy Azure Site Recovery: On-Premises to On-Premises Protection][Deploy Azure Site Recovery: On-Premises to On-Premises Protection]—Provides step-by-step instructions for a full deployment.

If you run into problems during this tutorial, review the wiki article [Azure Site Recovery: Common Error Scenarios and Resolutions][Azure Site Recovery: Common Error Scenarios and Resolutions], or post your questions on the [Azure Recovery Services Forum][Azure Recovery Services Forum].

</div>

## <span id="before"></span></a>Before you begin

<div class="dev-callout">

Before you start this tutorial check the prerequisites.

### <span id="HVRMPrereq"></span></a>Prerequisites

-   **Azure account**—You'll need an Azure account. If you don't have one, see [Azure free trial][Azure free trial]. Get pricing information at [Azure Site Recovery Manager Pricing Details][Azure Site Recovery Manager Pricing Details].
-   **VMM server**—You'll need at least one VMM server running on System Center 2012 SP1 or System Center 2012 R2.
-   **VMM clouds**—You should have at least one cloud on the source VMM server you want to protect, and one on the target VMM server. If you're running one VMM server it'll need two clouds. The primary cloud you want to protect must contain the following:
    -   One or more VMM host groups
    -   One or more Hyper-V host servers or clusters in each host group.
    -   One or more virtual machines located on the source Hyper-V server in the cloud.

## <span id="tutorial"></span></a>Tutorial steps

After verifying the prerequisites, do the following:

-   [Step 1: Create a vault][Step 1: Create a vault]—Create an Azure Site Recovery vault.
-   [Step 2: Install the Provider application][Step 2: Install the Provider application]—Generate a registration key, and run the Microsoft Azure Site Recovery Provider application on the VMM server. This installs the Provider and registers the VMM server in the vault.
-   [Step 3: Configure cloud protection][Step 3: Configure cloud protection]—Configure protection settings for VMM clouds.
-   <a href="#networkmapping">Step 5: Configure network mapping—You can optionally configure network mapping to map source VM networks to target VM networks.
-   [Step 6: Configure storage mapping][Step 6: Configure storage mapping]—You can optionally configure storage mapping to map storage classifications on the source VMM server to storage classifications on the target server.
-   [Step 7: Enable protection for virtual machines][Step 7: Enable protection for virtual machines]—Enable protection for virtual machines located in protected VMM clouds
-   [Step 8: Configure and run recovery plans][Step 8: Configure and run recovery plans]—Create a recovery plan and run a test failover for the plan.

<a name="vault"></a>

## Step 1: Create a vault

</p>
1.  Sign in to the [Management Portal][Management Portal].

2.  Expand **Data Services**, expand **Recovery Services**, and click **Site Recovery Vault**.

3.  Click **Create New** and then click **Quick Create**.

4.  In **Name**, enter a friendly name to identify the vault.

5.  In **Region**, select the geographic region for the vault. To check supported regions see Geographic Availability in [Azure Site Recovery Pricing Details][Azure Site Recovery Pricing Details]

6.  Click **Create vault**.

    ![New Vault][New Vault]

Check the status bar to confirm that the vault was successfully created. The vault will be listed as **Active** on the main Recovery Services page.

<a name="upload"></a>

## Step 2: Configure the vault

</p>
1.  In the **Recovery Services** page, click the vault to open the Quick Start page. Quick Start can also be opened at any time using the icon.

    ![Quick Start Icon][Quick Start Icon]

2.  In the dropdown list, select **Between two on-premises Hyper-V sites**.
3.  In **Prepare VMM Servers**, click **Generate registration key** file. The key is valid for 5 days after it's generated. Copy the file to the VMM server. You'll need it when you set up the Provider.

    ![Registration key][Registration key]

<a name="download"></a>

## Step 3: Install the Azure Site Recovery Provider

</p>
1.  On the **Quick Start** page, in **Prepare VMM servers**, click **Download Microsoft Azure Site Recovery Provider for installation on VMM servers** to obtain the latest version of the Provider installation file.

2.  Run this file on the source and target VMM servers.

3.  In **Pre-requirements Check** select to stop the VMM service to begin Provider setup. The service stops and will restart automatically when setup finishes.

    ![Prerequisites][Prerequisites]

4.  In **Microsoft Update** you can opt in for updates. With this setting enabled Provider updates will be installed according to your Microsoft Update policy.

    ![Microsoft Updates][Microsoft Updates]

After the Provider is installed continue setup to register the server in the vault.

1.  On the Internet Connection page specify how the Provider running on the VMM server connects to the Internet. select **Use default system proxy settings** to use the default Internet connection settings configured on the server.

    ![Internet Settings][Internet Settings]

2.  In **Registration Key**, select that you downloaded from Azure Site Recovery and copied to the VMM server.
3.  In **Vault name**, verify the name of the vault in which the server will be registered.
4.  In **Server name**, specify a friendly name to identify the VMM server in the vault.

    ![Server registration][Server registration]

5.  In **Initial cloud metadata** sync select whether you want to synchronize metadata for all clouds on the VMM server with the vault. This action only needs to happen once on each server. If you don't want to synchronize all clouds, you can leave this setting unchecked and synchronize each cloud individually in the cloud properties in the VMM console.

6.  In **Data Encryption** you generate a certificate that's used to encrypt data protected in Azure.
    This option isn’t relevant if you’re replicating from one on-premises site to another.

    ![Server registration][1]

7.  Click **Register** to complete the process. After registration, metadata from the VMM server is retrieved by Azure Site Recovery. The server is displayed on the ed on the **Resources** tab on the **Servers** page in the vault.

## <span id="clouds"></span></a>Step 4: Configure cloud protection settings

After VMM servers are registered, you can configure cloud protection settings. You enabled the option **Synchronize cloud data with the vault** when you installed the Provider so all clouds on the VMM server will appear in the **Protected Items** tab in the vault.

![Published Cloud][Published Cloud]

1.  On the Quick Start page, click **Set up protection for VMM clouds**.
2.  On the **Protected Items** tab, select the cloud that you want to configure and go to the **Configuration** tab. Note that:
3.  In **Target**, select **VMM**.
4.  In **Target location**, select the on-site VMM server that manages the cloud you want to use for recovery.
5.  In **Target cloud**, select the target cloud you want to use for failover of virtual machines in the source cloud. Note that:

    -   We recommend that you select a target cloud that meets recovery requirements for the virtual machines you'll protect.
    -   A cloud can only belong to a single cloud pair — either as a primary or a target cloud.

6.  In **Copy frequency** leave the default setting. This value specifies how frequently data should be synchronized between source and target locations. It's only relevant when the Hyper-V host is running Windows Server 2012 R2. For other servers a default setting of five minutes is used.
7.  In **Additional recovery points**, leave the default setting. This value specifies whether you want to create addition recovery points.The default zero value specifies that only the latest recovery point for a primary virtual machine is stored on a replica host server.
8.  In **Frequency of application-consistent snapshots**, leave the default setting. This value specifies how often to create snapshots. Snapshots use Volume Shadow Copy Service (VSS) to ensure that applications are in a consistent state when the snapshot is taken. If you do want to set this value for the tutorial walkthrough, ensure that it is set to less than the number of additional recovery points you configure.
    ![Configure protection settings][Configure protection settings]
9.  In **Data transfer compressed**, specify whether replicated data that is transferred should be compressed.
10. In **Authentication**, specify how traffic is authenticated between the primary and recovery Hyper-V host servers. For the purpose of this walkthrough select HTTPS unless you have a working Kerberos environment configured. Azure Site Recovery will automatically configure certificates for HTTPS authentication. No manual configuration is required. Note that this setting is only relevant for Hyper-V host servers running on Windows Server 2012 R2.
11. In **Port**, leave the default setting. This value sets the port number on which the source and target Hyper-V host computers listen for replication traffic.
12. In **Replication method**, specify how the initial replication of data from source to target locations will be handled, before regular replication starts.

    -   **Over network**—Copying data over the network can be time-consuming and resource-intensive. We recommend that you use this option if the cloud contains virtual machines with relatively small virtual hard disks, and if the primary VMM server is connected to the secondary VMM server over a connection with wide bandwidth. You can specify that the copy should start immediately, or select a time. If you use network replication, we recommend that you schedule it during off-peak hours.
    -   **Offline**—This method specifies that the initial replication will be performed using external media. It's useful if you want to avoid degradation in network performance, or for geographically remote locations. To use this method you specify the export location on the source cloud, and the import location on the target cloud. When you enable protection for a virtual machine, the virtual hard disk is copied to the specified export location. You send it to the target site, and copy it to the import location. The system copies the imported information to the replica virtual machines. For a complete list of offline replication prerequisites, see [Step 3: Configure protection settings for VMM clouds][Step 3: Configure protection settings for VMM clouds] in the Deployment Guide.

13. Select **Delete Replica Virtual Machine** to specify that the replica virtual machine should be deleted if you stop protecting the virtual machine by selecting the **Delete protection for the virtual machine** option on the Virtual Machines tab of the cloud properties. With this setting enabled, when you disable protection the virtual machine is removed from Azure Site Recovery, the Site Recovery settings for the virtual machine are removed in the VMM console, and the replica is deleted.
    ![Configure protection settings][2]

After you save the settings a job will be created and can be monitored on the **Jobs** tab. All Hyper-V host servers in the VMM source cloud will be configured for replication. Cloud settings can be modified on the **Configure** tab. If you want to modify the target location or target cloud you must remove the cloud configuration, and then reconfigure the cloud.

## <span id="networkmapping"></span></a>Step 5: Configure network mapping

This tutorial describes the simplest path to deploy Azure Site Recovery in a test environment. If you do want to configure network mapping as part of this tutorial, read [Prepare for network mapping][Prepare for network mapping] in the Planning Guide. To configure mapping follow the steps to [Configure network mapping][Configure network mapping] in the deployment guide.

## <span id="storagemapping"></span></a>Step 6: Configure storage mapping

This tutorial describes the simplest path to deploy Azure Site Recovery in a test environment. If you do want to configure storage mapping as part of this tutorial, follow the steps to [Configure storage mapping][Configure storage mapping] in the deployment guide.

## <span id="enablevirtual"></span></a>Step 7: Enable virtual machine protection

After servers, clouds, and networks are configured correctly, you can enable protection for virtual machines in the cloud.

1.  On the **Virtual Machines** tab in the cloud in which the virtual machine is located, click **Enable protection** and then select **Add virtual machines**.
2.  From the list of virtual machines in the cloud, select the one you want to protect.

![Enable virtual machine protection][Enable virtual machine protection]

Track progress of the Enable Protection action in the \*\*Jobs\*\* tab, including the initial replication. After the Finalize Protection job runs the virtual machine is ready for failover. After protection is enabled and virtual machines are replicated, you’ll be able to view them in Azure.

![Virtual machine protection job][Virtual machine protection job]

## <span id="recoveryplans"></span></a>Step 8: Test the deployment

To test your deployment you can run a test failover for a single virtual machine, or create a recovery plan consisting of multiple virtual machines and run a test failover for the plan. Test failover simulates your failover and recovery mechanism in an isolated network.

-   For instructions on creating a recovery plan see [Create and customize recovery plans: On-Premises to Azure][Create and customize recovery plans: On-Premises to Azure].
-   For instructions on running a test failover see [Test an on-premises to on-premises deployment][Test an on-premises to on-premises deployment].

### <span id="runtest"></span></a>Monitor activity

You can use the **Jobs** tab and **Dashboard** to view and monitor the main jobs performed by the Azure Site Recovery vault, including configuring protection for a cloud, enabling and disabling protection for a virtual machine, running a failover (planned, unplanned, or test), and committing an unplanned failover.

From the **Jobs** tab you view jobs, drill down into job details and errors, run job queries to retrieve jobs that match specific criteria, export jobs to Excel, and restart failed jobs.

From the **Dashboard** you can download the latest versions of Provider and Agent installation files, get configuration information for the vault, see the number of virtual machines that have protection managed by the vault, see recent jobs, manage the vault certificate, and resynchronize virtual machines.

For more information about interacting with jobs and the dashboard, see the [Operations and Monitoring Guide][Operations and Monitoring Guide].

## <span id="next"></span></a>Next steps

-   To plan and deploy Azure Site Recovery in a full production environment, see [Planning Guide for Azure Site Recovery][Plan for Azure Site Recovery Deployment] and [Deployment Guide for Azure Site Recovery][Deploy Azure Site Recovery: On-Premises to On-Premises Protection].
-   For questions, visit the [Azure Recovery Services Forum][Azure Recovery Services Forum].

</div>

  [Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Getting Started with Azure Site Recovery: On-Premises to Azure Protection]: http://go.microsoft.com/fwlink/?LinkId=398764
  [Plan for Azure Site Recovery Deployment]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Deploy Azure Site Recovery: On-Premises to On-Premises Protection]: http://go.microsoft.com/fwlink/?LinkId=321295
  [Azure Site Recovery: Common Error Scenarios and Resolutions]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Azure Recovery Services Forum]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Azure free trial]: http://aka.ms/try-azure
  [Azure Site Recovery Manager Pricing Details]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Step 1: Create a vault]: #vault
  [Step 2: Install the Provider application]: #download
  [Step 3: Configure cloud protection]: #clouds
  [Step 6: Configure storage mapping]: #storagemapping
  [Step 7: Enable protection for virtual machines]: #enablevirtual
  [Step 8: Configure and run recovery plans]: #recovery%20plans
  [Management Portal]: https://manage.windowsazure.com
  [Azure Site Recovery Pricing Details]: http://go.microsoft.com/fwlink/?LinkId=389880
  [New Vault]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Quick Start Icon]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Registration key]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [Prerequisites]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft Updates]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Internet Settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Server registration]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Published Cloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Configure protection settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [Step 3: Configure protection settings for VMM clouds]: http://go.microsoft.com/fwlink/?LinkId=323469
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [Prepare for network mapping]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Configure network mapping]: http://go.microsoft.com/fwlink/?LinkId=402534
  [Configure storage mapping]: http://go.microsoft.com/fwlink/?LinkId=402535
  [Enable virtual machine protection]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Virtual machine protection job]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Create and customize recovery plans: On-Premises to Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Test an on-premises to on-premises deployment]: http://go.microsoft.com/fwlink/?LinkId=511493
  [Operations and Monitoring Guide]: http://go.microsoft.com/fwlink/?LinkId=398534

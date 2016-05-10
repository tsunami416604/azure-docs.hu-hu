<properties urlDisplayName="Install SQL Server" pageTitle="Provision a SQL Server virtual machine in Azure " metaKeywords="Azure tutorial creating SQL Server, SQL Server vm, configuring SQL Server" description="A tutorial that teaches you how to create and configure a SQL Server virtual machine on Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Provisioning a SQL Server Virtual Machine on Azure" authors="selcint" solutions="" manager="jeffreyg" editor="tyson" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="selcint"></tags>

# Provisioning a SQL Server Virtual Machine on Azure

The Azure virtual machine gallery includes several images that contain Microsoft SQL Server. You can select one of the virtual machine images from the gallery and with a few clicks you can provision the virtual machine to your Azure environment.

In this tutorial, you will:

-   [Connect to the Azure management portal and provision a virtual machine from the gallery][Connect to the Azure management portal and provision a virtual machine from the gallery]
-   [Open the virtual machine using Remote Desktop and complete setup][Open the virtual machine using Remote Desktop and complete setup]
-   [Complete configuration steps to connect to the virtual machine using SQL Server Management Studio on another computer][Complete configuration steps to connect to the virtual machine using SQL Server Management Studio on another computer]
-   [Next steps][Next steps]

## <span id="Provision"></span>Connect to the Azure management portal and provision a virtual machine from the gallery</a>

1.  Log in to the [Azure Management Portal][Azure Management Portal] using your account. If you do not have an Azure account, visit [Azure free trial][Azure free trial].

2.  On the Azure Management Portal, at the bottom left of the web page, click **+NEW**, click **COMPUTE**, click **VIRTUAL MACHINE**, and then click **FROM GALLERY**.

3.  On the **Create a Virtual Machine** page, select a virtual machine image containing SQL Server, and then click the next arrow at the bottom right of the page. For the most up-to-date information on the supported SQL Server images on Azure, see [Getting Started with SQL Server in Azure Virtual Machines][Getting Started with SQL Server in Azure Virtual Machines] topic in the [SQL Server in Azure Virtual Machines][SQL Server in Azure Virtual Machines] documentation set.

    > [WACOM.NOTE] If you have a virtual machine created by using the platform image SQL Server Evaluation edition, you cannot upgrade it to a per-minute paid edition image in the gallery. You can choose one of the following two options:

    > -   You can create a new virtual machine by using the per-minute paid SQL Server edition from the gallery and migrate your database files to this new virtual machine by following the steps at [How to migrate SQL Server database files and schema between virtual machines in Azure using data disks][How to migrate SQL Server database files and schema between virtual machines in Azure using data disks]. **Or**,

    > -   You can upgrade an existing instance of SQL Server Evaluation edition to a different edition of SQL Server under the [License Mobility through Software Assurance on Azure][License Mobility through Software Assurance on Azure] agreement by following the steps at [Upgrade to a Different Edition of SQL Server 2014][Upgrade to a Different Edition of SQL Server 2014]. For information on how to purchase the licensed copy of SQL Server, see [How to Buy SQL Server][How to Buy SQL Server].

4.  On the first **Virtual Machine Configuration** page, provide the following information:

    -   Provide a **VIRTUAL MACHINE NAME**.
    -   In the **NEW USER NAME** box, type unique user name for the VM local administrator account.
    -   In the **NEW PASSWORD** box, type a strong password. For more information, see [Strong Passwords][Strong Passwords].
    -   In the **CONFIRM PASSWORD** box, retype the password.
    -   Select the appropriate **SIZE** from the drop down list.

    > [WACOM.NOTE] The size of the virtual machine is specified during provisioning:
    > A2 is the smallest size recommended for production workloads.
    > The minimum recommended size for a virtual machine is A3 when using SQL Server Enterprise Edition.
    > Select A3 or higher when using SQL Server Enterprise Edition.
    > Select A4 when using SQL Server 2012 or 2014 Enterprise Optimized for Transactional Workloads images.
    > Select A7 when using SQL Server 2012 or 2014 Enterprise Optimized for Data Warehousing Workloads images.
    > The size selected limits the number of data disks you can configure. For most up-to-date information on available virtual machine sizes and the number of data disks that you can attach to a virtual machine, see [Virtual Machine Sizes for Azure][Virtual Machine Sizes for Azure].

    Click the next arrow on the bottom right to continue.

    ![VM Configuration][VM Configuration]

5.  On the second **Virtual machine configuration** page, configure resources for networking, storage, and availability:

    -   In the **Cloud Service** box, choose **Create a new cloud service**.
    -   In the **Cloud Service DNS Name** box, provide the first portion of a DNS name of your choice, so that it completes a name in the format **TESTNAME.cloudapp.net**
    -   In the **REGION/AFFINITY GROUP/VIRTUAL NETWORK** box, select a region where this virtual image will be hosted.
    -   In the **Storage Account**, select an existing storage account or select an automatically generated one.
    -   In the **AVAILABILITY SET** box, select **(none)**.
    -   Read and accept the legal terms.

6.  Click the next arrow to continue.

7.  Click the check mark in the bottom right corner to continue.

8.  Wait while Azure prepares your virtual machine. Expect the virtual machine status to proceed through:

    -   Starting (Provisioning)
    -   Stopped
    -   Starting (Provisioning)
    -   Running (Provisioning)
    -   Running

## <span id="RemoteDesktop"></span>Open the virtual machine using Remote Desktop and complete setup</a>

1.  When provisioning completes, click on the name of your virtual machine to go to the DASHBOARD page. At the bottom of the page, click **Connect**.

2.  Choose to open the rpd file using the Windows Remote Desktop program (`%windir%\system32\mstsc.exe`).

3.  At the **Windows Security** dialog box, provide the password for the local administrator account that you specified in an earlier step. (You might be asked to verify the credentials of the virtual machine.)

4.  The first time you log on to this virtual machine, several processes may need to complete, including setup of your desktop, Windows updates, and completion of the Windows initial configuration tasks (sysprep). After Windows sysprep completes, SQL Server setup completes configuration tasks. These tasks make cause a short delay while they complete. `SELECT @@SERVERNAME` may not return the correct name until SQL Server setup completes.

Once you are connected to the virtual machine with Windows Remote Desktop, the virtual machine works much like any other computer. Connect to the default instance of SQL Server with SQL Server Management Studio (running on the virtual machine) in the normal way.

## <span id="SSMS"></span>Complete Configuration steps to connect to the virtual machine Using SQL Server Management Studio on another computer</a>

Before you can connect to the instance of SQL Server from the internet, you must complete the following tasks as described in the sections that follow:

-   [Create a TCP endpoint for the virtual machine][Create a TCP endpoint for the virtual machine]
-   [Open TCP ports in the Windows firewall][Open TCP ports in the Windows firewall]
-   [Configure SQL Server to listen on the TCP protocol][Configure SQL Server to listen on the TCP protocol]
-   [Configure SQL Server for mixed mode authentication][Configure SQL Server for mixed mode authentication]
-   [Create SQL Server authentication logins][Create SQL Server authentication logins]
-   [Determine the DNS name of the virtual machine][Determine the DNS name of the virtual machine]
-   [Connect to the Database Engine from another computer][Connect to the Database Engine from another computer]
-   [Connecting to the Database Engine from your application] (\#cdea)

The connection path is summarized by the following diagram:

![Connecting to a SQL Server virtual machine][Connecting to a SQL Server virtual machine]

### <span id="Endpoint"></span>Create a TCP endpoint for the virtual machine</a>

The virtual machine must have an endpoint to listen for incoming TCP communication. This Azure configuration step, directs incoming TCP port traffic to a TCP port that is accessible to the virtual machine.

1.  On the Azure Management Portal, click on **VIRTUAL MACHINES**.

2.  Click on your newly created virtual machine. Information about your virtual machine is presented.

3.  Near the top of the page, select the **ENDPOINTS** page, and then at the bottom of the page, click **ADD**.

4.  On the **Add an Endpoint to a Virtual Machine** page, click **Add a Stand-alone Endpoint**, and then click the Next arrow to continue.

5.  On the **Specify the details of the endpoint** page, provide the following information.

    -   In the **NAME** box, provide a name for the endpoint.
    -   In the **PROTOCOL** box, select **TCP**. You may type SQL Server's default listening port **1433** in the **Private Port** box. Similarly, you may type **57500** in the **PUBLIC PORT** box. Note that many organizations select different port numbers to avoid malicious security attacks.

6.  Click the check mark to continue. The endpoint is created.

### <span id="FW"></span>Open TCP ports in the Windows firewall for the default instance of the Database Engine</a>

1.  Connect to the virtual machine via Windows Remote Desktop. Once logged in, on the Start menu, click **Run**, type **WF.msc**, and then click **OK**.

    ![Start the Firewall Program][Start the Firewall Program]

2.  In the **Windows Firewall with Advanced Security**, in the left pane, right-click **Inbound Rules**, and then click **New Rule** in the action pane.

    ![New Rule][New Rule]

3.  In the **Rule Type** dialog box, select **Port**, and then click **Next**.

4.  In the **Protocol and Ports** dialog box, select **TCP**. Select **Specific local ports**, and then type the port number of the instance of the Database Engine (**1433** for the default instance or your choice for the private port in the endpoint step).

    ![TCP Port 1433][TCP Port 1433]

5.  Click **Next**.

6.  In the **Action** dialog box, select **Allow the connection**, and then click **Next**.

    **Security Note:** Selecting **Allow the connection if it is secure** can provide additional security. Select this option if you want to configure additional security options in your environment.

    ![Allow Connections][Allow Connections]

7.  In the **Profile** dialog box, select **Public**, and then click **Next**.

    **Security Note:** Selecting **Public** allows access over the internet. Whenever possible, select a more restrictive profile.

    ![Public Profile][Public Profile]

8.  In the **Name** dialog box, type a name and description for this rule, and then click **Finish**.

    ![Rule Name][Rule Name]

Open additional ports for other components as needed. For more information, see [Configuring the Windows Firewall to Allow SQL Server Access][Configuring the Windows Firewall to Allow SQL Server Access].

### <span id="TCP"></span>Configure SQL Server to listen on the TCP protocol</a>

1.  While connected to the virtual machine by using Remote Desktop, on the Start menu, click **All Programs**, click **Microsoft SQL Server** *version*, click **Configuration Tools**, and then click **SQL Server Configuration Manager**.

    ![Open SSCM][Open SSCM]

2.  In SQL Server Configuration Manager, in the console pane, expand **SQL Server Network Configuration**.

3.  In the console pane, click **Protocols for *instance name***. (The default instance is **Protocols for MSSQLSERVER**.)

4.  In the details pane, right-click TCP, it should be Enabled for the gallery images by default. For your custom images, click **Enable** (if its status is Disabled.)

    ![Enable TCP][Enable TCP]

5.  In the console pane, click **SQL Server Services**. (Restarting the Database Engine can be postponed until completion of the next step.)

6.  In the details pane, right-click **SQL Server (*instance name*)** (the default instance is **SQL Server (MSSQLSERVER)**), and then click **Restart**, to stop and restart the instance of SQL Server.

    ![Restart Database Engine][Restart Database Engine]

7.  Close SQL Server Configuration Manager.

For more information about enabling protocols for the SQL Server Database Engine, see [Enable or Disable a Server Network Protocol][Enable or Disable a Server Network Protocol].

### <span id="Mixed"></span>Configure SQL Server for mixed mode authentication</a>

The SQL Server Database Engine cannot use Windows Authentication without domain environment. To connect to the Database Engine from another computer, configure SQL Server for mixed mode authentication. Mixed mode authentication allows both SQL Server Authentication and Windows Authentication. (Configuring mixed mode authentication might not be necessary if you have configured an Azure Virtual Network. For more information, see [Connectivity Considerations for SQL Server in Azure Virtual Machines][Connectivity Considerations for SQL Server in Azure Virtual Machines] topic in the [SQL Server in Azure Virtual Machines][1] documentation set.

1.  While connected to the virtual machine by using Remote Desktop, on the Start menu, click **All Programs**, click **Microsoft SQL Server *version***, and then click **SQL Server Management Studio**.

    ![Start SSMS][Start SSMS]

    The first time you open Management Studio it must create the users Management Studio environment. This may take a few moments.

2.  When opening, Management Studio presents the **Connect to Server** dialog box. In the **Server name** box, type the name of the virtual machine to connect to the Database Engine with the Object Explorer. (Instead of the virtual machine name you can also use **(local)** or a single period as the **Server name**. Select **Windows Authentication**, and leave ***your\_VM\_name*\\your\_local\_administrator** in the **User name** box. Click **Connect**.

    ![Connect to Server][Connect to Server]

3.  In SQL Server Management Studio Object Explorer, right-click the name of the instance of SQL Server (the virtual machine name), and then click **Properties**.

    ![Server Properties][Server Properties]

4.  On the **Security** page, under **Server authentication**, select **SQL Server and Windows Authentication mode**, and then click **OK**.

    ![Select Authentication Mode][Select Authentication Mode]

5.  In the SQL Server Management Studio dialog box, click **OK** to acknowledge the requirement to restart SQL Server.

6.  In Object Explorer, right-click your server, and then click **Restart**. (If SQL Server Agent is running, it must also be restarted.)

    ![Restart][Restart]

7.  In the SQL Server Management Studio dialog box, click **Yes** to agree that you want to restart SQL Server.

### <span id="Logins"></span>Create SQL Server authentication logins</a>

To connect to the Database Engine from another computer, you must create at least one SQL Server authentication login.

1.  In SQL Server Management Studio Object Explorer, expand the folder of the server instance in which you want to create the new login.

2.  Right-click the **Security** folder, point to **New**, and select **Login...**.

    ![New Login][New Login]

3.  In the **Login - New** dialog box, on the **General** page, enter the name of the new user in the **Login name** box.

4.  Select **SQL Server authentication**.

5.  In the **Password** box, enter a password for the new user. Enter that password again into the **Confirm Password** box.

6.  To enforce password policy options for complexity and enforcement, select **Enforce password policy** (recommended). This is a default option when SQL Server authentication is selected.

7.  To enforce password policy options for expiration, select **Enforce password expiration** (recommended). Enforce password policy must be selected to enable this checkbox. This is a default option when SQL Server authentication is selected.

8.  To force the user to create a new password after the first time the login is used, select **User must change password at next login** (Recommended if this login is for someone else to use. If the login is for your own use, do not select this option.) Enforce password expiration must be selected to enable this checkbox. This is a default option when SQL Server authentication is selected.

9.  From the **Default database** list, select a default database for the login. **master** is the default for this option. If you have not yet created a user database, leave this set to **master**.

10. In the **Default language** list, leave **default** as the value.

    ![Login Properties][Login Properties]

11. If this is the first login you are creating, you may want to designate this login as a SQL Server administrator. If so, on the **Server Roles** page, check **sysadmin**.

    **Security Note:** Members of the sysadmin fixed server role have complete control of the Database Engine. You should carefully restrict membership in this role.

    ![sysadmin][sysadmin]

12. Click OK.

For more information about SQL Server logins, see [Create a Login][Create a Login].

### <span id="DNS"></span>Determine the DNS name of the virtual machine</a>

To connect to the SQL Server Database Engine from another computer, you must know the Domain Name System (DNS) name of the virtual machine. (This is the name the internet uses to identify the virtual machine. You can use the IP address, but the IP address might change when Azure moves resources for redundancy or maintenance. The DNS name will be stable because it can be redirected to a new IP address.)

1.  In the Azure Management Portal (or from the previous step), select **VIRTUAL MACHINES**.

2.  On the **VIRTUAL MACHINE INSTANCES** page, in the **DNS NAME** column, find and copy the DNS name for the virtual machine which appears preceded by **<http://>**. (The user interface might not display the entire name, but you can right-click on it, and select copy.)

### <span id="cde"></span>Connect to the Database Engine from another computer</a>

1.  On a computer connected to the internet, open SQL Server Management Studio.

2.  In the **Connect to Server** or **Connect to Database Engine** dialog box, in the **Server name** box, enter the DNS name of the virtual machine (determined in the previous task) and a public endpoint port number in the format of *DNSName,portnumber* such as **tutorialtestVM.cloudapp.net,57500**.

3.  In the **Authentication** box, select **SQL Server Authentication**.

4.  In the **Login** box, type the name of a login that you created in an earlier task.

5.  In the **Password** box, type the password of the login that you create in an earlier task.

6.  Click **Connect**.

    ![Connect using SSMS][Connect using SSMS]

### <span id="cdea"></span> Connecting to the Database Engine from your application</a>

If you can connect to an instance of SQL Server running on an Azure virtual machine by using Management Studio, you should be able to connect by using a connection string similar to the following.

    connectionString="Server=<DNS_Name>;Integrated Security=false;User ID=<login_name>;Password=<your_password>;"providerName="System.Data.SqlClient"

For more information, see [How to Troubleshoot Connecting to the SQL Server Database Engine][How to Troubleshoot Connecting to the SQL Server Database Engine].

## <span id="Optional"></span>Next Steps</a>

You've seen how to create and configure a SQL Server on an Azure virtual machine using the platform image. When using SQL Server in Azure Virtual Machines, we recommend that you follow the detailed guidance given in the [SQL Server in Azure Virtual Machines][SQL Server in Azure Virtual Machines] documentation in the library. This documentation set includes a series of articles and tutorials that provide detailed guidance. The series includes the following sections:

[SQL Server in Azure Virtual Machines][SQL Server in Azure Virtual Machines]

[Getting Started with SQL Server in Azure Virtual Machines][Getting Started with SQL Server in Azure Virtual Machines]

[Getting Ready to Migrate to SQL Server in Azure Virtual Machines][Getting Ready to Migrate to SQL Server in Azure Virtual Machines]

-   How to migrate SQL Server database files and schema between virtual machines in Azure using data disks

[SQL Server Deployment in Azure Virtual Machines][SQL Server Deployment in Azure Virtual Machines]

-   How to copy SQL Server data and setup files in a data disk from on-premises to Azure using CSUpload
-   How to create a base virtual machine on-premises using Hyper-V
-   How to create a SQL Server virtual machine in Azure using the existing on-premises SQL Server disk
-   How to create a SQL Server virtual machine in Azure using the existing on-premises SQL Server virtual machine
-   How to use PowerShell to set up a SQL Server virtual machine in Azure
-   How to Use Attached Data Disk to Store Database Files

[Connectivity Considerations for SQL Server in Azure Virtual Machines][2]

-   Tutorial: Connect to SQL Server in the same cloud service
-   Tutorial: Connect to SQL Server in a different cloud service
-   Tutorial: Connect ASP.NET application to SQL Server in Azure via Virtual Network

[Performance Considerations for SQL Server in Azure Virtual Machines][Performance Considerations for SQL Server in Azure Virtual Machines]

[Security Considerations for SQL Server in Azure Virtual Machines][Security Considerations for SQL Server in Azure Virtual Machines]

[Troubleshooting and Monitoring for SQL Server in Azure Virtual Machines][Troubleshooting and Monitoring for SQL Server in Azure Virtual Machines]

[High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines][High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines]

-   Tutorial: AlwaysOn Availability Groups in Azure (GUI)
-   Tutorial: AlwaysOn Availability Groups in Azure (PowerShell)
-   Tutorial: Listener Configuration for AlwaysOn Availability Groups
-   Tutorial: Add Azure Replica Wizard
-   Tutorial: Database Mirroring for Disaster Recovery in Azure
-   Tutorial: Database Mirroring for Disaster Recovery in Hybrid IT
-   Tutorial: Database Mirroring for High Availability in Azure
-   Tutorial: Log Shipping for Disaster Recovery in Hybrid IT
-   Troubleshooting Availability Group Listener in Azure

[Backup and Restore for SQL Server in Azure Virtual Machines][Backup and Restore for SQL Server in Azure Virtual Machines]

[SQL Server Business Intelligence in Azure Virtual Machines][SQL Server Business Intelligence in Azure Virtual Machines]

-   Use PowerShell to Create an Azure VM With SQL Server BI and SharePoint 2010
-   Use PowerShell to Create an Azure VM With SQL Server BI and SharePoint 2013
-   Use PowerShell to Create an Azure VM With a Native Mode Report Server

[SQL Server Data Warehousing in Azure Virtual Machines][SQL Server Data Warehousing in Azure Virtual Machines]

[Technical Articles for SQL Server in Azure Virtual Machines][Technical Articles for SQL Server in Azure Virtual Machines]

-   [White paper: Understand Azure SQL Database and SQL Server in Azure Virtual Machines][White paper: Understand Azure SQL Database and SQL Server in Azure Virtual Machines]

-   [White paper: Application Patterns and Development Strategies for SQL Server in Azure Virtual Machines][White paper: Application Patterns and Development Strategies for SQL Server in Azure Virtual Machines]

-   [White paper: Deploy SQL Server Business Intelligence in Azure Virtual Machines][White paper: Deploy SQL Server Business Intelligence in Azure Virtual Machines]

-   [White paper: Performance Guidance for SQL Server in Azure Virtual Machines][White paper: Performance Guidance for SQL Server in Azure Virtual Machines]

-   [White paper: Reporting Services report viewer control and Microsoft Azure virtual machine based report servers][White paper: Reporting Services report viewer control and Microsoft Azure virtual machine based report servers]

  [Connect to the Azure management portal and provision a virtual machine from the gallery]: #Provision
  [Open the virtual machine using Remote Desktop and complete setup]: #RemoteDesktop
  [Complete configuration steps to connect to the virtual machine using SQL Server Management Studio on another computer]: #SSMS
  [Next steps]: #Optional
  [Azure Management Portal]: http://manage.windowsazure.com
  [Azure free trial]: http://www.windowsazure.com/en-us/pricing/free-trial/
  [Getting Started with SQL Server in Azure Virtual Machines]: http://go.microsoft.com/fwlink/p/?LinkId=294720
  [SQL Server in Azure Virtual Machines]: http://go.microsoft.com/fwlink/p/?LinkId=294719
  [How to migrate SQL Server database files and schema between virtual machines in Azure using data disks]: http://go.microsoft.com/fwlink/p/?LinkId=294738
  [License Mobility through Software Assurance on Azure]: http://www.windowsazure.com/en-us/pricing/license-mobility/
  [Upgrade to a Different Edition of SQL Server 2014]: http://go.microsoft.com/fwlink/?LinkId=396915
  [How to Buy SQL Server]: http://www.microsoft.com/en-us/sqlserver/get-sql-server/how-to-buy.aspx
  [Strong Passwords]: http://msdn.microsoft.com/library/ms161962.aspx
  [Virtual Machine Sizes for Azure]: http://msdn.microsoft.com/en-us/library/azure/dn197896.aspx
  [VM Configuration]: ./media/virtual-machines-provision-sql-server/4VM-Config.png
  [Create a TCP endpoint for the virtual machine]: #Endpoint
  [Open TCP ports in the Windows firewall]: #FW
  [Configure SQL Server to listen on the TCP protocol]: #TCP
  [Configure SQL Server for mixed mode authentication]: #Mixed
  [Create SQL Server authentication logins]: #Logins
  [Determine the DNS name of the virtual machine]: #DNS
  [Connect to the Database Engine from another computer]: #cde
  [Connecting to a SQL Server virtual machine]: ./media/virtual-machines-provision-sql-server/SQLVMConnectionsOnAzure.GIF
  [Start the Firewall Program]: ./media/virtual-machines-provision-sql-server/12Open-WF.png
  [New Rule]: ./media/virtual-machines-provision-sql-server/13New-FW-Rule.png
  [TCP Port 1433]: ./media/virtual-machines-provision-sql-server/14Port-1433.png
  [Allow Connections]: ./media/virtual-machines-provision-sql-server/15Allow-Connection.png
  [Public Profile]: ./media/virtual-machines-provision-sql-server/16Public-Profile.png
  [Rule Name]: ./media/virtual-machines-provision-sql-server/17Rule-Name.png
  [Configuring the Windows Firewall to Allow SQL Server Access]: http://msdn.microsoft.com/en-us/library/cc646023.aspx
  [Open SSCM]: ./media/virtual-machines-provision-sql-server/9Click-SSCM.png
  [Enable TCP]: ./media/virtual-machines-provision-sql-server/10Enable-TCP.png
  [Restart Database Engine]: ./media/virtual-machines-provision-sql-server/11Restart.png
  [Enable or Disable a Server Network Protocol]: http://msdn.microsoft.com/en-us/library/ms191294.aspx
  [Connectivity Considerations for SQL Server in Azure Virtual Machines]: http://go.microsoft.com/fwlink/?LinkId=294723
  [1]: http://go.microsoft.com/fwlink/?LinkId=294719
  [Start SSMS]: ./media/virtual-machines-provision-sql-server/18Start-SSMS.png
  [Connect to Server]: ./media/virtual-machines-provision-sql-server/19Connect-to-Server.png
  [Server Properties]: ./media/virtual-machines-provision-sql-server/20Server-Properties.png
  [Select Authentication Mode]: ./media/virtual-machines-provision-sql-server/21Mixed-Mode.png
  [Restart]: ./media/virtual-machines-provision-sql-server/22Restart2.png
  [New Login]: ./media/virtual-machines-provision-sql-server/23New-Login.png
  [Login Properties]: ./media/virtual-machines-provision-sql-server/24Test-Login.png
  [sysadmin]: ./media/virtual-machines-provision-sql-server/25sysadmin.png
  [Create a Login]: http://msdn.microsoft.com/en-us/library/aa337562.aspx
  [Connect using SSMS]: ./media/virtual-machines-provision-sql-server/33Connect-SSMS.png
  [How to Troubleshoot Connecting to the SQL Server Database Engine]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx
  [Getting Ready to Migrate to SQL Server in Azure Virtual Machines]: http://go.microsoft.com/fwlink/p/?LinkId=294721
  [SQL Server Deployment in Azure Virtual Machines]: http://go.microsoft.com/fwlink/p/?LinkId=294722
  [2]: http://go.microsoft.com/fwlink/p/?LinkId=294723
  [Performance Considerations for SQL Server in Azure Virtual Machines]: http://go.microsoft.com/fwlink/?LinkId=294724
  [Security Considerations for SQL Server in Azure Virtual Machines]: http://go.microsoft.com/fwlink/p/?LinkId=294725
  [Troubleshooting and Monitoring for SQL Server in Azure Virtual Machines]: http://go.microsoft.com/fwlink/p/?LinkId=294726
  [High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines]: http://go.microsoft.com/fwlink/p/?LinkId=294727
  [Backup and Restore for SQL Server in Azure Virtual Machines]: http://go.microsoft.com/fwlink/p/?LinkId=294728
  [SQL Server Business Intelligence in Azure Virtual Machines]: http://go.microsoft.com/fwlink/p/?LinkId=294729
  [SQL Server Data Warehousing in Azure Virtual Machines]: http://msdn.microsoft.com/library/windowsazure/dn387396.aspx
  [Technical Articles for SQL Server in Azure Virtual Machines]: http://msdn.microsoft.com/library/azure/dn248435.aspx
  [White paper: Understand Azure SQL Database and SQL Server in Azure Virtual Machines]: http://azure.microsoft.com/en-us/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas/
  [White paper: Application Patterns and Development Strategies for SQL Server in Azure Virtual Machines]: http://msdn.microsoft.com/library/azure/dn574746.aspx
  [White paper: Deploy SQL Server Business Intelligence in Azure Virtual Machines]: http://msdn.microsoft.com/library/windowsazure/dn321998.aspx
  [White paper: Performance Guidance for SQL Server in Azure Virtual Machines]: http://msdn.microsoft.com/library/windowsazure/dn248436.aspx
  [White paper: Reporting Services report viewer control and Microsoft Azure virtual machine based report servers]: http://msdn.microsoft.com/library/azure/dn753698.aspx

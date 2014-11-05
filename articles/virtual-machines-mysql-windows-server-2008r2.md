<properties urlDisplayName="Install MySQL" pageTitle="Create a virtual machine running MySQL in Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/15/2013" ms.author="kathydav"></tags>

# Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure

[MySQL][MySQL] is a popular open source, SQL database. Using the [Azure Management Portal][Azure Management Portal], you can create a virtual machine running Windows Server 2008 R2 from the Image Gallery. You can then install and configure a MySQL database on the virtual machine.

In this tutorial, you will learn how to:

-   Use the Management Portal to create a virtual machine running Windows Server 2008 R2.

-   Install and run MySQL Community Server on the virtual machine.

## Create a virtual machine running Windows Server 2008 R2

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

## Attach a data disk

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

## Install and run MySQL Community Server on the virtual machine

Follow these steps to install, configure, and run MySQL Community Server:

1.  After you've connected to the virtual machine using Remote Desktop, open **Internet Explorer** from the **Start** menu.

2.  Select the **Tools** button in the upper right corner. In **Internet Options**, select the **Security** tab, and then select the **Trusted Sites** icon, and finally click the **Sites** button. Add *<http://>\*.mysql.com* to the list of trusted sites.

3.  Go to [Download MySQL Community Server][Download MySQL Community Server].

4.  Select **Microsoft Windows** in the **Platform** drop down menu and click **Select**.

5.  Find the most recent release of **Windows (x86, 64-bit), MSI Installer** and click **Download**.

6.  Select **No thanks, just start my download!** (or, register for an account). If prompted, select a mirror site to download the MySQL installer and save the installer to the desktop.

7.  Double-click the installer file on the desktop to begin installation.

8.  Click **Next**.

    ![MySQL Setup][MySQL Setup]

9.  Accept the license agreement and click **Next**.

    ![MySQL Setup][1]

10. Click **Typical** to install common features.

    ![MySQL Setup][2]

11. Click **Install**.

    ![MySQL Setup][3]

12. Start the MySQL Configuration Wizard and click **Next**.

    ![Configure MySQL][Configure MySQL]

13. Select **Detailed Configuration** and click Next.

    ![Configure MySQL][4]

14. Select **Server Machine** if you plan to run MySQL along with other applications on the server, or the select option that best fits your needs. Click **Next**.

    ![Configure MySQL][5]

15. Select **Multifunctional Database**, or the select option that best fits your needs. Click **Next**.

    ![Configure MySQL][6]

16. Select the data drive you attached in the steps above.

    ![Configure MySQL][7]

17. Select **Decision Support (DSS)/OLAP**, or the select option that best fits your needs. Click **Next**.

    ![Configure MySQL][8]

18. Select **Enable TCP/IP Networking** and **Add firewall exception for this port** (this will create an inbound rule in Windows Firewall named **MySQL Server**).

    ![Configure MySQL][9]

19. Select **Best Support For Multilingualism** if you need to store text in many different languages, or the select option that best fits your needs. Click **Next**.

    ![Configure MySQL][10]

20. Select **Install As Windows Service** and **Launch the MySQL Server automatically**. Also select **Include Bin Directory in Windows PATH**. Click **Next**.

    ![Configure MySQL][11]

21. Enter the root password. Do not check **Enable root access from remote machines** or **Create An Anonymous Account**. Click **Next**.

    ![Configure MySQL][12]

22. Click **Execute** and wait for configuration to complete.

    ![Configure MySQL][13]

23. Click **Finish**.

    ![Configure MySQL][14]

24. Click **Start** and select **MySQL 5.x Command Line Client** to start the command line client.

25. Enter the root password at the prompt (which you set in a previous step) and you'll be presented with a prompt where you can issue SQL statements to interact with the database.

26. To create a new MySQL user, run the following at the **mysql\>** prompt:

        mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    Note, the semi-colons (;) at the end of the lines are crucial for ending the commands.

27. To create a database and grant the `mysqluser` user permissions on it, issue the following commands:

        mysql> CREATE DATABASE testdatabase;
        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    Note that database user names and passwords are only used by scripts connecting to the database. Database user account names do not necessarily represent actual user accounts on the computer.

28. To login from another computer, execute the following:

        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

    where `ip-address` is the IP address of the computer from which you will connect to MySQL.

29. To exit the MySQL command line client, issue the following command:

        quit

30. Once MySQL is installed you must configure an endpoint so that MySQL can be accessed remotely. Log in to the [Azure Management Portal][Azure Management Portal]. In the Azure portal, click **Virtual Machines**, then the name of your new virtual machine, then **Endpoints**, and then **Add Endpoint**.

    ![Endpoints][Endpoints]

31. Select **Add Endpoint** and click arrow to continue.

    ![Endpoints][15]

32. Add an endpoint with name "MySQL", protocol **TCP**, and both **Public** and **Private** ports set to "3306". Click the check mark. This will allow MySQL to be accessed remotely.

    ![Endpoints][16]

33. You can remotely connect to MySQL running on your virtual machine in Azure. From a local computer running MySQL, run the following command to log in as the **mysqluser** user you created in the steps above:

        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

    For example, using the virtual machine created in this tutorial, the command would be:

        mysql -u mysqluser -p -h testwinvm.cloudapp.net

## Summary

In this tutorial you learned how to create a Windows 2008 R2 virtual machine and remotely connect to it. You also learned how to install and configure MySQL on the virtual machine, create a database and a new MySQL user. For more information on MySQL, see the [MySQL Documentation][MySQL Documentation].

  [MySQL]: http://www.mysql.com
  [Azure Management Portal]: http://manage.windowsazure.com
  [create-and-configure-windows-server-2008-vm-in-portal]: ../includes/create-and-configure-windows-server-2008-vm-in-portal.md
  [attach-data-disk-windows-server-2008-vm-in-portal]: ../includes/attach-data-disk-windows-server-2008-vm-in-portal.md
  [Download MySQL Community Server]: http://www.mysql.com/downloads/mysql/
  [MySQL Setup]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png
  [1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png
  [2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png
  [3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png
  [Configure MySQL]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png
  [4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png
  [5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png
  [6]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png
  [7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
  [8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png
  [9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
  [10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
  [11]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
  [12]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png
  [13]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png
  [14]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png
  [Endpoints]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png
  [15]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png
  [16]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png
  [MySQL Documentation]: http://dev.mysql.com/doc/

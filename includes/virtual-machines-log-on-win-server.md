<properties linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Learn how to log on to a virtual machine running Windows Server 2008 R2 by using the Azure Management Portal." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

> [WACOM.NOTE] For requirements and troubleshooting tips, see [Connect to an Azure virtual machine with RDP or SSH][Connect to an Azure virtual machine with RDP or SSH].

1.  If you have not already done so, sign in to the [Azure Management Portal][Azure Management Portal].

2.  Click **Virtual Machines**, and then select the appropriate virtual machine.

3.  On the command bar, click **Connect**.

    ![Log on to the virtual machine][Log on to the virtual machine]

4.  Click **Open** to use the Remote Desktop Protocol file that was automatically created for the virtual machine.

5.  Click **Connect** to proceed with the connection process.

    ![Continue with connecting][Continue with connecting]

6.  Type the user name and password of the administrative account on the virtual machine, and then click **OK**.

7.  Click **Yes** to verify the identity of the virtual machine.

    ![Verify the identity of the machine][Verify the identity of the machine]

    You can now work with the virtual machine just as you would with any other server.

  [Connect to an Azure virtual machine with RDP or SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Azure Management Portal]: http://manage.windowsazure.com
  [Log on to the virtual machine]: ./media/virtual-machines-log-on-win-server/connectwindows.png
  [Continue with connecting]: ./media/virtual-machines-log-on-win-server/connectpublisher.png
  [Verify the identity of the machine]: ./media/virtual-machines-log-on-win-server/connectverify.png

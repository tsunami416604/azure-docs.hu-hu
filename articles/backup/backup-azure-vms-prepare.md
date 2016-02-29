<properties
    pageTitle="準備環境以備份 Azure 虛擬機器 | Microsoft Azure"
    description="確認備份 Azure 虛擬機器的環境已準備就緒"
    services="backup"
    documentationCenter=""
    authors="Jim-Parker"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/17/2015"
    ms.author="trinadhk; aashishr; jimpark; markgal"/>

# 準備環境以備份 Azure 虛擬機器
備份 Azure 虛擬機器 (VM) 之前，您需要完成下面準備環境的必要條件。 如果您已這麼做，您可以啟動 [備份您的 Vm](backup-azure-vms.md)。 否則，請繼續進行下列所有步驟，以確保您的環境準備就緒。


## 1.備份保存庫

![備份保存庫](./media/backup-azure-vms-prepare/step1.png)

若要開始備份 Azure 虛擬機器，首先您必須建立備份保存庫。 保存庫是一個實體，會儲存所有時間以來建立的所有備份和復原點。 保存庫也包含備份虛擬機器時將套用的備份原則。

下圖顯示各種 Azure 備份實體之間的關聯性:
![「Azure 備份」實體及其關係](./media/backup-azure-vms-prepare/vault-policy-vm.png)

若要建立備份保存庫：

1. 登入 [Azure 入口網站](http://manage.windowsazure.com/)。

2. 按一下 [ **新** > **Data Services** > **復原服務** > **備份保存庫** > **快速建立**。 如果您有多個與組織帳戶相關聯的訂用帳戶，請選擇與備份保存庫相關聯的正確訂用帳戶。 每個 Azure 訂用帳戶都可以擁有多個備份保存庫，用以組織受保護的虛擬機器。

3. 在 **名稱**, ，輸入易記名稱來識別保存庫。 每個訂用帳戶皆需為唯一名稱。

4. 在 **區域**, ，選取保存庫的地理區域。 保存庫必須與您想要保護的虛擬機器位於相同區域。 如果您的虛擬機器位於各個不同區域，請在每個區域都建立保存庫。 儲存備份資料時，不需要指定儲存體帳戶，備份保存庫和「Azure 備份」服務會自動處理此作業。

    ![建立備份保存庫](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. 按一下 [ **建立保存庫**。 要等備份保存庫建立好，可能需要一些時間。 監視位於入口網站底部的狀態通知。

    ![建立保存庫快顯通知](./media/backup-azure-vms-prepare/creating-vault.png)

6. 將會顯示一則確認已順利建立保存庫的訊息。 它會列於 **復原服務** 頁存成 **Active**。 建立保存庫之後，請務必立即選擇適當的儲存體備援選項。 深入了解 [設定備份保存庫中的儲存體備援選項](backup-configure-vault.md#azure-backup---storage-redundancy-options)。

    ![備份保存庫的清單](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. 按一下以移至備份保存庫 **快速入門** ] 頁面上，上面會顯示備份 Azure 虛擬機器的指示。

    ![「儀表板」頁面上的虛擬機器備份指示](./media/backup-azure-vms-prepare/vmbackup-instructions.png)



## 2.網路連線

![網路連線](./media/backup-azure-vms-prepare/step2.png)

備份擴充功能必須連線至 Azure 公用 IP 位址才能正常運作，因為它會傳送命令給「Azure 儲存體」端點 (HTTP URL) 以管理 VM 的快照。 若無適當的網際網路連線，這些來自 VM 的 HTTP 要求將會逾時，而備份作業將會失敗。

### NSG 的網路限制

如果您的部署具有適當的存取限制 (例如，透過網路安全性群組 (NSG))，您就必須採取額外的步驟來確保傳送至備份保存庫的備份流量不受影響。

有兩種方式可提供備份流量的路徑：

1. 白名單 [Azure 資料中心 IP 範圍](http://www.microsoft.com/en-us/download/details.aspx?id=41653)。
2. 部署 HTTP Proxy 來路由傳送流量。

管理能力、精確控制和成本之間必須有所取捨。

|選項|優點|缺點|
|------|----------|-------------|
|選項 1：將 IP 範圍列入允許清單| 沒有額外的成本。<br><br>開啟 NSG 中的存取，請使用 <i>設定 AzureNetworkSecurityRule</i> Cmdlet 來啟用診斷功能。 | 複雜而難以管理為受影響的 IP 範圍隨著時間改變。<br>提供完整的 Azure，並不只是儲存體的存取。|
|選項 2：HTTP Proxy| 更精確地控制在 proxy 中允許的儲存體 Url。<br>單一點的網際網路存取 Vm。<br>不會因為 Azure IP 位址變更。| 使用 Proxy 軟體執行 VM 時的額外成本。|

### 使用 HTTP Proxy 進行 VM 備份
備份 VM 時，會使用 HTTPS API 將快照管理命令從備份擴充功能傳送到 Azure 儲存體。 此流量必須透過 Proxy 從擴充功能路由傳送，因為只有 Proxy 會被設定為具有公用網際網路存取權。

>[AZURE.NOTE] 沒有推薦應該使用的 proxy 軟體。 請務必挑選與下面設定步驟相容的 Proxy。

在下面的範例中，必須將「應用程式 VM」設定為針對前往公用網際網路的所有 HTTP 流量使用 Proxy VM。 必須將 Proxy VM 設定為允許來自虛擬網路中 VM 的連入流量。 最後，NSG 和 (名為 *NSG 鎖定*) 需要新的安全性規則，允許從 Proxy VM 的傳出網際網路流量。

![包含 HTTP Proxy 部署圖表的 NSG](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

**A) 允許連出網路連線：**

1. 若為 Windows 電腦，請在提高權限的命令提示字元中執行下列命令：

    ```
    netsh winhttp set proxy http://<proxy IP>:<proxy port>
    ```

    這會設定一個整部機器的 Proxy 設定，並用於任何連出 HTTP/HTTPS 流量。

2. 針對 Linux 電腦，請將下面一行新增至 ```/etc/environment``` 檔案：

    ```
    http_proxy=http://<proxy IP>:<proxy port>
    ```

    將下列幾行新增至 ```/etc/waagent.conf``` 檔案：

    ```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

**B) Allow incoming connections on the proxy server:**

1. Open Windows Firewall on the proxy server. Right-click  **Inbound Rules** and click **New Rule...**.

    ![Open the Firewall](./media/backup-azure-vms-prepare/firewall-01.png)

    ![Create a new rule](./media/backup-azure-vms-prepare/firewall-02.png)
2. In the **New Inbound Rule Wizard**, choose the **Custom** option for the **Rule Type** and click **Next**. On the page to select the **Program**, choose **All Programs** and click **Next**.

3. On the **Protocol and Ports** page, use the inputs in the table below and click **Next**:

    ![Create a new rule](./media/backup-azure-vms-prepare/firewall-03.png)

| Input field | Value |
| --- | --- |
| Protocol type | TCP |
| Local port    | Select **Specific Ports** in the dropdown. In the text box, enter the ```<Proxy Port>``` that has been configured. |
| Remote port   | Select **All Ports** in the dropdown. |

For the rest of the wizard, click all the way to the end and give this rule a name.

**C) Add an exception rule to the NSG:**

In an Azure PowerShell command prompt, type out the following command:

```
Get AzureNetworkSecurityGroup-命名為 「 NSG 鎖定 」 |
組 AzureNetworkSecurityRule-命名為 「 允許 proxy 」-允許動作-TCP 通訊協定-輸入輸出-優先順序 200 SourceAddressPrefix"10.0.0.5/32"-SourcePortRange"*"-DestinationAddressPrefix 網際網路-DestinationPortRange"80-443"
```

This command adds an exception to the NSG, which allows TCP traffic from any port on 10.0.0.5 to any Internet address on port 80 (HTTP) or 443 (HTTPS). If you need to hit a specific port in the public Internet, make sure that you add that to the ```-DestinationPortRange``` as well.

*Ensure that you replace the names in the example with the details appropriate to your deployment.*

## 3. VM agent

![VM agent](./media/backup-azure-vms-prepare/step3.png)

Before you can back up the Azure virtual machine, you should ensure that the Azure VM agent is correctly installed on the virtual machine. Since the VM agent is an optional component at the time that the virtual machine is created, ensure that the check box for the VM agent is selected before the virtual machine is provisioned.

### Manual installation and update

The VM agent is already present in VMs that are created from the Azure gallery. However, virtual machines that are migrated from on-premises datacenters would not have the VM agent installed. For such VMs, the VM agent needs to be installed explicitly. Read more about [installing the VM agent on an existing VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

| **Operation** | **Windows** | **Linux** |
| --- | --- | --- |
| Installing the VM agent | <li>Download and install the [agent MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). You will need Administrator privileges to complete the installation. <li>[Update the VM property](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) to indicate that the agent is installed. | <li> Install the latest [Linux agent](https://github.com/Azure/WALinuxAgent) from GitHub. You will need Administrator privileges to complete the installation. <li> [Update the VM property](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) to indicate that the agent is installed. |
| Updating the VM agent | Updating the VM agent is as simple as reinstalling the [VM agent binaries](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Ensure that no backup operation is running while the VM agent is being updated. | Follow the instructions on [updating the Linux VM agent ](../virtual-machines-linux-update-agent.md). <br><br>Ensure that no backup operation is running while the VM agent is being updated. |
| Validating the VM agent installation | <li>Navigate to the *C:\WindowsAzure\Packages* folder in the Azure VM. <li>You should find the WaAppAgent.exe file present.<li> Right-click the file, go to **Properties**, and then select the **Details** tab. The Product Version field should be 2.6.1198.718 or higher. | - |


Learn about the [VM agent](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) and [how to install it](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### Backup extension

To back up the virtual machine, the Azure Backup service installs an extension to the VM agent. The Azure Backup service seamlessly upgrades and patches the backup extension without additional user intervention.

The backup extension is installed if the VM is running. A running VM also provides the greatest chance of getting an application-consistent recovery point. However, the Azure Backup service will continue to back up the VM--even if it is turned off, and the extension could not be installed (aka Offline VM). In this case, the recovery point will be *crash consistent* as discussed above.


## Limitations

- Backing up Azure Resource Manager-based (aka IaaS V2) virtual machines is not supported.
- Backing up virtual machines with more than 16 data disks is not supported.
- Backing up virtual machines using Premium storage is not supported.
- Backing up virtual machines with a reserved IP address and no defined endpoint is not supported.
- Replacing an existing virtual machine during restore is not supported. First delete the existing virtual machine and any associated disks, and then restore the data from backup.
- Cross-region backup and restore is not supported.
- Backing up virtual machines by using the Azure Backup service is supported in all public regions of Azure (see the [checklist](http://azure.microsoft.com/regions/#services) of supported regions). If the region that you are looking for is unsupported today, it will not appear in the dropdown list during vault creation.
- Backing up virtual machines by using the Azure Backup service is supported only for select operating system versions:
  - **Linux**: See [the list of distributions that are endorsed by Azure](../virtual-machines-linux-endorsed-distributions.md). Other Bring-Your-Own-Linux distributions also should work as long as the VM agent is available on the virtual machine.
  - **Windows Server**:  Versions older than Windows Server 2008 R2 are not supported.
- Restoring a domain controller (DC) VM that is part of a multi-DC configuration is supported only through PowerShell. Read more about [restoring a multi-DC domain controller](backup-azure-restore-vms.md#restoring-domain-controller-vms).
- Restoring virtual machines that have the following special network configurations is supported only through PowerShell. VMs that you create by using the restore workflow in the UI will not have these network configurations after the restore operation is complete. To learn more, see [Restoring VMs with special network configurations](backup-azure-restore-vms.md#restoring-vms-with-special-netwrok-configurations).
    - Virtual machines under load balancer configuration (internal and external)
    - Virtual machines with multiple reserved IP addresses
    - Virtual machines with multiple network adapters

## Questions?
If you have questions, or if there is any feature that you would like to see included, [send us feedback](http://aka.ms/azurebackup_feedback).

## Next steps

- [Plan your VM backup infrastructure](backup-azure-vms-introduction.md)
- [Back up virtual machines](backup-azure-vms.md)
- [Manage virtual machine backups](backup-azure-manage-vms.md)


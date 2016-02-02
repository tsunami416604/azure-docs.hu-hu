<properties
   pageTitle="測試 Microsoft Azure SUSE Linux VM 上的 SAP NetWeaver | Microsoft Azure"
   description="在 Microsoft Azure SUSE Linux VM 上測試 SAP NetWeaver"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="hermanndms"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="11/26/2015"
   ms.author="hermannd"/>


# 在 Microsoft Azure SUSE Linux VM 上測試 SAP NetWeaver

以下是在 Microsoft Azure SUSE Linux VM 上測試 SAP NetWeaver 時要考量的項目清單。
目前尚無關於 SAP-Linux-Azure 的官方 SAP 支援的說明。 
不過客戶可以執行一些測試、 示範或原型，只要它們並非相依
官方 SAP 支援。

下列清單應該只用來協助避免一些潛在的問題，並讓生活更輕鬆：



## 在 Microsoft Azure 上測試 SAP 的 SUSE 映像

若要在 Azure 上進行 SAP 測試，只應使用 SLES 11SP4 和 SLES 12。 特殊的 SUSE 映像
Azure 映像庫中找到:"SLES 11 SP3 for SAP CAL 」
但這並不適用於一般使用方式。 這被保留 for SAP 雲端應用裝置程式庫
解決方案稱為 SAP 」 CAL 」 ( <https://cal.sap.com/> )。沒有任何選項
若要隱藏此映像從公用。 所以，就不使用它。

在 Azure 上的所有新測試都應使用 Azure 資源管理員進行。 尋找 SUSE SLES 映像 
並使用下列命令，使用 Azure Powershell 或 CLI 版本。 然後可以使用輸出
例如在部署新的 SUSE Linux VM 的 json 範本中定義之作業系統映像:

* 尋找現有的發行者，包括 SUSE：

   ```
   PS  : Get-AzureVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* 從 SUSE 尋找現有的供應項目：

   ```
   PS  : Get-AzureVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* 尋找 SUSE SLES 供應項目：

   ```
   PS  : Get-AzureVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* 尋找 SLES SKU 的特定版本：

   ```
   PS  : Get-AzureVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## 在 SUSE VM 中安裝 WALinuxAgent

此代理程式是 Azure 資源庫中的 SLES 映像的一部分。 以下是一個可以在哪裡找到的地方
手動安裝它的相關資訊 (例如當上傳 SLES OS vhd 從內部部署):

<http://software.opensuse.org/package/WALinuxAgent>

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-endorsed-distributions/>

<https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/>

## 將 Azure 資料磁碟連結至 Azure Linux VM

請永遠不要透過裝置識別碼將 Azure 資料磁碟掛接到 Azure Linux VM。 請改用 UUID。 請小心
當使用例如圖形工具來掛接 Azure 資料磁碟。 請仔細檢查 /et/fstab 中的項目。
裝置識別碼的問題是，它可能會變更，然後 Azure VM 可能會停止回應在開機 
程序。 其中一個可能會在 /etc/fstab 新增 nofail 參數來解決這個問題。 但請小心
與 nofail 應用程式可能使用掛接點和之前一樣，可能會導致寫入至根嗎
檔案系統，以防外部 Azure 資料磁碟不裝載在開機期間。

## 從內部部署將 SUSE VM 上傳至 Azure

下列部落格說明執行步驟：
<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-suse/>

如果您想要上傳保留例如現有的 SAP 的 VM 沒有在結束取消佈建步驟
安裝，以及主機名稱就必須檢查下列項目:

* 確定作業系統磁碟是透過 UUID 掛接，而不是透過裝置識別碼。 只在 /etc/fstab 變更 UUID 對作業系統磁碟而言並不夠。 不要忘了也要調整開機載入器，例如透過 yast 或編輯 /boot/grub/menu.lst
* 萬一使用者的 SUSE 作業系統磁碟使用 vhdx 格式，為了上傳至 Azure 而將它轉換為 vhd，很有可能網路裝置已從 eth0 變更為 eth1。
若要在 Azure 上稍後應該變更回 eth0 像開機時避免問題描述
這裡: <https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/>

除了文章中說明的以外，建議您一併移除

   # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/

只要沒有多個 nic，安裝 waagent 應該也可避免任何潛在問題。

## SAP 授權與硬體金鑰

正式的 SAP Windows Azure 憑證的新機制引進來計算
SAP 硬體金鑰用於 SAP 授權。 SAP 核心每次都要調整為請使用 
這個項目。 
Linux 目前的 SAP 核心版本不包括此程式碼變更。 因此它可能會發生此問題 
在某些情況下 (例如 Azure VM 調整大小)，SAP 硬體金鑰變更 」 和 「 SAP 授權
已不再有效

## 分散式 SAP 安裝中的 NFS 共用

如果想要安裝如資料庫和 SAP 的分散式安裝
在個別的 Vm 一個應用程式伺服器可能共用透過 NFS /sapmnt 目錄。 如果那里
您應該安裝步驟的問題後建立 NFS 共用 /sapmnt 檢查
如果 「 no_root_squash 」 設為共用。 這是內部測試案例中的解決方案


## 邏輯磁碟區

LVM 在 Azure 上並未經過徹底的驗證。 如果需要大量的邏輯磁碟區跨多個 Azure 
應該使用資料 (例如 SAP 資料庫) 的磁碟 mdadm。 以下是不錯的部落格的
描述如何使用 mdadm，在 Azure 上的 Linux RAID 設定:

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-raid/>


## SUSE Azure 儲存區域

如果應該有的問題有標準的 Azure SUSE 存放庫的存取 
簡單的命令來重設。 這種情形時在 Azure 中建立私用的作業系統映像
區域，然後將映像複製至不同的區域，其中一個想要部署新的 Vm
根據此私用的作業系統映像。 只要在 VM 中執行下列命令：

   ```
   service guestregister restart
   ```

## Gnome 桌面

如果有人想要安裝完整的 SAP 示範系統內使用如桌面
一個單一的 VM，包括 SAP GUI、 瀏覽器、 SAP 管理主控台等這裡有一個小提示 
安裝 Azure SLES 映像:

   SLES 11

   ```
   zypper in -t pattern gnome
   ```

   SLES 12

   ```
   zypper in -t pattern gnome-basic
   ```

## 雲端中的 Linux 上的 SAP-Oracle 支援

這實際上並非 Azure 的特定主題，而是一般主題。 不過很重要
了解。 在虛擬環境中，Oracle 對 Linux 的支援有所限制。
這表示結尾，SAP 不支援 Oracle SUSE 或 RedHat 公用定域機組中
例如 Azure。 
客戶應直接連絡 Oracle 來討論這個主題。








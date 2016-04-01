<properties
    pageTitle="準備 Debian Linux VHD | Microsoft Azure"
    description="了解如何建立 Debian 7 和 8 VHD，以供部署於 Azure 中。"
    services="virtual-machines"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/01/2015"
    ms.author="mingzhan"/>




#針對 Azure 準備 Debian VHD

##必要條件
本節假設您已經有安裝 Debian Linux 作業系統從.iso 檔案從下載 [Debian 網站](https://www.debian.org/distrib/) 虛擬硬碟。 可建立 .vhd 檔案的工具有多種，Hyper-V 只是其中一個範例。 如需使用 HYPER-V 的指示，請參閱 [安裝 HYPER-V 角色和設定虛擬機器](https://technet.microsoft.com/library/hh846766.aspx)。 


## 安裝注意事項

- Azure 不支援較新的 VHDX 格式。 您可以將磁碟轉換為 VHD 格式使用 HYPER-V 管理員或 **轉換 vhd** 指令程式。
- 安裝 Linux 系統時，建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。 這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。 如果願意，您可以在資料磁碟上使用 LVM 或 RAID。
- 請勿在作業系統磁碟上設定交換磁碟分割。 您可以設定 Azure Linux 代理程式，以在暫存資源磁碟上建立交換檔。 您可以在以下步驟中找到與此有關的詳細資訊。
- 所有 VHD 的大小都必須是 1 MB 的倍數。


##Debian 7.x 和 8.x

1. 在 Hyper-V 管理員中，選取虛擬機器。

2. 按一下 [ **連接** 開啟虛擬機器的主控台視窗。

3. 標記為註解的程式行， **deb cdrom** 中 `/etc/apt/source.list` 如果您設定的 VM 針對 ISO 檔案。

4. 編輯 `/etc/default/grub` 檔案，並修改 **GRUB_CMDLINE_LINUX** 參數，如下所示，包含用於 Azure 的其他核心參數。

        GRUB_CMDLINE_LINUX="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

5. 重建 grub，然後執行：

        # sudo update-grub 

6. 安裝 Azure Linux 代理程式的相依性封裝：

        # apt-get install -y git parted

7.  安裝 Azure Linux 代理程式從使用 Github [指引](virtual-machines-linux-update-agent.md) 並選擇 2.0.14 版本 ︰

            # wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
            # chmod +x waagent
            # cp waagent /usr/sbin
            # /usr/sbin/waagent -install -verbose

8. 取消佈建虛擬機器並準備將其佈建於 Azure 上，然後執行：

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
 
9. 按一下 [ **動作** ]-> [關閉關閉 HYPER-V 管理員中。 您現在可以將 Linux VHD 上傳至 Azure。

##使用 Credativ 指令碼來建立 Debian VHD

Credativ 網站具有協助您自動建立 Debian VHD 的指令碼。 您可以下載從 [這裡](https://gitlab.credativ.com/de/azure-manage) 並將它安裝在 Linux VM。 若要建立 Debian VHD (如 Debian 7)，請執行：

        # azure_build_image --option release=wheezy --option image_prefix=lilidebian7 --option image_size_gb=30 SECTION

如果有任何發行給使用這個指令碼，只是檔案 Credativ 問題 [這裡](https://gitlab.credativ.com/groups/de/issues)。

## 後續步驟

現在您可以開始使用 Debian.vhd 來建立新的 Azure 虛擬機器。



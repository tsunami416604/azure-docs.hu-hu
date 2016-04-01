<properties
    pageTitle="建立 Linux VM 的不同方式 | Microsoft Azure"
    description="列出在 Azure 建立 Linux 虛擬機器的不同方式，並提供進一步指示的連結。"
    services="virtual-machines"
    documentationCenter=""
    authors="dsk-2015"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="08/12/2015"
    ms.author="dkshir"/>

# 建立 Linux 虛擬機器的不同方式

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure 提供建立 VM 的不同方式，因為 VM 適用於不同的使用者和用途。 這表示您必須進行一些關於 VM 的選擇，並選擇建立它的方式。 本文提供這些選項及指示連結的摘要說明。

我們最近推出 Azure 資源管理員範本做為一種方式，用以建立虛擬機器，並將它與其不同資源當成一個邏輯部署單位來管理。 以下提供這個可用方法的指示。 若要深入了解 Azure 資源管理員，以及如何管理資源當成一個單位，請參閱 [概觀][]。

## 工具選項

### GUI：Azure 傳統入口網站或 Azure 入口網站

入口網站的圖形化使用者介面是用來嘗試設定虛擬機器的簡單方法，特別是在您剛開始使用 Azure 時。 使用其中一種 [Azure 傳統入口網站](http://manage.windowsazure.com) 或 [Azure 入口網站](http://portal.azure.com) 來建立 VM。 如需一般指示，請參閱 [建立自訂虛擬機器][] ，然後選取任何 Linux 映像 **圖庫**。 請注意， [Azure 傳統入口網站](http://manage.windowsazure.com) 會建立虛擬機器使用傳統部署模型。

### 命令殼層：Azure CLI 或 Azure PowerShell

如果您偏好在命令殼層中工作，可選擇適用於 Mac 和 Linux 使用者的 Azure 命令列介面 (CLI) 或 Azure PowerShell (其中具有適用於 Azure 和自訂主控台的 Windows PowerShell Cmdlet)。

針對 Azure CLI，請參閱 [建立執行 Linux 的虛擬機器][]。 若要使用範本，請參閱 [部署和管理虛擬機器使用 Azure 資源管理員範本和 Azure CLI][]。

針對 Azure PowerShell，請參閱 [使用 Azure PowerShell 建立和預先設定以 Linux 為基礎的虛擬機器][]。 若要使用範本，請參閱 [部署和管理虛擬機器使用 Azure 資源管理員範本和 PowerShell][]。

### 開發環境：Visual Studio

[使用 Visual Studio 建立網站的虛擬機器][]

[使用 Compute、Network 和 Storage .NET Library 部署 Azure 資源][]

## 作業系統和映像選項

根據您想要執行的作業系統來選擇映像。 Azure 與其合作夥伴提供許多映像，其中有些包括應用程式和工具。 或者，使用您自己的其中一個映像。

### Azure 映像

這些指示會示範如何使用 Azure 映像來建立虛擬機器，此虛擬機器是使用適用於網路功能、負載平衡，以及更多選項來自訂。 請參閱 [如何建立自訂虛擬機器在 Azure 中執行 Linux][]。

### 使用您自己的映像

使用現有的 Azure 虛擬機器的映像 *擷取* 該 VM 或上傳自己的映像儲存在虛擬硬碟 (VHD):

- [如何使用 CLI 擷取 Linux 虛擬機器以做為範本][]
- [建立及上傳包含 Linux 作業系統的虛擬硬碟][]

## 後續步驟

[登入虛擬機器][]

[附加資料磁碟][]

## 其他資源

[基本設定測試環境][]

[Azure 混合式雲端測試環境][]

[在使用適用於 Mac、Linux 及 Windows 的 Azure CLI 的 VM 操作中的對等資源管理員和服務管理命令][]

<!-- LINKS -->
[overview]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md

[Equivalent Resource Manager and Service Management Commands for VM Operations with the Azure CLI for Mac, Linux, and Windows]:xplat-cli-azure-manage-vm-asm-arm.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and the Azure CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and PowerShell]:  virtual-machines-deploy-rmtemplates-powershell.md
[Use Azure PowerShell to create and preconfigure Linux-based Virtual Machines]: virtual-machines-ps-create-preconfigure-linux-vms.md

[How to Create a Custom Virtual Machine Running Linux in Azure]: virtual-machines-linux-create-custom.md
[How to Capture a Linux Virtual Machine to Use as a Template with the CLI]: virtual-machines-linux-capture-image.md

[Creating and Uploading a Virtual Hard Disk that Contains the Linux Operating System]: virtual-machines-linux-create-upload-vhd.md

[Creating a virtual machine for a website with Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Deploy Azure Resources Using the Compute, Network, and Storage .NET Libraries]: virtual-machines-arm-deployment.md

[Log On to the Virtual Machine]: virtual-machines-linux-how-to-log-on.md

[Attach a Data Disk]: virtual-machines-linux-how-to-attach-disk.md

[Base Configuration Test Environment]: virtual-machines-base-configuration-test-environment.md
[Azure hybrid cloud test environments]: virtual-machines-hybrid-cloud-test-environments.md

[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md
[Create a Custom Virtual Machine]: virtual-machines-create-custom.md



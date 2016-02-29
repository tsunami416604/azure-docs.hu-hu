<properties
    pageTitle="建立及上傳 Linux VHD | Microsoft Azure"
    description="以包含 Linux 作業系統的傳統部署模型建立並上傳 Azure 虛擬硬碟 (VHD)。"
    services="virtual-machines"
    documentationCenter=""
    authors="dsk-2015"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/04/2015"
    ms.author="dkshir"/>

# 建立及上傳包含 Linux 作業系統的虛擬硬碟

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


本文說明如何建立及上傳虛擬硬碟 (VHD)，以便用它做為您自己的映像，在 Azure 中建立虛擬機器。 您將了解如何準備作業系統，以便用它根據該映像建立多台虛擬機器。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Azure 中的虛擬機器會執行根據您建立虛擬機器時所選擇映像的作業系統。 您的映像會以 VHD 格式的 .vhd 檔案儲存在儲存體帳戶中。 如需詳細資訊，請參閱 [Azure 中的磁碟](virtual-machines-disks-vhds.md) 和 [在 Azure 中的映像](virtual-machines-images.md)。

在建立虛擬機器時，您可以自訂部分作業系統設定，使用它們適合您要執行的應用程式。 如需指示，請參閱 [如何建立自訂虛擬機器](virtual-machines-create-custom.md)。

**重要**: 中，Azure 平台 SLA 才適用於使用組態來使用的其中一個背書散發套件時，才執行 Linux OS 的虛擬機器詳細資料做為支援版本 」 下指定 [Linux on azure 背書散發套件](virtual-machines-../linux-endorsed-distributions.md)。 Azure 映像庫中的所有 Linux 散發套件，皆為使用必要組態的背書散發套件。


## 必要條件
本文假設您具有下列項目：

- **管理憑證** -您已建立的訂閱，想要上傳 VHD，並將憑證匯出至.cer 檔案的管理憑證。 如需有關建立憑證的詳細資訊，請參閱 [azure 憑證概觀](../cloud-services/cloud-services-certs-create.md)。

- **Linux 作業系統安裝在.vhd 檔案**  -您已安裝支援的 Linux 作業系統虛擬硬碟。 有多項工具可用來建立 .vhd 檔案，例如，您可以使用虛擬化解決方案 (例如 Hyper-V) 來建立 .vhd 檔案並安裝作業系統。 如需指示，請參閱 [安裝 HYPER-V 角色和設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

    **重要**: Azure 不支援較新的 VHDX 格式。 您可以使用 Hyper-V 管理員或 convert-vhd Cmdlet，將磁碟轉換為 VHD 格式。

    如需背書散發套件，請參閱 [Linux on azure 背書散發套件](../linux-endorsed-distributions.md)。 如需一般的 Linux 散發套件清單，請參閱 [非背書散發套件的資訊](virtual-machines-linux-create-upload-vhd-generic.md)。

- **Azure 命令列介面** -如果您使用 Linux 作業系統建立映像，您會使用 [Azure 命令列介面](../virtual-machines-command-line-tools.md) 上傳 VHD。

- **Azure Powershell 工具** - `Add-AzureVhd` cmdlet 也可用來上傳 VHD。 請參閱 [Azure 下載](http://azure.microsoft.com/downloads/) 下載 Azure Powershell cmdlet。 如需參考資訊，請參閱 [Add-azurevhd](https://msdn.microsoft.com/library/azure/dn495173.aspx)。

<a id="prepimage"> </a>
## 步驟 1：準備要上傳的映像

Azure 支援各種 Linux 散發套件 (請參閱 [背書散發套件](../linux-endorsed-distributions.md))。 下列文章會逐步引導您如何準備各種 Azure 支援的 Linux 散發套件：

- **[CentOS 型散發套件](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Debian Linux](virtual-machines-linux-create-upload-vhd-debian.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-create-upload-vhd-redhat.md)**
- **[SLES 及 openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[其他 - 非背書散發套件](virtual-machines-linux-create-upload-vhd-generic.md)**

另請參閱 **[Linux 安裝注意事項](virtual-machines-linux-create-upload-vhd-generic.md#linuxinstall)** 如需有關準備 azure 的 Linux 映像的秘訣。

完成上述指南中的步驟之後，您應會有一個 VHD 檔案，準備好上傳到 Azure。

<a id="connect"> </a>
## 步驟 2：準備與 Azure 的連接

您必須先在電腦與 Azure 中的訂閱之間建立安全連線，才能上傳 .vhd 檔案。


### 如果使用 Azure CLI

最新的 Azure CLI 預設會進入「資源管理員」部署模型，因此請使用下列命令來確定您是在傳統部署模型中：

        azure config mode asm  

接著，使用下列任一登入方法連接到您的 Azure 訂用帳戶。 

使用 Azure AD 方法登入：

1. 開啟 Azure CLI 視窗

2. 輸入：

    `azure login`

    出現提示時，輸入您的使用者名稱和密碼。

**或者**, ，改為使用 PublishSettings 檔案:

1. 開啟 Azure CLI 視窗

2. 輸入：

    `azure account download`

    此命令會開啟瀏覽器視窗，並自動下載含有您的 Azure 訂閱所需之資訊和憑證的 .publishsettings 檔案。

3. 儲存 .publishsettings 檔案

4. 輸入：

    `azure account import <PathToFile>`

    其中 `<PathToFile>` 是 .publishsettings 檔案的完整路徑。

    如需詳細資訊，請參閱 [從 Azure CLI 連接至 Azure](../xplat-cli-connect.md)。


### 如果使用 Azure PowerShell

使用 Azure AD 方法登入：

1. 開啟 Azure PowerShell 視窗。

2. 輸入：

    `Add-AzureAccount`

    出現提示時，請鍵入貴組織的使用者識別碼和密碼。

**或者**, ，改為使用 PublishSettings 檔案:

1. 開啟 Azure PowerShell 視窗。

2. 輸入：

    `Get-AzurePublishSettingsFile`

    此命令會開啟瀏覽器視窗，並自動下載含有您的 Azure 訂閱所需之資訊和憑證的 .publishsettings 檔案。

3. 儲存 .publishsettings 檔案。

4. 輸入：

    `Import-AzurePublishSettingsFile <PathToFile>`

    其中 `<PathToFile>` 是 .publishsettings 檔案的完整路徑。

    如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md)

> [AZURE.NOTE] 我們建議您登入您的 Azure 訂閱，從 Azure CLI 或 Azure PowerShell 使用較新的 Azure Active Directory 方法。

<a id="upload"> </a>
## 步驟 3：將映像上傳至 Azure

### 如果使用 Azure CLI

使用 Azure CLI 上傳映像。 您可以使用下列命令來上傳映像：

        azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>

### 如果使用 PowerShell

您需要一個可供上傳 VHD 檔案的儲存體帳戶。 您可以選取現有的或建立新的儲存體帳戶。 若要建立儲存體帳戶，請參閱 [建立儲存體帳戶](../storage-create-storage-account.md)

在上傳 .vhd 檔案時，您可以將 .vhd 檔案放在 Blob 儲存體中的任一處。 在下列命令範例中， **BlobStorageURL** 是您打算使用的儲存體帳戶的 URL **YourImagesFolder** 是您要儲存映像的 blob 儲存體中的容器。 **VHDName** 是出現在標籤 [Azure 傳統入口網站](http://manage.windowsazure.com) 來識別虛擬硬碟。 **PathToVHDFile** 是完整路徑和.vhd 檔案的名稱。

從您在上一個步驟使用的 Azure PowerShell 視窗中，輸入：

        Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>

如需詳細資訊，請參閱 [Add-azurevhd](https://msdn.microsoft.com/library/azure/dn495173.aspx)。

> [AZURE.NOTE]  [Azure Powershell 1.0 預覽版本](https://azure.microsoft.com/blog/azps-1-0-pre/) 大幅改變處理傳統和資源管理員部署模型的指令程式的方式。 本文尚未使用「預覽」版本。 


[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload


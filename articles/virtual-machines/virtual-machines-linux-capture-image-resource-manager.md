<properties
    pageTitle="擷取 Linux VM 作為範本使用 | Microsoft Azure"
    description="了解如何擷取以 Azure 資源管理員部署模型建立之以 Linux 為主的 Azure 虛擬機器 (VM) 的映像。"
    services="virtual-machines"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/05/2015"
    ms.author="danlep"/>



# 如何擷取 Linux 虛擬機器作為資源管理員範本使用

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [傳統部署模型](virtual-machines-linux-capture-image.md)。


本文說明如何使用 Azure 命令列介面 (CLI) 擷取執行 Linux 的 Azure 虛擬機器，以便用它作為 Azure 資源管理員範本來建立其他虛擬機器。 此範本會指定 OS 磁碟和連結虛擬機器的資料磁碟。 但不包含建立 Azure 資源管理員 VM 所需的虛擬網路資源，因此在大部分的情況下，您必須先個別設定這些資源，再建立另一部使用此範本的虛擬機器。

## 開始之前

這些步驟假設您已在 Azure 資源管理員部署模型中建立 Azure 虛擬機器，並設定好作業系統，包括連接任何資料磁碟以及進行其他自訂作業 (如安裝應用程式)。 如果您尚未這麼做，請參閱在 Azure 資源管理員模式中使用 Azure CLI 的相關指示：

- [部署和管理虛擬機器使用 Azure 資源管理員範本和 Azure CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

例如，您可能會在美國中部區域建立名為 *MyResourceGroup* 的資源群組。 然後使用如下所示的 **azure vm quick-create** 命令在資源群組中部署 Ubuntu 14.04 LTS VM。

    azure vm quick-create -g MyResourceGroup -n <your-virtual-machine-name> "centralus" -y Linux -Q canonical:ubuntuserver:14.04.2-LTS:14.04.201507060 -u <your-user-name> -p <your-password>

佈建並執行 VM 之後，您可能想要連接和掛接資料磁碟。 請參閱指示 [這裡](virtual-machines-linux-tutorial.md#attach-and-mount-a-disk)。

若要執行其他自訂作業，您需要使用您選擇的 SSH 用戶端來連接到 VM。 如需詳細資訊，請參閱 [連接到您的 Azure Linux VM 使用 ssh](virtual-machines-linux-tutorial-portal-rm.md#connect-to-your-azure-linux-vm-using-strongsshstrong)。


## 擷取 VM

1. 當您準備好擷取 VM 時，請使用 SSH 用戶端進行連接。

2. 在 SSH 視窗中，輸入下列命令。 請注意，不同版本的 **waagent** 公用程式可能會有稍微不同的輸出：

    `sudo waagent-deprovision + user`

    此命令會嘗試清除系統，使之適合重新佈建。 這項作業會執行下列工作：

    - 移除 SSH 主機金鑰 (如果組態檔中的 Provisioning.RegenerateSshHostKeyPair 是 'y')
    - 清除 /etc/resolv.conf 中的名稱伺服器設定
    - 移除 `根` eg /etc/ 陰影 (如果 Provisioning.DeleteRootPassword 是 'y' 組態檔中的) 從使用者的密碼
    - 移除快取的 DHCP 用戶端租用
    - 將主機名稱重設為 localhost.localdomain
    - 刪除最後佈建的使用者帳戶 (取自於 /var/lib/waagent) 和相關聯的資料。
    >[AZURE.NOTE] 解除佈建會刪除檔案與資料，以將映象「一般化」。 只在您想要擷取作為映像的 VM 上執行這個命令。 這不能保證映像檔中的所有機密資訊都會清除完畢或適合轉散發給第三方。

3. 輸入 **y** 繼續。 您可以加入 **-force** 參數，便不用進行此確認步驟。

4. 輸入 **exit** 關閉 SSH 用戶端。
    >[AZURE.NOTE] 接下來的步驟假設您已經 [安裝 Azure CLI](../xplat-cli-install.md) 用戶端電腦上。

5. 從用戶端電腦，開啟 Azure CLI 並登入您的 Azure 訂用帳戶。 如需詳細資訊，請參閱 [從 Azure CLI 連接至 Azure 訂用帳戶](../xplat-cli-connect.md)。

6. 請確定您處於資源管理員模式中：

    `azure config mode arm`

7. 使用下列命令，停止您已經解除佈建的 VM：

    `azure vm 停止 – g < 您-資源-群組-名稱 >-n < 您的虛擬-機器-名稱 >`

8. 使用下列命令將 VM 一般化：

    `azure vm 一般化 – g < 您-資源-群組-名稱 >-n < 您的虛擬-機器-名稱 >`

9. 現在使用下列命令來擷取映像和本機檔案範本：

    `azure vm 擷取 – g < 您-資源-群組-名稱 >-n < 您的虛擬-機器-名稱 >-p < 您的 vhd-名稱-前置詞 >-t < 您的範本-檔案-name.json >`

    此命令會使用您為 VM 磁碟指定的 VHD 名稱前置詞，建立一般化 OS 映像。 在原始 VM 使用的相同儲存體帳戶中預設會建立映像 VHD 檔案。 **-t** 選項會建立本機 JSON 檔案範本，以便從映像建立新的 VM。

>[AZURE.TIP] 若要尋找映像的位置，請開啟 JSON 檔案範本。 在 **storageProfile** 中，尋找位於 **system** 容器的**映像**的 **uri**。 例如，作業系統磁碟映像的 uri 是類似於 `https://clixxxxxxxxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/your-prefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`。

## 從擷取的映像部署新的 VM

現在使用具有範本的映像來建立新的 Linux VM。 下列步驟將示範如何使用 Azure CLI 和您建立的 JSON 檔案範本 `azure vm 擷取` 命令來建立新的虛擬網路中的 VM。

### 建立網路資源

若要使用範本，您必須先為新的 VM 設定虛擬網路和 NIC。 建議您為這些資源建立新的資源群組。 執行類似下列的命令，替換您的資源名稱和適當的 Azure 位置 (在這些命令列中為 "centralus")：

    azure group create <your-new-resource-group-name> -l "centralus"
    
    azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"
    
    azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>
    
    azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"
    
    azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"

若要使用您在擷取期間所儲存的 JSON，從映像部署 VM，您需要 NIC 的識別碼。 執行下列命令來取得識別碼。

    azure network nic show <your-new-resource-group-name> <your-nic-name>

輸出中的**識別碼**就像下列的字串。

    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<your-new-resource-group-name>/providers/Microsoft.Network/networkInterfaces/<your-nic-name>

### 建立新的部署

現在執行下列命令，從擷取的 VM 映像和您儲存的範本 JSON 檔案建立 VM。

    azure group deployment create –g <your-new-resource-group-name> -n <your-new-deployment-name> -f <your-template-file-name.json>

系統會提示您提供新的 VM 名稱、系統管理員使用者名稱和密碼，以及您先前建立的 NIC 識別碼。

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    vmName: mynewvm
    adminUserName: myadminuser
    adminPassword: ********
    networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

如果部署成功，您會看到類似下列的輸出。

    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "dlnewdeploy"
    + Waiting for deployment to complete
    data:    DeploymentName     : mynewdeploy
    data:    ResourceGroupName  : mynewrg
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-10-29T16:35:47.3419991Z
    data:    Mode               : Incremental
    data:    Name                Type          Value
    
    
    data:    ------------------  ------------  -------------------------------------
    
    data:    vmName              String        mynewvm
    
    
    data:    vmSize              String        Standard_D1
    
    
    data:    adminUserName       String        myadminuser
    
    
    data:    adminPassword       SecureString  undefined
    
    
    data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic
    info:    group deployment create command OK

### 驗證部署

現在使用您建立的虛擬機器的 SSH 來驗證部署並開始使用新的 VM。 若要透過 SSH 連接，請尋找您藉由執行下列命令所建立的 VM 的 IP 位址：

    azure network public-ip show <your-new-resource-group-name> <your-ip-name>

公用 IP 位址會列在命令輸出中。 根據預設，您會經由連接埠 22 上的 SSH 連接到 Linux VM。

## 使用範本建立其他 VM

依照前一節所述的步驟，使用擷取的映像和範本來部署其他 VM。

* 確保您的 VM 映像位於將裝載 VM 之 VHD 的相同儲存體帳戶。
* 複製範本 JSON 檔案，並針對各 VM 的 VHD 輸入唯一的 **uri** 值。
* 在相同或不同的虛擬網路中建立新的 NIC
* 使用修改過的範本 JSON 檔案，在您設定虛擬網路的資源群組中建立部署

如果您想設定當您從自動建立 VM 映像，使用網路 [101 vm 從-使用者-映像範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) github。 此範本會從自訂映像和必要的虛擬網路、公用 IP 位址和 NIC 資源建立 VM。 在 Azure 入口網站中使用範本的逐步解說，請參閱 [如何從使用 ARM 範本的自訂映像建立虛擬機器](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/)。

## 使用 azure vm create 命令

您通常會想要使用資源管理員範本從映像建立 VM。 不過，您可以使用 **azure vm create** 命令搭配 **--os-disk-vhd** (**-d**) 參數，以命令方式建立 VM__。

對映像執行 **azure vm create** 之前，請執行下列動作：

1.  建立新的資源群組，或識別現有的資源群組以供部署。

2.  為新的 VM 建立公用 IP 位址資源和 NIC 資源。 如需建立虛擬網路、 公用 IP 位址，以及 NIC 使用 CLI 的步驟，請參閱稍早在本文中。 (**azure vm 建立** 也可以建立新的 NIC，但是您必須為虛擬網路和子網路的其他參數傳遞。)

3.  確定您將映像 VHD 複製到沒有資料夾 (虛擬目錄) 的 Blob 容器位置。 根據預設所擷取的映像會儲存在儲存體 blob 容器中的巢狀資料夾中 (類似於 URI `https://clixxxxxxxxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/your-prefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`。 **azure vm create** 命令目前只能從儲存在 Blob 容器最頂層的 OS 磁碟 VHD 建立 VM。 比方說，您可能將映像 VHD 複製到 `https://yourstorage.blob.core.windows.net/vhds/your-prefix-OsDisk.vhd`。

然後執行類似下列的命令。

    azure vm create -g <your-resource-group-name> -n <your-new-vm-name> -l eastus -y Linux -o <your-storage-account-name> -d "https://yourstorage.blob.core.windows.net/vhds/your-prefix-OsDisk.vhd" -z Standard_A1 -u <your-admin-name> -p <your-admin-password> -f <your-nic-name>

如需額外的命令選項，執行 `azure 協助建立 vm`。

## 後續步驟

若要管理您使用 CLI 的 Vm，請參閱中的工作 [部署和管理虛擬機器使用 Azure 資源管理員範本和 Azure CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)。





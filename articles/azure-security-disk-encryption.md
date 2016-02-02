<properties
   pageTitle="Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密 | Microsoft Azure"
   description="本文件提供 Windows 和 Linux IaaS VM 適用的 Microsoft Azure 磁碟加密的概觀。"
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="devtiw"/>



# Windows 和 Linux IaaS VM Preview 適用的 Azure 磁碟加密

> [AZURE.NOTE] 本文資訊適用於預覽版本的 Azure 磁碟加密。


Microsoft Azure 強烈承諾確保您的資料隱私權、資料主權，並透過一系列進階技術來加密、控制和管理加密金鑰、控制和稽核資料存取，讓您控制您的 Azure 託管資料。這會提供 Azure 客戶靈活度，可選擇最符合其商務需求的解決方案。本文中，我們將為您介紹新的技術解決方案「Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密」，以協助保護及保障您的資料，以便符合組織的安全性和符合性的承諾。本文提供有關如何使用 Azure 磁碟加密功能的詳細指引，包括支援的案例和使用者體驗。

**附註**：本文包含的特定建議可能會導致資料、網路或計算資源使用量增加，導致額外的授權或訂用帳戶成本。

## 概觀

Azure 磁碟加密是可讓您加密您的 Windows 和 Linux IaaS 虛擬機器磁碟的新功能。

### 加密案例

Azure 磁碟加密解決方案支援下列 3 個客戶加密案例：

- 對透過客戶加密的 VHD 和加密金鑰建立的新 IaaS VM 啟用加密

- 對透過 Azure 資源庫建立的新 IaaS VM 啟用加密

- 在 Azure 中已執行的現有 IaaS VM 上啟用加密

在 Microsoft Azure 中啟用時，解決方案支援下列公用預覽版本適用的 IaaS VM：

- 與 Azure 金鑰保存庫整合

- 

- 

- 


解決方案不支援公用預覽版本中的下列案例、功能和技術：

- 基本 VM 和標準 DS (進階儲存體) 系列 IaaS VM

- 使用傳統的 VM 建立方法建立的 IaaS VM

- 可停用透過 Azure 磁碟加密而啟用的 IaaS VM 上的加密功能

- 與您的內部部署金鑰管理服務整合

- Windows Server Technical Preview 3

- Red Hat Enterprise Linux


### 加密功能

為 Azure IaaS VM 啟用並部署 Azure 磁碟加密時，會啟用下列功能，視提供的組態而定：

- 加密作業系統磁碟區以輕鬆保護客戶儲存體中的開機磁碟區

- 加密資料磁碟區以輕鬆保護客戶儲存體中的資料磁碟區

- 保護客戶 Azure 金鑰保存庫訂用帳戶中的加密金鑰和密碼

- 報告加密 IaaS VM 的加密狀態

- 從 IaaS 虛擬機器移除磁碟加密組態設定

Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密解決方案包含 Windows 適用的磁碟加密擴充、Linux 適用的磁碟加密擴充、磁碟加密 PowerShell Cmdlet、磁碟加密 CLI Cmdlet 和磁碟加密 Azure 資源管理員範本。 在執行 Windows 或 Linux OS 的 IaaS VM 上支援 Azure 磁碟加密解決方案。 如需支援的作業系統的詳細資訊，請參閱以下的必要條件一節。

在公用預覽期間，使用 Azure 磁碟加密來加密 VM 磁碟完全免費。 我們也預期在磁碟加密公開上市之後仍會繼續這個情況。 不過，價格可能會根據市場及競爭態勢而變更。

### 價值主張

Azure 磁碟加密管理解決方案可帶來雲端中的下列商務需求：

-   IaaS VM 會使用業界標準的加密技術輕鬆加以保護，解決組織的安全性與法務遵循的需求。

-   IaaS VM 會在客戶控制金鑰和原則下開機，而且客戶可以在金鑰保存庫中稽核其使用方式。


### 加密工作流程

為 Windows 和 Linux VM 啟用磁碟加密所需的高階步驟如下：

1. 客戶從上述的 3 個加密案例選擇加密案例

2. 客戶選擇透過 Azure 磁碟加密 ARM 範本、PS Cmdlet 或 CLI 命令啟用磁碟加密，並指定加密組態

    - 針對客戶加密的 VHD 案例，客戶上傳加密的 VHD 至其儲存體帳戶和加密金鑰資訊到其金鑰保存庫，並提供加密組態以在新 IaaS VM 上啟用加密

    - 針對透過 Azure 資源庫和已在 Azure 中執行的現有 VM 建立新的 VM，客戶會提供加密組態以在 IaaS VM 上啟用加密

3. 客戶會授與存取給 Azure 平台，以從其金鑰保存庫讀取加密金鑰資料 (Windows 系統的 BitLocker 加密金鑰和 Linux 的複雜密碼)，藉以在 IaaS VM 上啟用加密

4. 客戶提供 Azure AD 身分識別以將加密金鑰資料寫入其金鑰保存庫，在上述的案例 2 和 3 的 IaaS VM 上啟用加密。

5.  Azure 服務管理會使用加密和金鑰保存庫組態更新 VM 服務模型，並為客戶佈建加密的 VM

![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig1.JPG)

## 必要條件

以下是針對概觀一節提到支援的案例，在 Azure IaaS VM 上啟用 Azure 磁碟加密的必要條件

- 使用者必須擁有有效的作用中 Azure 訂用帳戶，才能在 Azure 支援的區域中建立資源

- 在下列 Windows 伺服器 SKU 上支援 Azure 磁碟加密 - Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2。 Windows Server 2008 作業系統上不支援此解決方案。 公用預覽版本中不支援 Windows Server Technical Preview。

**附註**：針對 Windows Server 2008 R2，必須先安裝 .NET Framework 4.5，才能在 Azure 中啟用加密。

- 在下列 Linux 伺服器 SKU 上支援 Azure 磁碟加密 - Ubuntu、CentOS、SUSE 和 SUSE Linux Enterprise Server (SLES)。 公用預覽版本中不支援 Red Hat Enterprise Linux。

- 

**注意** - Azure 磁碟加密需要金鑰保存庫和 VM 位於相同的 Azure 區域。 在不同區域中設定它們會導致啟用 Azure 磁碟加密功能失敗。

- 

- 

- 

- 

- Azure 平台需要存取客戶 Azure 金鑰保存庫中的加密金鑰或密碼，才能讓它們可供 VM 用來開機和解密虛擬機器作業系統磁碟區。 若要授與權限給 Azure 平台以存取金鑰保存庫，必須針對此需求在金鑰保存庫上設定 **enabledForDiskEncryption** 屬性。

- 金鑰保存庫密碼和金鑰加密金鑰 (KEK) URL 必須已設定版本。 Azure 服務管理會強制執行設定版本的這項限制。 請參閱以下範例以取得有效的密碼和 KEK URL：

    - 有效密碼 URL 的範例：

        *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

    - 有效 KRK KEK 的範例：

        *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*


- Azure 磁碟加密不支援將連接埠號碼指定為金鑰保存庫密碼和 KEK URL 的一部分。 請參閱以下範例以取得支援的金鑰保存庫 URL：

    - 不被接受的金鑰保存庫 URL

        *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

    - 可接受的金鑰保存庫 URL

        *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*


- 若要啟用 Azure 磁碟加密功能，IaaS VM 必須符合下列網路端點組態需求：

    - 

    - IaaS VM 必須能夠連接到 Azure 金鑰保存庫端點，以將加密金鑰寫入客戶金鑰保存庫

    - IaaS VM 必須能夠連接到託管 Azure 擴充儲存機制的 Azure 儲存體端點，和託管 VHD 檔案的 Azure 儲存體帳戶



- 若要執行任何 Azure 磁碟加密 PowerShell Cmdlet，您必須先安裝 Azure PowerShell 1.0.1 版：

    - 

    - 

**附註：**本文件假設您已了解基本概念，例如模組、Cmdlet 和工作階段。

- 若要執行任何 Azure CLI 命令並將它與您的 Azure 訂用帳戶產生關聯，您必須先安裝 Azure CLI 版本：

    - 

    - 

- Azure 磁碟加密解決方案對 Windows IaaS VM 使用 BitLocker 外部金鑰保護裝置。 如果您的 VM 加入網域，請勿推送會強制使用 TPM 保護裝置的任何群組原則。

### 術語

使用術語表做為了解一些此技術所使用的一般術語的參考：


| 術語| 定義|
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure AD| Azure AD 帳戶是從金鑰保存庫驗證、儲存和擷取密碼的必要條件。|
| Azure 金鑰保存庫 [AKV]| |
| ARM| Azure 資源管理員|
| BitLocker| |
| BEK| BitLocker 加密金鑰可用來加密作業系統開機磁碟區和資料磁碟區。BitLocker 金鑰會在客戶的 Azure 金鑰保存庫中以密碼形式保護。|
| CLI| |
| DM-Crypt| |
| KEK| 金鑰加密金鑰是非對稱金鑰 (RSA 2048)，用來在需要時保護或包裝密碼。您可以提供 HSM 保護的金鑰或軟體保護的金鑰。|
| PS Cmdlet| |

## 磁碟加密部署案例和使用者體驗

在許多案例下您可以啟用磁碟加密，而步驟可能會因案例而有所不同。 下列各節將對這些案例的詳細資料進一步討論。

### 對透過 Azure 資源庫建立的新 IaaS VM 啟用加密

按一下 Azure 快速入門範本上的 [部署至 Azure] 按鈕，在參數刀鋒視窗中輸入加密組態，然後按一下 [確定]。 選取訂用帳戶、資源群組、資源群組位置、法律條款與協議，並按一下 [建立] 按鈕以在新 IaaS VM 上啟用加密。

**附註：**此範本會使用 Windows Server 2012 的資源庫映像來建立新的加密 Windows VM。

您可以在下表中查看使用 Azure AD 用戶端識別碼、來自 Azure 資源庫案例的新 VM 的 ARM 範本參數詳細資料：

| 參數| 說明|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName| 虛擬機器的系統管理使用者名稱|
| adminPassword| 虛擬機器的系統管理使用者密碼|
| newStorageAccountName| 要儲存作業系統和資料 VHD 的儲存體帳戶名稱|
| vmSize| VM 的大小。目前僅支援標準的 A、D 和 G 系列|
| virtualNetworkName| VM NIC 應該隸屬的 VNet 的名稱。|
| subnetName| VM NIC 應該隸屬的 VNet 中子網路的名稱|
| AADClientID| 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端識別碼|
| AADClientSecret| 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端密碼|
| keyVaultResourceID,ResourceID| 識別 ARM 中的金鑰保存庫資源。您可以使用 PowerShell Cmdlet 取得它：(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).ResourceId|
| keyVaultURL| 應上傳 Bitlocker 金鑰的金鑰保存庫的 URL。您可以使用 Cmdlet 取得它：(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI|
| keyEncryptionKeyURL| 用來加密所產生 BitLocker 金鑰的金鑰加密金鑰的 URL。這是選擇性。|
| vmName| 要執行加密作業所在 VM 的名稱


**附註：**KeyEncryptionKeyURL 是選擇性參數。 您可以使用自己的 KEK，在金鑰保存庫中進一步保護資料加密金鑰 (複雜密碼)。

### 對透過客戶加密的 VHD 和加密金鑰建立的新 IaaS VM 啟用加密

在這個案例中，您可以使用 ARM 範本、PowerShell Cmdlet 或 CLI 命令啟用加密。 下列各節將更進一步說明 ARM 範本和 CLI 命令的詳細資料。

#### 使用 ARM 範本

按一下 Azure 快速入門範本上的 [部署至 Azure] 按鈕，在參數刀鋒視窗中輸入加密組態，然後按一下 [確定]。 選取訂用帳戶、資源群組、資源群組位置、法律條款與協議，並按一下 [建立] 按鈕以在新 IaaS VM 上啟用加密。

下表將描述客戶加密的 VHD 案例的 ARM 範本參數詳細資料：

| 參數| 說明|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| newStorageAccountName| 要儲存加密的作業系統 VHD 的儲存體帳戶名稱。應該已在與 VM 相同的資源群組和相同的位置中建立此儲存體帳戶|
| osVhdUri| 來自儲存體帳戶作業系統 VHD 的 URI|
| osType| 作業系統產品類型 (Windows/Linux)|
| virtualNetworkName| VM NIC 應該隸屬的 VNet 的名稱。應該已在與 VM 相同的資源群組和相同的位置中建立此項目|
| subnetName| VM NIC 應該隸屬的 VNet 中子網路的名稱|
| vmSize| VM 的大小。目前僅支援標準的 A、D 和 G 系列|
| keyVaultResourceID| 識別 ARM 中金鑰保存庫資源的 ResourceID。|
| keyVaultSecretUrl| ​金鑰保存庫中佈建的磁碟加密金鑰的 URL|
| keyVaultKekUrl| 用來加密所產生磁碟加密金鑰的金鑰加密金鑰的 URL|
| ​vmName| ​IaaS VM 的名稱



#### 使用 PowerShell Cmdlet



#### 使用 CLI 命令

遵循下列步驟使用 CLI 命令啟用此案例的磁碟加密：

1. 設定金鑰保存庫的存取原則：
    - 
    - 
2.  
    
3. 
4. 若要從客戶加密的 VHD 啟用新 VM 上的加密，請使用以下參數搭配 “azure vm create” 命令：
    - 
    - 
    - 
    - 


### 在 Azure 中現有/執行中 IaaS Windows VM 上啟用加密

在這個案例中，您可以使用 ARM 範本、PowerShell Cmdlet 或 CLI 命令啟用加密。 下列各節將更進一步說明如何使用 ARM 範本和 CLI 命令來啟用它的詳細資料。

#### 使用 ARM 範本

按一下 Azure 快速入門範本上的 [部署至 Azure] 按鈕，在參數刀鋒視窗中輸入加密組態，然後按一下 [確定]。 選取訂用帳戶、資源群組、資源群組位置、法律條款與協議，並按一下 [建立] 按鈕以在現有/執行中 IaaS VM 上啟用加密。

使用 Azure AD 用戶端識別碼的現有/執行中 VM 案例的 ARM 範本參數詳細資料，可在下表中取得：

| 參數| 說明|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID| ​具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端識別碼|
| AADClientSecret| ​具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端密碼|
| **​**| 識別 ARM 中金鑰保存庫資源的 ResourceID。|
| ​keyVaultURL| ​應上傳 Bitlocker 金鑰的金鑰保存庫的 URL。VaultURI|
| ​ keyEncryptionKeyURL| ​用來加密所產生 BitLocker 金鑰的金鑰加密金鑰的 URL。這是選擇性的|
| ​volumeType| ​執行加密作業所在磁碟區的類型。有效值為 "OS"、"Data"、"All"|
| sequenceVersion| BitLocker 作業的順序版本。每當在相同的 VM 上執行磁碟加密作業時便遞增此版本號碼|
| ​vmName| ​要執行加密作業所在 VM 的名稱


**附註：**KeyEncryptionKeyURL 是選擇性參數。 您可以使用自己的 KEK，在金鑰保存庫中進一步保護資料加密金鑰 (BitLocker 加密密碼)。

#### 使用 PowerShell Cmdlet



#### 使用 CLI 命令

請依照下列步驟，在 Azure 中使用 CLI 命令來啟用現有/執行中 IaaS Windows VM 上的加密：

1. 設定金鑰保存庫的存取原則：
    - 
    - 
2.  
    
3. 
4. 若要從客戶加密的 VHD 啟用新 VM 上的加密，請使用以下參數搭配 “azure vm create” 命令：
    - 
    - 
    - 
    - 


### 在 Azure 中現有/執行中 IaaS Linux VM 上啟用加密

按一下 Azure 快速入門範本上的 [部署至 Azure] 按鈕，在參數刀鋒視窗中輸入加密組態，然後按一下 [確定]。 選取訂用帳戶、資源群組、資源群組位置、法律條款與協議，並按一下 [建立] 按鈕以在現有/執行中 IaaS VM 上啟用加密。

使用 Azure AD 用戶端識別碼的現有/執行中 VM 案例的 ARM 範本參數詳細資料，將在下表中說明：

| 參數| 說明|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID| ​具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端識別碼|
| AADClientSecret| ​具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端密碼|
| **​**| 識別 ARM 中金鑰保存庫資源的 ResourceID。|
| ​keyVaultURL| ​應上傳 Bitlocker 金鑰的金鑰保存庫的 URL。VaultURI|
| ​ keyEncryptionKeyURL| ​用來加密所產生 BitLocker 金鑰的金鑰加密金鑰的 URL。這是選擇性的|
| ​volumeType| ​執行加密作業所在磁碟區的類型。有效的支援值為 "Data"。Linux VM 不支援在執行 Linux VM 的作業系統磁碟區上啟用加密
| sequenceVersion| BitLocker 作業的順序版本。每當在相同的 VM 上執行磁碟加密作業時便遞增此版本號碼|
| passPhrase| 輸入強式複雜密碼做為資料加密金鑰|
| ​vmName| ​要執行加密作業所在 VM 的名稱


**附註：**KeyEncryptionKeyURL 是選擇性參數。 您可以使用自己的 KEK，在金鑰保存庫中進一步保護資料加密金鑰 (複雜密碼)。

#### CLI 命令

請依照下列步驟，在 Azure 中使用 CLI 命令來啟用現有/執行中 IaaS Linux VM 上的加密：

1. 設定金鑰保存庫的存取原則：
    - 
    - 
2. 
    
3. 
4. 若要從客戶加密的 VHD 啟用新 VM 上的加密，請使用以下參數搭配 “azure vm create” 命令。
    - 
    - 
    - 
    - 

### 取得加密的 IaaS VM 的加密狀態

下列各節將說明如何使用 Azure 管理 (預覽) 入口網站和 CLI 命令來取得加密狀態。

#### 使用 Azure 管理預覽入口網站取得加密 IaaS VM 的加密狀態

您可以從 Azure 管理預覽入口網站取得 IaaS VM 的加密狀態。  您可以從 [訂用帳戶] 下拉式清單中選取訂用帳戶名稱，以篩選虛擬機器檢視。 按一下位於虛擬機器頁面功能表頂端的資料行。 從選擇資料行刀鋒視窗選取 [磁碟加密] 資料行，並按一下 [更新]。 您應該會看到對於每個 VM，磁碟加密資料行顯示加密狀態為「已啟用」或「未啟用」，如下圖所示。

![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig2.JPG)

#### 使用磁碟加密 PS Cmdlet 取得加密 IaaS VM 的加密狀態

您可以透過磁碟加密 PS Cmdlet “Get-AzureRmVMDiskEncryptionStatus” 來取得 IaaS VM 的加密狀態。 若要取得您的 VM 的加密設定，請在您的 Azure PowerShell 工作階段中輸入：

    PS C:\Windows\System32\WindowsPowerShell\v1.0> Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName <yourResourceGroupName> -VMName <yourVMName>
    
    OsVolumeEncrypted: True
    OsVolumeEncryptionSettings : {
      "DiskEncryptionKey": {
       SecretUrl":"https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
            "SourceVault": {
            "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxxx/providers/Mi                            crosoft.KeyVault/vaults/xxxxxxx"
                                }
                            },
                    "KeyEncryptionKey": null
                             }
    DataVolumesEncrypted: True

OSVolumeEncrypted 和 DataVolumesEncrypted 設定值設定為 "True"，顯示這兩個磁碟區都是使用 Azure 磁碟加密來加密。

#### 透過磁碟加密 CLI 命令取得 IaaS VM 的加密狀態。

您可以透過磁碟加密 CLI 命令 *azure vm show-disk-encryption-status* 取得 IaaS VM 的加密狀態。 若要取得您的 VM 的加密設定，請在您的 Azure CLI 工作階段中輸入：

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

## 附錄

### 連線至您的訂用帳戶

繼續之前，請務必檢閱本文件的必要條件一節。 確保已滿足所有必要條件之後，請依照下列步驟來連接到您的訂用帳戶：

1.開始 Azure PowerShell 工作階段，並使用下列命令登入您的 Azure 帳戶：

    

2.

    

3.

    

4.

    

5.

    

6.

    
                                                                 
                                                        
                                                        
    


### 針對 Azure 磁碟加密的使用方式設定與配置 Azure 金鑰保存庫

Azure 磁碟加密會保護 Azure 金鑰保存庫中的磁碟加密金鑰和密碼。 請依照以下各節的步驟，為 Azure 磁碟加密使用方式設定金鑰保存庫。

**附註：**金鑰保存庫必須與 VM 位於相同區域。

### 建立新金鑰保存庫

若要建立新的金鑰保存庫，請使用下列兩個選項之一：

- 
- 

**附註：**如果您已為您的訂用帳戶設定金鑰保存庫，請繼續下一節。

### 佈建金鑰加密金鑰 (選擇性)

如果您想要使用金鑰加密金鑰 (KEK) 獲得額外層次的安全性，以包裝 BitLocker 加密金鑰，您應將 KEK 加入您的金鑰保存庫，才能在佈建程序中使用。

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

### 設定金鑰保存庫權限以允許 Azure 平台存取金鑰和密碼

Azure 平台需要存取您的 Azure 金鑰保存庫中的加密金鑰或密碼，才能讓它們可供 VM 用來開機和解密磁碟區。 若要授與權限給 Azure 平台，使它可以存取金鑰保存庫，必須在金鑰保存庫上設定 *enabledForDiskEncryption* 屬性。 您可以使用金鑰保存庫 PS Cmdlet，在您的金鑰保存庫上設定 enabledForDiskEncryption 屬性：

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

您必須如之前所提及，在您的金鑰保存庫上設定 *enabledForDiskEncryption* 屬性。  確定該以上詳述的屬性已正確設定，否則部署將會失敗。

### 在 Azure Active Directory 中設定 Azure AD 應用程式

若需要在 Azure 中執行中的 VM 上啟用加密，Azure 磁碟加密會產生並將加密金鑰寫入金鑰保存庫。 在金鑰保存庫中管理加密金鑰需要 Azure AD 驗證。

基於此目的，應該建立 Azure AD 應用程式。  這篇文章也包含一些有關佈建及設定金鑰保存庫的實用範例。 針對驗證目的，可以使用用戶端密碼式驗證或用戶端憑證式 Azure AD 驗證。

#### Azure AD 的用戶端密碼式驗證

下列各節含有為 Azure AD 設定用戶端密碼型驗證的必要步驟。

##### 使用 Azure PowerShell 建立新 Azure AD 應用程式

使用以下的 PowerShell Cmdlet 來建立新的 Azure AD 應用程式：

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId




##### 從 Azure 服務管理入口網站佈建 Azure AD 用戶端識別碼和密碼



1.

![Azure 磁碟加密](./media/azure-security-disk-encryption\disk-encryption-fig3.JPG)

2.

![Azure 磁碟加密](./media/azure-security-disk-encryption\disk-encryption-fig4.JPG)

3.

![Azure 磁碟加密](./media/azure-security-disk-encryption\disk-encryption-fig5.JPG)

4.應用程式的組態頁面隨即出現。 注意 Azure AD 用戶端識別碼位於頁面底部，如下圖所示。

![Azure 磁碟加密](./media/azure-security-disk-encryption\disk-encryption-fig6.JPG)

5.按一下 [儲存] 按鈕並記下 [金鑰] 文字方塊中的密碼，這是 Azure AD 用戶端密碼。 您應該適當地保護 Azure AD 用戶端密碼。

![Azure 磁碟加密](./media/azure-security-disk-encryption\disk-encryption-fig7.JPG)


**附註：**預覽入口網站不支援上述的流程。

##### 使用現有的應用程式



**附註：**必須從新的 PowerShell 視窗執行下列命令。 請勿使用 Azure PowerShell 或 Azure 資源管理員視窗來執行這些命令。 這項建議的原因是因為這些 Cmdlet 在 MSOnline 模組或 Azure AD PowerShell 中。

    $clientSecret = ‘<yourAadClientSecret>’ 
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### Azure AD 的憑證式驗證

下列各節含有為 Azure AD 設定憑證型驗證的必要步驟。

##### 建立新 Azure AD 應用程式

執行以下的 PowerShell Cmdlet 來建立新的 Azure AD 應用程式：

**附註：** 將「您的密碼」字串取代為您的安全密碼，並設定密碼保護。

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

一旦您完成此步驟，請將 .pfx 檔案上傳至金鑰保存庫，並啟用將該憑證部署到 VM 所需的存取原則。

##### 使用現有的 Azure AD 應用程式

如果您要為現有應用程式設定憑證式驗證，請使用以下的 PowerShell Cmdlet。 務必從新的 PowerShell 視窗執行它們。

    $certLocalPath = 'C:\certs\myaadapp.cer' 
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

一旦您完成此步驟，請將 .pfx 檔案上傳至金鑰保存庫，並啟用將該憑證部署到 VM 所需的存取原則。

##### 上傳 PFX 檔案至金鑰保存庫

不過，對於這項工作您只需要下列 PowerShell Cmdlet。 務必從 Azure PowerShell 主控台執行它們：

**附註：** 將「您的密碼」字串取代為您的安全密碼，並設定密碼保護。

    $certLocalPath = 'C:\certs\myaadapp.pfx' 
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    
    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    
    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@
    
    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
    
    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### 將金鑰保存庫中的憑證部署到現有 VM

完成上傳 PFX 之後，請使用下列步驟來將金鑰保存庫中的憑證部署到現有 VM：

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName

### 設定 Azure AD 應用程式的金鑰保存庫存取原則

您的 Azure AD 應用程式需要權限，才能存取保存庫中的金鑰或密碼。   下面還有如何透過 PowerShell 執行這項工作中的範例：

    $keyVaultName = ‘yourKeyVaultName’
    $aadClientID = '<youAadAppClientID>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys all -PermissionsToSecrets all

### 準備已預先加密的 Windows VHD

下列各節是準備預先加密的 Windows VHD 以在 Azure IaaS 中部署為加密的 VHD 的必要項目。 這些步驟可用來準備和啟動 Hyper-V 或 Azure 上的全新 Windows VM (VHD)。

#### 更新群組原則以對作業系統保護允許非 TPM

您需要設定稱為「BitLocker 磁碟機加密」的 BitLocker 群組原則設定，其位於此路徑：本機電腦原則\電腦設定\系統管理範本\Windows 元件。 如下圖所示，將這個設定變更為：*作業系統磁碟機 - 啟動時需要其他驗證 - 在不含相容 TPM 的情形下允許使用 BitLocker*：

![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig8.JPG)

#### 安裝 BitLocker 功能元件

針對 Windows Server 2012 和以上版本，請使用下列命令：

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

針對 Windows Server 2008 R2，請使用下列命令：

    ServerManagerCmd -install BitLockers

#### 使用 bdehdcfg 準備用於 BitLocker 的作業系統磁碟區

執行下面的命令來壓縮作業系統磁碟分割及為 BitLocker 準備電腦。

    bdehdcfg -target c: shrink -quiet

#### 使用 BitLocker 保護作業系統磁碟區

下次重新開機後，將會在系統/開機磁碟區上啟用加密。

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**附註：**必須先使用不同的資料/資源 VHD 準備 VM，才能使用 BitLocker 取得外部金鑰。

#### 準備已預先加密的 Linux VHD

##### Ubuntu 14.

1.注意 KeyFileName，因為它是 Azure 放置的複雜密碼檔案名稱。

    
    
    
    
    
    
    
    
    
        
        
        
        
        
                
                
                
                break
        
        
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"

     else
        
    

2.它看起來應該如下所示：

    

3.
4.

##### openSUSE 13.2.

1.

2.

    
    
    
    
    
    
    
    
    

3.

4.

    
    
    
    
    
    
       
       
       
          
          
          break
       
    done

5.

##### CentOS 7

1.

2.

    
    
    
    
    
    
    
    
    

3.

4.

    
    
    
    
    
    
    
    
    
        
        
        break
    
    done

5.

### 上傳加密的 VHD 至 Azure 儲存體帳戶

一旦啟用 BitLocker 加密或 DM-Crypt 加密，便需要將本機加密的 VHD 上傳至儲存體帳戶。

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>] 

### 上傳已預先加密 VM 的磁碟加密密碼至金鑰保存庫

必須將先前取得的磁碟加密密碼上傳，做為金鑰保存庫中的密碼。

#### 未使用 KEK 加密的磁碟加密密碼

如果是 Windows 虛擬機器，bek 檔案會以 base64 字串編碼，然後使用 Set-AzureKeyVaultSecret Cmdlet 上傳至金鑰保存庫。 如果是 Linux，複雜密碼會以 base64 字串編碼，然後上傳至金鑰保存庫。 此外，請確定在金鑰保存庫中建立密碼時會設定下列標記。

    "tags":
    {
       “DiskEncryptionKeyEncryptionAlgorithm”: “RSA-OAEP (optional)”
       "DiskEncryptionKeyFileName": "Bek file name (windows) or Passphrase filename (linux)"
    }
    
    param(
      [Parameter(Mandatory=$True)]
      [String]$BekFilePath = "C:\vm\nbox\2640EE52-41B3-426C-87B9-484232452CE4.BEK",
      [String]$VaultName = "DiskEncryptionTestAus",
      [String]$SecretName = "BitLockerKey"
      ) 
    
    #"EAN//ojeIQk="
    $bekFileName = split-path $BekFilePath -leaf
    echo "Bek file name = $bekFileName"
    
    $secretBytes = [System.IO.File]::ReadAllBytes($BekFilePath);
    $secret = [Convert]::ToBase64String($secretBytes);
    echo "Secret = $secret"
    
    $secureSecret = ConvertTo-SecureString $secret -AsPlainText -Force
    $tags = @{"DiskEncryptionKeyFileName" = "$bekFileName"} 
    
    echo "Tags = $tags"
    echo "Vault = $VaultName"
    echo "Secret name = $SecretName"
    echo "Adding secret to Key vault"
    
    Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secureSecret -tags $tags

#### 使用 KEK 加密的磁碟加密密碼

可以選擇性地使用金鑰加密金鑰來加密密碼，之後再上傳到金鑰保存庫。


## 下載此指南




## 如需 Blob 的詳細資訊，











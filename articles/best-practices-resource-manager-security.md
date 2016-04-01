<properties
    pageTitle="Azure 資源管理員的安全性考量"
    description="在 Azure 資源管理員中顯示建議的方法，以便透過金鑰和密碼、角色型存取控制和網路安全性群組保護資源的安全。"
    services="azure-resource-manager"
    documentationCenter=""
    authors="george-moore"
    manager="georgem"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/13/2015"
    ms.author="georgem"/>


# Azure 資源管理員的安全性考量

檢視您的 Azure 資源管理員範本的安全性層面時，有幾個區域需要考慮 ︰ 金鑰和密碼、 角色型存取控制 
與網路安全性群組。

本主題假設您熟悉 Azure 資源管理員中的角色型存取控制 (RBAC)。 如需詳細資訊，請參閱 
[Microsoft Azure 入口網站中的角色型存取控制](role-based-access-control-configure.md) 和 
[管理和稽核資源存取權](resource-group-rbac.md) 

本主題是較大份白皮書的一部分。 若要閱讀完整的文件、 下載 [世界類別 ARM 範本的考量和已證實實務] (http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World 類別 ARM 範本-考量和證明 Practices.pdf)。

## 密碼和憑證

Azure 虛擬機器、 Azure 資源管理員和 Azure 金鑰保存庫已完全整合以提供安全處理憑證的支援 
在 VM 中部署。  利用 Azure 金鑰保存庫搭配資源管理員來協調並儲存 VM 密碼和憑證是最佳作法和 
提供下列優點 ︰

- 範本僅包含密碼的 URI 參考，也就是說，實際的密碼不在程式碼、組態或原始程式碼儲存機制中。 這可防止 
內部或外部儲存機制，例如 GitHub 中的 harvest-bots 金鑰網路釣魚攻擊。
- 金鑰保存庫中儲存的密碼受到受信任的操作員的完整 RBAC 控制。  如果受信任的操作員離開公司或傳輸中 
公司至新的群組，不再有他們在保存庫中建立的金鑰存取權。
- 所有資產的完整區隔化：
      -部署金鑰的範本 
      -部署 VM 的索引鍵參考的範本 
      -保存庫中的實際金鑰資料。  
  每個範本 (和動作) 都可以從屬於不同 RBAC 角色，以完整區分職責。
- 將密碼載入 VM 在部署期間可以透過 Azure 網狀架構與 Microsoft 的範圍內的金鑰保存庫之間的直接通道進行 
資料中心。  一旦金鑰位於金鑰保存庫之後，他們就永遠不會在資料中心外部不受信任的通道「見光」。  
- 金鑰保存庫永遠是地區性的，因此，密碼與 VM 永遠具有區域性 (以及主權)。 沒有全域金鑰保存庫。

### 將金鑰與部署分開

最佳做法是維護以下事項的個別範本：

1.  保存庫的建立 (其中將包含金鑰資料)
2.  VM 的部署 (與保存庫中所包含金鑰的 URI 參考)

一般企業案例是將一小群受信任的操作員可以存取所部署的工作負載中的重要密碼與更廣泛的群組具有 
開發/操作人員可以建立或更新 VM 部署。  以下是範例 ARM 範本可建立和設定新的保存庫的內容中 
Azure Active Directory 中的目前已驗證的使用者身分識別。  此使用者擁有的預設權限建立、 刪除、 列出、 更新、 備份， 
還原和下半部的索引鍵取得公用，這個新的金鑰保存庫中。

雖然大部分的此範本中的欄位應該都很容易理解， **enableVaultForDeployment** 設定需要更多背景 ︰ 保存庫並沒有任何預設值 
任何其他 Azure 基礎結構元件的固定存取權。 藉由設定此值，可讓 Azure 運算基礎結構元件唯讀存取這 
特定的具名保存庫。 因此，更進一步的最佳做法是不要將公司機密資料摻和在與虛擬機器密碼相同的保存庫中。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the Vault"
                }
            },
            "location": {
                "type": "string",
                "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
                "metadata": {
                    "description": "Location of the Vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                    "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AD user. Get using Get-AzureADUser cmdlet"
                }
            },
            "skuName": {
                "type": "string",
                "allowedValues": ["Standard", "Premium"],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enableVaultForDeployment": {
                "type": "bool",
                "allowedValues": [true, false],
                "metadata": {
                    "description": "Specifies if the vault is enabled for a VM deployment"
                }
            }
        },
        "resources": [{
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2014-12-19-preview",
            "location": "[parameters('location')]",
            "properties": {
                "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
                "tenantid": "[parameters('tenantId')]",
                "accessPolicies": [{
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "secrets": ["all"],
                        "keys": ["all"]
                    }
                }],
                "sku": {
                    "name": "[parameters('skuName')]",
                    "family": "A"
                }
            }
        }]
    }

一旦建立保存庫之後，下一步就是參考新 VM 部署範本中的該保存庫。  如上所述，最佳作法是將 
不同的開發/操作群組管理 VM 部署，與該群組無法直接存取金鑰，儲存在保存庫中。

以下範本片段會組成更高的順序部署建構，每個安全地參考不是高度敏感的機密資料 
受操作員直接控制。

    "vaultName": {
        "type": "string",
        "metadata": {
            "description": "Name of Key Vault that has a secret"
        }
    },
    {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[parameters('vmName')]",
        "location": "[parameters('location')]",
        "properties": {
            "osProfile": {
                "secrets": [{
                    "sourceVault": {
                        "id": "[resourceId('vaultrg', 'Microsoft.KeyVault/vaults', 'kayvault')]"
                    },
                    "vaultCertificates": [{
                        "certificateUrl": "[parameters('secretUrlWithVersion')]",
                        "certificateStore": "My"
                    }]
                }]
            }
        }
    }

## 跨訂用帳戶互動的服務主體

Active Directory 目錄中會以服務主體來代表服務身分。 服務主體將位於啟用企業 IT 組織、系統整合者 (SI) 和雲端服務廠商 (CSV) 的主要案例的中心。 具體而言，這些組織中將會有一個組織需要與他們其中一個客戶的訂用帳戶互動的情況。  

您的組織可以提供一種服務，監視在您的客戶環境和訂用帳戶中部署的解決方案。 在此情況下，您必須 
取得記錄檔和客戶帳戶內的其他資料，讓您可以使用該監視解決方案中。 如果您是公司的 IT 組織、 系統整合者，您可以提供服務給客戶，您將在其中部署和管理功能， 
例如，資料分析平台，提供項目會位於客戶自己訂用帳戶。

在這些使用案例中，您的組織需要可以賦予在客戶訂用帳戶環境中執行這些動作的存取權的身分識別。  

這些案例為您的客戶提供某些考量：

-   基於安全性理由，存取的範圍可能需要設定為特定類型的動作，例如唯讀存取。
-   由於部署的資源是按成本提供，因此，基於財務考量，對於所需的存取權可能會有類似的條件約束。
-   基於安全性理由，存取的範圍可能需要設定為只針對一個特定資源 (儲存體帳戶)，或針對多個資源 (包含環境或解決方案的資源群組)
-   客戶與供應商的關係可能會變更，因此，客戶可能希望能夠啟用/停用對 SI 或 CSV 的存取
-   由於針對此帳戶進行的動作與計費息息相關，因此，客戶希望能夠在計費的審核性和問責制方面獲得支援。
-   從法規的觀點而言，客戶會想要能夠稽核您在他們的環境的行為

服務主體和 RBAC 的組合可用來解決這些需求。

## 網路安全性群組

許多案例將會要求指定如何控制傳輸至您虛擬網路中一個或多個 VM 執行個體的流量。 您可以使用網路安全性 
群組 (NSG)，若要這樣做為部署 ARM 範本的一部分。

網路安全性群組是與您的訂用帳戶相關聯的最上層物件。 NSG 包含存取控制規則，允許或拒絕流量 
VM 執行個體。 NSG 的規則可以隨時變更，而變更時會套用至所有相關聯的執行個體。 若要使用 NSG，您必須具有虛擬網路 
這是與區域 （位置） 相關聯。 NSG 不相容於與同質群組相關聯的虛擬網路。 如果您不需要 
區域虛擬網路，而且您想要控制端點的流量，請參閱 [關於網路存取控制清單 (Acl)](../virtual-network/virtual-networks-acl.md)。

您可以讓 NSG 與 VM 產生關聯，或與虛擬網路內的子網路產生關聯。 當與 VM 相關聯，NSG 會套用至傳送的所有流量， 
收到的 VM 執行個體。 套用至您的虛擬網路內的子網路時，它適用於傳送和接收的所有 VM 執行個體的所有流量 
在子網路。 VM 或子網路可以僅與 1 個 NSG 產生關聯，但每個 NSG 可以包含最多 200 個規則。 每個訂用帳戶您可以擁有 100 個 NSG。

>[AZURE.NOTE]  端點式 Acl 和網路安全性群組，不支援相同的 VM 執行個體上。 如果您想要使用 NSG 且已經擁有就地端點 ACL，請先移除端點 ACL。 如需如何執行這項操作，請參閱 [管理存取控制清單 (Acl) 端點使用 PowerShell](../virtual-network/virtual-networks-acl-powershell.md)。

### 網路安全性群組的運作方式

網路安全性群組不同於端點式 ACL。 端點 ACL 僅在透過輸入端點公開的公用連接埠上運作。 「 
NSG 一或多個 VM 執行個體上運作，並控制輸入和輸出的 VM 上的所有流量。

網路安全性群組具有 *名稱*, ，相關聯 *區域* （其中一個支援的 Azure 位置），以及具有描述性的標籤。 它包含兩種類型 
規則 ︰ 輸入及輸出。 輸出規則會套用至傳輸到 VM 的傳入封包，而輸入規則會套用至來自 VM 的傳出封包。 
這些規則會套用至 VM 所在的伺服器電腦。 傳入或傳出的封包必須符合「允許」規則才能允許，否則將會遭到捨棄。

規則會依照優先順序進行處理。 比方說，優先順序編號較高的規則之前處理具有較低的優先順序數字，例如 100 的規則 
例如 200。 一旦找到相符項目，則不會處理其他更多的規則。

規則會指定下列項目：

-   名稱：規則的唯一識別碼
-   類型：輸入/輸出
-   優先順序：一個介於 100 和 4096 之間的整數 (處理原則為從低到高)
-   來源 IP 位址：來源 IP 範圍的 CIDR
-   來源連接埠範圍：一個介於 0 和 65536 之間的整數或範圍
-   目的地 IP 範圍：目的地 IP 範圍的 CIDR
-   目的地連接埠範圍：一個介於 0 和 65536 之間的整數或範圍
-   通訊協定 ︰ TCP、 UDP 或 ' \ *'
-   存取：允許/拒絕

### 預設規則

NSG 包含預設規則。 無法刪除預設規則，但其會指派最低優先權，因為它們會覆寫規則， 
您所建立的。 預設規則會描述平台所建議的預設設定。 如下方預設規則所示，流量起始和結束 
虛擬網路中同時允許輸入和輸出方向。

雖然輸出方向允許連接到網際網路，但預設會封鎖輸入方向。 預設規則允許 Azure 負載平衡器 
探查 VM 的健全狀況。 如果 VM 或 NSG 下的 VM 集合不參與負載平衡集合，您可以覆寫此規則。

下表顯示預設規則。

**輸入預設規則**

名稱 |  優先順序 |  來源 IP | 來源連接埠 |   目的地 IP |    目的地連接埠 |  通訊協定 |  存取
--- | --- | --- | --- | --- | --- | --- | ---
允許 VNet 輸入  | 65000 | VIRTUAL_NETWORK | \* |    VIRTUAL_NETWORK | \* |  \*  | 允許
允許 Azure 負載平衡器輸入   | 65001 | AZURE_LOADBALANCER    | \*    | \*    | \*    | \*    | 允許
拒絕所有輸入    | 65500 | \*    | \*    | \*    | \*    | \*    | 拒絕

**輸出預設規則**

名稱 |  優先順序 |  來源 IP | 來源連接埠 |   目的地 IP |    目的地連接埠 |  通訊協定 |  存取
--- | --- | --- | --- | --- | --- | --- | ---
允許 VNet 輸出 | 65000 | VIRTUAL_NETWORK   | \*    | VIRTUAL_NETWORK   | \*    | \*    | 允許
允許網際網路輸出 | 65001 | \*    | \*    | 網際網路  | \*    | \*    | 允許
拒絕所有輸出   | 65500 | \*    | \*    | \*    | \*    | \*    | 拒絕

### 特殊的基礎結構規則

NSG 規則是明確的。 不允許所有流量，或拒絕超出 NSG 規則中所指定的流量。 不過，兩種類型的流量一律允許不論 
網路安全性群組規格。 這些佈建用於支援基礎結構：

- **主機節點的虛擬 IP**︰ 基本的基礎結構服務，例如 DHCP、 DNS 和健康狀態監視透過提供虛擬化的主機 IP 位址 
168.63.129.16. 這個公用 IP 位址屬於 Microsoft，且是針對此目的唯一用於所有區域的虛擬 IP。 此 IP 位址對應到 
裝載 VM 之伺服器電腦 （主機節點） 實體的 IP 位址。 主機節點做為 DHCP 轉送、 DNS 遞迴解析程式，以及探查來源 
負載平衡器健全狀況探查和電腦健康狀態探查。 此 IP 位址的通訊不應視為一種攻擊。
- **授權 （金鑰管理服務）**︰ 應該授權在 Vm 中執行的 Windows 映像。 若要這樣做，授權要求傳送至金鑰管理服務 
處理此類查詢的主機伺服器。 這會一律位於輸出連接埠 1688。

### 預設標籤

預設標籤是系統提供的識別項，用來解決 IP 位址的類別。 在使用者定義的規則中，可以指定預設標籤。

**NSG 的預設標籤**

標記 |   說明
--- | ---
VIRTUAL_NETWORK |   表示您所有的網路位址空間。 其包含虛擬網路位址空間 (在 Azure 中的 IP CIDR)，以及所有已連接的內部部署位址空間 (區域網路)。 這也包括虛擬網路至虛擬網路的位址空間。
AZURE_LOADBALANCER | 表示 Azure 基礎結構負載平衡器，而且將會轉譯成做為 Azure 健康狀態探查來源的 Azure 資料中心 IP。 此標籤僅在與 NSG 相關聯的 VM 或 VM 集合參與負載平衡集合時才需要。
網際網路 | 表示虛擬網路以外且可以透過公用網際網路進行存取的 IP 位址空間。 此範圍也包括 Azure 擁有的公用 IP 空間。

### 連接埠和連接埠範圍

NSG 規則可以針對單一來源或目的地連接埠指定，也可以針對一個連接埠範圍指定。 您想要開啟各種不同的連接埠時，這個方法會特別有用 
應用程式，例如 FTP。 範圍必須循序排列，且無法混合使用個別的連接埠規格。
若要指定連接埠的範圍，請使用連字號 (–) 字元。 例如， **100-500**。

### ICMP 流量

您可以使用目前的 NSG 規則，指定 TCP 或 UDP (但不是 ICMP) 做為通訊協定。 不過，允許 ICMP 流量虛擬網路內依預設會透過 
支援，以及任何連接埠和通訊協定流量的輸入的規則 (\ *) 中的虛擬網路。

### 讓 NSG 與 VM 產生關聯

當 NSG 與 VM 直接產生關聯時，NSG 中的網路存取規則會直接套用至目的地為 VM 的所有流量。 每當 NSG 是 
更新規則變更時，流量處理會反映在幾分鐘內更新。 當 NSG 與 vm 解除關聯時，狀態會回復成其 
NSG 前狀況 — 也就是為了 NSG 之前的系統預設值導入了。

### 讓 NSG 與子網路產生關聯

當 NSG 與子網路產生關聯時，NSG 中的網路存取規則會套用至子網路中的所有 VM。 每當 NSG 中的存取規則會 
更新，所做的變更會套用到子網路中的所有 Vm 分鐘內。

### 讓 NSG 與子網路和 VM 產生關聯

您可以讓一個 NSG 與 VM 產生關聯，並讓另一個 NSG 與 VM 所在的子網路產生關聯。 若為 VM 提供兩個保護層，則支援此案例。 
對於輸入流量，封包會遵循在子網路中所指定的存取規則，接著是 VM 中的規則。 當輸出時，封包會遵循指定的規則 
在 VM 中第一次，然後依照如下所示，子網路中指定的規則。

![讓 NSG 與子網路和 VM 產生關聯](./media/best-practices-resource-manager-security/nsg-subnet-vm.png)

當 NSG 與 VM 或子網路產生關聯時，網路存取控制規則會變得非常明確。 平台不會插入任何隱含規則來允許 
特定連接埠的流量。 在此情況下，如果您在 VM 中建立端點，則您也必須建立規則以允許來自網際網路的流量。 如果您不 
如此一來， *VIP: {Port}* 無法從存取之外。

例如，您可以建立新的 VM 和新的 NSG。 您讓 NSG 與 VM 產生關聯。 VM 可以透過虛擬網路中其他 Vm 與通訊 
允許 VNET 輸入規則。 VM 也可以使用「允許網際網路輸出」規則，進行網際網路的輸出連接。 稍後，連接埠 80 上建立端點 
若要接收在 VM 中執行您網站的流量。 從網際網路送往 VIP （公用虛擬 IP 位址） 的連接埠 80 的封包不會到達 VM，直到 
您可以新增規則類似於下表至 NSG。

**允許輸入至特定連接埠的流量的明確規則**

名稱 |  優先順序 |  來源 IP | 來源連接埠 |   目的地 IP |    目的地連接埠 |  通訊協定 |  存取
--- | --- | --- | --- | --- | --- | --- | ---
WEB | 100   | 網際網路 | *  | * | 80    | TCP   | 允許

## 使用者定義的路由

Azure 使用路由表決定如何根據每個封包的目的地轉送 IP 流量。 雖然 Azure 提供的預設路由表，根據 
虛擬網路設定，您可能需要將自訂路由加入至該資料表。

路由表中最常見的自訂項目需求是在 Azure 環境中使用虛擬應用裝置。 考慮中所示的案例 
下圖中。 假設您想要確保所有流量導向至中介層，而備份起始從前端子網路的子網路瀏覽 
虛擬防火牆應用裝置。 如果只將應用裝置新增至您的虛擬網路，並將其連接到不同的子網路，並不會提供這項功能。 
您也必須變更套用至您子網路的路由表，以確保封包轉送到虛擬防火牆應用裝置。

如果您在 Azure 虛擬網路與網際網路之間實作虛擬 NAT 應用裝置以控制流量，也會達到相同的效果。 若要確保虛擬 
應用裝置會使用您必須建立一個路由，指定指向網際網路的所有流量必須都送給虛擬應用裝置。

### 路由

根據在實體網路上的每個節點定義的路由表，封包是透過 TCP/IP 網路路由傳送。 路由表是個別的集合 
用來決定要在何處轉送封包的目的地 IP 位址為基礎的路由。 路由是由下列項目所組成：

- 位址前置詞。 路由所套用的目的地 CIDR，例如 10.1.0.0/16。
- 下一個躍點類型。 要接收封包的 Azure 躍點的類型。 可能的值包括：
  - 本機。 代表本機虛擬網路。 例如，如果您在相同的虛擬網路中有兩個子網路 (分別是 10.1.0.0/16 和 10.2.0.0/16)，則路由表中每個子網路的路由的下一個躍點值為「本機」。
  - VPN 閘道。 代表 Azure S2S VPN 閘道。
  - 網際網路。 代表 Azure 基礎結構所提供的預設網際網路閘道
  - 虛擬應用裝置。 代表您新增至 Azure 虛擬網路的虛擬應用裝置。
  - NULL。 代表黑洞。 轉送至黑洞的封包將完全不會被轉送。
-   Nexthop 值。 下一個躍點值包含應該要將封包轉送到哪個 IP 位址。 在下一個躍點類型所在的路由中，才允許下一個躍點值 *虛擬應用裝置*。 下一個躍點必須位於子網路 (根據網路識別碼的虛擬應用裝置本機介面)，而不是遠端子網路。 

![路由](./media/best-practices-resource-manager-security/routing.png)

### 預設路由

在虛擬網路中建立的每個子網路都會自動與包含下列預設路由規則的路由表產生關聯：

- 本機 VNet 規則：此規則會自動針對虛擬網路中的每個子網路建立。 它會指定 VNet 中 VM 之間的直接連結，沒有下一個中繼躍點。 這讓相同子網路上的 VM (無論 VM 所在位置的網路識別碼為何)，不需要預設閘道位址即可互相通訊。
- 內部部署規則：此規則適用於目的地為內部部署位址範圍的所有流量，並使用 VPN 閘道做為下一個躍點目的地。
- 網際網路規則：此規則處理目的地為公開網際網路的所有流量，並使用基礎結構網際網路閘道做為目的地為網際網路所有流量的下一個躍點。

### BGP 路由

在撰寫本文時 [ExpressRoute](expressroute/expressroute-introduction.md) 尚不支援在 [網路資源提供者](virtual-network/resource-groups-networking.md) Azure 資源管理員。  如果您有 ExpressRoute 連線之間您 
在內部網路和 Azure，您可以啟用 BGP，在 NRP 支援 ExpressRoute 之後，將傳播路由從內部部署網路至 Azure。 這 
BGP 路由的使用方式與預設路由每個 Azure 子網路中的使用者定義路由相同。 如需詳細資訊，請參閱 
[ExpressRoute 簡介](expressroute/expressroute-introduction.md)。

>[AZURE.NOTE] 當支援 ExpressRoute NRP 上的時，您可以設定 Azure 環境使用藉由建立使用者定義的路由的下一個躍點中使用 VPN 閘道的子網路 0.0.0.0/0 透過內部網路強制通道。 不過，這只有在您使用的是 VPN 閘道，而不是 ExpressRoute 時，才有作用。 若是 Expressroute，強制通道是透過 BGP 設定。

### 使用者定義的路由

您無法檢視以上在 Azure 環境中指定的預設路由，而且對於大部分的環境而言，這些是您將需要的唯一路由。 
不過，您可能需要在特定情況下建立路由表，並新增一個或多個路由，例如：

-   透過內部部署網路連線到網際網路的強制通道。
-   在 Azure 環境中使用虛擬應用裝置。

在上述情況下，您必須建立一個路由表，並將使用者定義的路由新增到該路由表。 您可以擁有多個路由表，而且相同的路由表可以 
是一個或多個子網路相關聯。 此外，每個子網路只能與單一路由資料表產生關聯。 所有 Vm 和雲端服務的子網路中都使用的路由表 
與子網路相關聯。

子網路會依賴預設路由，直到路由表與子網路產生關聯為止。 一旦關聯存在時，路由是根據 [最長前置詞比對 (LPM)](https://en.wikipedia.org/wiki/Longest_prefix_match) 
使用者定義的路由和預設路由。 如果有多個符合相同 LPM 的路由被選取根據其來源，以下列 
順序 ︰

1.  使用者定義的路由
2.  BGP 路由 (使用 ExpressRoute 時)
3.  預設路由

>[AZURE.NOTE] 使用者定義路由僅會套用至 Azure Vm 和雲端服務。 例如，如果您想要在內部部署網路與 Azure 之間新增防火牆虛擬應用裝置，您必須為您的 Azure 路由表建立一個使用者定義的路由，此路由會將前往內部部署位址空間的所有流量轉送到虛擬應用裝置。 不過，來自內部部署位址空間的連入流量將會流經您的 VPN 閘道或直接到 Azure 環境的 ExpressRoute 電路，略過虛擬應用裝置。

### IP 轉送

如上所述，建立使用者定義的路由的主要原因之一是將流量轉送到虛擬應用裝置。 虛擬應用裝置沒有多個 
執行用來處理網路流量以某種方式，例如防火牆或 NAT 裝置的應用程式的 VM。

此虛擬應用裝置 VM 必須能夠接收未定址到本身的連入流量。 若要讓 VM 接收定址到其他目的地的流量 
您必須啟用 IP 轉送 VM 中。

## 後續步驟
- 若要了解如何將具有正確的存取權的安全性主體設定為使用您的組織中的資源，請參閱 [驗證服務主體與 Azure 資源管理員](resource-group-authenticate-service-principal.md)
- 如果您需要鎖定資源的存取權，可以使用管理鎖定。 請參閱 [鎖定的資源與 Azure 資源管理員](resource-group-lock-resources.md)
- 若要設定路由和 IP 轉送，請參閱 [如何建立路由與 Azure 中啟用 IP 轉送](virtual-network/virtual-networks-udr-how-to.md) 
- 如需角色型存取控制的概觀，請參閱 [Microsoft Azure 入口網站中的角色型存取控制](role-based-access-control-configure.md)



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

 


本主題假設您熟悉 Azure 資源管理員中的角色型存取控制 (RBAC)。 如需詳細資訊，請參閱 
 


本主題是較大份白皮書的一部分。

## 密碼和憑證

 
 


- 範本僅包含密碼的 URI 參考，也就是說，實際的密碼不在程式碼、組態或原始程式碼儲存機制中。 

- 金鑰保存庫中儲存的密碼受到受信任的操作員的完整 RBAC 控制。 

- 所有資產的完整區隔化：
       
       
        
  每個範本 (和動作) 都可以從屬於不同 RBAC 角色，以完整區分職責。
-  
一旦金鑰位於金鑰保存庫之後，他們就永遠不會在資料中心外部不受信任的通道「見光」。
- 金鑰保存庫永遠是地區性的，因此，密碼與 VM 永遠具有區域性 (以及主權)。 沒有全域金鑰保存庫。

### 將金鑰與部署分開

最佳做法是維護以下事項的個別範本：

1.  保存庫的建立 (其中將包含金鑰資料)
2.  VM 的部署 (與保存庫中所包含金鑰的 URI 參考)

 
 
 


 
 
因此，更進一步的最佳做法是不要將公司機密資料摻和在與虛擬機器密碼相同的保存庫中。

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

一旦建立保存庫之後，下一步就是參考新 VM 部署範本中的該保存庫。 


 


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

您的組織可以提供一種服務，監視在您的客戶環境和訂用帳戶中部署的解決方案。 
 


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

許多案例將會要求指定如何控制傳輸至您虛擬網路中一個或多個 VM 執行個體的流量。 


網路安全性群組是與您的訂用帳戶相關聯的最上層物件。 
NSG 的規則可以隨時變更，而變更時會套用至所有相關聯的執行個體。 
NSG 不相容於與同質群組相關聯的虛擬網路。 


您可以讓 NSG 與 VM 產生關聯，或與虛擬網路內的子網路產生關聯。 
 
VM 或子網路可以僅與 1 個 NSG 產生關聯，但每個 NSG 可以包含最多 200 個規則。 每個訂用帳戶您可以擁有 100 個 NSG。
>[AZURE.NOTE]  端點式 ACL 和網路安全性群組，不支援用於相同的 VM 執行個體。 如果您想要使用 NSG 且已經擁有就地端點 ACL，請先移除端點 ACL。

### 網路安全性群組的運作方式

網路安全性群組不同於端點式 ACL。 端點 ACL 僅在透過輸入端點公開的公用連接埠上運作。 「 


網路安全性群組有「名稱」**，且與某個「區域」** (某個 Azure 支援的位置) 相關聯，還有描述性的標籤。 
輸出規則會套用至傳輸到 VM 的傳入封包，而輸入規則會套用至來自 VM 的傳出封包。 
這些規則會套用至 VM 所在的伺服器電腦。 傳入或傳出的封包必須符合「允許」規則才能允許，否則將會遭到捨棄。

規則會依照優先順序進行處理。 
一旦找到相符項目，則不會處理其他更多的規則。

規則會指定下列項目：

-   名稱：規則的唯一識別碼
-   類型：輸入/輸出
-   優先順序：一個介於 100 和 4096 之間的整數 (處理原則為從低到高)
-   來源 IP 位址：來源 IP 範圍的 CIDR
-   來源連接埠範圍：一個介於 0 和 65536 之間的整數或範圍
-   目的地 IP 範圍：目的地 IP 範圍的 CIDR
-   目的地連接埠範圍：一個介於 0 和 65536 之間的整數或範圍
-   
-   存取：允許/拒絕

### 預設規則

NSG 包含預設規則。 
預設規則會描述平台所建議的預設設定。 


雖然輸出方向允許連接到網際網路，但預設會封鎖輸入方向。 
如果 VM 或 NSG 下的 VM 集合不參與負載平衡集合，您可以覆寫此規則。

下表顯示預設規則。

**輸入預設規則**

 名稱| 優先順序| 來源 IP| 來源連接埠| 目的地 IP| 目的地連接埠| 通訊協定| 存取
--- | --- | --- | --- | --- | --- | --- | ---
 允許 VNet 輸入| 65000| VIRTUAL_NETWORK| \*| VIRTUAL_NETWORK| \*| \*| 允許
 允許 Azure 負載平衡器輸入| 65001| AZURE_LOADBALANCER| \*| \*| \*| \*| 允許
 拒絕所有輸入| 65500| \*| \*| \*| \*| \*| 拒絕

**輸出預設規則**

 名稱| 優先順序| 來源 IP| 來源連接埠| 目的地 IP| 目的地連接埠| 通訊協定| 存取
--- | --- | --- | --- | --- | --- | --- | ---
 允許 VNet 輸出| 65000| VIRTUAL_NETWORK| \*| VIRTUAL_NETWORK| \*| \*| 允許
 允許網際網路輸出| 65001| \*| \*| 網際網路| \*| \*| 允許
 拒絕所有輸出| 65500| \*| \*| \*| \*| \*| 拒絕

### 特殊的基礎結構規則

NSG 規則是明確的。 不允許所有流量，或拒絕超出 NSG 規則中所指定的流量。 
這些佈建用於支援基礎結構：

- 
168.63.129.16. 這個公用 IP 位址屬於 Microsoft，且是針對此目的唯一用於所有區域的虛擬 IP。 
 
此 IP 位址的通訊不應視為一種攻擊。
- **授權 (金鑰管理服務)**：在 VM 中執行的 Windows 映像必須獲得授權。 
這會一律位於輸出連接埠 1688。

### 預設標籤

預設標籤是系統提供的識別項，用來解決 IP 位址的類別。 在使用者定義的規則中，可以指定預設標籤。

**NSG 的預設標籤**

 標記| 說明
--- | ---
 VIRTUAL_NETWORK| 表示您所有的網路位址空間。其包含虛擬網路位址空間 (在 Azure 中的 IP CIDR)，以及所有已連接的內部部署位址空間 (區域網路)。這也包括虛擬網路至虛擬網路的位址空間。
 AZURE_LOADBALANCER| 表示 Azure 基礎結構負載平衡器，而且將會轉譯成做為 Azure 健康狀態探查來源的 Azure 資料中心 IP。此標籤僅在與 NSG 相關聯的 VM 或 VM 集合參與負載平衡集合時才需要。
 網際網路| 表示虛擬網路以外且可以透過公用網際網路進行存取的 IP 位址空間。此範圍也包括 Azure 擁有的公用 IP 空間。

### 連接埠和連接埠範圍

NSG 規則可以針對單一來源或目的地連接埠指定，也可以針對一個連接埠範圍指定。 
範圍必須循序排列，且無法混合使用個別的連接埠規格。
若要指定連接埠的範圍，請使用連字號 (–) 字元。 例如 **100-500**。

### ICMP 流量

您可以使用目前的 NSG 規則，指定 TCP 或 UDP (但不是 ICMP) 做為通訊協定。 


### 讓 NSG 與 VM 產生關聯

當 NSG 與 VM 直接產生關聯時，NSG 中的網路存取規則會直接套用至目的地為 VM 的所有流量。 
 


### 讓 NSG 與子網路產生關聯

當 NSG 與子網路產生關聯時，NSG 中的網路存取規則會套用至子網路中的所有 VM。 


### 讓 NSG 與子網路和 VM 產生關聯

您可以讓一個 NSG 與 VM 產生關聯，並讓另一個 NSG 與 VM 所在的子網路產生關聯。 若為 VM 提供兩個保護層，則支援此案例。 
對於輸入流量，封包會遵循在子網路中所指定的存取規則，接著是 VM 中的規則。 


![讓 NSG 與子網路和 VM 產生關聯](./media/best-practices-resource-manager-security/nsg-subnet-vm.png)

當 NSG 與 VM 或子網路產生關聯時，網路存取控制規則會變得非常明確。 
在此情況下，如果您在 VM 中建立端點，則您也必須建立規則以允許來自網際網路的流量。 


例如，您可以建立新的 VM 和新的 NSG。 您讓 NSG 與 VM 產生關聯。 
VM 也可以使用「允許網際網路輸出」規則，進行網際網路的輸出連接。 
 


**允許輸入至特定連接埠的流量的明確規則**

 名稱| 優先順序| 來源 IP| 來源連接埠| 目的地 IP| 目的地連接埠| 通訊協定| 存取
--- | --- | --- | --- | --- | --- | --- | ---
 WEB| 100| 網際網路| *| *| 80| TCP| 允許

## 使用者定義的路由

Azure 使用路由表決定如何根據每個封包的目的地轉送 IP 流量。 


路由表中最常見的自訂項目需求是在 Azure 環境中使用虛擬應用裝置。 
 
如果只將應用裝置新增至您的虛擬網路，並將其連接到不同的子網路，並不會提供這項功能。 
您也必須變更套用至您子網路的路由表，以確保封包轉送到虛擬防火牆應用裝置。

如果您在 Azure 虛擬網路與網際網路之間實作虛擬 NAT 應用裝置以控制流量，也會達到相同的效果。 


### 路由

根據在實體網路上的每個節點定義的路由表，封包是透過 TCP/IP 網路路由傳送。 
路由是由下列項目所組成：

- 位址前置詞。 路由所套用的目的地 CIDR，例如 10.1.0.0/16。
- 下一個躍點類型。 要接收封包的 Azure 躍點的類型。 可能的值包括：
  - 本機。 代表本機虛擬網路。 例如，如果您在相同的虛擬網路中有兩個子網路 (分別是 10.1.0.0/16 和 10.2.0.0/16)，則路由表中每個子網路的路由的下一個躍點值為「本機」。
  - VPN 閘道。 代表 Azure S2S VPN 閘道。
  - 網際網路。 代表 Azure 基礎結構所提供的預設網際網路閘道
  - 虛擬應用裝置。 代表您新增至 Azure 虛擬網路的虛擬應用裝置。
  - NULL。 代表黑洞。 轉送至黑洞的封包將完全不會被轉送。
-   Nexthop 值。 下一個躍點值包含應該要將封包轉送到哪個 IP 位址。 只有在下一個躍點類型為*虛擬應用裝置*所在的路由中，才允許下一個躍點值。 下一個躍點必須位於子網路 (根據網路識別碼的虛擬應用裝置本機介面)，而不是遠端子網路。

![路由](./media/best-practices-resource-manager-security/routing.png)

### 預設路由

在虛擬網路中建立的每個子網路都會自動與包含下列預設路由規則的路由表產生關聯：

- 本機 VNet 規則：此規則會自動針對虛擬網路中的每個子網路建立。 它會指定 VNet 中 VM 之間的直接連結，沒有下一個中繼躍點。 這讓相同子網路上的 VM (無論 VM 所在位置的網路識別碼為何)，不需要預設閘道位址即可互相通訊。
- 內部部署規則：此規則適用於目的地為內部部署位址範圍的所有流量，並使用 VPN 閘道做為下一個躍點目的地。
- 網際網路規則：此規則處理目的地為公開網際網路的所有流量，並使用基礎結構網際網路閘道做為目的地為網際網路所有流量的下一個躍點。

### BGP 路由

 
這 
 

>[AZURE.NOTE] 在 NRP 上支援 ExpressRoute 時，您將可以設定 Azure 環境透過內部部署網路使用強制通道，方法是，為使用 VPN 閘道做為下一個躍點的子網路 0.0.0.0/0 建立使用者定義的路由。 不過，這只有在您使用的是 VPN 閘道，而不是 ExpressRoute 時，才有作用。 若是 Expressroute，強制通道是透過 BGP 設定。

### 使用者定義的路由

您無法檢視以上在 Azure 環境中指定的預設路由，而且對於大部分的環境而言，這些是您將需要的唯一路由。 
不過，您可能需要在特定情況下建立路由表，並新增一個或多個路由，例如：

-   透過內部部署網路連線到網際網路的強制通道。
-   在 Azure 環境中使用虛擬應用裝置。

在上述情況下，您必須建立一個路由表，並將使用者定義的路由新增到該路由表。 
此外，每個子網路只能與單一路由資料表產生關聯。 


子網路會依賴預設路由，直到路由表與子網路產生關聯為止。 
 


1.  使用者定義的路由
2.  BGP 路由 (使用 ExpressRoute 時)
3.  預設路由

>[AZURE.NOTE] 使用者定義的路由僅會套用至 Azure VM 和雲端服務。 例如，如果您想要在內部部署網路與 Azure 之間新增防火牆虛擬應用裝置，您必須為您的 Azure 路由表建立一個使用者定義的路由，此路由會將前往內部部署位址空間的所有流量轉送到虛擬應用裝置。 不過，來自內部部署位址空間的連入流量將會流經您的 VPN 閘道或直接到 Azure 環境的 ExpressRoute 電路，略過虛擬應用裝置。

### IP 轉送

如上所述，建立使用者定義的路由的主要原因之一是將流量轉送到虛擬應用裝置。 


此虛擬應用裝置 VM 必須能夠接收未定址到本身的連入流量。 


## 後續步驟

- 
- 如果您需要鎖定資源的存取權，可以使用管理鎖定。
- 
- 






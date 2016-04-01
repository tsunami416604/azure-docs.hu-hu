<properties
   pageTitle="使用 Azure 資源管理員和 PowerShell 設定 ExpressRoute 線路 | Microsoft Azure"
   description="本文將逐步引導您建立和佈建 ExpressRoute 線路。 本文也會示範如何檢查狀態、更新或刪除和取消佈建線路。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/04/2015"
   ms.author="cherylmc"/>

# 使用 Azure 資源管理員及 PowerShell 建立和修改 ExpressRoute 線路

> [AZURE.SELECTOR]
[PowerShell - 傳統](expressroute-howto-circuit-classic.md)
[PowerShell - 資源管理員](expressroute-howto-circuit-arm.md)

本文將逐步引導您使用 PowerShell Cmdlet 和 Azure 資源管理員部署模型建立 ExpressRoute 線路。 下列步驟也會示範如何檢查狀態、更新或刪除和取消佈建 ExpressRoute 線路。 

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)] 

## 組態必要條件

- 您需要最新版的 Azure PowerShell 模組 (版本 1.0 或更新版本)。 按照指示 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 頁面，如需如何設定電腦以使用 Azure PowerShell 模組的逐步指引。 
- 請確定您已檢閱 [必要條件](expressroute-prerequisites.md) 頁面和 [工作流程](expressroute-workflows.md) 之前開始設定] 頁面上。

## 建立和佈建 ExpressRoute 線路

1. **匯入 ExpressRoute 的 PowerShell 模組。**

    您必須安裝最新的 PowerShell 安裝程式，從 [PowerShell 組件庫](http://www.powershellgallery.com/) 匯 PowerShell 工作階段中的 Azure 資源管理員模組，以便開始使用 ExpressRoute cmdlet。 您必須以系統管理員身分執行 PowerShell。

        Install-Module AzureRM

        Install-AzureRM

    匯入已知語意版本範圍內所有的 AzureRM.* 模組

        Import-AzureRM

    您也可以只匯入已知語意版本範圍內選取的模組 
        
        Import-Module AzureRM.Network 

    登入您的帳戶

        Login-AzureRmAccount

    選取您想要建立 ExpressRoute 線路的訂用帳戶
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
            


2. **取得支援的提供者、位置和頻寬清單。**

    建立 ExpressRoute 線路之前，您需要有連線提供者、支援的位置和頻寬選項等清單。 PowerShell cmdlet *Get AzureRmExpressRouteServiceProvider* 傳回這項資訊，您將在稍後步驟中使用。

        PS C:\> Get-AzureRmExpressRouteServiceProvider

    請檢查是否列出您的連線服務提供者。 請記住下列事項，在您建立線路時會需要。
    
    - 名稱
    - PeeringLocations
    - BandwidthsOffered

    您現在已經準備好建立 ExpressRoute 線路。

        
3. **建立 ExpressRoute 線路。**

    若您在建立您的 ExpressRoute 線路之前還沒有資源群組，您必須先建立一個。 您可以執行下列命令來這麼做。

        New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"

    以下示範如何透過矽谷的 Equinix 建立 200 Mbps ExpressRoute 線路。 如果您使用不同的提供者和不同的設定，請在提出要求時替換成該資訊。

    以下是新服務金鑰的要求範例：

        New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

    請確定您指定正確的 SKU 層和 SKU 系列。
 
     - SKU 層會判斷 ExpressRoute 標準或 ExpressRoute 高階附加元件是否啟用。 您可以指定 *標準* 取得標準 SKU 或 *高階* premium 附加元件
     - SKU 系列決定計費類型。 您可以選取 *metereddata* 計量的資料計劃和 * unlimiteddata 「 無限制的資料計劃。 **注意 ︰** 不能建立循環之後變更付款類型。 

    
    The response will contain the service key. You can get detailed descriptions of all the parameters by running the following:

        get-help New-AzureRmExpressRouteCircuit -detailed 

4. **列出所有 ExpressRoute 線路。**

    您可以執行 *Get AzureRmExpressRouteCircuit* 命令，以取得一份所有的 ExpressRoute 電路的您所建立。

        #Getting service key
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    回應如下列範例所示：

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : NotProvisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []


    您可以擷取這項資訊在任何時候使用 *Get AzureRmExpressRouteCircuit* 指令程式。 執行呼叫時，若未指定任何參數，將會列出所有線路。 您的服務金鑰將列在 *ServiceKey* 欄位。

        Get-AzureRmExpressRouteCircuit

    回應如下列範例所示：

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : NotProvisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []



    您可以執行下列命令來取得所有參數的詳細描述：

        get-help Get-AzureRmExpressRouteCircuit -detailed 

5. **將金鑰傳送給連線提供者來佈建。**

    當您建立新的 ExpressRoute 線路時，線路會是下列狀態：
    
        ServiceProviderProvisioningState : NotProvisioned
        
        CircuitProvisioningState         : Enabled

     *ServiceProviderProvisioningState* 提供佈建服務提供者端和狀態的目前狀態的詳細資訊可在 Microsoft 端提供狀態。 ExpressRoute 線路必須處於下列狀態，才可供您使用。

        ServiceProviderProvisioningState : Provisioned
        
        CircuitProvisioningState         : Enabled

    當連線提供者正在為您啟用線路時，線路會處於下列狀態。 

        ServiceProviderProvisioningState : Provisioned
        
        Status                           : Enabled



5. **定期檢查線路金鑰的情況和狀態。**

    這樣可讓您知道提供者何時已啟用您的線路。 設定循環之後 *ServiceProviderProvisioningState* 將會顯示為 *已佈建* 如下列範例所示。

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    回應如下列範例所示：

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []

6. **建立路由設定。**
    
    請參閱 [ExpressRoute 電路的路由組態 （建立及修改電路對等互連）](expressroute-howto-routing-arm.md) 畫面來取得逐步指示。 

7. **將 VNet 連結到 ExpressRoute 線路。** 

    接下來，將 VNet 連結到 ExpressRoute 線路。 您可以使用 [此範本](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) 時使用 Azure 資源管理員部署模式。 我們目前正在進行 PowerShell 步驟。

##  取得 ExpressRoute 線路的狀態

您可以擷取這項資訊在任何時候使用 *Get AzureRmExpressRouteCircuit* 指令程式。 執行呼叫時，若未指定任何參數，將會列出所有線路。 

        Get-AzureRmExpressRouteCircuit

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []

您可以透過將資源群組名稱和線路名稱做為參數傳遞至呼叫，取得特定 ExpressRoute 線路的資訊。

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    The response will be something similar to the example below:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []

您可以執行下列命令來取得所有參數的詳細描述：

        get-help get-azurededicatedcircuit -detailed 

## 修改 ExpressRoute 線路

您可以修改 ExpressRoute 線路的某些屬性，而不會影響連線。 

您可以執行下列動作： 

- 在完全不停機的情況下，啟用或停用 ExpressRoute 線路的 ExpressRoute Premium 附加元件。
- 在完全不停機的情況下，增加 ExpressRoute 線路的頻寬。

請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md) 如需限制的詳細資訊。 

### 如何啟用 ExpressRoute Premium 附加元件

您可以使用下列 PowerShell Cmdlet，為現有的線路啟用 ExpressRoute Premium 程式碼片段：

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        $ckt.Sku.Name = "Premium"
        $ckt.sku.Name = "Premium_MeteredData"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
    
        
您的線路現在將啟用 ExpressRoute Premium 附加功能。 請注意，順利執行命令後，我們會開始向您收取進階附加元件功能的費用。

### 如何停用 ExpressRoute Premium 附加元件

您可以使用下列 PowerShell Cmdlet，為現有的線路停用 ExpressRoute Premium 附加元件：
    
        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
        
        $ckt.Sku.Tier = "Standard"
        $ckt.sku.Name = "Standard_MeteredData"
        
        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


您的線路現在已經停用進階附加元件。 

請注意，如果您使用的資源超出標準線路所允許的數量，這項作業可能會失敗。

- 從高階降級為標準之前，您必須確定連結至線路的虛擬網路數目小於 10。 如果您沒有這麼做，則更新要求將會失敗，將會以高階費率向您收費。
- 您必須取消連結其他地理政治區域中的所有虛擬網路。 如果您沒有這麼做，則更新要求將會失敗，將會以高階費率向您收費。
- 就私用對等而言，路由表必須是少於 4000 個路由。 如果路由表大小超過 4000 個路由，BGP 工作階段將會中斷，而且在通告的首碼數目降到 4000 以下之前不會重新啟用。


### 如何更新 ExpressRoute 線路頻寬

檢查 [ExpressRoute 常見問題集](expressroute-faqs.md) 頁面，以您的提供者支援的頻寬選項。 您可以挑選任何比現有線路規模還大的大小。 一旦決定需要的大小後，您可以使用下列命令來調整線路大小。

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        $ckt.ServiceProviderProperties.BandwidthInMbps = 1000

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

我們會在 Microsoft 端調整電路的大小。 您必須連絡連線提供者，將他們那邊的設定更新為符合這項變更。 請注意，從這個階段起，我們將開始計算更新頻寬選項的費用。

>[AZURE.IMPORTANT] 您不能減少不中斷地 ExpressRoute 電路的頻寬。 頻寬降級需要取消佈建 ExpressRoute 線路，然後重新佈建新的 ExpressRoute 線路。

## 刪除和佈建 ExpressRoute 線路

您可以執行下列命令來刪除 ExpressRoute 線路：

        Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"

請注意，您必須取消連結 ExpressRoute 的所有虛擬網路，此作業才會成功。 如果此作業失敗，請檢查您是否有任何虛擬網路連結至線路。

如果已啟用 ExpressRoute 電路服務提供者佈建狀態，狀態會移至 *停用* 從啟用狀態。 您必須與服務提供者一起合作，取消佈建他們那邊的線路。 我們將繼續保留資源並向您收取費用，直到線路服務提供者完成取消佈建並通知我們。

如果服務提供者已取消佈建電路 (服務提供者佈建狀態設定為 *未佈建*) 執行上述指令程式之前，我們將會取消佈建電路，並停止計費。 

## 後續步驟

- [設定路由](expressroute-howto-routing-arm.md)




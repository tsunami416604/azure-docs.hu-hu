<properties 
    pageTitle="使用 ExpressRoute 的網路組態詳細資料" 
    description="在連接至 ExpressRoute 循環之虛擬網路中執行 App Service 環境的網路組態詳細資料。" 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/23/2015" 
    ms.author="stefsch"/>   

# 使用 ExpressRoute 之 App Service 環境的網路組態詳細資料 

## 概觀 ##
客戶可以連線 [Azure ExpressRoute][ExpressRoute] 虛擬網路的基礎結構，因此將其內部部署網路延伸至 Azure 的電路。  App Service 環境可以建立這個子網路中 [虛擬網路][virtualnetwork] 基礎結構。  在 App Service 環境上執行的應用程式接著可以建立與後端資源的安全連線，而後端資源只能透過 ExpressRoute 連線來存取。  

**注意 ︰**  無法在"v2"的虛擬網路中建立 App Service 環境。  App Service 環境目前僅支援處理傳統「v1」虛擬網路。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## 需要的網路連線 ##
在連接至 ExpressRoute 的虛擬網路中，可能一開始會不符合 App Service 環境的一些網路連線需求。  App Service 環境需要下列所有項目，才能正確運作：


-  全球 Azure 儲存體端點的輸出網路連線。  這包括端點位於與 App Service 環境中，相同的區域，以及儲存體端點位於 **其他** Azure 區域。  在下列的 DNS 網域的 azure 儲存體端點解析 ︰ *.table.core.windows.net*, ，*windows.net*, ，*queue.core.windows.net* 和 *windows.net*。  
-  位於與 App Service 環境相同區域中的 SQL DB 端點的輸出網路連接。  SQl 資料庫端點解析下列網域下 ︰  *.database.windows.net*。
-  Azure 管理平面端點 (ASM 和 ARM 端點) 的輸出網路連線。  這包括兩者的輸出連線 *management.core.windows.net* 和 *management.azure.com*。 
-  輸出網路連線 *ocsp.msocsp.com*。  需要此連線才能支援 SSL 功能。
-  虛擬網路的 DNS 設定必須能夠解析前面幾點所提到的所有端點和網域。  如果無法解析這些端點，App Service 環境建立嘗試將會失敗，而且現有的 App Service 環境會標示為狀況不良。
-  如果 VPN 閘道的另一端有自訂 DNS 伺服器存在，則必須可從包含 App Service 環境的子網路連接該 DNS 伺服器。 
-  輸出網路路徑不可經過內部公司 Proxy，也不可使用強制通道傳送至內部部署。  這麼會變更來自 App Service 環境的輸出網路流量的有效 NAT 位址。  變更 App Service 環境之輸出網路流量的 NAT 位址會導致上述眾多端點的連接失敗。  這會導致 App Service 環境建立嘗試失敗，而之前狀況良好的 App Service 環境會標示為狀況不良。  
-  中所述，必須允許 App Service 環境的輸入網路存取必要的連接埠 [文章][requiredports]。

確定已針對虛擬網路設定及維護有效的 DNS 基礎結構，即可符合 DNS 需求。  如果 DNS 設定在建立 App Service 環境之後因為任何原因而變更，開發人員可以強制 App Service 環境挑選新的 DNS 組態。  在 App Service 環境管理刀鋒視窗頂端觸發輪流的環境重新開機，使用 「 重新啟動 」 圖示位於 [Azure 入口網站][NewPortal] 會挑選新的 DNS 組態的環境。

可以藉由設定符合輸入的網路存取的需求 [網路安全性群組][NetworkSecurityGroups] App Service 環境的子網路，以允許必要的存取，如本 [文章][requiredports]。

## 啟用 App Service 環境的輸出網路連線##
新建立的 ExpressRoute 循環預設會通告允許輸出網際網路連線的預設路由。  使用此組態，App Service 環境將可以連接至其他 Azure 端點。

不過，常見的客戶組態是定義其專屬預設路由 (0.0.0.0/0)，以強制輸出網際網路流量來替代透過內部部署方式流動。  此流量流程一定會中斷 App Service 環境，因為已透過內部部署方式封鎖輸出流量，或 NAT 至無法再使用各種 Azure 端點的一組無法辨識位址。

解決方法是在子網路上定義包含 App Service 環境的一 (或多個) 使用者定義路由 (UDR)。  UDR 會定義將使用的子網路特有路由，而非預設路由。

如果可能，建議使用下列設定：

- ExpressRoute 組態會通告 0.0.0.0/0 而且預設會使用強制通道將所有輸出流量傳送至內部部署。
- 已套用至包含 App Service 環境之子網路的 UDR 會使用網際網路的下一個躍點類型定義 0.0.0.0/0 (本文後面會提供其範例)。

這些步驟的合併效果是子網路層級 UDR 會優先於 ExpressRoute 強制通道，因而確保來自 App Service 環境的輸出網際網路存取。

**重要事項 ︰**  UDR 中定義的路由 **必須** 會不夠明確，優先於通告 ExpressRoute 組態的任何路由。  以下範例使用廣泛 0.0.0.0/0 位址範圍，因此使用更明確的位址範圍，有可能會不小心由路由通告所覆寫。

**非常重要 ︰**  ExpressRoute 組態不支援 App Service 環境， **不正確地跨-通告路由公用互連路徑從私用對等互連路徑**。  已設定公用對等互連的 ExpressRoute 組態，會收到來自 Microsoft 的一大組 Microsoft Azure IP 位址範圍的路由通告。  如果這些位址範圍在私人對等互連路徑上不正確地交叉通告，最後的結果會是來自 App Service 環境子網路的所有輸出網路封包都會不正確地使用強制通道傳送至客戶的內部部署網路基礎結構。  此網路流程將會破壞 App Service 環境。  此問題的解決方案是停止從公用對等互連路徑至私人對等互連路徑的交叉通告路由。

使用者定義路由的背景資訊可在此 [概觀][UDROverview]。  

建立及設定使用者定義的路由的詳細資訊可在此 [作法指南][UDRHowTo]。

## App Service 環境的範例 UDR 組態 ##

**必要條件**

1. 安裝最新的 Azure Powershell 從 [Azure 下載頁面][AzureDownloads] （年 6 月，2015年或更新版本）。  在 [命令列工具] 的 [Windows Powershell] 下，有一個 [安裝] 連結可安裝最新的 Powershell Cmdlet。

2. 建議您建立唯一的子網路，以專供 App Service 環境使用。  這可確保套用至子網路的 UDR 只會開啟 App Service 環境的輸出流量。
3. **重要**︰ 不要部署 App Service 環境，直到 **之後** 遵循下列設定步驟。  這可確保輸出網路連線可用，再嘗試部署 App Service 環境。

**步驟 1 ︰ 建立具名的路由表**

下列程式碼片段會在 West US Azure 區域中建立稱為 "DirectInternetRouteTable" 的路由表：

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**步驟 2 ︰ 建立路由表中的一個或多個路由**

您必須將一或多個路由新增至路由表中，才能啟用輸出網際網路存取。  

設定網際網路輸出存取的建議方法是定義 0.0.0.0/0 的路由，如下所示。
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

請記住 0.0.0.0/0 是廣泛的位址範圍，因此會被 ExpressRoute 所通告的更明確位址範圍所覆寫。  若要重新反覆執行先前的建議，具有 0.0.0.0/0 路由的 UDR 應搭配也只會通告 0.0.0.0/0 的 ExressRoute 組態使用。 

或者，您可以下載完整和更新的由 Azure 使用中的 CIDR 範圍清單。  包含所有的 Azure IP 位址範圍的 Xml 檔案是可從 [Microsoft 下載中心][DownloadCenterAddressRanges]。  

不過請注意，這些範圍會隨著時間改變，因此需要定期手動更新 UDR 才能保持同步。  此外，因為單一 UDR 有 100 個路由的上限，所以您需要「總結」Azure IP 位址範圍以符合 100 個路由的限制，請記住，UDR 定義的路由必須比 ExpressRoute 所通告的路由更明確。   


**步驟 3 ︰ 將包含 App Service 環境之子網路的路由表產生關聯**

最後一個設定步驟是將路由表與子網路關聯 App Service 環境的部署位置。  下列命令會建立 "DirectInternetRouteTable" 與最後將包含 App Service 環境之 "ASESubnet" 的關聯。

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**步驟 4 ︰ 最後步驟**

路由表繫結至子網路之後，建議您先測試並確認預期的效果。  例如，將虛擬機器部署至子網路，並確認：


- 輸出傳輸至 Azure-Azure 端點先前所述這篇文章是 **不** 到 ExpressRoute 循流動。  請務必確認此行為，因為如果來自子網路的輸出流量仍是使用強制通道傳送至內部部署，App Service 環境建立一律會失敗。 
- 已正確解析前面所提端點的所有 DNS 查閱。 

一旦確認上述步驟後，您必須刪除虛擬機器，因為在建立 App Service 環境時，子網路必須是「空的」。
 
然後繼續建立 App Service 環境！

## 開始使用

若要開始使用 App Service 環境，請參閱 [App Service 環境簡介][IntroToAppServiceEnvironment]

如需 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][AzureAppService]。

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]:  https://portal.azure.com
 

<!-- IMAGES -->



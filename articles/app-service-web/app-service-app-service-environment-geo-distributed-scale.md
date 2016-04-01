<properties 
    pageTitle="App Service 環境的異地分散調整" 
    description="了解如何透過流量管理員和 App Service 環境，使用異地分散功能水平調整應用程式。" 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/08/2015" 
    ms.author="stefsch"/>   

# App Service 環境的異地分散調整

## 概觀 ##
需要極高延展性的應用程式案例，可能會超過單一應用程式部署可用的運算資源容量。  例如，投票應用程式、體育活動及電視娛樂活動，都屬於需要極高延展性的案例。 只要對單一區域內和跨區域的多個應用程式部署進行應用程式的水平相應放大，即可達到高延展性需求，以處理極高的負載需求。

App Service 環境是水平相應放大的理想平台。  在選取可支援已知要求率的 App Service 環境組態後，開發人員即可透過「千篇一律」的方式部署其他 App Service 環境，以獲得所需的尖峰負載容量。

例如，假設在 App Service 環境組態上執行的應用程式已經過測試，每秒可處理 20K 個要求 (RPS)。  如果所需的尖峰負載容量是 100K RPS，則可以建立並設定五 (5) 個 App Service 環境，以確保應用程式能夠處理最大預測負載。

由於客戶通常會使用自訂 (或虛名) 網域存取應用程式，因此開發人員必須要有將應用程式要求分散到所有 App Service 環境執行個體的方法。  若要這麼做的好方法是要解析自訂網域使用 [Azure 流量管理員設定檔][AzureTrafficManagerProfile]。  流量管理員設定檔可設定為指向所有個別的 App Service 環境。  流量管理員會根據流量管理員設定檔中的負載平衡設定，自動將客戶分散到所有的 App Service 環境間。  無論所有的 App Service 環境是部署在單一 Azure 區域，還是跨多個 Azure 區域部署於世界各地，此方法都可運作。

此外，由於客戶是透過虛名網域存取應用程式的，因此無從得知執行應用程式的 App Service 環境數目。  因此，開發人員可以根據觀察到的流量負載，快速且輕鬆地新增和移除 App Service 環境。

下方的概念圖表說明在單一區域內以水平方式相應放大至三個 App Service 環境的應用程式。

![概念性架構][ConceptualArchitecture] 

本主題的其餘部分將逐步解說使用多個 App Service 環境為範例應用程式設定分散式拓撲所需的步驟。

## 規劃拓樸 ##
在建置分散式應用程式的使用量之前，最好先備妥幾項資訊。

- **應用程式的自訂網域 ︰**  客戶用來存取應用程式的自訂網域名稱為何？  範例應用程式的自訂網域名稱是 *www.scalableasedemo.com*
- **流量管理員網域 ︰**  的網域名稱必須在建立時選擇 [Azure 流量管理員設定檔][AzureTrafficManagerProfile]。  此名稱將會結合 *trafficmanager.net* 尾碼來登錄管理流量管理員網域項目。  範例應用程式中，選擇的名稱是 *可擴充 ase 示範*。  如此一來管理流量管理員的完整網域名稱是 *可擴充 ase demo.trafficmanager.net*。
- **調整應用程式使用量的策略 ︰**  應用程式使用量分散到多個應用程式服務環境，在單一區域中？  多個區域嗎？  兩種方法混合搭配使用嗎？  決策依據應來自於客戶流量的來源位置，以及其餘應用程式的支援後端基礎結構的可調整性。  例如，使用 100%的無狀態應用程式，應用程式可以可大幅調整每個 Azure 區域，乘以跨越多個 Azure 區域部署 App Service 環境中使用多個 App Service 環境的組合。  由於有 15 個以上的公用 Azure 區域可供選擇，客戶將可真正建置全球性超高延展性的應用程式使用量。  在本文所使用的範例應用程式中，有三個 App Service 環境建立在單一 Azure 區域 (美國中南部) 中。
- **App Service 環境的命名慣例 ︰**  每個 App Service 環境需要唯一的名稱。  有兩個或更多 App Service 環境時，命名慣例將有助於識別每個 App Service 環境。  範例應用程式中使用簡單的命名慣例。  三個 App Service 環境的名稱是 *fe1ase*, ，*fe2ase*, ，和 *fe3ase*。
- **應用程式命名慣例 ︰**  將部署的應用程式的多個執行個體，因為需要部署應用程式的每個執行個體的名稱。  有一項鮮為人知、但非常方便的 App Service 環境功能，是多個 App Service 環境可以使用相同的應用程式名稱。  由於每個 App Service 環境都有唯一的網域尾碼，開發人員可以選擇在每個環境中重複使用相同的應用程式名稱。  例如開發人員可以讓應用程式名稱，如下所示 ︰  *myapp.foo1.p.azurewebsites.net*, ，*myapp.foo2.p.azurewebsites.net*, ，*myapp.foo3.p.azurewebsites.net*, 等等...但範例應用程式的每個應用程式執行個體也都有唯一名稱。  使用的應用程式執行個體名稱 *webfrontend1*, ，*webfrontend2*, ，和 *webfrontend3*。


## 設定流量管理員設定檔 ##
一旦應用程式的多個執行個體部署在多個應用程式服務環境上，個別的應用程式執行個體可以註冊使用流量管理員。  範例應用程式流量管理員設定檔用於 *可擴充 ase demo.trafficmanager.net* ，可以將客戶路由到任何下列部署之應用程式執行個體 ︰

- **webfrontend1.fe1ase.p.azurewebsites.net:**  部署在第一個 App Service 環境的範例應用程式執行個體。
- **webfrontend2.fe2ase.p.azurewebsites.net:**  部署在第二個 App Service 環境的範例應用程式執行個體。
- **webfrontend3.fe3ase.p.azurewebsites.net:**  部署在第三個 App Service 環境的範例應用程式執行個體。

最簡單的方法，以註冊多個 Azure 應用程式服務端點，在所有執行 **相同** Azure 區域，是使用預覽 Powershell [Azure 資源管理員 (ARM) 流量管理員支援][ARMTrafficManager]。  

第一個步驟是建立 Azure 流量管理員設定檔。  下列程式碼說明如何為範例應用程式建立設定檔：

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

請注意如何 *RelativeDnsName* 參數已設為 *可擴充 ase 示範*。  這是如何將網域名稱 *可擴充 ase demo.trafficmanager.net* 建立並與流量管理員設定檔相關聯。

 *TrafficRoutingMethod* 參數定義的負載平衡的原則的流量管理員用來判斷如何負載分散於客戶的所有可用的端點。  在此範例中 *加權* 選擇的方法。  這會使客戶要求根據與每個端點相關聯的相對加權，分散到所有已註冊的應用程式端點間。 

建立設定檔，每個應用程式執行個體加入做為設定檔 *外部端點*。  下列程式碼說明新增至設定檔的三個應用程式執行個體的 URL。

    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target webfrontend1.fe1ase.p.azurewebsites.net –EndpointStatus Enabled –Weight 10
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target webfrontend2.fe2ase.p.azurewebsites.net –EndpointStatus Enabled –Weight 10
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target webfrontend3.fe3ase.p.azurewebsites.net –EndpointStatus Enabled –Weight 10
    
    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

請注意若要一次呼叫 *Add-azuretrafficmanagerendpointconfig* 每個個別的應用程式執行個體。   *目標* 在每個 Powershell 命令的參數指向每三個部署的應用程式執行個體的完整的網域名稱 (FQDN)。 不同的 Fqdn 是將用來逐步 DNS CNAME 鏈結值 *可擴充 ase demo.trafficmanager.net* 為了流量負載分散在流量管理員設定檔中註冊的所有端點。

所有三個端點使用相同的值 (10) *權數* 參數。  這會使流量管理員將客戶要求較平均地分散在所有的三個應用程式執行個體間。 

*注意 ︰*  ARM 流量管理員支援目前為預覽狀態，因為必須設定 Azure 應用程式服務端點 *類型* 參數 *ExternalEndpoints*。  Azure App Service 端點日後將會以端點類型的形式受到 ARM 變異流量管理員的原生支援。

## 將應用程式的自訂網域指向流量管理員網域 ##
最後一個必要步驟是將應用程式的自訂網域指向流量管理員網域。  這表示範例應用程式指向 *www.scalableasedemo.com* 在 *可擴充 ase demo.trafficmanager.net*。  此步驟必須以管理自訂網域的網域註冊機構來完成。  

若使用註冊機構的網域管理工具，必須建立一個將自訂網域指向流量管理員網域的 CNAME 記錄。  下圖顯示此 CNAME 組態的範例：

![自訂網域的 CNAME][CNAMEforCustomDomain] 

雖然本主題並未說明，但請記住，每個應用程式執行個體也都需要註冊其自訂網域。  否則，在對應用程式執行個體發出要求時，如果應用程式並未註冊應用程式的自訂網域，要求將會失敗。  

在此範例中的自訂網域是 *www.scalableasedemo.com*, ，且每個應用程式執行個體具有與它相關聯的自訂網域。

![自訂網域][CustomDomain] 

註冊自訂網域與 Azure App Service 應用程式的概述，請參閱下列文件，在 [註冊自訂網域][RegisterCustomDomain]。

## 嘗試使用分散式拓撲 ##
流量管理員及 DNS 組態的最終結果是，要求 *www.scalableasedemo.com* 將會流經以下順序 ︰

1. 瀏覽器或裝置可讓 DNS 查閱 *www.scalableasedemo.com*
2. 網域註冊機構上的 CNAME 項目使 DNS 查閱重新導向至 Azure 流量管理員。
3. DNS 查閱進行 *可擴充 ase demo.trafficmanager.net* Azure 流量管理員 DNS 伺服器的其中之一。
4. 根據負載平衡原則 ( *TrafficRoutingMethod* 建立流量管理員設定檔時，請使用稍早的參數)，流量管理員將會選取其中一個設定的端點，並傳回至瀏覽器或裝置的 FQDN，該端點。
5.  由於端點的 FQDN 是在 App Service 環境上執行的應用程式執行個體的 URL，因此瀏覽器或裝置會要求 Microsoft Azure DNS 伺服器將 FQDN 解析為 IP 位址。 
6. 瀏覽器或裝置會將 HTTP/S 要求傳送至此 IP 位址。  
7. 要求會送達在其中一個 App Service 環境上執行的應用程式執行個體之一。

下列主控台圖片顯示範例應用程式自訂網域的 DNS 查閱；該網域已成功解析為在三個範例 App Service 環境之一 (在此案例中圍三個 App Service 環境中的第二個) 上執行的應用程式執行個體：

![DNS 查閱][DNSLookup] 

## 其他連結和資訊 ##
文件預覽 Powershell [Azure 資源管理員 (ARM) 流量管理員支援][ARMTrafficManager]。  

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]:  https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]:  https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]:  https://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 



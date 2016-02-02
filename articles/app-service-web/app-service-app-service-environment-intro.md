<properties 
    pageTitle="App Service 環境簡介" 
    description="了解可提供安全、VNet 聯結、專用延展單位的 App Service 環境功能，以便執行您所有的應用程式。" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/06/2015"
    ms.author="stefsch"/>


# App Service 環境簡介

## 概觀

App Service 環境是 [高階 ][premiumtier] 服務提供完全隔離和專用的環境，以便安全地高大規模執行 Azure 應用程式服務應用程式的 Azure 應用程式服務方案選項包括 [Web 應用程式 ][webapps], ，[行動應用程式 ][mobileapps], ，和 [API 應用程式 ][apiapps]。

適合應用程式工作負載的 App Service 環境需要：

- 非常高的延展性
- 隔離和安全的網路存取

客戶可以在單一 Azure 區域，以及跨多個 Azure 區域中建立多個 App Service 環境。 這使得 App Service 環境很適合用來水平調整無狀態應用程式層的規模，以支援高 RPS 工作負載。

App Service 環境已經過隔離，可執行只有單一客戶的應用程式，且一律會部署到虛擬網路。 客戶對於輸入和輸出的應用程式網路流量都有更細微的控制，且應用程式可以透過虛擬網路建立與內部部署公司資源的高速安全連線。

如需 App Service 環境如何啟用高延展性和安全性的概觀網路存取，請參閱 [AzureCon 深入了解 ][azurecondeepdive] 在 App Service 環境!

深入探討在水平縮放使用多個應用程式服務環境參閱文件如何安裝 [地理位置分散的應用程式使用量 ][geodistributedappfootprint]。

顯示 AzureCon 深入探討的安全性架構的設定方式，請參閱文件上實作 [分層安全性架構](app-service-app-service-environment-layered-security.md) 使用 App Service 環境。

在 App Service 環境中執行之應用程式的存取權可能會受到 Web 應用程式防火牆 (WAF) 等上游裝置的管制。 上的文件 [App Service 環境中設定的 WAF](app-service-app-service-environment-web-application-firewall.md) 涵蓋這種情況。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 專用計算資源

App Service 環境中的所有計算資源皆專屬於單一訂用帳戶，且 App Service 環境可以設定最多五十 (50) 個計算資源，讓單一應用程式獨佔使用。

App Service 環境是由前端計算資源集區，以及一到三個背景工作計算資源集區所組成。

前端集區包含負責處理 SSL 終止以及 App Service 環境中應用程式要求的自動負載平衡的計算資源。

每個背景工作集區包含計算資源配置給 [應用程式服務計劃 ][appserviceplan], ，其中又包含一或多個 Azure App Service 應用程式。 因為 App Service 環境中可有多達三個不同的背景工作集區，所以您有彈性可為每個背景工作集區選擇不同的計算資源。

比方說，您可以針對主要用於開發或測試應用程式的 App Service 方案，建立一個計算資源較不強大的背景工作集區。 第二個 (或甚至第三個) 背景工作集區可以使用比較強大的運算資源，以供 App Service 方案執行生產應用程式。

如需前端和背景工作集區可用計算資源數量的詳細資訊，請參閱 [如何設定 App Service 環境 ][howtoconfigureanappserviceenvironment]。

如需 App Service 環境中支援的可用計算資源大小的詳細資訊，請參閱 [App Service 定價 ][appservicepricing] 頁面，並檢閱 Premium 定價層中 App Service 環境的可用選項。

## 虛擬網路支援

App Service 環境可以建立於預先存在的地區傳統"v1"虛擬網路或新的地區傳統"v1"虛擬網路 ([虛擬網路 ][moreinfoonvirtualnetworks])。 因為 App Service 環境一律存在於區域虛擬網路，而更精確來說是在區域虛擬網路的子網路中，所以您可以運用虛擬網路的安全性功能來控制傳入和傳出網路通訊。

您可以使用 [網路安全性群組 ][networksecuritygroups] 將 App Service 環境所在的子網路的傳入的網路通訊限制。 這可讓您在上游裝置和服務 (例如 Ｗeb 應用程式防火牆和網路 SaaS 提供者) 背後執行應用程式。

應用程式也經常需要存取公司資源，例如內部資料庫和 Web 服務。 常見的方法是讓這些端點僅可用於在 Azure 虛擬網路中傳送的內部網路流量。 一旦 App Service 環境加入與內部服務相同的虛擬網路，在環境中執行的應用程式可以存取，其中包括可透過連線端點 [站台對站 ][sitetosite] 和 [Azure ExpressRoute ][expressroute] 連線。

如需 App Service 環境的虛擬網路和內部網路的運作方式的詳細資訊，請參閱下列文件上 [網路架構 ][networkarchitectureoverview], ，[控制輸入流量 ][controllinginboundtraffic], ，和 [安全地連接到後端 ][securelyconnectingtobackends]。

**附註:**  無法在"v2"的虛擬網路中建立 App Service 環境。

## 開始使用

若要開始使用 App Service 環境，請參閱 [如何建立 App Service 環境 ][howtocreateanappserviceenvironment]

如需 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service ][azureappservice]。

如需 App Service 環境網路架構的概觀，請參閱 [網路架構概觀 ][networkarchitectureoverview] 文件。

如需透過 ExpressRoute，使用 App Service 環境的詳細資訊，請參閱下列文章 [Express Route 與 App Service 環境 ][networkconfigdetailsforexpressroute]。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]









[premiumtier]: http://azure.microsoft.com/pricing/details/app-service/ 
[moreinfoonvirtualnetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/ 
[appserviceplan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/ 
[howtocreateanappserviceenvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/ 
[azureappservice]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[webapps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/ 
[mobileapps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/ 
[apiapps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/ 
[logicapps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/ 
[azurecondeepdive]: https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/ 
[geodistributedappfootprint]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/ 
[networksecuritygroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[sitetosite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/ 
[expressroute]: http://azure.microsoft.com/services/expressroute/ 
[howtoconfigureanappserviceenvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/ 
[controllinginboundtraffic]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/ 
[securelyconnectingtobackends]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/ 
[networkarchitectureoverview]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/ 
[networkconfigdetailsforexpressroute]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/ 
[appservicepricing]: http://azure.microsoft.com/pricing/details/app-service/ 


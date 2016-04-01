<properties
    pageTitle="Web 應用程式概觀"
    description="深入了解應用程式服務 Web 應用程式"
    services="app-service\web"
    documentationCenter=""
    authors="jaime-espinosa"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2015"
    ms.author="jaime.espinosa"/>


#Web 應用程式概觀

[應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714) 是一組豐富的功能帶入 web、 行動和整合案例的專業開發人員的完全受管理平台。 使用 Azure 應用程式服務，快速建立和部署隨著企業延展的任務關鍵 Web 應用程式。

運用 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 若要使用的語言與您所知且相依的架構，部署至 Azure 雲端快速應用程式，並持續改善您的程式碼，而不需要再擔心基礎結構。

![Web Marketplace](./media/app-service-web-overview/marketplace.png)

## 不只是網站##

現代企業使用比以前更複雜的方式與客戶互動。 所有類型的公司都會將公司網站空間視為其業務的重要部分、其業務方案中的主要元件。 為因應此重要性，企業正尋求能提供它們具有彈性、安全性與延展性的平台。 此外，它們需要能連結至其現有商務系統的能力，以在全球快速部署新的程式碼和運轉執行個體。 使用 Azure 應用程式服務和 Web 應用程式，組織可以快速並以符合成本效益方式來取悅其客戶。

## 為什麼是 Web 應用程式？ ##

Azure 應用程式服務 Web 應用程式是完全受管理的平台，可讓您快速建置、部署和延展企業級 Web 應用程式。 專注於應用程式碼，並讓 Azure 負責延展並安全地執行基礎結構。 Web 應用程式：

- **熟悉並快速** -使用現有技能，以您偏好的語言、 架構和 IDE 中的程式碼。 只要按幾下，就可以將版本控制、更新、單一登入、識別代理人、隔離儲存體和效能監視加入現有的 Web 應用程式。  存取豐富的組件庫，以用做加速開發的建置組塊。 使用連續整合、即時網站偵錯和業界領先 Visual Studio IDE 這類尖端功能，體驗前所未有的開發人員產能。
- **企業級** -Web 應用程式設計來建置和裝載安全的關鍵任務應用程式。 建置安全地連線到內部部署資源的 Active Directory 整合商務應用程式，然後將它們裝載在 ISO、SOC2 和 PCI 相容的安全雲端平台。 同時享有企業層級 SLA。
- **全球規模** -Web 應用程式已最佳化對通用資料中心基礎結構提供可用性和自動調整。 依需求輕鬆地向上延展或向下延展應用程式。 運用位於和跨不同地理區域提供的高可用性。 在多個位置中複寫資料並裝載服務十分快速並簡單，只要按一下滑鼠，就可以擴充到新的區域和地理位置。  

## Web 應用程式概念 ##

- **Web 應用程式庫** -從不斷成長的現有 web 應用程式範本清單中選取。 使用單鍵安裝封裝 (例如 Wordpress、Joomla 和 Drupal)，充分運用 OSS 應用程式社群。 運用 .NET MVC、Django 和 CakePHP 這類架構，就能開始應用程式開發程序。
- **自動調整** -Web 應用程式可讓您快速地向上延展或向下延展以處理任何內送的客戶載入。 手動選取 VM 的數目和大小，或設定自動調整以根據負載或排程來調整您的伺服器。
- **連續整合** -設定連續整合和部署工作流程與 VSTS、 GitHub、 TeamCity、 Hudson 或 BitBucket – 讓您自動建置、 測試和部署 web 應用程式的每個成功的程式碼簽入或整合測試。
- **部署位置** -實作 [預備部署] [Slots] 確認等同於在 Azure App Service 中的生產 web 應用程式的預先生產環境中的程式碼。 符合時，執行交換作業，以發行零停機時間的應用程式新版本。 
- **在生產環境中測試** -將分段部署帶到下一個層級，並執行 A / B 測試以確認新的即時流量的可設定部分的程式碼。 
- **Webjobs** -執行任何程式或 Web 應用程式 Vm 上的指令碼。 在多個 VM 上，持續或依排程和規模執行工作。 使用 Azure [WebJobs SDK][Webjobs] 與 Azure 儲存體或服務匯流排整合。
- **混合式連線** -存取內部資料使用 [混合式連線](../integration-hybrid-connection-overview.md) 和 [VNET](../app-service-web/web-sites-integrate-with-vnet.md)。

## 開始使用 ##
若要開始使用 Web 應用程式，請遵循 [建立 ASP.NET web 應用程式] [create] 教學課程。

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][appservice]。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: web-sites-dotnet-get-started.md
[Webjobs]: websites-dotnet-webjobs-sdk-get-started.md
[Slots]: web-sites-staged-publishing.md

 



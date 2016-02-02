<properties 
    pageTitle="在 Azure App Service Web Apps 上建立數位行銷活動" 
    description="本指南提供如何使用 Azure 應用程式服務 Web 應用程式來建立數位行銷活動的技術概觀。這包括部署、社交媒體整合、調整策略和監視。" 
    editor="jimbe" 
    manager="wpickett" 
    authors="cephalin" 
    services="app-service\web" 
    documentationCenter=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/10/2015" 
    ms.author="cephalin"/>


# 在 Azure App Service Web Apps 上建立數位行銷活動

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps 是數位行銷活動的絕佳選擇。 數位行銷活動通常只有短暫週期，目的是推廣短期的行銷目標。 有兩個需要考量的主要案例。 在第一個案例中，協力廠商行銷公司在促銷期間為其客戶建立及管理行銷活動。 第二個案例則涉及行銷公司建立數位行銷活動資源擁有權，然後將該擁有權轉移給其客戶。 客戶接著便可自行執行及管理數位行銷活動。 為這兩種案例完全相符。
>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

以下是使用 Azure 應用程式服務 Web 應用程式進行全域、多管道之數位行銷活動的範例。 此範例示範不需要使用複雜的技術，只要組合應用程式服務 Web 應用程式和其他服務便能完成的工作。 **按一下拓樸中的項目以讀取詳細資訊。**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>
> [AZURE.NOTE]
> 本指南說明在 Azure 應用程式服務 Web 應用程式中執行數位行銷活動最常見的區域和工作。 不過，還有其他可在應用程式服務 Web 應用程式中實作的常見解決方案。 若要檢閱這些解決方案，請參閱其他指南 [全域網站空間](web-sites-global-web-presence-solution-overview.md) 和 [商務應用程式](web-sites-business-application-solution-overview.md)。

## 從頭開始建立或沿用現有資產

從組件庫中受歡迎的 CMS 快速建立新的 Web 應用程式，或在應用程式服務 Web 應用程式中沿用現有各種不同語言與架構的 Web 資產。

Azure Marketplace 提供受歡迎網站內容管理系統 (CMS)，例如 Orchard、 Umbraco、 Drupal 和 [WordPress] 範本。 您可以使用喜愛的 CMS 風格來建立 Web 應用程式。 您可以選擇的各種資料庫後端，以符合您的需求，包括 [Azure SQL Database] 和 [MySQL]。

您現有的 Web 資產 (無論是 .NET、PHP、Java、Node.js 或 Python) 都可以在 Web Apps 上執行。 您可以將它們移至 Web 應用程式使用熟悉的 [FTP] 工具。 如果您經常建立數位行銷活動，您可能在原始檔控制管理系統已經有 Web 資產。 您可以直接從受歡迎的原始檔控制選項，例如 [Visual Studio]，部署至 Web 應用程式 [Visual Studio Team Services]，和 [Git]-本機、 GitHub、 BitBucket、 DropBox、 Mercurial 等...

## 維持敏捷性

透過直接從您現有的原始檔控制持續發佈，以及在應用程式服務 Web 應用程式中執行 A/B 測試，以維持敏捷性。

在計劃、 原型和初期開發 web 應用程式，您和您的客戶可以查看行銷活動應用程式的實際運作版本之前，先透過 [部署到預備位置] 的 web 應用程式。 透過整合原始檔控制與應用程式服務 Web 應用程式，您可以 [持續發行] 到預備位置，並準備好時切換到實際執行環境沒有停機時間。

此外，規劃的作用中的 web 應用程式的變更時，您可以輕鬆地 [執行 A / B 測試] 功能針對提議的更新，在生產環境中使用測試並分析實際的使用者行為，幫助您做出明智的應用程式設計決策。


## 社交網路化

透過向受歡迎的提供者 (例如 Facebook 與 Twitter) 驗證，應用程式服務 Web 應用程式中的數位行銷活動可以和社交媒體整合。 如需 ASP.NET 應用程式使用這個方法的範例，請參閱 [使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式和部署至 Azure App Service]。

此外，每個社交媒體網站通常會提供透過 .NET 和許多其他架構來與它整合的資訊。

## 使用多媒體並觸達所有裝置

使用其他 Azure 服務豐富您的數位行銷活動，例如：

-  上傳和串流處理視訊全域使用 [Azure 媒體服務]
-  傳送電子郵件給使用者的 [Azure Marketplace 中的 SendGrid 服務]
-  在 Windows、 iOS 和 Android 的裝置，使用 [行動服務] 上建立空間
-  將推播通知傳送至數百萬個裝置，並在 [通知中心]

## 邁向全球

使用 Azure 流量管理員來為遍佈全球的地區網站提供服務，以及使用 Azure CDN 快如閃電地提供內容。

若要全域客客戶提供服務，使用 [Azure 流量管理員] 來路由站台可提供最佳效能的地區網站訪客。 或者，您可以將網站負載平均分配給多個地區的多個 Web 應用程式。

閃電將靜態內容使用者全域的 [與 Azure CDN 整合 web 應用程式]。 Azure CDN 會在 [CDN 節點] 的靜態內容快取最接近使用者，以便有效減少延遲和 web 應用程式的連線。

## 最佳化

透過使用自動調整規模自動調整、使用 Azure Redis Cache 執行快取、使用 WebJobs 執行背景工作，以及使用 Azure 流量管理員維持高可用性，以最佳化您的 Web 應用程式。

[相應增加和放大] 應用程式服務 Web 應用程式的能力非常適合無法預期的工作負載，這正是數位行銷活動的情況。 您以手動方式透過 web 應用程式相應 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715), ，以程式設計方式透過 [服務管理 API] 或 [PowerShell 指令碼]，或是自動調整功能。 在「標準」****層中，「自動調整規模」可讓您依據 CPU 使用量自動相應放大 Web 應用程式。 這項功能只會在必要時依據使用者活動向外調整 Web 應用程式，因此可協助您達到最大敏捷性，同時將成本降至最低。 如需最佳做法，請參閱 [Troy Hunt] 的 [我了解快速調整與 Azure web 應用程式的 10 件事]。

加速您 web 應用程式的 [Azure Redis 快取] 回應速度。 您可以使用它來快取後端資料庫中的資料和其他項目，例如 [ASP.NET 工作階段狀態] 和 [輸出快取]。

使用 [Azure 流量管理員] 來維持 Web 應用程式的高可用性。 使用「容錯移轉」****方法時，若主要網站發生問題，流量管理員會自動將流量路由至次要網站。

## 監視和分析

使用 Azure 或協力廠商工具，掌握關於您 Web 應用程式效能的最新資訊。 接收重要 Web 應用程式事件的警示。 使用 Application Insight 或使用 HDInsight 的 Web 記錄分析，取得使用者深入觀點。

取得 web 應用程式的刀鋒視窗中的 [快速概覽] 的 web 應用程式的目前效能標準與資源配額 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)。您的應用程式的可用性、 效能和使用方式的 360 度檢視，使用 [Azure Application Insights]，讓您快速和強大的疑難排解、 診斷和使用方式的深入觀點。或者，使用像是 [New Relic] 協力廠商工具提供的進階監視 web 應用程式的資料。

在**標準**層中，每當您的 Web 應用程式沒有回應時，監視 Web 應用程式回應便會收到電子郵件通知。 如需詳細資訊，請參閱 [如何: 接收警示通知及管理在 Azure 中的警示規則]。

## 其他資源

- [App Service Web Apps 文件](/services/app-service/web/)
- [Azure App Service Web Apps 的學習規劃](websites-learning-map.md)
- [Azure Web 部落格](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


[azure app service]: /services/app-service/web/ 
[wordpress]: web-sites-php-web-site-gallery.md 
[mysql]: web-sites-php-mysql-deploy-use-git.md 
[azure sql database]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md 
[ftp]: web-sites-deploy.md#ftp 
[visual studio]: web-sites-dotnet-get-started.md 
[visual studio team services]: ../cloud-services-continuous-delivery-use-vso.md 
[git]: web-sites-publish-source-control.md 
[deploying to a staging slot]: web-sites-staged-publishing.md 
[continuously publish]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/ 
[run a/b tests]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx 
[create an asp.net mvc app with auth and sql db and deploy to azure app service]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md 
[azure media services]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx 
[sendgrid service in azure marketplace]: sendgrid-dotnet-how-to-send-email.md 
[mobile services]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md 
[notification hub]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md 
[azure traffic manager]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx 
[integrating your web app with azure cdn]: cdn-websites-with-cdn.md 
[cdn node]: https://msdn.microsoft.com/library/azure/gg680302.aspx 
[scale up and out]: /manage/services/web-sites/how-to-scale-websites/ 
[azure management portal]: http://manage.windowsazure.com/ 
[service management api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx 
[powershell scripting]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx 
[troy hunt]: https://twitter.com/troyhunt 
[10 things i learned about rapidly scaling web apps with azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html 
[azure redis cache]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/ 
[asp.net session state]: https://msdn.microsoft.com/library/azure/dn690522.aspx 
[output cache]: https://msdn.microsoft.com/library/azure/dn798898.aspx 
[quick glance]: /manage/services/web-sites/how-to-monitor-websites/ 
[azure application insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx 
[new relic]: /develop/net/how-to-guides/new-relic/ 
[how to: receive alert notifications and manage alert rules in azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx 
[gitstaging]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites 


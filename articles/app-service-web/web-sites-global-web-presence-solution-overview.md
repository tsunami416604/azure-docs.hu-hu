<properties 
    pageTitle="在 Azure App Service Web Apps 上建立全域網站空間" 
    description="本指南提供技術概觀，說明如何在 Azure App Service Web Apps 上託管您的組織的 (.COM) 網站。 這包括部署、自訂網域、SSL 和監視。" 
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


# 在 Azure App Service Web Apps 上建立全域網站空間

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web 應用程式都有您需要建立.COM 網站全域網站空間的所有功能。 無論組織的規模大小，您都需要強固、安全且可擴充的平台，以促進您的業務、品牌認知以及客戶通訊。 App Service Web Apps 藉由 Microsoft 支援的營運持續性，協助維持企業的品牌與識別。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

以下是在 App Service Web Apps 上執行的.COM 網站範例。 此範例示範不需要使用複雜的技術，只要組合 Web Apps 和其他服務便能完成的工作。 **若要進一步了解拓樸中的項目上按一下而已。** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]
> 本指南說明在 Azure App Service Web Apps 中執行公用端 .COM 網站最常見的區域和工作。 不過，還有其他可在 Azure App Service Web Apps 中實作的常見解決方案。 若要檢閱這些解決方案，請參閱其他指南 [數位行銷活動](web-sites-digital-marketing-application-solution-overview.md) 和 [商務應用程式](web-sites-business-application-solution-overview.md)。

## 從頭開始建立或沿用現有資產

從組件庫中受歡迎的 CMS 快速建立新網站，或在 App Service Web 應用程式中沿用現有各種不同語言與架構的 Web 資產。

Azure Marketplace 提供受歡迎的網站的範本內容的管理系統 」 (CMS)，例如 Orchard、 Umbraco、 Drupal 和 [WordPress]。 您可以使用喜愛的 CMS 風格來建立 Web 應用程式。 您可以選擇各種資料庫後端以符合您的需求，包括 [Azure SQL Database] 和 [MySQL]。

您現有的 Web 資產 (無論是 .NET、PHP、Java、Node.js 或 Python) 都可以在 App Service Web 應用程式上執行。 您可以將它們移至 Web 應用程式使用熟悉 [FTP] 工具或原始檔控制管理系統。 Web 應用程式支援從受歡迎的原始檔控制選項直接發行，例如 [Visual Studio], ，[Visual Studio Team Services], ，和 [Git] -本機、 GitHub、 BitBucket、 DropBox、 Mercurial 等...

## 可靠地發行

從現有的原始檔控制系統並即時測試內容，持續地直接發行，可靠地發行網站。 

在計劃、 原型和站台的早期開發，您可以查看網站的實際運作版本之前，先由 [deploying to a staging slot] 您的網站上的應用程式服務 Web 應用程式。 透過整合原始檔控制與 Web 應用程式，您可以 [continuously publish] 到預備位置，並交換至生產環境沒有停機時間，當您準備好要執行這項操作。 如果生產網站發生問題，您也可以立即換回舊版的網站。 

此外，規劃即時網站的變更時，您可以輕鬆地 [run A/B tests] 功能針對提議的更新，在生產環境中使用測試並分析實際的使用者行為，幫助您做出明智的網站設計決策。

## 品牌與安全

免費使用 App Service Web Apps 網域或對應至註冊的網域名稱，然後利用 CA 簽署的 SSL 憑證保護品牌安全。

 **\*.Azurewebsites.net** 網域是免費，當您在 Web 應用程式上執行您的網站。 或者，您可以將網站對應至 [custom domain] -例如 contoso.com 從任何 DNS 登錄，例如 GoDaddy 中取得。

如果您收集任何使用者資訊、 執行電子商務或管理任何機密資料，您可以保護品牌信譽與客戶 [HTTPS]。  **\*.Azurewebsites.net** 網域名稱已經隨附 SSL 憑證，以及如果您使用自訂網域，您可以將您的 SSL 憑證為其 Web 應用程式。 每個月都會收取與各個 SSL 憑證相關聯的費用 (每小時依比例)。 如需詳細資訊，請參閱 [App Service Pricing Details]。

## 邁向全球

使用 Azure 流量管理員來為遍佈全球的地區網站提供服務，以及使用 Azure CDN 快如閃電地提供內容。

若要提供服務客全球的客戶，請使用 [Azure Traffic Manager] 路由可提供最佳效能的地區網站的網站訪客。 或者，您可以將網站負載平均分配給多個地區的多個網站複本。

閃電將靜態內容給使用者快速地 [integrating your web app with Azure CDN]。 Azure CDN 會快取中的靜態內容 [CDN node] 最接近使用者，以便有效減少延遲和與網站連線。

## 最佳化

透過使用自動調整功能自動地進行調整、使用 Azure Redis 快取功能進行快取、使用 WebJobs 執行背景工作，還有使用 Azure 流量管理員維持高可用性，最佳化 .COM 網站。

App Service Web Apps 的能力 [scale up and out] 都能符合您.COM 網站，無論您的工作負載的大小需求。 您的網站以手動方式透過相應 [Azure 入口網站](https://portal.azure.com), 、 以程式設計方式透過 [Service Management API] 或 [PowerShell scripting], ，或是自動調整功能。 在 **標準** 主控方案中，自動調整可讓您向外擴充網站，自動根據 CPU 使用率。 如需最佳做法，請參閱 [Troy Hunt]的 [10 things I learned about rapidly scaling web apps with Azure]。

加速您網站的回應速度更快的 [Azure Redis Cache]。 使用此快取資料從後端資料庫以及其他項目這類 [ASP.NET session state] 和 [output cache]。

維持高可用性的網站使用 [Azure Traffic Manager]。 使用 **容錯移轉** 方法，流量管理員會自動將流量路由至次要站台如果主要站台上的問題。

## 監視和分析

使用 Azure 或協力廠商工具掌握最新的網站效能。 收到重大網站事件的警示。 使用 Application Insight 或使用 HDInsight 的 Web 記錄分析，取得使用者深入觀點。 

取得 [quick glance] 網站目前效能標準與 web 應用程式的刀鋒視窗中的資源配額 [Azure 入口網站](https://portal.azure.com)。 如需應用程式的可用性、 效能和使用方式的 360 度檢視，使用 [Azure Application Insights] ，讓您快速和強大的疑難排解、 診斷和使用方式的深入觀點。 或者，使用之類的協力廠商工具 [New Relic] 提供進階監視資料，為您的網站。

在 **標準** 主控方案中，監視網站回應能力時，收到電子郵件通知在網站毫無回應。 如需詳細資訊，請參閱 [How to: Receive Alert Notifications and Manage Alert Rules in Azure]。

## 使用多媒體並觸達所有裝置

透過多媒體提升 .COM 網站吸睛度，例如：

-  上傳和串流處理視訊使用全域 [Azure Media Services]
-  傳送電子郵件給使用者 [SendGrid service in Azure Marketplace]

## 其他資源

- [App Service Web 應用程式文件](/services/app-service/web/)
- [Azure App Service Web 應用程式的學習規劃](websites-learning-map.md)
- [Azure Web 部落格](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


[Azure App Service]: /services/app-service/web/

[WordPress]:web-sites-php-web-site-gallery.md
[MySQL]:web-sites-php-mysql-deploy-use-git.md
[Azure SQL Database]:web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]:web-sites-deploy.md#ftp
[Visual Studio]:web-sites-dotnet-get-started.md
[Visual Studio Team Services]:../cloud-services-continuous-delivery-use-vso.md
[Git]:web-sites-publish-source-control.md

[deploying to a staging slot]:web-sites-staged-publishing.md 
[continuously publish]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[run A/B tests]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[custom domain]:web-sites-custom-domain-name.md
[HTTPS]:web-sites-configure-ssl-certificate.md
[App Service Pricing Details]: /pricing/details/app-service/#ssl-connections

[Azure Traffic Manager]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[integrating your web app with Azure CDN]:cdn-websites-with-cdn.md 
[CDN node]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[scale up and out]:web-sites-scale.md
[Azure Management Portal]:http://manage.windowsazure.com/
[Service Management API]:https://msdn.microsoft.com/library/azure/ee460799.aspx
[PowerShell scripting]:https://msdn.microsoft.com/library/azure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 things I learned about rapidly scaling web apps with Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET session state]:https://msdn.microsoft.com/library/azure/dn690522.aspx
[output cache]:https://msdn.microsoft.com/library/azure/dn798898.aspx

[quick glance]:web-sites-monitor.md
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:../store-new-relic-cloud-services-dotnet-application-performance-management.md
[How to: Receive Alert Notifications and Manage Alert Rules in Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Azure Media Services]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[SendGrid service in Azure Marketplace]:sendgrid-dotnet-how-to-send-email.md

 


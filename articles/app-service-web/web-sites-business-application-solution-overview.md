<properties 
    pageTitle="在 Azure App Service 中建立企業營運 Web 應用程式" 
    description="本指南提供如何使用 Azure App Service Web 應用程式來建立內部網路、企業營運應用程式的技術概觀。 這包括驗證策略、Service Bus Relay 和監視。" 
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



# 在 Azure App Service 中建立企業營運 Web 應用程式

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web 應用程式是針對特定業務應用程式的絕佳選擇。 這些應用程式是供內部業務使用的內部網路應用程式，應受到保護。 它們通常需要驗證，一般是針對公司目錄，有一部分則是內部部署資料和服務的存取或整合。 

將企業營運應用程式移至 App Service Web 應用程式有幾項主要優點，例如：

-  隨動態工作負載向上和向下調整，例如處理年度績效審查的應用程式。 在審查期間，大公司的流量就會激增到高峰層級。 Azure 提供調整選項，可讓公司一方面在高流量審查期間相應擴充規模以處理負載，另一方面又可在一年中其餘的時候調整回來以節省開支。 
-  投注較少的心力在基礎結構的取得和管理上，更專注於應用程式開發上
-  提供員工和合作夥伴更多的支援，讓他們可以從任何位置使用應用程式。 使用者不須連線到公司網路，即可使用應用程式，而 IT 群組也可避免使用複雜的反向 Proxy 方案。 有數個驗證選項可以確保公司應用程式的存取受到保護。

以下是在 App Service Web 應用程式上執行的企業營運應用程式範例。 此範例示範不需要使用複雜的技術，只要組合 Web Apps 和其他服務便能完成的工作。 **若要進一步了解拓樸中的項目上按一下而已。** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]
> 本指南呈現一些與企業營運應用程式整合的最常見領域和工作。 不過，還有其他 Azure App Service Web 應用程式功能可供您在特定的實作中使用。 若要檢閱這些功能，請參閱其他指南上 [全域網站空間](web-sites-global-web-presence-solution-overview.md) 和 [數位行銷活動](web-sites-digital-marketing-application-solution-overview.md)。

## 使用現有的資產

將各種語言和架構的現有 Web 資產應用在 App Service Web 應用程式。

您現有的 Web 資產 (無論是 .NET、PHP、Java、Node.js 或 Python) 都可以在 App Service Web 應用程式上執行。 您可以將它們移至 Web 應用程式使用熟悉 [FTP] 工具或原始檔控制管理系統。 Web 應用程式支援從受歡迎的原始檔控制選項直接發行，例如 [Visual Studio], ，[Visual Studio Team Services], ，和 [Git] -本機、 GitHub、 BitBucket、 DropBox、 Mercurial 等...

## 保護您的資產

以加密、驗證公司使用者是現場或遠端使用者的方式保護資產，並授權他們使用資產。 

保護內部資產，與竊聽 [HTTPS]。  **\*.Azurewebsites.net** 網域名稱已經隨附 SSL 憑證，以及如果您使用自訂網域，您可以將您的 SSL 憑證為其應用程式服務 Web 應用程式。 每個月都會收取與各個 SSL 憑證相關聯的費用 (每小時依比例)。 如需詳細資訊，請參閱 [App Service Pricing Details]。

[Authenticate users] 針對公司目錄中。 App Service Web 應用程式可以向內部部署的身分識別提供者 (例如，Active Directory Federation Services (AD FS)) 驗證使用者，或向已和您公司的 Active Directory 部署同步的 Azure Active Directory 租用戶驗證使用者。 當使用者在辦公室內或外出時，均可透過單一登入存取您在 Web 應用程式的 Web 內容。 現有的服務 (例如 Office 365 或 Microsoft Intune) 已在使用 Azure Active Directory。 透過 [Easy Auth], ，開啟的功能使用相同的 Azure Active Directory 租用戶驗證 web 應用程式非常簡單。 

[Authorize users] web 內容使用。 使用最少的額外程式碼，即可將相同的內部部署 ASP.NET 程式碼撰寫模式運用在 App Service Web Apps，例如使用 `[Authorize]` 裝飾。 與內部部署的應用程式一樣，您可以保留精密存取控制的彈性。

## 連線到內部部署資源 ##

連線到雲端 (效能考量) 或內部部署 (法規遵循考量) 的 Web 應用程式資料或資源。 如需有關如何在 Azure 中保存資料的詳細資訊，請參閱 [Azure Trust Center]。 

您可以選擇在 Azure 中的各種資料庫後端 web 應用程式的需求包括 [Azure SQL Database] 和 [MySQL]。 將資料安全地保存在 Azure 中，可以使資料在地理位置上接近您的 Web 應用程式，進而最佳化其效能。

不過，您的企業可能需將資料保存在公司內部。 應用程式服務 Web 應用程式可讓您輕鬆地設定 [hybrid connection] 至您在內部部署資源，例如資料庫後端。 如果您希望統一的管理您的內部連線，與其中一個整合許多 web 應用程式 [Azure Virtual Network] 具有站台對站 VPN。 接著，就像 Web 應用程式是內部部署的一樣，您就能存取內部部署資源。

## 最佳化

透過使用自動調整規模自動調整、使用 Azure Redis Cache 執行快取、使用 WebJobs 執行背景工作，以及使用 Azure 流量管理員維持高可用性，以最佳化您的企業營運應用程式。

App Service Web Apps 的能力 [scale up and out] 都能符合您的業務的應用程式，不論您的工作負載的大小需求。 您以手動方式透過 web 應用程式相應 [Azure Portal], 、 以程式設計方式透過 [Service Management API] 或 [PowerShell scripting], ，或是自動調整功能。 在 **標準** 層，自動調整 」 可讓您向外擴充 web 應用程式會自動依據 CPU 使用量。 如需最佳做法，請參閱 [Troy Hunt]的 [10 things I learned about rapidly scaling web apps with Azure]。

加速您 web 應用程式的回應速度與 [Azure Redis Cache]。 使用此快取資料從後端資料庫以及其他項目這類 [ASP.NET session state] 和 [output cache]。

使用 [Azure 流量管理員] 來維持 Web 應用程式的高可用性。 使用 **容錯移轉** 方法，流量管理員會自動將流量路由至次要站台如果主要站台上的問題。

## 監視和分析

使用 Azure 或協力廠商工具，掌握關於您 Web 應用程式效能的最新資訊。 接收重要 Web 應用程式事件的警示。 使用 Application Insight 或使用 HDInsight 的 Web 記錄分析，取得使用者深入觀點。 

取得 [quick glance] web 應用程式的目前效能標準與 web 應用程式的刀鋒視窗中的資源配額 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)。 如需應用程式的可用性、 效能和使用方式的 360 度檢視，使用 [Azure Application Insights] ，讓您快速和強大的疑難排解、 診斷和使用方式的深入觀點。 或者，使用之類的協力廠商工具 [New Relic] 提供進階監視 web 應用程式資料。

在 **標準** 層，監視應用程式回應便會收到電子郵件通知每當您的應用程式沒有回應時。 如需詳細資訊，請參閱 [How to: Receive Alert Notifications and Manage Alert Rules in Azure]。

## 其他資源

- [App Service Web 應用程式文件](/services/app-service/web/)
- [Azure App Service Web 應用程式的學習規劃](websites-learning-map.md)
- [Azure Web 部落格](/blog/topics/web/)

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]



[Azure App Service]: /services/app-service/web/

[FTP]:web-sites-deploy.md#ftp
[Visual Studio]:web-sites-dotnet-get-started.md
[Visual Studio Team Services]:../cloud-services-continuous-delivery-use-vso.md
[Git]:web-sites-publish-source-control.md

[HTTPS]:web-sites-configure-ssl-certificate.md
[App Service Pricing Details]: /pricing/details/app-service/#ssl-connections
[Authenticate users]:web-sites-authentication-authorization.md
[Easy Auth]:/blog/2014/11/13/azure-websites-authentication-authorization/
[Authorize users]:web-sites-authentication-authorization.md

[Azure Trust Center]:/support/trust-center/
[MySQL]:web-sites-php-mysql-deploy-use-git.md
[Azure SQL Database]:web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[hybrid connection]:web-sites-hybrid-connection-get-started.md
[Azure Virtual Network]:web-sites-integrate-with-vnet.md

[scale up and out]:web-sites-scale.md
[Azure Portal]:http://portal.azure.com/
[Service Management API]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell scripting]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 things I learned about rapidly scaling web apps with Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET session state]:https://msdn.microsoft.com/library/azure/dn690522.aspx
[output cache]:https://msdn.microsoft.com/library/azure/dn798898.aspx

[quick glance]:web-sites-monitor.md
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:../store-new-relic-cloud-services-dotnet-application-performance-management.md
[How to: Receive Alert Notifications and Manage Alert Rules in Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

 


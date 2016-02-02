<properties 
    pageTitle="在 Azure App Service 中建立企業營運 Web 應用程式" 
    description="本指南提供如何使用 Azure App Service Web 應用程式來建立內部網路、企業營運應用程式的技術概觀。這包括驗證策略、Service Bus Relay 和監視。" 
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

以下是在 App Service Web 應用程式上執行的企業營運應用程式範例。 此範例示範不需要使用複雜的技術，只要組合 Web 應用程式和其他服務便能完成的工作。 **按一下拓樸中的項目以讀取詳細資訊。**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>
> [AZURE.NOTE]
> 本指南呈現一些與企業營運應用程式整合的最常見領域和工作。 不過，還有其他 Azure App Service Web 應用程式功能可供您在特定的實作中使用。 若要檢閱這些功能，請參閱其他指南上 [全域網站空間](web-sites-global-web-presence-solution-overview.md) 和 [數位行銷活動](web-sites-digital-marketing-application-solution-overview.md)。

## 使用現有的資產

將各種語言和架構的現有 Web 資產應用在 App Service Web 應用程式。

您現有的 Web 資產 (無論是 .NET、PHP、Java、Node.js 或 Python) 都可以在 App Service Web 應用程式上執行。 您可以將它們移至 Web 應用程式使用熟悉的 [FTP] 工具或原始檔控制管理系統。 Web 應用程式支援從受歡迎的原始檔控制選項，例如 [Visual Studio]，直接發行 [Visual Studio Team Services]，和 [Git]-本機、 GitHub、 BitBucket、 DropBox、 Mercurial 等...

## 保護您的資產

以加密、驗證公司使用者是現場或遠端使用者的方式保護資產，並授權他們使用資產。

保護內部資產，使用 [HTTPS] 竊聽。  **\*.Azurewebsites.net** 網域名稱已經隨附 SSL 憑證，以及如果您使用自訂網域，您可以將您的 SSL 憑證為其應用程式服務 Web 應用程式。 每個月都會收取與各個 SSL 憑證相關聯的費用 (每小時依比例)。 如需詳細資訊，請參閱 [App Service 定價詳細資料]。

[驗證的使用者] 針對公司目錄。 App Service Web 應用程式可以向內部部署的身分識別提供者 (例如，Active Directory Federation Services (AD FS)) 驗證使用者，或向已和您公司的 Active Directory 部署同步的 Azure Active Directory 租用戶驗證使用者。 當使用者在辦公室內或外出時，均可透過單一登入存取您在 Web 應用程式的 Web 內容。 現有的服務 (例如 Office 365 或 Microsoft Intune) 已在使用 Azure Active Directory。 透過 [簡單驗證]，開啟與相同的 Azure Active Directory 租用戶 web 應用程式的驗證方法很簡單。

[授權使用者] 的 web 內容使用。 使用最少的額外程式碼，即可將相同的內部部署 ASP.NET 程式碼撰寫模式應用程式服務 Web 應用程式使用 `Authorize` 裝飾，例如。 與內部部署的應用程式一樣，您可以保留精密存取控制的彈性。

## 連線到內部部署資源

連線到雲端 (效能考量) 或內部部署 (法規遵循考量) 的 Web 應用程式資料或資源。 如需有關如何在 Azure 中保存資料的詳細資訊，請參閱 [Azure 信任中心]。

您可以選擇在 Azure 中的各種資料庫後端以符合您的 web 應用程式，包括 [Azure SQL Database] 和 [MySQL] 需求。 將資料安全地保存在 Azure 中，可以使資料在地理位置上接近您的 Web 應用程式，進而最佳化其效能。

不過，您的企業可能需將資料保存在公司內部。 應用程式服務 Web 應用程式可讓您輕鬆地設定 [混合式連線] 至您在內部部署資源，例如資料庫後端。 如果您希望統一的管理您的內部連線，您可以整合許多 web 應用程式與其中一個 [Azure 虛擬網路] 有站台對站 VPN。 接著，就像 Web 應用程式是內部部署的一樣，您就能存取內部部署資源。

## 最佳化

透過使用自動調整規模自動調整、使用 Azure Redis Cache 執行快取、使用 WebJobs 執行背景工作，以及使用 Azure 流量管理員維持高可用性，以最佳化您的企業營運應用程式。

[相應增加和放大] 應用程式服務 Web 應用程式的能力都能符合您的業務的應用程式，不論您的工作負載的大小需求。 向外延展您的 web 應用程式，以手動方式透過 [Azure 網站]，以程式設計方式透過 [服務管理 API] 或 [PowerShell 指令碼]，或是自動調整功能。 在「標準」****層中，「自動調整規模」可讓您依據 CPU 使用量自動相應放大 Web 應用程式。 如需最佳做法，請參閱 [Troy Hunt] 的 [我了解快速調整與 Azure web 應用程式的 10 件事]。

加速您 web 應用程式的 [Azure Redis 快取] 回應速度。 您可以使用它來快取後端資料庫中的資料和其他項目，例如 [ASP.NET 工作階段狀態] 和 [輸出快取]。

使用 [Azure 流量管理員] 來維持 Web 應用程式的高可用性。 使用「容錯移轉」****方法時，若主要網站發生問題，流量管理員會自動將流量路由至次要網站。

## 監視和分析

使用 Azure 或協力廠商工具，掌握關於您 Web 應用程式效能的最新資訊。 接收重要 Web 應用程式事件的警示。 使用 Application Insight 或使用 HDInsight 的 Web 記錄分析，取得使用者深入觀點。

取得 web 應用程式的刀鋒視窗中的 [快速概覽] 的 web 應用程式的目前效能標準與資源配額 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)。您的應用程式的可用性、 效能和使用方式的 360 度檢視，使用 [Azure Application Insights]，讓您快速和強大的疑難排解、 診斷和使用方式的深入觀點。或者，使用像是 [New Relic] 協力廠商工具提供的進階監視 web 應用程式的資料。

在「標準」****層中，每當您的應用程式沒有回應時，監視應用程式回應便會收到電子郵件通知。 如需詳細資訊，請參閱 [如何: 接收警示通知及管理在 Azure 中的警示規則]。

## 其他資源

- [App Service Web Apps 文件](/services/app-service/web/)
- [Azure App Service Web Apps 的學習規劃](websites-learning-map.md)
- [Azure Web 部落格](/blog/topics/web/)

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]




[azure app service]: /services/app-service/web/ 
[ftp]: web-sites-deploy.md#ftp 
[visual studio]: web-sites-dotnet-get-started.md 
[visual studio team services]: ../cloud-services-continuous-delivery-use-vso.md 
[git]: web-sites-publish-source-control.md 
[https]: web-sites-configure-ssl-certificate.md 
[app service pricing details]: /pricing/details/app-service/#ssl-connections 
[authenticate users]: web-sites-authentication-authorization.md 
[easy auth]: /blog/2014/11/13/azure-websites-authentication-authorization/ 
[authorize users]: web-sites-authentication-authorization.md 
[azure trust center]: /support/trust-center/ 
[mysql]: web-sites-php-mysql-deploy-use-git.md 
[azure sql database]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md 
[hybrid connection]: web-sites-hybrid-connection-get-started.md 
[azure virtual network]: web-sites-integrate-with-vnet.md 
[scale up and out]: web-sites-scale.md 
[azure portal]: http://portal.azure.com/ 
[service management api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx 
[powershell scripting]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx 
[troy hunt]: https://twitter.com/troyhunt 
[10 things i learned about rapidly scaling web apps with azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html 
[azure redis cache]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/ 
[asp.net session state]: https://msdn.microsoft.com/library/azure/dn690522.aspx 
[output cache]: https://msdn.microsoft.com/library/azure/dn798898.aspx 
[quick glance]: web-sites-monitor.md 
[azure application insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx 
[new relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md 
[how to: receive alert notifications and manage alert rules in azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx 


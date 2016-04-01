<properties 
    pageTitle="在 Azure App Service 中使用 Azure Redis 快取的工作階段狀態" 
    description="了解如何使用 Azure 快取服務來支援 ASP.NET 工作階段狀態快取。" 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="Rick-Anderson" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="12/08/2015" 
    ms.author="riande"/>


# 在 Azure App Service 中使用 Azure Redis 快取的工作階段狀態


本主題說明如何將 Azure Redis 快取服務用於工作階段狀態。

如果您的 ASP.NET Web 應用程式使用工作階段狀態，則將需要設定外部工作階段狀態提供者 (可為 Redis 快取服務或 SQL Server 工作階段狀態提供者)。 如果您使用工作階段狀態，但並未使用外部提供者，則您的 Web 應用程式只能限定一個執行個體。 Redis 快取服務是最快最簡單的啟用方式。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

##<a id="createcache"></a>建立快取
請依照下列 [這些指示](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) 建立快取。

##<a id="configureproject"></a>將 RedisSessionStateProvider NuGet 封裝加入 Web 應用程式
安裝 NuGet `RedisSessionStateProvider` 封裝。  若要從封裝管理員主控台安裝中使用下列命令 (**工具** > **NuGet 封裝管理員** > **Package Manager Console**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
若要從安裝 **工具** > **NuGet 封裝管理員** > **管理解決方案的 NugGet 封裝**, ，搜尋 `RedisSessionStateProvider`。

如需詳細資訊，請參閱 [NuGet RedisSessionStateProvider 頁面](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) 和 [設定快取用戶端](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet)。

##<a id="configurewebconfig"></a>修改 Web.Config 檔案
除了對快取進行組件參考，NuGet 封裝新增虛設常式項目中的 *web.config* 檔案。 

1. 開啟 *web.config* 並尋找 **sessionState** 項目。

1. 輸入 `host`、`accessKey`、`port` (SSL 連接埠應為 6380)，然後將 `SSL` 設定為 `true`。 這些值可以取自 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715) 刀鋒視窗中，您的快取執行個體。 如需詳細資訊，請參閱 [連接到快取](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache)。 請注意，預設會為新的快取停用非 SSL 連接埠。 如需啟用非 SSL 連接埠的詳細資訊，請參閱 [存取連接埠](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) 一節中 [Azure Redis 快取中設定快取](https://msdn.microsoft.com/library/azure/dn793612.aspx) 主題。 下列標記顯示所做的變更 *web.config* 檔案，特別是，變更 *連接埠*, ，*主機*, ，accessKey * 和 *ssl*。

          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;


##<a id="usesessionobject"></a> 在程式碼中使用工作階段物件
最後一步，則是開始在您的 ASP.NET 程式碼中使用工作階段物件。 您將物件加入至工作階段狀態使用 **Session.Add** 方法。 此方法使用機碼值組，將項目儲存到工作階段狀態快取。

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

以下程式碼會從工作階段狀態擷取此值。

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue; 

您也可以使用 Redis 快取來快取 Web 應用程式中物件。 如需詳細資訊，請參閱 [MVC 電影應用程式使用 Azure Redis 快取在 15 分鐘內](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/)。
如需有關如何使用 ASP.NET 工作階段狀態的詳細資訊，請參閱 [ASP.NET 工作階段狀態概觀][]。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

  *由 [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net  
  [ASP.NET Session State Overview]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 



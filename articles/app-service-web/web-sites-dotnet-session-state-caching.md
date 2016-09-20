<properties 
    pageTitle="Munkamenet-állapot az Azure Redis Cache-ben, az Azure App Service szolgáltatásban" 
    description="További tudnivalók az Azure Cache Service használatáról az ASP.NET munkamenet-állapotának gyorsítótárazásához." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="Rick-Anderson" 
    manager="wpickett" 
    editor="none"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="get-started-article" 
    ms.date="06/27/2016" 
    ms.author="riande"/>


# Munkamenet-állapot az Azure Redis Cache-ben, az Azure App Service szolgáltatásban


Ez a témakör leírja, hogyan használható az Azure Redis Cache szolgáltatás a munkamenet-állapotokhoz.

Ha az ASP.NET webalkalmazás munkamenet-állapotot használ, be kell állítania egy külső állapotszolgáltatót (a Redis Cache Service vagy az SQL Server munkamenetállapot-szolgáltatóját). Ha munkamenet-állapotot használ, de nem használ külső szolgáltatót, a szolgáltatás a webalkalmazás egyetlen példányára lesz korlátozva. A legegyszerűbben és leggyorsabban engedélyezhető megoldás a Redis Cache Service.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

##<a id="createcache"></a>A gyorsítótár létrehozása
Kövesse [ezt az útmutatót](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) a gyorsítótár létrehozásához.

##<a id="configureproject"></a>A RedisSessionStateProvider NuGet csomagjának hozzáadása a webalkalmazáshoz
Telepítse a NuGet `RedisSessionStateProvider` csomagot.  A következő paranccsal végezzen telepítést a csomagkezelő konzolról (**Eszközök** > **NuGet-csomagkezelő** > **Csomagkezelő konzol**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Az **Eszközök** > **NuGet-csomagkezelő** > **Megoldás NuGet csomagjainak kezelése** menüből való telepítéshez keressen a következő kifejezésre: `RedisSessionStateProvider`.

További információk: [NuGet RedisSessionStateProvider oldal](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) és [A gyorsítótárügyfél konfigurálása](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

##<a id="configurewebconfig"></a>A Web.Config fájl módosítása
A NuGet csomag szerelvényhivatkozások a gyorsítótárhoz való létrehozása mellett csonkbejegyzéseket ad a *web.config* fájlhoz. 

1. Nyissa meg a *web.config* fájlt, és keresse meg a **sessionState** elemet.

1. Adja meg a `host`, az `accessKey`, és a `port` értékét (az SSL-port legyen 6380), és állítsa az `SSL` tulajdonságot `true` értékűre. Ezek az értékek a gyorsítótárpéldány [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715) paneljéről szerezhetők be. További információ: [Csatlakozás a gyorsítótárhoz](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Vegye figyelembe, hogy a nem SSL port az új gyorsítótárakhoz alapértelmezés szerint le van tiltva. További információk a nem SSL port engedélyezésével kapcsolatban: [Configure a cache in Azure Redis Cache](https://msdn.microsoft.com/library/azure/dn793612.aspx) (Gyorsítótár konfigurálása az Azure Redis Cache-ben) témakör, [Access Ports](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) (Hozzáférési portok) című szakasz. A következő kódban láthatók a *web.config* fájlon eszközölt módosítások, vagyis a *port*, a *host*, az accessKey*, és az *ssl* változásai.

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


##<a id="usesessionobject"></a> A munkamenet-objektum használata a kódban
A végső lépés a munkamenet-objektum használatának megkezdése az ASP.NET-kódban. Objektumokat a **Session.Add** metódussal adhat a munkamenet-állapothoz. Ez a metódus kulcs-érték párokat használ az elemek tárolására a munkamenetállapot-gyorsítótárban.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

A következő kód lekéri ezt az értéket a munkamenet-állapotból.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue; 

A webalkalmazáson belüli objektumok gyorsítótárazásához ezenkívül használhatja a Redis Cache-t is. További információk: [MVC movie app with Azure Redis Cache in 15 minutes](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/) (MVC filmalkalmazás 15 perc alatt az Azure Redis Cache segítségével).
További részletek az ASP.NET-munkamenetállapot használatával kapcsolatban: [ASP.NET Session State Overview (Az ASP.NET-munkamenetállapot áttekintése)][] .

>[AZURE.NOTE] Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, lépjen [Az Azure App Service kipróbálása](http://go.microsoft.com/fwlink/?LinkId=523751) oldalra, ahol azonnal létrehozhat egy rövid élettartamú alapszintű webalkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.

## A változások
* Információk a Websites szolgáltatásról az App Service-re való váltásról: [Az Azure App Service és a hatása a meglévő Azure-szolgáltatásokra](http://go.microsoft.com/fwlink/?LinkId=529714)

  *Szerző: [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [legfrissebb telepítve]: http://www.windowsazure.com/downloads/?sdk=net  
  [ASP.NET Session State Overview (Az ASP.NET-munkamenetállapot áttekintése)]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 



<!--HONumber=sep16_HO1-->



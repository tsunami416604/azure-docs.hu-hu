---
title: Gyorsítótár ASP.NET munkamenet-állapotszolgáltató
description: Ismerje meg, hogyan tárolhatja ASP.NET munkamenetállapot memóriában az Azure Cache for Redis használatával.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 25cef95e2d01012506148f03be45104e455e1fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530274"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Az Azure Cache for Redis ASP.NET munkamenetállapot-szolgáltatója

Az Azure Cache for Redis egy munkamenet-állapot-szolgáltatót biztosít, amely segítségével a munkamenet-állapot a memóriában tárolható az Azure Cache for Redis-rel az SQL Server-adatbázis helyett. A gyorsítótárazási munkamenet-állapot szolgáltatójának használatához először konfigurálja a gyorsítótárat, majd konfigurálja a ASP.NET alkalmazást a gyorsítótárhoz az Azure Cache for Redis Session State NuGet csomag használatával.

Ez gyakran nem praktikus egy valós felhőappban, hogy elkerülje valamilyen állapottárolását egy felhasználói munkamenethez, de egyes megközelítések jobban befolyásolják a teljesítményt és a méretezhetőséget, mint mások. Ha az állapotot kell tárolnia, a legjobb megoldás az, ha az állapot mennyisége kicsi, és tárolja a cookie-kban. Ha ez nem valósítható meg, a következő legjobb megoldás az, ha ASP.NET munkamenet-állapotot használja egy szolgáltatóval az elosztott, memórián belüli gyorsítótárhoz. A legrosszabb megoldás a teljesítmény és a méretezhetőség szempontjából az adatbázis által támogatott munkamenet-állapot szolgáltató használata. Ez a témakör útmutatást nyújt a ASP.NET a munkamenet-állapot-szolgáltató az Azure-gyorsítótár redis használatával kapcsolatban. A munkamenetállapot egyéb beállításairól [a ASP.NET munkamenetállapot-beállítások című témakörben](#aspnet-session-state-options)talál további információt.

## <a name="store-aspnet-session-state-in-the-cache"></a>Store ASP.NET session state in the cache (ASP.NET munkamenet-állapot tárolása a gyorsítótárban)

Ha egy ügyfélalkalmazást szeretne beállítani a Visual Studióban az Azure Cache for Redis Session State NuGet csomag használatával, kattintson az Eszközök menü **NuGet csomagkezelő**, **Csomagkezelő konzol** **parancsára.**

Futtassa az alábbi parancsot a `Package Manager Console` ablakából.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Ha a fürtözési szolgáltatást a prémium szintről használja, a [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1-es vagy újabb verziót kell használnia, vagy kivételt kell kijelölnie. A 2.0.1-es vagy újabb verzióra való áttérés törésváltozás; További információ: [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). A cikk frissítésekének időpontjában a csomag aktuális verziója a 2.2.3.
> 
> 

A Redis Session State Provider NuGet csomag függőséget okoz a StackExchange.Redis.StrongName csomagtól. Ha a StackExchange.Redis.StrongName csomag nem található a projektben, akkor telepítve van.

>[!NOTE]
>Az erős nevű StackExchange.Redis.StrongName csomag mellett a StackExchange.Redis nem erős nevű verziója is megjelenik. Ha a projekt a nem erős nevű StackExchange.Redis verziót használja, el kell távolítania, különben névütközéseket kap a projektben. Ezekről a csomagokról további információt a [.NET gyorsítótárazási ügyfelek konfigurálása című témakörben talál.](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)
>
>

A NuGet csomag letölti és hozzáadja a szükséges összeállítási hivatkozásokat, és hozzáadja a következő szakaszt a web.config fájlhoz. Ez a szakasz tartalmazza a szükséges konfigurációt a ASP.NET alkalmazás az Azure-gyorsítótár redis munkamenet állapotszolgáltató használatához.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

A megjegyzéssel végzett szakasz az egyes attribútumok attribútumainak attribútumait és mintabeállításait ismerteti.

Konfigurálja az attribútumokat a Microsoft Azure portalon a gyorsítótár-panelértékeivel, és szükség szerint konfigurálja a többi értéket. A gyorsítótár tulajdonságainak eléréséről az [Azure Cache beállítása a Redis-beállításokhoz című](cache-configure.md#configure-azure-cache-for-redis-settings)témakörben olvashat.

* **host** – adja meg a gyorsítótár végpontját.
* **port** – használja a nem SSL-portot vagy az SSL-portot, az ssl beállításaitól függően.
* **accessKey** – használja az elsődleges vagy másodlagos kulcsot a gyorsítótárhoz.
* **ssl** – igaz, ha azt szeretnénk, hogy biztonságos cache / ügyfél kommunikáció ssl; egyébként hamis. Ügyeljen arra, hogy a megfelelő portot adja meg.
  * A nem SSL port az új gyorsítótárakhoz alapértelmezés szerint le van tiltva. Adja meg a true értéket ehhez a beállításhoz az SSL-port használatához. A nem SSL-port engedélyezéséről a [Gyorsítótár konfigurálása](cache-configure.md) témakör [Access Ports](cache-configure.md#access-ports) című szakaszában olvashat bővebben.
* **throwOnError** – igaz, ha azt szeretné, hogy hiba esetén kivétel tetszhet, vagy hamis, ha azt szeretné, hogy a művelet némán sikertelen legyen. A hiba ellenőrzéséhez ellenőrizze a statikus Microsoft.Web.Redis.RedisSessionStateProvider.LastException tulajdonságot. Az alapértelmezett érték igaz.
* **retryTimeoutEz milliszekundum** – A sikertelen műveleteket ez időszakalatt újra megkíséreli, ezredmásodpercben megadva. Az első újrapróbálkozás 20 ezredmásodperc után következik be, majd az újrapróbálkozások minden másodpercben megtörténnek, amíg az újrapróbálkozási idő szintEzidő lejár. Közvetlenül ezen időköz után a művelet et újra megkíséreli a rendszer egy utolsó alkalommal. Ha a művelet továbbra is sikertelen, a kivétel a throwOnError beállítástól függően visszakerül a hívóhoz. Az alapértelmezett érték 0, ami azt jelenti, hogy nincs újrapróbálkozás.
* **databaseId** – Megadja, hogy melyik adatbázist használja a gyorsítótár kimeneti adataihoz. Ha nincs megadva, a rendszer a 0 alapértelmezett értéket használja.
* **applicationName** - A kulcsok a `{<Application Name>_<Session ID>}_Data`redis-ben tárolódnak. Ez az elnevezési séma lehetővé teszi, hogy több alkalmazás ugyanazt a Redis-példányt ossza meg. Ez a paraméter nem kötelező, és ha nem adja meg, a rendszer alapértelmezett értéket használ.
* **connectionTimeoutEzmilliszekundum** – Ez a beállítás lehetővé teszi a StackExchange.Redis ügyfél connectTimeout beállításának felülbírálását. Ha nincs megadva, a rendszer az 5000-es alapértelmezett connectTimeout beállítást használja. További információ: [StackExchange.Redis konfigurációs modell.](https://go.microsoft.com/fwlink/?LinkId=398705)
* **operationTimeoutInMilliseconds** – Ez a beállítás lehetővé teszi a StackExchange.Redis ügyfél syncTimeout beállításának felülbírálásához. Ha nincs megadva, a rendszer az 1000-es alapértelmezett syncTimeout beállítást használja. További információ: [StackExchange.Redis konfigurációs modell.](https://go.microsoft.com/fwlink/?LinkId=398705)
* **redisSerializerType** - Ez a beállítás lehetővé teszi a Redis-nek küldött munkamenet-tartalom egyéni szerializálásának megadását. A megadott típusnak `Microsoft.Web.Redis.ISerializer` végre kell hajtania, és deklarálnia kell a nyilvános paraméter nélküli konstruktorat. Alapértelmezés `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` szerint a program használja.

Ezekről a tulajdonságokról további információt a [Announceing ASP.NET Session State Provider for Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)című témakörben talál.

Ne felejtse el kommentálni a web.config szabványos InProc munkamenet-állapot szolgáltató szakaszát.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Miután ezeket a lépéseket végrehajtotta, az alkalmazás úgy van konfigurálva, hogy az Azure Cache redis session state provider. Ha munkamenet-állapotot használ az alkalmazásban, az egy Azure-gyorsítótár redis-példányban tárolódik.

> [!IMPORTANT]
> A gyorsítótárban tárolt adatoknak szerializálhatónak kell lenniük, ellentétben az alapértelmezett memórián belüli ASP.NET munkamenetállapot-szolgáltatóban tárolható adatokkal. A Redis munkamenetállapot-szolgáltatójának használatos, győződjön meg arról, hogy a munkamenet-állapotban tárolt adattípusok szerializálhatók.
> 
> 

## <a name="aspnet-session-state-options"></a>ASP.NET munkamenet-állapot beállításai

* A memóriamunkamenet-állapotszolgáltatóban – Ez a szolgáltató a munkamenet-állapotot a memóriában tárolja. Az előnye, hogy használja ezt a szolgáltatót, hogy egyszerű és gyors. A webalkalmazások azonban nem méretezhetők, ha a memóriaszolgáltatóban használja, mivel az nincs elosztva.
* Sql Server munkamenet-állapotszolgáltató – Ez a szolgáltató az Sql Server kiszolgálón tárolja a munkamenet-állapotot. Használja ezt a szolgáltatót, ha a munkamenet-állapotot állandó tárolóban szeretné tárolni. A Web App skálázhatja, de az Sql Server for Session használatával teljesítményhatással van a webalkalmazásra. Ezt a szolgáltatót [memóriabeli OLTP-konfigurációval](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) is használhatja a teljesítmény javítása érdekében.
* Elosztott a memóriában session state provider, például az Azure Cache for Redis session state provider – Ez a szolgáltató a két világ ból a legjobbat adja. A webalkalmazás rendelkezhet egy egyszerű, gyors és méretezhető munkamenetállapot-szolgáltatóval. Mivel ez a szolgáltató a munkamenet-állapotot egy gyorsítótárban tárolja, az alkalmazásnak figyelembe kell vennie az elosztott memória-gyorsítótárral való beszélgetéshez kapcsolódó összes jellemzőt, például az átmeneti hálózati hibákat. A gyorsítótár használatával kapcsolatos gyakorlati [Caching guidance](../best-practices-caching.md) tanácsok a Microsoft Patterns & gyakorlatok [Azure Cloud Alkalmazástervezési és -megvalósítási útmutatóban című témakörben talál.](https://github.com/mspnp/azure-guidance)

A munkamenetállapotról és az egyéb ajánlott eljárásokról a [Webfejlesztés gyakorlati tanácsai (Valós idejű felhőalapú alkalmazások létrehozása az Azure-ral)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices)című témakörben talál további információt.

## <a name="third-party-session-state-providers"></a>Harmadik fél munkamenet-állapotszolgáltatói

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache gyullad](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>További lépések

Tekintse meg a [ASP.NET kimeneti gyorsítótár-szolgáltatót a Redis Azure Cache for Redis számára.](cache-aspnet-output-cache-provider.md)

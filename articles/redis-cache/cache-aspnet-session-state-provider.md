---
title: "Gyorsítótár ASP.NET munkamenetállapot-szolgáltató |} Microsoft Docs"
description: "Útmutató: Azure Redis Cache használatának az ASP.NET munkamenet-állapot tárolása"
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/01/2017
ms.author: wesmc
ms.openlocfilehash: 485375f2f2ffb83b7d0fdeef8daab5880a8bbc27
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Az Azure Redis Cache ASP.NET munkamenetállapot-szolgáltatója
Azure Redis Cache biztosít a munkamenetállapot-szolgáltatóját, amelyek segítségével a munkamenet-állapot tárolása helyett a memória gyorsítótárban, vagy az SQL Server-adatbázisban. A gyorsítótárazási munkamenetállapot-szolgáltatóját használatához először konfigurálja a gyorsítótárhoz, és konfigurálja az ASP.NET-alkalmazás a Redis gyorsítótár munkamenet állapota NuGet csomag segítségével gyorsítótár.

Nincs gyakran gyakorlati állapot egyaránt működik valamilyen formában tárolja a felhasználói munkamenet elkerülése érdekében valós felhő alkalmazásban, de néhány módszerek hatással lehet a teljesítmény és méretezhetőség több, mint a többire. Ha állapot tárolására, a legjobb megoldás, hogy maradjon kicsi a mennyiség szerinti és cookie-kban tárolja. Amely nem valósítható meg, ha a következő legjobb megoldás az ASP.NET munkamenet-állapot használandó szolgáltató elosztott, memóriában lévő gyorsítótárhoz. A teljesítmény és méretezhetőség tükrözik a legrosszabb megoldást az adatbázis biztonsági munkamenetállapot-szolgáltatóját. Ez a témakör az ASP.NET munkamenetállapot-szolgáltatóját használja az Azure Redis Cache nyújt útmutatást. A más munkamenet-állapotra vonatkozó információkért lásd: [az ASP.NET munkamenet-állapot beállításai](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Store ASP.NET session state in the cache (ASP.NET munkamenet-állapot tárolása a gyorsítótárban)
Ügyfélalkalmazás konfigurálása a Visual Studio használatával a Redis gyorsítótár munkamenet állapota NuGet-csomagot, kattintson a **NuGet-Csomagkezelő**, **Csomagkezelő konzol** a a **eszközök** menü.

Futtassa az alábbi parancsot a `Package Manager Console` ablakából.
    
```
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Ha a fürtszolgáltatást a prémium tarifacsomagra használ, akkor kell használnia [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 vagy magasabb vagy kivétel lépett fel. Helyezze át a 2.0.1 vagy újabb rendszerre használhatatlanná tévő változást; További információkért lásd: [v2.0.0 Megtörje Változásrészletek](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). Ez a cikk a frissítés során ez a csomag aktuális verziója 2.2.3.
> 
> 

A Redis munkamenet állapota szolgáltató NuGet-csomagot a StackExchange.Redis.StrongName csomag függőség rendelkezik. Ha a StackExchange.Redis.StrongName csomag nincs jelen a projektben, telepíti a rendszer.

>[!NOTE]
>Az erős névvel ellátott StackExchange.Redis.StrongName csomag mellett is van a StackExchange.Redis nem-erős névvel ellátott verzióra. A projekt távolítsa el a nem-erős névvel ellátott StackExchange.Redis verzióját használja, ha más módon meg beolvasása ütközése a projektben. Ezeket a csomagokat kapcsolatos további információkért lásd: [konfigurálása .NET-gyorsítótárazási ügyfelek számára](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

A NuGet-csomagot tölti le, és hozzáadja a szükséges összeállítási referenciát, és hozzáadja az alábbi szakasz a web.config fájlt. Ez a szakasz az ASP.NET-alkalmazás a Redis gyorsítótár munkamenetállapot-szolgáltatóját használja a szükséges konfigurációját tartalmazza.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
    <!--
    <add name="MySessionStateStore"
           host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "0" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
    />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
</sessionState>
```

A megjegyzésként szakasz és a minta-beállítások minden attribútum példaként szolgál.

Az attribútumok konfigurálása a Microsoft Azure-portálon a gyorsítótár paneljét értékeivel, és konfigurálja a többi érték tetszés szerint. A gyorsítótár tulajdonságai elérése, lásd: [konfigurálása Redis gyorsítótár beállításainak](cache-configure.md#configure-redis-cache-settings).

* **állomás** – adja meg a gyorsítótár végpontjához.
* **port** – használja a nem SSL port vagy az SSL-port, attól függően, hogy az ssl-beállítások.
* **accessKey** – az elsődleges vagy másodlagos kulcsot használja a gyorsítótárhoz.
* **SSL** – igaz, ha azt szeretné, hogy biztonságos SSL/gyorsítótárügyfél kommunikációhoz; ellenkező esetben hamis. Mindenképp adja meg a megfelelő porthoz.
  * A nem SSL port az új gyorsítótárakhoz alapértelmezés szerint le van tiltva. Adja meg az IGAZ állítja ezt a beállítást, az SSL-port használatára. A nem SSL port engedélyezésével kapcsolatos további információkért lásd: a [hozzáférési portok](cache-configure.md#access-ports) szakasz a [gyorsítótár konfigurálása](cache-configure.md) témakör.
* **throwOnError** – igaz, ha azt szeretné, hogy a kivételt jelezni, ha a hiba, illetve a hamis Ha azt szeretné, hogy a művelet csendes meghiúsul. A statikus Microsoft.Web.Redis.RedisSessionStateProvider.LastException tulajdonság ellenőrizheti hibája miatt. Az alapértelmezett érték igaz.
* **retryTimeoutInMilliseconds** – sikertelen műveletek ismétlődnek, ezen időszakban, ezredmásodpercben megadva. Az első újrapróbálkozásnál 20 ezredmásodperc után következik be, és az újrapróbálkozások kerül sor, amíg a retryTimeoutInMilliseconds időköz másodpercenként. Az intervallum után azonnal a műveletet a rendszer ismét megkísérli egy befejező időpontja. Ha a művelet továbbra is sikertelen, a kivétel történt vissza a hívónak, attól függően, hogy a throwOnError beállítást. Az alapértelmezett értéke 0, ami azt jelenti, hogy nincs újrapróbálás.
* **databaseId** – adja meg a kimeneti adatok gyorsítótárazása használandó adatbázis. Ha nincs megadva, az alapértelmezett érték a 0 szolgál.
* **applicationName** – kulcsok vannak tárolva, a redis `{<Application Name>_<Session ID>}_Data`. Az elnevezési sémát lehetővé teszi, hogy több alkalmazás közösen a Redis-példányt. Ez a paraméter nem kötelező, és ha nem ad meg, egy alapértelmezett értéket használja.
* **connectionTimeoutInMilliseconds** – Ez a beállítás lehetővé teszi a StackExchange.Redis-ügyfélben connectTimeout-beállításának felülbírálása. Ha nincs megadva, az alapértelmezett connectTimeout 5000 beállítással. További információkért lásd: [StackExchange.Redis konfigurációs modell](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – Ez a beállítás lehetővé teszi a StackExchange.Redis-ügyfélben syncTimeout-beállításának felülbírálása. Ha nincs megadva, az alapértelmezett syncTimeout 1000 beállítással. További információkért lásd: [StackExchange.Redis konfigurációs modell](http://go.microsoft.com/fwlink/?LinkId=398705).

Ezeket a tulajdonságokat kapcsolatos további információkért tekintse meg az eredeti blogbejegyzésben található: [bejelentése ASP.NET munkamenetállapot-szolgáltatóját a Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Ne feledje megjegyzéssé a szabványos InProc munkamenet állapota szolgáltató szakasz a Web.config fájlban.

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

A lépések elvégzése után az alkalmazás a Redis gyorsítótár munkamenetállapot-szolgáltatóját használatára van konfigurálva. A munkamenet-állapot az alkalmazás használatakor Azure Redis Cache példány tárolódik.

> [!IMPORTANT]
> A gyorsítótárban tárolt adatok szerializálhatónak kell lennie, eltérően az adatokat, az alapértelmezett tárolható memórián belüli az ASP.NET munkamenet szolgáltató állapot. A munkamenetállapot-szolgáltatóját a Redis használata esetén győződjön meg, hogy szerializálható-e a munkamenet-állapot tárolt adatok típusát.
> 
> 

## <a name="aspnet-session-state-options"></a>Az ASP.NET munkamenet-állapot beállításai
* Ez a szolgáltató memória munkamenetállapot-szolgáltató - tárolja a munkamenet-állapot, a memória. Ez a szolgáltató használatának előnye gyorsan és egyszerűen. A webalkalmazások azonban használata a memória-szolgáltató nem elosztott óta nem méretezhető.
* SQL Server munkamenetállapot-szolgáltató - szolgáltató Sql Server tárolja a munkamenet-állapot. Használja ezt a szolgáltatót, ha azt szeretné, hogy a munkamenet-állapot tárolásához az állandó tároló. A webalkalmazás méretezheti, de használata az Sql Server munkamenet teljesítmény hatást gyakorol a webalkalmazás.
* Elosztott a memória munkamenetállapot-szolgáltatóját például a Redis gyorsítótár munkamenetállapot-szolgáltatóját – Ez a szolgáltató lehetővé teszi a legjobb mindkét világot. A webes alkalmazás lehet egy egyszerű, gyors és méretezhető munkamenetállapot-szolgáltatóját. Ez a szolgáltató gyorsítótárban tárolja a munkamenet-állapot, mert az alkalmazás ki a kapcsolódó, amikor egy elosztott a gyorsítótárban, például az átmeneti hálózati hibák való jellemzők melyeket figyelembe venni. Gyakorlati tanácsok a gyorsítótár használatával, lásd: [útmutatást gyorsítótárazás](../best-practices-caching.md) Microsoft Patterns & eljárások [Azure Cloud alkalmazás tervezési és megvalósítási útmutatást](https://github.com/mspnp/azure-guidance).

További információ a munkamenet-állapot és egyéb bevált gyakorlatokat: [webes fejlesztési gyakorlati tanácsok (épület valós felhőalapú alkalmazásokat az Azure-ral)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>További lépések
Tekintse meg a [az ASP.NET kimeneti gyorsítótár-szolgáltató Azure Redis Cache](cache-aspnet-output-cache-provider.md).


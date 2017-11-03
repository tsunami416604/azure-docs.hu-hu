---
title: "Gyorsítótár az ASP.NET kimeneti gyorsítótár-szolgáltató"
description: "Útmutató: Azure Redis Cache használatának ASP.NET lap kimeneti gyorsítótár"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: sdanie
ms.openlocfilehash: 845f25637a0e48460fc76c1ee36060274b3cec38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Az ASP.NET kimeneti gyorsítótár-szolgáltató az Azure Redis gyorsítótár
A Redis kimeneti gyorsítótár-szolgáltató egy olyan folyamaton-tárolási mechanizmus a kimeneti gyorsítótár adatokhoz. Ezek az adatok kifejezetten a teljes HTTP-válaszok van (a kimeneti gyorsítótár oldalon). A szolgáltató csatlakozik az új kimeneti gyorsítótár szolgáltató bővítési pontot, az ASP.NET 4 lett bevezetve.

A kimeneti gyorsítótár Redis szolgáltatót használja, először konfigurálja a gyorsítótárhoz, és konfigurálja az ASP.NET-alkalmazás a Redis kimeneti gyorsítótár szolgáltató NuGet csomag segítségével. Ez a témakör az alkalmazás használhatja a Redis kimeneti gyorsítótár-szolgáltató konfigurálása nyújt útmutatást. Létrehozásával és az Azure Redis Cache példány konfigurálásával kapcsolatos további információkért lásd: [gyorsítótár létrehozásához](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET lap kimenete tárolja a gyorsítótárban
Ügyfélalkalmazás konfigurálása a Visual Studio használatával a Redis gyorsítótár munkamenet állapota NuGet-csomagot, kattintson a **NuGet-Csomagkezelő**, **Csomagkezelő konzol** a a **eszközök** menü.

Futtassa az alábbi parancsot a `Package Manager Console` ablakából.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

A Redis kimeneti gyorsítótár szolgáltató NuGet-csomagot a StackExchange.Redis.StrongName csomag függőség rendelkezik. Ha a StackExchange.Redis.StrongName csomag nincs jelen a projektben, telepíti a rendszer. A Redis kimeneti gyorsítótár szolgáltató NuGet-csomag kapcsolatos további információkért tekintse meg a [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet lap.

>[!NOTE]
>Az erős névvel ellátott StackExchange.Redis.StrongName csomag mellett is van a StackExchange.Redis nem-erős névvel ellátott verzióra. A projekt távolítsa el a nem-erős névvel ellátott StackExchange.Redis verzióját használja, ha más módon meg beolvasása ütközése a projektben. Ezeket a csomagokat kapcsolatos további információkért lásd: [konfigurálása .NET-gyorsítótárazási ügyfelek számára](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

A NuGet-csomagot tölti le, és hozzáadja a szükséges összeállítási referenciát, és hozzáadja az alábbi szakasz a web.config fájlt. Ez a szakasz a kötelező beállítani az ASP.NET-alkalmazás a Redis kimeneti gyorsítótár-szolgáltató használatára.

```xml
<caching>
  <outputCachedefault Provider="MyRedisOutputCache">
    <providers>
      <!--
      <add name="MyRedisOutputCache"
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
  </outputCache>
</caching>
```

A megjegyzésként szakasz és a minta-beállítások minden attribútum példaként szolgál.

Az attribútumok konfigurálása a Microsoft Azure-portálon a gyorsítótár paneljét értékeivel, és konfigurálja a többi érték tetszés szerint. A gyorsítótár tulajdonságai elérése, lásd: [konfigurálása Redis gyorsítótár beállításainak](cache-configure.md#configure-redis-cache-settings).

* **állomás** – adja meg a gyorsítótár végpontjához.
* **port** – használja a nem SSL port vagy az SSL-port, attól függően, hogy az ssl-beállítások.
* **accessKey** – az elsődleges vagy másodlagos kulcsot használja a gyorsítótárhoz.
* **SSL** – igaz, ha azt szeretné, hogy biztonságos SSL/gyorsítótárügyfél kommunikációhoz; ellenkező esetben hamis. Mindenképp adja meg a megfelelő porthoz.
  * A nem SSL port az új gyorsítótárakhoz alapértelmezés szerint le van tiltva. Adja meg az IGAZ állítja ezt a beállítást, az SSL-port használatára. A nem SSL port engedélyezésével kapcsolatos további információkért lásd: a [hozzáférési portok](cache-configure.md#access-ports) szakasz a [gyorsítótár konfigurálása](cache-configure.md) témakör.
* **databaseId** – megadott gyorsítótár használandó adatbázis kimeneti adatokat. Ha nincs megadva, az alapértelmezett érték a 0 szolgál.
* **applicationName** – kulcsok vannak tárolva, a redis `<AppName>_<SessionId>_Data`. Az elnevezési sémát lehetővé teszi, hogy több alkalmazás közösen ugyanazzal a kulccsal. Ez a paraméter nem kötelező, és ha nem ad meg, egy alapértelmezett értéket használja.
* **connectionTimeoutInMilliseconds** – Ez a beállítás lehetővé teszi a StackExchange.Redis-ügyfélben connectTimeout-beállításának felülbírálása. Ha nincs megadva, az alapértelmezett connectTimeout 5000 beállítással. További információkért lásd: [StackExchange.Redis konfigurációs modell](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – Ez a beállítás lehetővé teszi a StackExchange.Redis-ügyfélben syncTimeout-beállításának felülbírálása. Ha nincs megadva, az alapértelmezett syncTimeout 1000 beállítással. További információkért lásd: [StackExchange.Redis konfigurációs modell](http://go.microsoft.com/fwlink/?LinkId=398705).

Minden oldalon, amelynek a kimeneti gyorsítótár kívánja hozzáadása az OutputCache direktívában.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

A korábbi példában a gyorsítótárazott oldaladatokat a gyorsítótárban marad 60 másodpercen keresztül, és minden egyes paraméter kombináció gyorsítótárazza a lap egy másik verziója. Az OutputCache direktívában kapcsolatos további információkért lásd: [ @OutputCache ](http://go.microsoft.com/fwlink/?linkid=320837).

A lépések elvégzése után az alkalmazás a Redis kimeneti gyorsítótár-szolgáltató használatára van konfigurálva.

## <a name="next-steps"></a>Következő lépések
Tekintse meg a [ASP.NET munkamenetállapot-szolgáltatóját az Azure Redis Cache](cache-aspnet-session-state-provider.md).


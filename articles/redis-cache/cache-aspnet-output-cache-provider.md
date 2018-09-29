---
title: Cache ASP.NET kimenetigyorsítótár-szolgáltatója
description: Ismerje meg, hogyan gyorsítótárazása az ASP.NET-lap kimenete Azure Redis Cache használatával
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: wesmc
ms.openlocfilehash: a6c3314a981b46aa6f1cbca1f34392d1e1ae6c9a
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431644"
---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>A Redis Cache ASP.NET kimenetigyorsítótár-szolgáltatója az Azure-hoz
A redis Cache kimeneti gyorsítótár-szolgáltatóját egy folyamaton tárolási mechanizmus kimeneti gyorsítótár adatainak. Ezeket az adatokat a kifejezetten a teljes HTTP-válaszok (kimeneti gyorsítótár oldalon). A szolgáltató rendkívüli az új kimeneti gyorsítótár szolgáltató indításkiterjesztési pont az ASP.NET 4 verzióban jelent meg.

A redis Cache kimeneti gyorsítótár-szolgáltató használatához először konfigurálja a gyorsítótárat, és konfigurálja az ASP.NET-alkalmazást, a redis Cache kimeneti gyorsítótár szolgáltató NuGet-csomag használatával. Ez a témakör útmutatást nyújt az alkalmazás használhatja a redis Cache kimeneti gyorsítótár-szolgáltató konfigurálásáról. Létrehozásával és az Azure Redis Cache-példány konfigurálásával kapcsolatos további információkért lásd: [hozzon létre egy gyorsítótárat](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Store ASP.NET lap kimenete a gyorsítótárban
Ügyfélalkalmazás konfigurálása a Redis Cache munkamenet állapota NuGet-csomag használatával a Visual studióban, kattintson a **NuGet-Csomagkezelő**, **Package Manager Console** származó a **eszközök**menüben.

Futtassa az alábbi parancsot a `Package Manager Console` ablakából.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

A redis Cache kimeneti gyorsítótár szolgáltató NuGet-csomag maga a StackExchange.Redis.StrongName csomagot. Ha a StackExchange.Redis.StrongName csomag nem található a projekt, telepíti a rendszer. A redis Cache kimeneti gyorsítótár szolgáltató NuGet-csomaggal kapcsolatos további információkért lásd: a [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet-oldalon.

>[!NOTE]
>Az erős elnevezésű StackExchange.Redis.StrongName csomagon kívül van a StackExchange.Redis nem – erős elnevezésű verzióját. A projekt távolítsa el a nem – erős elnevezésű StackExchange.Redis verzióját használja, ha egyébként, lekérése az elnevezések ütközésének a projektben. Ezek a csomagok kapcsolatos további információkért lásd: [gyorsítótárügyfelek konfigurálása .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

A NuGet-csomag letölti és hozzáadja a szükséges szerelvényhivatkozásokat, és hozzáadja az alábbi szakasz a web.config fájl. Ez a szakasz tartalmazza a szükséges konfigurációval, hogy az ASP.NET alkalmazás a redis Cache kimeneti gyorsítótár-szolgáltatóját használja.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <!-- For more details check https://github.com/Azure/aspnet-redis-providers/wiki -->
      <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
      <!-- 'databaseId' and 'applicationName' can be used with both options. -->
      <!--
      <add name="MyRedisOutputCache" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
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
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

A megjegyzésekkel szakasz mutatja be az attribútumokat és a minta beállítások minden attribútum.

Az attribútumok konfigurálása a Microsoft Azure Portalon a gyorsítótár panelről az értékekkel, és igény szerint állítsa be a más értékeket. Utasítások a fér hozzá a gyorsítótár tulajdonságai: [a redis Cache-gyorsítótár beállításai](cache-configure.md#configure-redis-cache-settings).

* **gazdagép** – adja meg a gyorsítótár végpontját.
* **port** – a nem SSL port vagy az SSL-port, attól függően, az ssl-beállítások használatára.
* **accessKey** – az elsődleges vagy másodlagos kulcsot használja a gyorsítótárat.
* **az SSL** – igaz, ha biztonságos gyorsítótárügyfél/kommunikáció ssl-lel szeretne; ellenkező esetben hamis. Mindenképpen adja meg a megfelelő portot.
  * A nem SSL port az új gyorsítótárakhoz alapértelmezés szerint le van tiltva. Adja meg az igaz értékre állítja ezt a beállítást, az SSL-port használatára. A nem SSL port engedélyezésével kapcsolatos további információkért lásd: a [hozzáférési portok](cache-configure.md#access-ports) című rész a [gyorsítótár konfigurálása](cache-configure.md) témakör.
* **adatbázis-azonosítóhoz** – megadott gyorsítótár használandó adatbázis kimeneti adatokat. Ha nincs megadva, az alapértelmezett érték a 0 használatos.
* **applicationName** – kulcsok vannak tárolva, a redis `<AppName>_<SessionId>_Data`. Az elnevezési sémája lehetővé teszi több alkalmazás ugyanazt a kulcsot használják. Ez a paraméter nem kötelező, és ha nem rendelkeznek egy alapértelmezett értéket használja.
* **connectionTimeoutInMilliseconds** – Ez a beállítás lehetővé teszi, hogy a StackExchange.Redis ügyfél connectTimeout-beállításának felülbírálása. Ha nincs megadva, az alapértelmezett connectTimeout beállítás 5000-es szolgál. További információkért lásd: [StackExchange.Redis konfigurációs modell](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – Ez a beállítás lehetővé teszi, hogy a StackExchange.Redis ügyfél syncTimeout-beállításának felülbírálása. Ha nincs megadva, az alapértelmezett syncTimeout beállítás 1000 szolgál. További információkért lásd: [StackExchange.Redis konfigurációs modell](http://go.microsoft.com/fwlink/?LinkId=398705).

Adjon hozzá egy OutputCache irányelv minden olyan oldalhoz, amelyhez szeretné, a kimeneti gyorsítótárban.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

Az előző példában az oldal gyorsítótárazott adatokat a gyorsítótárban marad 60 másodpercig, és a egy másik verziót az oldal az egyes paraméterkombináció van gyorsítótárazva. Direktiva OutputCache kapcsolatos további információkért lásd: [ @OutputCache ](http://go.microsoft.com/fwlink/?linkid=320837).

Ezek a lépések elvégzése után az alkalmazás a redis Cache kimeneti gyorsítótár-szolgáltató használatára van konfigurálva.

## <a name="next-steps"></a>További lépések
Tekintse meg a [ASP.NET munkamenetállapot-szolgáltatóját Azure Redis Cache](cache-aspnet-session-state-provider.md).


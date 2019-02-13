---
title: Cache ASP.NET munkamenetállapot-szolgáltatója |} A Microsoft Docs
description: 'Útmutató: Azure Cache a Redis használata ASP.NET-munkamenetállapot tárolására'
services: azure-cache-for-redis
documentationcenter: na
author: yegu-ms
manager: cfowler
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: azure-cache-for-redis
ms.workload: tbd
ms.date: 05/01/2017
ms.author: yegu
ms.openlocfilehash: 09be5cc75d72a47071f7e3256fd3354a5041993e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56117714"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Redis Azure Cache for ASP.NET munkamenetállapot-szolgáltatója
Az Azure Cache redis biztosít egy használó SQL Server-adatbázis helyett tárolni a munkamenet állapota a memóriában az Azure Cache Redis munkamenetállapot-szolgáltatóját. A gyorsítótárazási munkamenetállapot-szolgáltató használatához először konfigurálja a gyorsítótár, és konfigurálja az ASP.NET-alkalmazások gyorsítótár használatával az Azure Cache Redis munkamenet-állapot NuGet-csomagot.

Gyakran nem célszerű egy való életből vett felhőalapú alkalmazás valamilyen állapot tárolásához egy felhasználói munkamenethez tartozó elkerülése érdekében, de néhány olyan módszert hatással van a teljesítmény és méretezhetőség több, mint mások. Ha már állapot tárolására, a legjobb megoldás, maradjon kicsi állapot mennyisége és a cookie-kban tárolja. Amely nem megvalósítható, ha a következő legjobb megoldás, hogy az ASP.NET munkamenet-állapot szolgáltatóval elosztott, memóriában lévő gyorsítótárhoz. A legrosszabb megoldás a teljesítmény- és skálázhatósági szempontból az adatbázis biztonsági munkamenetállapot-szolgáltatóját. Ez a témakör útmutatást nyújt az Azure Cache ASP.NET munkamenetállapot-szolgáltatója a Redis használata. További munkamenet-állapot lehetőségekről további információkért lásd: [ASP.NET munkamenet-állapot beállításai](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Store ASP.NET session state in the cache (ASP.NET munkamenet-állapot tárolása a gyorsítótárban)
Ügyfélalkalmazás konfigurálása az Azure Cache Redis munkamenet-állapot NuGet-csomag használatával a Visual studióban, kattintson a **NuGet-Csomagkezelő**, **Package Manager Console** származó a **eszközök**  menüben.

Futtassa az alábbi parancsot a `Package Manager Console` ablakából.
    
```
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Ha a prémium szintről a fürtszolgáltatást használ, kell használnia [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 vagy magasabb vagy kivétel lépett fel. Használhatatlanná tévő változást; áthelyezése 2.0.1 vagy újabb rendszer További információkért lásd: [v2.0.0 használhatatlanná tévő változás részletei](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). Ez a cikk a frissítés során ez a csomag aktuális verziója 2.2.3.
> 
> 

A Redis munkamenet állapota szolgáltató NuGet-csomag maga a StackExchange.Redis.StrongName csomagot. Ha a StackExchange.Redis.StrongName csomag nem található a projekt, telepíti a rendszer.

>[!NOTE]
>Az erős elnevezésű StackExchange.Redis.StrongName csomagon kívül van a StackExchange.Redis nem – erős elnevezésű verzióját. A projekt távolítsa el a nem – erős elnevezésű StackExchange.Redis verzióját használja, ha egyébként, lekérése az elnevezések ütközésének a projektben. Ezek a csomagok kapcsolatos további információkért lásd: [gyorsítótárügyfelek konfigurálása .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

A NuGet-csomag letölti és hozzáadja a szükséges szerelvényhivatkozásokat, és hozzáadja az alábbi szakasz a web.config fájl. Ez a szakasz tartalmazza a szükséges konfigurációval, hogy az ASP.NET alkalmazás az Azure Cache redis Cache munkamenetállapot-szolgáltatóját használja.

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
</sessionState>
```

A megjegyzésekkel szakasz mutatja be az attribútumokat és a minta beállítások minden attribútum.

Az attribútumok konfigurálása a Microsoft Azure Portalon a gyorsítótár panelről az értékekkel, és igény szerint állítsa be a más értékeket. Utasítások a fér hozzá a gyorsítótár tulajdonságai: [konfigurálása Azure Cache Redis beállítások](cache-configure.md#configure-azure-cache-for-redis-settings).

* **gazdagép** – adja meg a gyorsítótár végpontját.
* **port** – a nem SSL port vagy az SSL-port, attól függően, az ssl-beállítások használatára.
* **accessKey** – az elsődleges vagy másodlagos kulcsot használja a gyorsítótárat.
* **az SSL** – igaz, ha biztonságos gyorsítótárügyfél/kommunikáció ssl-lel szeretne; ellenkező esetben hamis. Mindenképpen adja meg a megfelelő portot.
  * A nem SSL port az új gyorsítótárakhoz alapértelmezés szerint le van tiltva. Adja meg az igaz értékre állítja ezt a beállítást, az SSL-port használatára. A nem SSL port engedélyezésével kapcsolatos további információkért lásd: a [hozzáférési portok](cache-configure.md#access-ports) című rész a [gyorsítótár konfigurálása](cache-configure.md) témakör.
* **throwOnError** – igaz, ha azt szeretné, hogy kivételt kell fordul elő, ha hiba vagy hamis értéket, ha azt szeretné, hogy a művelet csendes meghiúsul. A statikus Microsoft.Web.Redis.RedisSessionStateProvider.LastException tulajdonság ellenőrzésével hibája ellenőrizheti. Az alapértelmezett érték true.
* **retryTimeoutInMilliseconds** – műveletek megismétlődnek erre az időszakra, ezredmásodpercben megadva. Az első újrapróbálkozás után 20 ezredmásodperc történik, és az újrapróbálkozások kerül sor, amíg le nem jár a retryTimeoutInMilliseconds időköz másodpercenként. Ez az időtartam után azonnal a művelet végső egyszer rendszer. Ha a művelet továbbra is sikertelen, a kivétel történt vissza a hívónak a throwOnError beállítás függően. Az alapértelmezett értéke 0, ami azt jelenti, hogy nincs újrapróbálkozás.
* **adatbázis-azonosítóhoz** – Itt adhatja meg a kimeneti adatok gyorsítótárazása használandó adatbázis. Ha nincs megadva, az alapértelmezett érték a 0 használatos.
* **applicationName** – kulcsok vannak tárolva, a redis `{<Application Name>_<Session ID>}_Data`. Az elnevezési sémája lehetővé teszi több alkalmazás megosztása a Redis-példányt. Ez a paraméter nem kötelező, és ha nem rendelkeznek egy alapértelmezett értéket használja.
* **connectionTimeoutInMilliseconds** – Ez a beállítás lehetővé teszi, hogy a StackExchange.Redis ügyfél connectTimeout-beállításának felülbírálása. Ha nincs megadva, az alapértelmezett connectTimeout beállítás 5000-es szolgál. További információkért lásd: [StackExchange.Redis konfigurációs modell](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – Ez a beállítás lehetővé teszi, hogy a StackExchange.Redis ügyfél syncTimeout-beállításának felülbírálása. Ha nincs megadva, az alapértelmezett syncTimeout beállítás 1000 szolgál. További információkért lásd: [StackExchange.Redis konfigurációs modell](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** – ezzel a beállítással adhatja meg az egyéni szerializálás a Redis küldött munkamenet-tartalom. A megadott típus musí implementovat `Microsoft.Web.Redis.ISerializer` és deklarálni kell a nyilvános paraméter nélküli konstruktor. Alapértelmezés szerint `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` szolgál.

Ezek a Tulajdonságok kapcsolatos további információkért lásd: az eredeti blogbejegyzés-közleményben, [bejelentése ASP.NET munkamenetállapot-szolgáltatóját a Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Ne felejtse el a szabványos InProc munkamenet állapota szolgáltató szakasz a web.config fájlban tegye megjegyzésbe.

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

Ezek a lépések elvégzése után az alkalmazás van konfigurálva az Azure Cache redis Cache munkamenetállapot-szolgáltatóját használja. Az alkalmazás munkamenet-állapotot használ, tárolja a Redis-példányt az Azure Cache-ben.

> [!IMPORTANT]
> A gyorsítótárban tárolt adatokat a szerializálható, ellentétben az alapértelmezett tárolhatja az adatokat a memóriában az ASP.NET munkamenet szolgáltató állapot. A redis munkamenetállapot-szolgáltató használata esetén lehet arra, hogy a munkamenet-állapot az tárolt adattípusokat szerializálható.
> 
> 

## <a name="aspnet-session-state-options"></a>Az ASP.NET munkamenet-állapot beállításai
* Ez a szolgáltató memória munkamenetállapot-szolgáltatója – a munkamenet-állapot, a memóriában tárolja. Ez a szolgáltató használatával előnye, egyszerű és gyors. A Web Apps viszont ha használja a szolgáltató memóriája óta nem elosztott nem skálázhatja.
* Az SQL Server munkamenetállapot-szolgáltatója – Ez a szolgáltató az Sql Server tárolja a munkamenet-állapot. Ez a szolgáltató használja, ha a munkamenet-állapot tárolásához az állandó tárolóban. Képes méretezni a webalkalmazásokat, de a teljesítmény hatással van az Sql Server használata a munkamenet. Is használhatja ezt a szolgáltató egy [In-Memory OLTP konfigurációs](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) teljesítményének javítása érdekében.
* Elosztott a memória munkamenetállapot-szolgáltató például az Azure Cache a redis Cache munkamenetállapot-szolgáltató – Ez a szolgáltató biztosítja használja ki mindkét világ előnyeit. A webes alkalmazás lehet egy egyszerű, gyors és méretezhető munkamenetállapot-szolgáltatóját. Ez a szolgáltató gyorsítótárban tárolja a munkamenet-állapot, mert az alkalmazás rendelkezik veszi figyelembe a jellemzők kapcsolódó, ha egy elosztott a gyorsítótárban, például az átmeneti hálózati hibák folytatott kommunikációra. Ajánlott eljárások a gyorsítótár használatával, lásd: [gyorsítótárazási útmutató](../best-practices-caching.md) a Microsoft Patterns & eljárások [Azure felhőalapú alkalmazás tervezési és implementálási segédlet](https://github.com/mspnp/azure-guidance).

A munkamenet-állapot és más ajánlott eljárásokról kapcsolatos további információkért lásd: [webkiszolgáló alkalmazásfejlesztési gyakorlatok (valós felhőalapú alkalmazások létrehozása az Azure-ral)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>További lépések
Tekintse meg a [ASP.NET kimenetigyorsítótár-szolgáltatója az Azure Cache redis](cache-aspnet-output-cache-provider.md).


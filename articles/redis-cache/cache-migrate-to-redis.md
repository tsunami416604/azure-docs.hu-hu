---
title: "Telepítse át a Redis - Azure Managed Cache Service-alkalmazások |} Microsoft Docs"
description: "Ismerje meg, hogyan telepítheti át az Azure Redis Cache Managed Cache Service és a szerepköralapú gyorsítótár alkalmazások"
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/30/2017
ms.author: wesmc
ms.openlocfilehash: 0d52454ae1c2159814d4601d07259aba319e8598
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Azure Redis Cache Managed Cache Service áttelepítése
Az Azure Managed Cache Service Azure Redis Cache használó alkalmazások áttelepítése az alkalmazáshoz, attól függően, hogy a Managed Cache Service szolgáltatásokat használják a gyorsítótárazási alkalmazás legfeljebb minimális változtatásokra is elvégezhető. Míg az API-k nem pontosan ugyanaz hasonló, és nagy részét a meglévő kód Managed Cache Service alapján fér hozzá újrahasználhatók minimális módosításait. Ez a témakör bemutatja, hogyan végezheti el a szükséges konfigurációs és telepítse át a Managed Cache Service-alkalmazások Azure Redis Cache az alkalmazás módosításait, és bemutatja, hogyan néhány Azure Redis Cache funkciója használható Managed funkcióinak végrehajtásához Gyorsítótár gyorsítótára.

>[!NOTE]
>Felügyelt Cache Service és a szerepköralapú gyorsítótár [kivont](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) November 30 2016. Ha az áttelepítendő Azure Redis Cache-hoz tartozó szerepköralapú gyorsítótár telepítéssel rendelkezik, a cikkben leírt lépéseket követheti.

## <a name="migration-steps"></a>Áttelepítés lépései
A következő lépések szükségesek, áttelepítheti egy Managed Cache Service-alkalmazást az Azure Redis Cache használata.

* Azure Redis Cache van leképezve Managed Cache Service szolgáltatások
* Válassza ki a Cache-ajánlatot
* A gyorsítótár létrehozása
* A gyorsítótár-ügyfelek konfigurálása
  * A Managed Cache Service konfiguráció eltávolítása
  * A StackExchange.Redis NuGet csomag segítségével gyorsítótár-ügyfél konfigurálása
* Telepítse át a Managed Cache Service kódot
  * Csatlakozás a gyorsítótárhoz, a ConnectionMultiplexer osztály használatával
  * Hozzáférés primitív adattípusokat a gyorsítótárban
  * .NET-objektumok használata a gyorsítótárban
* Az ASP.NET munkamenet-állapot és a kimeneti gyorsítótár Azure Redis Cache áttelepítése 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Azure Redis Cache van leképezve Managed Cache Service szolgáltatások
Az Azure Managed Cache Service és az Azure Redis Cache hasonló, de egyes szolgáltatásaik különböző módon végrehajtása. Ez a szakasz közötti fontosabb különbségeket ismerteti, és az Azure Redis Cache Managed Cache Service részeit, végrehajtási nyújt útmutatást.

| Felügyelt Cache Service szolgáltatás | Felügyelt gyorsítótár szolgáltatás támogatása | Azure Redis Cache-támogatás |
| --- | --- | --- |
| Elnevezett gyorsítótárak |Egy alapértelmezett gyorsítótár van beállítva, és a Standard és Premium gyorsítótárában ajánlatokat, legfeljebb kilenc további gyorsítótárak nevű beállítható úgy, ha szükséges. |Azure Redis gyorsítótár rendelkeznie konfigurálható adatbázisok (alapértelmezett 16-os) az elnevezett gyorsítótárak hasonló funkciót végrehajtásához használható. További információ: [Mik azok a Redis-adatbázisok?](cache-faq.md#what-are-redis-databases) és [A Redis-kiszolgáló alapértelmezett konfigurációja](cache-configure.md#default-redis-server-configuration). |
| Magas rendelkezésre állás |Az elemek a gyorsítótárban a Standard és Premium gyorsítótár ajánlatokat a magas rendelkezésre állást biztosít. Ha a cikkeket egy hiba miatt megszakadt, a gyorsítótár elemeinek biztonsági másolatok továbbra is elérhetők. A másodlagos gyorsítótár bejegyzést ír a szinkron módon történik. |Magas rendelkezésre állás a Standard és prémium szintű gyorsítótár ajánlatok, amely két csomópont elsődleges vagy replika konfigurációt (a Premium-gyorsítótár egyes shard rendelkezik egy elsődleges vagy replika pár) érhető el. Írási műveleteket ad ki a replika aszinkron módon történik. További információkért lásd: [Azure Redis Cache árképzési](https://azure.microsoft.com/pricing/details/cache/). |
| Értesítések |Lehetővé teszi az ügyfelek aszinkron értesítéseket, amikor egy elnevezett gyorsítótár gyorsítótár-műveletekhez különböző. |Ügyfélalkalmazások használhatja a Redis pub/sub vagy [kulcstérértesítések használatával értesítések](cache-configure.md#keyspace-notifications-advanced-settings) értesítések hasonló funkciót eléréséhez. |
| Helyi gyorsítótár |Az ügyfélen a extra gyors hozzáférés helyileg tárolja a gyorsítótárazott objektum egy példányát. |Ügyfélalkalmazások kellene ezt a funkciót a szótár vagy hasonló adatszerkezet végrehajtásához. |
| A kiürítési házirend |Nincs vagy LRU. Az alapértelmezett házirend LRU. |Azure Redis Cache támogatja a következő kiürítési házirendek: ideiglenes-lru, allkeys-lru, ideiglenes véletlenszerű, allkeys-véletlen ideiglenes ttl, noeviction. Az alapértelmezett házirend ideiglenes-lru. További információkért lásd: [kiszolgálókonfiguráció alapértelmezett Redis](cache-configure.md#default-redis-server-configuration). |
| Elévülési szabályzatának |Az alapértelmezett lejárati házirendet abszolút, és az alapértelmezett lejárati időszak tíz perc. A késleltetett és Never házirendek is elérhetők. |Alapértelmezés szerint a gyorsítótárban elemek nem jár le, de egy lejárati gyorsítótár set túlterhelés használatával / írási szinten konfigurálható. További információkért lásd: [hozzáadása, és kérje le a gyorsítótárból objektumok](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache). |
| Régiók és címkézés |Régiók a következők elemek gyorsítótárazott alcsoportok. Régiók is támogatja a jegyzet gyorsítótárazott elemek további leíró karakterláncok címkéket. Régiók támogatja az adott régióban címkézett elemek keresési műveletek végrehajtásához. Régión belül az összes elemet a gyorsítótár-fürt egyetlen csomópont belül találhatók. |A Redis gyorsítótár áll egyetlen csomópont (kivéve, ha engedélyezve van a Redis-fürt), a Managed Cache Service régiók fogalma nem érvényes. Redis támogatja a keresést és a helyettesítő művelet, így leíró címkék a kulcsnevek ágyazva, és később kiolvassa az elemeket a kulcsok beolvasása közben. Egy Redis használó címkézési megoldás végrehajtási példáért lásd: [címkézés – Redis gyorsítótár végrehajtási](http://stackify.com/implementing-cache-tagging-redis/). |
| Szerializálási |Felügyelt gyorsítótár NetDataContractSerializer, BinaryFormatter és egyéni objektumszerializáló használatát támogatja. Az alapértelmezett érték a NetDataContractSerializer. |A feladata az ügyfélalkalmazás .NET-objektumokat szerializálni előtt a szerializáló legfeljebb az ügyfél alkalmazásfejlesztő választható a gyorsítótárba helyezi őket. További információk és mintakód [.NET objektumok a gyorsítótárban](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Gyorsítótár-emulátor |Felügyelt gyorsítótár biztosít egy helyi gyorsítótár emulátor. |Azure Redis Cache-gyorsítótár nem rendelkezik az emulátor, azonban úgy is [futtassa helyileg a redis-server.exe MSOpenTech buildverzióját](cache-faq.md#cache-emulator) az emulátor érdekében. |

## <a name="choose-a-cache-offering"></a>Válassza ki a Cache-ajánlatot
A Microsoft Azure Redis Cache a következő szinteken érhető el:

* **Alapszintű** – Egyetlen csomópont. Többféle méret legfeljebb 53 GB-ig.
* **Standard** – Két csomópontból álló elsődleges/replika. Többféle méret legfeljebb 53 GB-ig. 99,9%-os SLA.
* **Prémium** – Két csomópontból álló elsődleges/replika, legfeljebb 10 szegmenssel. Többféle méret 6 GB és 530 GB között. Tartalmazza a Standard szint összes szolgáltatását és továbbiakat, beleértve a [Redis-fürtök](cache-how-to-premium-clustering.md), a [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) és az [Azure Virtual Network](cache-how-to-premium-vnet.md) támogatását. 99,9%-os SLA.

Az egyes szintek szolgáltatási feltételei és díjszabása eltérő. A szolgáltatások tartoznak, és az árakkal kapcsolatos további információ az útmutató későbbi részében, lásd: [gyorsítótár díjszabás](https://azure.microsoft.com/pricing/details/cache/).

Egy áttelepítés kiindulópontja válasszon, amely megfelel az előző Managed Cache Service gyorsítótár méretének méretét, és majd felfelé vagy lefelé méretezési attól függően, hogy az alkalmazás követelményeinek. A megfelelő Azure Redis Cache-ajánlatot kiválasztásáról További útmutatóért lásd: [milyen Redis Cache-ajánlatot és méretet használjam?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>A gyorsítótár létrehozása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>A gyorsítótár-ügyfelek konfigurálása
A gyorsítótár létrehozása és konfigurálása után a következő lépéssel távolítsa el a Managed Cache Service-konfigurációt, és adja hozzá az Azure Redis Cache konfigurációs hozzáadása és, hogy a gyorsítótár-ügyfelek hozzáférhetnek a gyorsítótár hivatkozik.

* A Managed Cache Service konfiguráció eltávolítása
* A StackExchange.Redis NuGet csomag segítségével gyorsítótár-ügyfél konfigurálása

### <a name="remove-the-managed-cache-service-configuration"></a>A Managed Cache Service konfiguráció eltávolítása
Ahhoz, hogy az ügyfélalkalmazások konfigurálható az Azure Redis Cache, a meglévő Managed Cache Service-konfigurációt, és eltávolítva a Managed Cache Service NuGet csomag szerelvényhivatkozások el kell távolítani.

Távolítsa el a Managed Cache Service NuGet-csomagot, kattintson a jobb gombbal az ügyfélprojekt **Megoldáskezelőben** válassza **NuGet-csomagok kezelése**. Válassza ki a **telepített csomag** csomópont, és írja be W**indowsAzure.Caching** a keresőmezőbe telepített csomagok. Válassza ki **Windows** **Azure Cache** (vagy **Windows** **Azure gyorsítótárazás** attól függően, hogy a NuGet csomag verziója), kattintson **Eltávolítás**, és kattintson a **Bezárás**.

![Távolítsa el az Azure Managed Cache Service NuGet-csomag](./media/cache-migrate-to-redis/IC757666.jpg)

A Managed Cache Service NuGet-csomag eltávolítása eltávolítja a Managed Cache Service szerelvények és a Managed Cache Service bejegyzések az app.config vagy a Web.config fájlban, az ügyfélalkalmazás. A NuGet-csomag eltávolításakor néhány testre szabott beállításokat nem lehet eltávolítani, mert nyissa meg a web.config vagy az App.config fájlt, és győződjön meg arról, hogy a rendszer teljesen eltávolítja a következő elemeket.

Győződjön meg arról, hogy a `dataCacheClients` bejegyzés törlődik a `configSections` elemet. Ne távolítsa el a teljes `configSections` elem; csak távolítsa el a `dataCacheClients` bejegyzést, ha telepítve.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Győződjön meg arról, hogy a `dataCacheClients` szakaszban a rendszer eltávolítja. A `dataCacheClients` szakasz az alábbi példához hasonló lesz.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

A Managed Cache Service configuration eltávolítást követően konfigurálhatja a gyorsítótárügyfél, a következő szakaszban leírt módon.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>A StackExchange.Redis NuGet csomag segítségével gyorsítótár-ügyfél konfigurálása
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Telepítse át a Managed Cache Service kódot
Az API a StackExchange.Redis gyorsítótárügyfél a hasonlít a Managed Cache Service. Ez a szakasz áttekintést a különbségeket.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Csatlakozás a gyorsítótárhoz, a ConnectionMultiplexer osztály használatával
A Managed Cache Service, a gyorsítótár kapcsolatok kezelt a `DataCacheFactory` és `DataCache` osztályok. Az Azure Redis Cache, ezek a kapcsolatok kezeli a `ConnectionMultiplexer` osztály.

Adja hozzá a következő utasítással aljától a tetejéig minden olyan fájlt, amelyből a kívánt gyorsítótár-hozzáféréshez.

```csharp
using StackExchange.Redis
```

Ha ehhez a névtérhez nem oldja meg, ne feledje, hogy hozzáadta a StackExchange.Redis NuGet-csomagot a [a gyorsítótár-ügyfelek konfigurálása](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

> [!NOTE]
> Vegye figyelembe, hogy a StackExchange.Redis-ügyfél igényli-e a .NET-keretrendszer 4-es vagy újabb verzióját.
> 
> 

Csatlakozás az Azure Redis Cache példányt, hívja meg a statikus `ConnectionMultiplexer.Connect` metódus és a végpont és a kulcsot adjon át. Az alkalmazásban egy `ConnectionMultiplexer` példány megosztására egy lehetséges módszer, ha létrehoz egy statikus tulajdonságot, amely egy csatlakoztatott példányt ad vissza, a következő példához hasonlóan. Ez egy szálbiztos módszer csak egyetlen csatlakoztatott `ConnectionMultiplexer`-példány inicializálásához. Ebben a példában `abortConnect` értéke HAMIS, ami azt jelenti, hogy a hívás sikeres lesz, még akkor is, ha nem jön létre a kapcsolat a gyorsítótárba. A `ConnectionMultiplexer` egyik fontos szolgáltatása, hogy automatikusan visszaállítja a kapcsolatot a gyorsítótárral, amint a hálózati problémák vagy egyéb hibák elhárulnak.

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

A gyorsítótár végpontjához, a kulcsok és a portok lehet lekérni a **Redis Cache** a gyorsítótárpéldány panelen. További információkért lásd: [Redis Cache-gyorsítótár tulajdonságai](cache-configure.md#properties).

A kapcsolat létrejötte után térjen vissza a Redis gyorsítótár adatbázis mutató hivatkozás meghívásával a `ConnectionMultiplexer.GetDatabase` metódust. A `GetDatabase` metódussal visszaadott objektum egy egyszerűsített továbbított objektum, amelyet nem kell tárolni.

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

A StackExchange.Redis ügyfél használja a `RedisKey` és `RedisValue` típusok elérésére, és az elemek tárolására a gyorsítótárban. Ezek a típusok alakzatot leginkább egyszerű nyelvi típusok, beleértve a karakterláncot, hozzárendelését, gyakran nem használhatók közvetlenül. Redis karakterláncok a legalapvetőbb típusú Redis értéket, és tartalmazhatnak számos különböző típusú adatok, beleértve a szerializált bináris adatfolyamok, és a típus közvetlenül nem használhatók, amíg használandó módszerek tartalmazó `String` a neve. A legtöbb primitív adattípusokat tároljuk, és az elemek beolvasása a gyorsítótár a a `StringSet` és `StringGet` módszerek, kivéve, ha tárolja gyűjtemények és más Redis adattípusok a gyorsítótárban. 

`StringSet`és `StringGet` nagyon hasonló a Managed Cache Service `Put` és `Get` módszerek, az egyik fő különbség, hogy állítsa be, és a gyorsítótárba egy .NET-objektum beolvasása előtt meg kell (MUST) azt először. 

Meghívásakor `StringGet`, ha az objektum létezik, akkor adja vissza, és ha nem, null ad vissza. Ebben az esetben az értéket lekérheti a kívánt adatforrásból, és a gyorsítótárban tárolhatja későbbi használatra. Ez az úgynevezett gyorsítótár-feltöltő mód.

Egy elem lejáratának megadásához a gyorsítótárban használja a `TimeSpan` `StringSet` paraméterét.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure Redis Cache .NET objektumok, valamint a primitív adattípusokat együttműködik, de előtt egy .NET-objektum gyorsítótárazható következően szerializálni kell. Ez az alkalmazás fejlesztőjének feladata. A fejlesztői rugalmasságot Ez biztosítja, hogy a szerializáló. További információk és mintakód [.NET objektumok a gyorsítótárban](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Az ASP.NET munkamenet-állapot és a kimeneti gyorsítótár Azure Redis Cache áttelepítése
Azure Redis Cache van az ASP.NET munkamenet-állapot és a lap kimeneti gyorsítótár-szolgáltatók. Az ilyen szolgáltatók Managed Cache Service verzióit használó alkalmazások áttelepítéséhez először távolítsa el a meglévő szakaszokat a Web.config fájlban, és adja meg az Azure Redis Cache-verziók a szolgáltatók. Az Azure Redis Cache ASP.NET-szolgáltatókat használ, lásd: [ASP.NET munkamenetállapot-szolgáltatóját az Azure Redis Cache](cache-aspnet-session-state-provider.md) és [az ASP.NET kimeneti gyorsítótár-szolgáltató Azure Redis Cache](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>További lépések
Megismerkedhet a [Azure Redis Cache dokumentáció](https://azure.microsoft.com/documentation/services/cache/) az oktatóanyagok, példák, videók és több.


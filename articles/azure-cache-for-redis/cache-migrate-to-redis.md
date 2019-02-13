---
title: Managed Cache Service-alkalmazások a Redis - Azure-bA |} A Microsoft Docs
description: Ismerje meg, hogyan telepítheti át a Managed Cache Service és a szerepköralapú gyorsítótár alkalmazások az Azure Cache redis
services: azure-cache-for-redis
documentationcenter: na
author: yegu-ms
manager: cfowler
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: azure-cache-for-redis
ms.workload: tbd
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: a98b6b426ee1bf9784394ce4a83dca83179f7c28
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110878"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Managed Cache Service szolgáltatásból át az Azure Cache redis
Az alkalmazások, amelyek használják az Azure Managed Cache Service az Azure Cache redis migrálása az alkalmazását, az gyorsítótárazási alkalmazása által használt Managed Cache Service funkcióktól függően legfeljebb minimális változtatásokra is elvégezhető. Míg a API-k nem pontosan ugyanaz hasonló, és legfeljebb minimális változtatásokra nagy része a meglévő kódot, amely a gyorsítótár elérésére használja a Managed Cache Service szolgáltatáshoz felhasználhatók. Ez a cikk bemutatja, hogyan javíthatja a szükséges konfigurációs és alkalmazások áttelepítése a Managed Cache Service-alkalmazások Azure Cache a Redis használata módosítja, és bemutatja, hogyan Azure Cache redis funkcióit használható működésének megvalósítása a Managed Cache Service gyorsítótár.

>[!NOTE]
>Managed Cache Service és a szerepköralapú gyorsítótár is [elavult](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 2016. November 30. Ha a szerepköralapú gyorsítótár telepítések Azure Cache redis áttelepíteni kívánt, követheti a jelen cikkben ismertetett lépések.

## <a name="migration-steps"></a>Áttelepítés lépései
Azure Cache a Redis használata Managed Cache Service-alkalmazások áttelepítése a következő lépések szükségesek.

* Azure Cache redis Managed Cache Service szolgáltatások leképezése
* Válasszon egy Cache-ajánlatot
* Gyorsítótár létrehozása
* A Gyorsítótárügyfelek konfigurálása
  * A Managed Cache szolgáltatás konfigurációjának eltávolítása
  * A StackExchange.Redis NuGet-csomag használatával egy gyorsítótár-ügyfél konfigurálása
* Managed Cache Service kód áttelepítése
  * Csatlakozás a gyorsítótárhoz, a ConnectionMultiplexer osztály használatával
  * Hozzáférés primitív adattípusokat a gyorsítótárban
  * .NET-objektumok használata a gyorsítótárban
* Telepítse át az ASP.NET munkamenet-állapot, és a kimeneti gyorsítótárazása az Azure Cache redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Azure Cache redis Managed Cache Service szolgáltatások leképezése
Az Azure Managed Cache Service és a Redis gyorsítótár hasonlóak, de egyes hozzájuk tartozó funkciók többféle végrehajtása. Ez a szakasz néhány különbségeket mutatja be, és útmutatást nyújt az a funkciók a Managed Cache Service megvalósítása az Azure Cache redis.

| A felügyelt gyorsítótár-szolgáltatás a szolgáltatás | A felügyelt gyorsítótár szolgáltatás támogatása | Az Azure Cache Redis-támogatás |
| --- | --- | --- |
| Nevesített gyorsítótárat foglal magában |Alapértelmezett gyorsítótár van konfigurálva, és a Standard és prémium szintű gyorsítótár az ajánlatok, legfeljebb kilenc további gyorsítótárakat nevű konfigurálható legyen szükség. |Az Azure Cache redis számos konfigurálható az adatbázisok (alapértelmezés szerint 16), amelyek segítségével megvalósítani egy hasonló funkciókat nevesített gyorsítótárat foglal magában. További információ: [Mik azok a Redis-adatbázisok?](cache-faq.md#what-are-redis-databases) és [A Redis-kiszolgáló alapértelmezett konfigurációja](cache-configure.md#default-redis-server-configuration). |
| Magas rendelkezésre állás |Magas rendelkezésre állást biztosít a Standard és prémium szintű gyorsítótár-ajánlatok a gyorsítótár elemeinek. Ha elemeket egy hiba miatt megszakadt, a gyorsítótár elemeinek biztonsági másolatok továbbra is elérhetők. A másodlagos gyorsítótárba ír a szinkron módon történik. |A Standard és prémium szintű gyorsítótár ajánlatokat, amely egy két csomópontos elsődleges/replika configuration (a prémium szintű gyorsítótár minden egyes szegmens rendelkezik elsődleges/replika párban) rendelkeznie magas rendelkezésre állás érhető el. A replika írási műveletek aszinkron módon történik. További információkért lásd: [Azure Cache Redis díjszabási](https://azure.microsoft.com/pricing/details/cache/). |
| Értesítések |Lehetővé teszi az ügyfelek gyorsítótárfürt gyorsítótár-műveleteiről egy nevesített gyorsítótárat a tájékoztató aszinkron üzenetek fogadását. |Ügyfélalkalmazások használhatja a Redis közzétevői/előfizetői vagy [kulcstérértesítések](cache-configure.md#keyspace-notifications-advanced-settings) elérése érdekében a hasonló funkciókat az értesítésekre. |
| Helyi gyorsítótár |Tárolja a gyorsítótárazott objektumok helyileg extra gyors hozzáférés az ügyfélen. |Ügyfélalkalmazások megvalósítása a funkció használatával egy szótárt és hasonló adatszerkezetből kellene. |
| Kiürítési szabályzat |Nincs vagy LRU. Az alapértelmezett szabályzat az LRU. |Az Azure Cache a redis támogatja a következő kiürítési szabályzat: ideiglenes-lru, allkeys-lru, ideiglenes véletlenszerű, allkeys-véletlen ideiglenes ttl, noeviction. Az alapértelmezett házirend ideiglenes-lru. További információkért lásd: [kiszolgálókonfiguráció Redis alapértelmezett](cache-configure.md#default-redis-server-configuration). |
| Elévülési szabályzatot |Az alapértelmezett elévülési szabályzatot abszolút pedig az alapértelmezett lejárati időközét 10 perc. Késleltetett, és soha házirendek is elérhetők. |Alapértelmezés szerint a gyorsítótárban lévő elemek nem járnak le, de egy lejárati konfigurálható a gyorsítótár beállított túlterheléssel használatával / írási történik. |
| Régiók és címkézése |Régiók csak az alcsoportok gyorsítótárazott elemek. Régió is támogatja a gyorsítótárazott elemek jegyzet címkéket további leíró karakterláncot tartalmazó. Régió támogatja a keresési műveletek végrehajtása a bármilyen címkével ellátott elemek az adott régióban. Egy adott régión belül minden elem a gyorsítótár-fürt egyetlen csomóponton belül találhatók. |az Azure Cache redis áll egyetlen csomópont (kivéve, ha engedélyezve van a Redis-fürttel), a Managed Cache Service régiók fogalma nem vonatkozik. Kulcsok beolvasása közben, így leíró címkéket a kulcsnevek beágyazva, és később lekérheti az elemeket a redis támogatja a keresést és helyettesítő karakterekre vonatkozó műveletek. Egy példa egy címkézési megoldást a Redis használatával végrehajtási: [címkézés a Redis gyorsítótár megvalósítása](https://stackify.com/implementing-cache-tagging-redis/). |
| Szerializálási |Managed Cache NetDataContractSerializer BinaryFormatter és egyéni objektumszerializáló használatát támogatja. Az alapértelmezett érték NetDataContractSerializer. |A feladata az ügyfélalkalmazás, mielőtt azokat a gyorsítótárba, akár az ügyféloldali alkalmazás fejlesztője a szerializáló kiválasztása a .NET-objektumokká szerializálásához. További információk és mintakód: [.NET-objektumok használata a gyorsítótárban](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Gyorsítótár-emulátor |Managed Cache biztosít a helyi gyorsítótár-emulátoron. |Az Azure Redis gyorsítótár nem rendelkezik az emulátor, de Ön is [helyileg történő futtatása a redis-server.exe MSOpenTech build](cache-faq.md#cache-emulator) emulátor biztosít. |

## <a name="choose-a-cache-offering"></a>Válasszon egy Cache-ajánlatot
A Microsoft Azure redis Cache az alábbi szinteken érhető el:

* **Alapszintű** – Egyetlen csomópont. Többféle méret legfeljebb 53 GB-ig.
* **Standard** – Két csomópontból álló elsődleges/replika. Többféle méret legfeljebb 53 GB-ig. 99,9%-os SLA.
* **Prémium** – Két csomópontból álló elsődleges/replika, legfeljebb 10 szegmenssel. Többféle méret 6 GB és 530 GB között. Tartalmazza a Standard szint összes szolgáltatását és továbbiakat, beleértve a [Redis-fürtök](cache-how-to-premium-clustering.md), a [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) és az [Azure Virtual Network](cache-how-to-premium-vnet.md) támogatását. 99,9%-os SLA.

Az egyes szintek szolgáltatási feltételei és díjszabása eltérő. A funkciókra terjed ki, és a díjszabásról további információt az útmutató későbbi részében, lásd: [díjszabás – redis Cache](https://azure.microsoft.com/pricing/details/cache/).

Áttelepítés a kiindulási pont, hogy válassza ki a méretet, amely megegyezik az előző Managed Cache Service gyorsítótár méretét, és majd méretezzen az alkalmazás követelményeitől függően. A Redis-ajánlatra a megfelelő Azure Cache kiválasztásáról további információkért lásd: [milyen az Azure Cache a Redis-ajánlatot és -méretet használjam](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>A Gyorsítótárügyfelek konfigurálása
A gyorsítótár létrehozása és konfigurálása után a következő lépés, hogy távolítsa el a Managed Cache Service-konfigurációt, és adja hozzá az Azure Cache Redis-konfiguráció, és úgy, hogy a gyorsítótár-ügyfelek hozzáférhetnek a gyorsítótárban hivatkozik.

* A Managed Cache szolgáltatás konfigurációjának eltávolítása
* A StackExchange.Redis NuGet-csomag használatával egy gyorsítótár-ügyfél konfigurálása

### <a name="remove-the-managed-cache-service-configuration"></a>A Managed Cache szolgáltatás konfigurációjának eltávolítása
Mielőtt Azure Cache for az ügyfélalkalmazások konfigurálhatók a Redis-, a meglévő Managed Cache Service konfigurációs és szerelvényre hivatkozik el kell távolítani a Managed Cache Service NuGet-csomag eltávolításával.

A Managed Cache Service NuGet-csomag eltávolításához kattintson a jobb gombbal a projektre a **Megoldáskezelőben** válassza **NuGet-csomagok kezelése**. Válassza ki a **telepített csomagokat** csomópontot, és írja be a W**indowsAzure.Caching** szöveget a keresőmezőbe telepített csomagokat. Válassza ki **Windows** **Azure Cache** (vagy **Windows** **Azure gyorsítótárazás** a NuGet-csomag verziójától függően), kattintson **Eltávolítás**, és kattintson a **Bezárás**.

![Távolítsa el az Azure Managed Cache Service NuGet-csomag](./media/cache-migrate-to-redis/IC757666.jpg)

A Managed Cache Service NuGet-csomag eltávolítása a Managed Cache Service szerelvényeket és a Managed Cache Service bejegyzéseket az app.config vagy az ügyfélalkalmazás web.config. Egyes testre szabott beállítások nem távolítható el, ha a NuGet-csomag eltávolítása, mert nyissa meg a web.config vagy az App.config fájlt, és győződjön meg arról, hogy a következő elemek törlődnek.

Ügyeljen arra, hogy a `dataCacheClients` bejegyzés törlődik a `configSections` elemet. Ne távolítsa el a teljes `configSections` elem; csak távolítsa el a `dataCacheClients` bejegyzést, ha létezik.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Ügyeljen arra, hogy a `dataCacheClients` szakaszban a rendszer eltávolítja. A `dataCacheClients` szakasz az alábbi példához hasonló lesz.

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

A Managed Cache Service konfigurációs van az Eltávolítás beállíthatja a gyorsítótár-ügyfél, a következő szakaszban leírtak szerint.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>A StackExchange.Redis NuGet-csomag használatával egy gyorsítótár-ügyfél konfigurálása
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Managed Cache Service kód áttelepítése
Az API a Redis-kliensével StackExchange.Azure Cache-hez hasonlít a Managed Cache Service szolgáltatáshoz. Ez a szakasz áttekintést a különbségeket.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Csatlakozás a gyorsítótárhoz, a ConnectionMultiplexer osztály használatával
A Managed Cache Service által kezelt kapcsolatok a gyorsítótárhoz az `DataCacheFactory` és `DataCache` osztályokat. Az Azure redis Cache által kezelt kapcsolatok az `ConnectionMultiplexer` osztály.

Adja hozzá a következő using utasítást a lista tetejére minden fájlhoz, amelyből el szeretne érni a gyorsítótárban.

```csharp
using StackExchange.Redis
```

Ha ez a névtér nem oldja meg, győződjön meg, hogy a StackExchange.Redis NuGet-csomagot hozzáadta, leírtak szerint [a rövid útmutató: Azure Cache a Redis használata .NET-alkalmazás](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Vegye figyelembe, hogy a StackExchange.Redis ügyfél igényel-e a .NET-keretrendszer 4 vagy újabb verzió.
> 
> 

Szeretne csatlakozni egy Azure Cache a Redis-példányt, hívja meg a statikus `ConnectionMultiplexer.Connect` metódust, és adja meg a végpont és a kulcsot. Az alkalmazásban egy `ConnectionMultiplexer` példány megosztására egy lehetséges módszer, ha létrehoz egy statikus tulajdonságot, amely egy csatlakoztatott példányt ad vissza, a következő példához hasonlóan. Ez a megközelítés egy szálbiztos módszert egyetlen csatlakoztatott inicializálása biztosít `ConnectionMultiplexer` példány. Ebben a példában `abortConnect` értéke false, ami azt jelenti, hogy a hívás sikeres lesz, még akkor is, ha a gyorsítótár kapcsolat nem jön létre. A `ConnectionMultiplexer` egyik fontos szolgáltatása, hogy automatikusan visszaállítja a kapcsolatot a gyorsítótárral, amint a hálózati problémák vagy egyéb hibák elhárulnak.

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

A gyorsítótár végpontját, a kulcsok és a portok szerezhető be a **Azure Cache redis** a gyorsítótárpéldány panelen. További információkért lásd: [Azure Cache Redis tulajdonságok](cache-configure.md#properties).

Ha a kapcsolat létrejött, küldhet vissza egy hivatkozást a Redis-adatbázishoz az Azure Cache meghívásával a `ConnectionMultiplexer.GetDatabase` metódust. A `GetDatabase` metódussal visszaadott objektum egy egyszerűsített továbbított objektum, amelyet nem kell tárolni.

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

A StackExchange.Redis ügyfél használja a `RedisKey` és `RedisValue` típusok elérésére és elemek tárolása a gyorsítótárban. Ezek a típusok gyakran nem használja közvetlenül, és a legtöbb nyelvi típusokban, beleértve a karakterlánc, az alakzatot térkép. Redis Cache karakterláncok a legalapvetőbb típusú Redis értéket, és az adatok, például szerializált bináris adatfolyamok, több adattípust is tartalmazhatnak, és a típus közvetlenül nem használhatók, amíg használandó tartalmazó metódusokat `String` a nevében. A legtöbb primitív adattípusokat, tárolja és a gyorsítótár az elemek lekéréséhez a `StringSet` és `StringGet` módszer, kivéve, ha a gyűjtemények vagy más Redis-adattípusok vannak tárolása a gyorsítótárban. 

`StringSet` és `StringGet` hasonlóak a Managed Cache Service `Put` és `Get` módszerekkel egyik fő különbség az, hogy beállítása és lekérése a .NET-objektumokat a gyorsítótárba előtt meg kell szerializálni, először. 

Hívásakor `StringGet`, ha az objektum létezik, akkor ad vissza, és nem jelenik meg, ha null adja vissza. Ebben az esetben az értéket lekérheti a kívánt adatforrásból, és a későbbi használatra a gyorsítótárban tárolhatja. Ez a minta az úgynevezett gyorsítótár-feltöltő.

Egy elem lejáratának megadásához a gyorsítótárban használja a `TimeSpan` `StringSet` paraméterét.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Az Azure Redis gyorsítótár .NET-objektumokat, valamint a primitív adattípusokat is együttműködik, de a .NET-objektumokat a gyorsítótárazásuk előtt kell szerializálni. A szerializálás feladata, az alkalmazás fejlesztője, és a fejlesztői rugalmasságot biztosít, hogy a szerializáló. További információk és mintakód: [.NET-objektumok használata a gyorsítótárban](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Telepítse át az ASP.NET munkamenet-állapot, és a kimeneti gyorsítótárazása az Azure Cache redis
Az Azure Cache redis tartozik az ASP.NET munkamenet-állapot és a lap kimeneti gyorsítótár-szolgáltatók. Ezek a szolgáltatók a Managed Cache Service verziókat használó alkalmazását át, először távolítsa el a meglévő szakaszokat a web.config, és konfigurálja az Azure Cache Redis verziója, a szolgáltatók. A redis Cache ASP.NET-szolgáltatók az Azure Cache használatával kapcsolatos utasításokért lásd: [ASP.NET munkamenetállapot-szolgáltatóját Azure Cache redis](cache-aspnet-session-state-provider.md) és [ASP.NET kimenetigyorsítótár-szolgáltatója az Azure Cache redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>További lépések
Fedezze fel a [Azure Cache Redis dokumentációt](https://azure.microsoft.com/documentation/services/cache/) oktatóanyagok, minták, videók és más rendszerekhez.


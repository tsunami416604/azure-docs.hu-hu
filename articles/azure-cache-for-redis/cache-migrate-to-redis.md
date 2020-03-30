---
title: Felügyelt gyorsítótár-szolgáltatás alkalmazások áttelepítése a Redis-be – Azure
description: A felügyelt gyorsítótár-szolgáltatás és a szerepköron ként i gyorsítótárba telepített alkalmazások áttelepítése a Redis Azure-gyorsítótárába
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 9596b8cb771f114cb09c5d6c6ae33b4fc4a8cada
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74122687"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Migrálás a Managed Cache Service szolgáltatásból az Azure Cache for Redisbe
Az Azure felügyelt gyorsítótárszolgáltatást használó alkalmazások áttelepítése az Azure Cache for Redis-be az alkalmazás minimális módosításával valósítható meg, a gyorsítótárazási alkalmazás által használt felügyelt gyorsítótárszolgáltatás-funkcióktól függően. Bár az API-k nem pontosan azonosak, hasonlóak, és a meglévő kód nagy része, amely felügyelt gyorsítótár-szolgáltatást használ a gyorsítótár eléréséhez, minimális módosításokkal újra felhasználható. Ez a cikk bemutatja, hogyan hajthatja végre a szükséges konfigurációs és alkalmazásmódosításokat a felügyelt gyorsítótár-szolgáltatás alkalmazásainak az Azure Cache for Redis használatához történő áttelepítéséhez, és bemutatja, hogy az Azure Cache for Redis egyes funkciói hogyan használhatók a a felügyelt gyorsítótár-szolgáltatás gyorsítótára.

>[!NOTE]
>2016. november 30-án a felügyelt gyorsítótár-szolgáltatás és a szerepkörön ként betöltött gyorsítótár [kilett vonva.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) Ha rendelkezik olyan szerepkörön kívül lévő gyorsítótár-telepítésekkel, amelyeket át szeretne telepíteni az Azure Cache for Redis-hez, kövesse a cikkben leírt lépéseket.

## <a name="migration-steps"></a>Áttelepítési lépések
A következő lépések szükségesek a felügyelt gyorsítótár-szolgáltatás alkalmazás áttelepítéséhez az Azure Cache for Redis használatához.

* Felügyelt gyorsítótár-szolgáltatás szolgáltatásainak hozzárendelése a Redis Azure-gyorsítótárába
* Gyorsítótár-ajánlat kiválasztása
* Gyorsítótár létrehozása
* A gyorsítótár-ügyfelek konfigurálása
  * A felügyelt gyorsítótárszolgáltatás konfigurációjának eltávolítása
  * Gyorsítótár-ügyfél konfigurálása a StackExchange.Redis NuGet csomag használatával
* Felügyelt gyorsítótár-szolgáltatás kódjának áttelepítése
  * Csatlakozás a gyorsítótárhoz a ConnectionMultiplexer osztály használatával
  * Primitív adattípusok elérése a gyorsítótárban
  * .NET-objektumok használata a gyorsítótárban
* ASP.NET munkamenet-állapot és a kimeneti gyorsítótárazás áttelepítése a Redis-hez tartozó Azure-gyorsítótárba 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Felügyelt gyorsítótár-szolgáltatás szolgáltatásainak hozzárendelése a Redis Azure-gyorsítótárába
Az Azure managed cache service és az Azure Cache for Redis hasonlóak, de megvalósítása egyes funkciók különböző módon. Ez a szakasz ismerteti a különbségeket, és útmutatást nyújt a felügyelt gyorsítótár-szolgáltatás szolgáltatásainak megvalósításához az Azure Cache for Redis.

| Felügyelt gyorsítótárszolgáltatás szolgáltatása | Felügyelt gyorsítótár-szolgáltatás támogatása | Azure Cache for Redis támogatás |
| --- | --- | --- |
| Elnevezett gyorsítótárak |Az alapértelmezett gyorsítótár konfigurálva van, és a standard és prémium szintű gyorsítótár-ajánlatokban legfeljebb kilenc további elnevezett gyorsítótár konfigurálható, ha szükséges. |A Redis Azure Cache for Redis konfigurálható számú adatbázissal rendelkezik (alapértelmezett 16), amelyek az elnevezett gyorsítótárakhoz hasonló funkciók megvalósításához használhatók. További információ: [Mik azok a Redis-adatbázisok?](cache-faq.md#what-are-redis-databases) és [A Redis-kiszolgáló alapértelmezett konfigurációja](cache-configure.md#default-redis-server-configuration). |
| Magas rendelkezésre állás |Magas rendelkezésre állást biztosít a standard és prémium szintű gyorsítótár-ajánlatok gyorsítótárában lévő elemek számára. Ha az elemek hiba miatt elvesznek, a gyorsítótárban lévő elemek biztonsági másolatai továbbra is elérhetők. A másodlagos gyorsítótárba irányuló írások szinkron módon készülnek. |Magas rendelkezésre állás érhető el a standard és prémium szintű gyorsítótár-ajánlatok, amelyek két csomópont elsődleges/replika konfiguráció (minden szegmens egy prémium szintű gyorsítótár rendelkezik egy elsődleges/replika pár). A kópiának írt adatok aszinkron módon készülnek. További információ: [Azure Cache for Redis pricing](https://azure.microsoft.com/pricing/details/cache/). |
| Értesítések |Lehetővé teszi az ügyfelek számára, hogy aszinkron értesítéseket kapjanak, ha különböző gyorsítótár-műveletek történnek egy elnevezett gyorsítótárban. |Az ügyfélalkalmazások a Redis pub/sub vagy [Keyspace értesítésekkel](cache-configure.md#keyspace-notifications-advanced-settings) az értesítésekhez hasonló funkciókat érhetnek el. |
| Helyi gyorsítótár |A gyorsítótárazott objektumok egy példányát helyileg tárolja az ügyfélen az extra gyors hozzáférés érdekében. |Az ügyfélalkalmazásoknak ezt a funkciót szótár vagy hasonló adatstruktúra használatával kell megvalósítaniuk. |
| Kilakoltatási szabályzat |Nincs vagy LRU. Az alapértelmezett házirend az FVT. |Az Azure Cache for Redis támogatja a következő kilakoltatási szabályzatok: illékony-lru, allkeys-lru, illékony-véletlen, allkeys-random, illékony-ttl, nokilakoltatás. Az alapértelmezett házirend az illékony-lru. További információt az [Alapértelmezett Redis-kiszolgáló konfigurációja](cache-configure.md#default-redis-server-configuration)című témakörben talál. |
| Elévülési szabályzat |Az alapértelmezett lejárati szabályzat abszolút, az alapértelmezett lejárati időköz pedig 10 perc. Csúszó és soha házirendek is rendelkezésre állnak. |Alapértelmezés szerint a gyorsítótárban lévő elemek nem járnak le, de a lejárat írási alapon konfigurálható a gyorsítótár készlettúlcsordulásának használatával. |
| Régiók és címkézés |A régiók a gyorsítótárazott elemek alcsoportjai. A régiók támogatják a gyorsítótárazott elemek további leíró karakterláncokkal, a címkékkel való jegyzetelését is. A régiók támogatják a keresési műveletek elvégzését az adott régióban lévő címkézett elemeken. A régión belüli összes elem a gyorsítótár-fürt egyetlen csomópontján belül található. |a Redis Azure Cache egy csomópontból áll (kivéve, ha a Redis-fürt engedélyezve van), így a felügyelt gyorsítótár-szolgáltatás régióinak fogalma nem alkalmazható. A Redis támogatja a keresést és a helyettesítő karakteres műveleteket a kulcsok beolvasásakor, így a leíró címkék beágyazhatók a kulcsnevekbe, és később az elemek lekéréséhez használhatók. A Redis használatával a címkézési megoldás megvalósításáról a [Gyorsítótár-címkézés megvalósítása a Redis használatával](https://stackify.com/implementing-cache-tagging-redis/)című témakörben található. |
| Szerializálás |A felügyelt gyorsítótár támogatja a NetDataContractSerializer, BinaryFormatter és az egyéni szerializálók használatát. Az alapértelmezett érték a NetDataContractSerializer. |Az ügyfélalkalmazás felelőssége, hogy szerializálja a .NET objektumokat, mielőtt a gyorsítótárba helyezné őket, a szerializáló kiválasztásával az ügyfélalkalmazás fejlesztőjéhez. További információ és mintakód a [Gyorsítótárban lévő .NET-objektumok használata](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)című témakörben található. |
| Gyorsítótár-emulátor |A felügyelt gyorsítótár helyi gyorsítótár-emulátort biztosít. |A Redis Azure Cache nem rendelkezik emulátorral, de [a redis-server.exe MSOpenTech buildje helyileg futtatható](cache-faq.md#cache-emulator) az emulátor élményének biztosításához. |

## <a name="choose-a-cache-offering"></a>Gyorsítótár-ajánlat kiválasztása
A Microsoft Azure Cache for Redis a következő szinteken érhető el:

* **Alapszintű** – Egyetlen csomópont. Többféle méret legfeljebb 53 GB-ig.
* **Standard** – Két csomópontból álló elsődleges/replika. Többféle méret legfeljebb 53 GB-ig. 99,9%-os SLA.
* **Prémium** – Két csomópontból álló elsődleges/replika, legfeljebb 10 szegmenssel. Több méret 6 GB-tól 1,2 TB-ig. Tartalmazza a Standard szint összes szolgáltatását és továbbiakat, beleértve a [Redis-fürtök](cache-how-to-premium-clustering.md), a [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) és az [Azure Virtual Network](cache-how-to-premium-vnet.md) támogatását. 99,9%-os SLA.

Az egyes szintek szolgáltatási feltételei és díjszabása eltérő. A funkciókat az útmutató későbbi részében ismerteti, és az árképzésről további információt a [Gyorsítótár árképzési részletei című témakörben talál.](https://azure.microsoft.com/pricing/details/cache/)

Az áttelepítés kiindulópontja a korábbi felügyelt gyorsítótár-szolgáltatás gyorsítótárának méretének megfelelő méret kiválasztásához, majd az alkalmazás követelményeitől függően fel- vagy leskálázás. A Megfelelő Azure-gyorsítótár a Redis-ajánlat kiválasztásáról a [Mi az Azure-gyorsítótár a Redis-ajánlatokhoz és a mérethez.](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>A gyorsítótár-ügyfelek konfigurálása
A gyorsítótár létrehozása és konfigurálása után a következő lépés a felügyelt gyorsítótár-szolgáltatás konfigurációjának eltávolítása, és adja hozzá az Azure Cache for Redis konfigurációját és hivatkozásait, hogy a gyorsítótár-ügyfelek hozzáférhessenek a gyorsítótárhoz.

* A felügyelt gyorsítótárszolgáltatás konfigurációjának eltávolítása
* Gyorsítótár-ügyfél konfigurálása a StackExchange.Redis NuGet csomag használatával

### <a name="remove-the-managed-cache-service-configuration"></a>A felügyelt gyorsítótárszolgáltatás konfigurációjának eltávolítása
Mielőtt az ügyfélalkalmazások konfigurálhatók lennének az Azure Cache for Redis-hez, a meglévő felügyelt gyorsítótár-szolgáltatás konfigurációját és összeállítási hivatkozásait el kell távolítani a Felügyelt gyorsítótárszolgáltatás NuGet csomagjának eltávolításával.

A Menedzselt gyorsítótárszolgáltatás NuGet csomagjának eltávolításához kattintson a jobb gombbal az ügyfélprojektre a **Megoldáskezelőben,** és válassza **a NuGet-csomagok kezelése parancsot.** Jelölje ki a **Telepített csomagok csomópontot,** és írja be a W**indowsAzure.Caching** parancsot a Telepített csomagok keresése mezőbe. Válassza a **Windows** **Azure-gyorsítótárat** **(vagy** a Windows **Azure gyorsítótárazást** a NuGet csomag verziójától függően), kattintson az **Eltávolítás gombra,** majd a **Bezárás**gombra.

![Az Azure felügyelt gyorsítótárszolgáltatás nuget csomagjának eltávolítása](./media/cache-migrate-to-redis/IC757666.jpg)

A Menedzselt gyorsítótárszolgáltatás NuGet csomagjának eltávolítása eltávolítja a felügyelt gyorsítótárszolgáltatás-szerelvényeket és a felügyelt gyorsítótárszolgáltatás bejegyzéseit az ügyfélalkalmazás app.config vagy web.config fájljában. Mivel előfordulhat, hogy egyes testreszabott beállítások nem törlődnek a NuGet csomag eltávolításakor, nyissa meg a web.config vagy az app.config webhelyet, és győződjön meg arról, hogy a következő elemek et eltávolítja.

Győződjön `dataCacheClients` meg arról, `configSections` hogy a bejegyzés törlődik az elemből. Ne távolítsa el `configSections` a teljes elemet; csak távolítsa `dataCacheClients` el a bejegyzést, ha jelen van.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Győződjön `dataCacheClients` meg arról, hogy a szakasz el van távolítva. A `dataCacheClients` szakasz hasonló lesz a következő példához.

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

A felügyelt gyorsítótár-szolgáltatás konfigurációjának eltávolítása után konfigurálhatja a gyorsítótár-ügyfelet a következő szakaszban leírtak szerint.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Gyorsítótár-ügyfél konfigurálása a StackExchange.Redis NuGet csomag használatával
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Felügyelt gyorsítótár-szolgáltatás kódjának áttelepítése
A StackExchange.Azure cache redis-ügyfél api-ja hasonló a felügyelt gyorsítótár-szolgáltatáshoz. Ez a rész áttekintést nyújt a különbségekről.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Csatlakozás a gyorsítótárhoz a ConnectionMultiplexer osztály használatával
A Felügyelt gyorsítótár-szolgáltatásban a gyorsítótárhoz `DataCacheFactory` való `DataCache` kapcsolatokat a és az osztályok kezelték. Az Azure Cache for Redis, ezeket a kapcsolatokat az `ConnectionMultiplexer` osztály kezeli.

Adja hozzá a következő utasítást minden olyan fájl tetejéhez, amelyből a gyorsítótárat el szeretné érni.

```csharp
using StackExchange.Redis
```

Ha ez a névtér nem oldható fel, győződjön meg arról, hogy hozzáadta a StackExchange.Redis NuGet csomagot a [rövid útmutatóban leírtak szerint: Az Azure Cache for Redis használata .NET alkalmazással.](cache-dotnet-how-to-use-azure-redis-cache.md)

> [!NOTE]
> Vegye figyelembe, hogy a StackExchange.Redis ügyfél .NET Framework 4 vagy újabb szükséges.
> 
> 

Ha csatlakozni szeretne egy Azure-gyorsítótár redis-példány, hívja meg a statikus `ConnectionMultiplexer.Connect` metódust, és adja át a végpontot és a kulcsot. Az alkalmazásban egy `ConnectionMultiplexer` példány megosztására egy lehetséges módszer, ha létrehoz egy statikus tulajdonságot, amely egy csatlakoztatott példányt ad vissza, a következő példához hasonlóan. Ez a megközelítés egy szál-biztonságos módja `ConnectionMultiplexer` annak, hogy inicializálni egy csatlakoztatott példány. Ebben a `abortConnect` példában hamis, ami azt jelenti, hogy a hívás akkor is sikeres lesz, ha nincs kapcsolat a gyorsítótárral. A `ConnectionMultiplexer` egyik fontos szolgáltatása, hogy automatikusan visszaállítja a kapcsolatot a gyorsítótárral, amint a hálózati problémák vagy egyéb hibák elhárulnak.

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

A gyorsítótár-végpont, kulcsok és portok az **Azure Cache for Redis** panel a gyorsítótár-példány beszerezhető. További információ: [Azure Cache for Redis properties](cache-configure.md#properties).

A kapcsolat létrejötte után adja vissza a módszert az Azure `ConnectionMultiplexer.GetDatabase` Cache for Redis adatbázisra mutató hivatkozást. A `GetDatabase` metódussal visszaadott objektum egy egyszerűsített továbbított objektum, amelyet nem kell tárolni.

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

A StackExchange.Redis ügyfél `RedisKey` `RedisValue` a és a típusok at használja a gyorsítótárban lévő elemek eléréséhez és tárolásához. Ezek a típusok a legtöbb primitív nyelvtípusra vannak leképezve, beleértve a karakterláncot is, és gyakran nem használatosak közvetlenül. A Redis karakterláncok a Redis-értékek legalapvetőbb típusai, és sokféle adatot tartalmazhatnak, beleértve a szerializált bináris adatfolyamokat `String` is, és bár nem használhatja közvetlenül a típust, a névben található módszereket fogja használni. A legtöbb primitív adattípus esetében az elemeket `StringSet` a `StringGet` gyorsítótárból a és a módszerek használatával tárolhatja és lekérheti, kivéve, ha gyűjteményeket vagy más Redis-adattípusokat tárol a gyorsítótárban. 

`StringSet`és `StringGet` hasonlóak a `Put` felügyelt `Get` gyorsítótár-szolgáltatáshoz és metódusokhoz, az egyik fő különbség az, hogy mielőtt beállítanál és bejuttatnál egy .NET objektumot a gyorsítótárba, először szerializálnunk kell. 

A `StringGet`híváskor , ha az objektum létezik, a rendszer visszaadja, és ha nem, akkor null értéket ad vissza. Ebben az esetben lekérheti az értéket a kívánt adatforrásból, és tárolhatja a gyorsítótárban későbbi használatra. Ezt a mintát gyorsítótár-félremintának nevezzük.

Egy elem lejáratának megadásához a gyorsítótárban használja a `TimeSpan``StringSet` paraméterét.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Az Azure Cache for Redis is használható .NET-objektumokkal és primitív adattípusokkal, de a .NET-objektumok gyorsítótárazása előtt szerializáltnak kell lennie. Ez a szerializálás az alkalmazás fejlesztőjének felelőssége, és rugalmasságot biztosít a fejlesztőnek a szerializáló kiválasztásában. További információ és mintakód a [Gyorsítótárban lévő .NET-objektumok használata](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)című témakörben található.

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>ASP.NET munkamenet-állapot és a kimeneti gyorsítótárazás áttelepítése a Redis-hez tartozó Azure-gyorsítótárba
A Redis Azure Cache szolgáltatói ASP.NET munkamenetállapot- és lapkimeneti gyorsítótárazáshoz is rendelkeznek. Az alkalmazás áttelepítéséhez, amely a felügyelt gyorsítótár-szolgáltatás verzióit használja ezeka szolgáltatók, először távolítsa el a meglévő szakaszokat a web.config, majd konfigurálja az Azure Cache a Redis verziói a szolgáltatók. A Redis ASP.NET-szolgáltatók Azure-gyorsítótárának használatával kapcsolatos tudnivalókért [olvassa el ASP.NET A Redis Azure-gyorsítótárhoz való munkamenetállapot-szolgáltatóés](cache-aspnet-session-state-provider.md) ASP.NET A [Redis Azure-gyorsítótárszolgáltatója](cache-aspnet-output-cache-provider.md)című témakört.

## <a name="next-steps"></a>További lépések
Ismerje meg az [Azure Cache for Redis dokumentációt](https://azure.microsoft.com/documentation/services/cache/) oktatóanyagok, minták, videók és egyebek.


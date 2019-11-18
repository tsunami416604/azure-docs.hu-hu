---
title: Managed Cache Service alkalmazások migrálása a Redis-re – Azure
description: Megtudhatja, hogyan telepíthet át Managed Cache Service és szerepköralapú gyorsítótár alkalmazásokat az Azure cache Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 9596b8cb771f114cb09c5d6c6ae33b4fc4a8cada
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122687"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Áttelepítés Managed Cache Serviceról az Azure cache-be a Redis-hez
Az Azure Managed Cache Servicet az Azure cache-t használó alkalmazások áttelepítése a Redis az alkalmazás minimális változásaival, a gyorsítótárazási alkalmazás által használt Managed Cache Service-funkcióktól függően valósítható meg. Habár az API-k nem pontosan ugyanazok, mint a hasonlók, és a meglévő kódok nagy része, amely a Managed Cache Servicet használja a gyorsítótár eléréséhez, a minimális módosításokkal újra felhasználhatók. Ez a cikk bemutatja, hogyan módosíthatja a szükséges konfigurációs és alkalmazás-módosításokat a Managed Cache Service-alkalmazások áttelepítéséhez az Azure cache Redis való használatához, és bemutatja, hogyan használhatók az Azure cache egyes funkciói a Redis funkcióinak megvalósításához egy Managed Cache Service gyorsítótár.

>[!NOTE]
>Managed Cache Service és szerepköralapú gyorsítótár 2016 [. november 30](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) -án megszűnt. Ha olyan szerepköralapú gyorsítótár üzemelő példányokkal rendelkezik, amelyeket át szeretne telepíteni az Azure cache-be a Redis-hez, kövesse a cikkben ismertetett lépéseket.

## <a name="migration-steps"></a>Áttelepítési lépések
A következő lépések szükségesek ahhoz, hogy áttelepítsen egy Managed Cache Service alkalmazást az Azure cache Redis való használatához.

* Az Azure cache Managed Cache Service funkcióinak leképezése a Redis
* Gyorsítótár-ajánlat kiválasztása
* Gyorsítótár létrehozása
* A gyorsítótár-ügyfelek konfigurálása
  * A Managed Cache Service konfigurációjának eltávolítása
  * Gyorsítótár-ügyfél konfigurálása a StackExchange. Redis NuGet-csomag használatával
* Managed Cache Service-kód migrálása
  * Kapcsolódás a gyorsítótárhoz a ConnectionMultiplexer osztály használatával
  * A primitív adattípusok elérése a gyorsítótárban
  * .NET-objektumok használata a gyorsítótárban
* A ASP.NET munkamenet-állapotának áttelepítése és a kimeneti gyorsítótárazás az Azure cache-be a Redis-ben 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Az Azure cache Managed Cache Service funkcióinak leképezése a Redis
Az Azure Managed Cache Service és az Azure cache for Redis hasonlóak, de különböző módokon implementálják a szolgáltatásaikat. Ez a szakasz néhány különbséget ismertet, és útmutatást nyújt a Managed Cache Service funkcióinak megvalósításához a Redis készült Azure cache-ben.

| Managed Cache Service funkció | Managed Cache Service támogatás | Azure cache Redis-támogatáshoz |
| --- | --- | --- |
| Nevesített gyorsítótárak |Az alapértelmezett gyorsítótár konfigurálva van, a standard és a prémium szintű gyorsítótár-ajánlatokban pedig akár kilenc további megnevezett gyorsítótár is konfigurálható, ha szükséges. |A Redis-hez készült Azure cache egy konfigurálható (alapértelmezett 16) adatbázist tartalmaz, amelyek segítségével hasonló funkciók hozhatók létre a nevesített gyorsítótárak számára. További információ: [Mik azok a Redis-adatbázisok?](cache-faq.md#what-are-redis-databases) és [A Redis-kiszolgáló alapértelmezett konfigurációja](cache-configure.md#default-redis-server-configuration). |
| Magas rendelkezésre állás |Magas rendelkezésre állást biztosít a gyorsítótárban lévő elemek számára a standard és a prémium szintű gyorsítótár-ajánlatokban. Ha egy hiba miatt elvesznek az elemek, a gyorsítótárban lévő elemek biztonsági másolatai továbbra is elérhetők. A másodlagos gyorsítótárba való írás szinkron módon történik. |A magas rendelkezésre állás a standard és a prémium szintű gyorsítótár-ajánlatokban érhető el, amelyek két csomópontos elsődleges/replika konfigurációval rendelkeznek (a prémium szintű gyorsítótárban található minden szegmens elsődleges/replika párral rendelkezik). A replikába való írás aszinkron módon történik. További információt az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/)ismertető témakörben talál. |
| Értesítések |Lehetővé teszi, hogy az ügyfelek aszinkron értesítéseket kapjanak, amikor elnevezett gyorsítótárban végeznek különféle gyorsítótárazási műveleteket. |Az ügyfélalkalmazások a Redis pub/sub vagy a [Space értesítéseket](cache-configure.md#keyspace-notifications-advanced-settings) is használhatják az értesítések hasonló funkcióinak eléréséhez. |
| Helyi gyorsítótár |A gyorsítótárazott objektumok egy példányát helyileg tárolja az ügyfélen a extra gyors hozzáféréshez. |Az ügyfélalkalmazások egy szótárral vagy hasonló adatstruktúrával kell implementálni ezt a funkciót. |
| Kizárási szabályzat |Nincs vagy LRU. Az alapértelmezett szabályzat a LRU. |A Redis készült Azure cache a következő kizárási házirendeket támogatja: felejtő-LRU, allkeys-LRU, illékony-random, allkeys-random, illékony-TTL, kizárás. Az alapértelmezett házirend a felejtő-LRU. További információ: az [alapértelmezett Redis-kiszolgáló konfigurációja](cache-configure.md#default-redis-server-configuration). |
| Elévülési szabályzat |Az alapértelmezett elévülési szabályzat abszolút, és az alapértelmezett lejárati idő 10 perc. A csúszó és soha nem házirendek is elérhetők. |A gyorsítótárban lévő alapértelmezett elemek nem járnak le, de a lejáratot írási alapon is konfigurálhatja a gyorsítótár-készlet túlterhelések használatával. |
| Régiók és címkézés |A régiók a gyorsítótárazott elemek alcsoportja. A régiók a gyorsítótárazott elemek megjegyzéseit is támogatják, a címkék nevű további leíró sztringekkel együtt. A régiók támogatják a keresési műveletek végrehajtását a régió bármely címkézett elemén. Egy adott régióban lévő összes elem a gyorsítótár-fürt egyetlen csomópontján belül található. |a Redis-hez készült Azure cache egyetlen csomópontból áll (kivéve, ha a Redis-fürt engedélyezve van), így a Managed Cache Service régiók fogalma nem érvényes. A Redis támogatja a kereséseket és a helyettesítő karaktereket a kulcsok beolvasása során, így a leíró címkék beágyazása a kulcsok neveibe, és a későbbiekben az elemek lekéréséhez használható. A címkézési megoldás Redis használatával történő megvalósítására példát a [gyorsítótár címkézésének implementálása a Redis](https://stackify.com/implementing-cache-tagging-redis/)segítségével című témakörben talál. |
| Szerializálási |A Managed cache támogatja a NetDataContractSerializer, a BinaryFormatter és az egyéni szerializálók használatát. Az alapértelmezett érték a NetDataContractSerializer. |Az ügyfélalkalmazás feladata, hogy szerializálja a .NET-objektumokat, mielőtt azokat a gyorsítótárba helyezi, és a szerializáló az ügyfélalkalmazás fejlesztői számára is választható. További információ és mintakód: [.net-objektumok használata a gyorsítótárban](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Gyorsítótár-emulátor |A Managed cache egy helyi gyorsítótár-emulátort biztosít. |Az Azure cache for Redis nem rendelkezik emulátorral, de [a Redis-Server. exe MSOpenTech-buildjét helyileg is futtathatja](cache-faq.md#cache-emulator) , hogy az emulátort biztosítson. |

## <a name="choose-a-cache-offering"></a>Gyorsítótár-ajánlat kiválasztása
A Redis Microsoft Azure gyorsítótára a következő szinteknél érhető el:

* **Alapszintű** – Egyetlen csomópont. Többféle méret legfeljebb 53 GB-ig.
* **Standard** – Két csomópontból álló elsődleges/replika. Többféle méret legfeljebb 53 GB-ig. 99,9%-os SLA.
* **Prémium** – Két csomópontból álló elsődleges/replika, legfeljebb 10 szegmenssel. Több méret 6 GB-ról 1,2 TB-ra. Tartalmazza a Standard szint összes szolgáltatását és továbbiakat, beleértve a [Redis-fürtök](cache-how-to-premium-clustering.md), a [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) és az [Azure Virtual Network](cache-how-to-premium-vnet.md) támogatását. 99,9%-os SLA.

Az egyes szintek szolgáltatási feltételei és díjszabása eltérő. A funkciók az útmutató későbbi részében jelennek meg, és a díjszabással kapcsolatos további információkért lásd a [gyorsítótár-díjszabás részleteit](https://azure.microsoft.com/pricing/details/cache/).

Az áttelepítés kiindulási pontja a korábbi Managed Cache Service gyorsítótár méretének megfelelő méret kiválasztása, majd az alkalmazás követelményeitől függően vertikális fel-vagy leskálázás. A Redis-ajánlathoz megfelelő Azure cache kiválasztásával kapcsolatos további információkért tekintse meg az [Azure cache Redis-ajánlat és-méret használatát](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)ismertető témakört.

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>A gyorsítótár-ügyfelek konfigurálása
A gyorsítótár létrehozása és konfigurálása után a következő lépés az Managed Cache Service konfigurációjának eltávolítása, és az Azure cache hozzáadása a Redis-konfigurációhoz és-hivatkozásokhoz, hogy a gyorsítótár-ügyfelek hozzáférhessenek a gyorsítótárhoz.

* A Managed Cache Service konfigurációjának eltávolítása
* Gyorsítótár-ügyfél konfigurálása a StackExchange. Redis NuGet-csomag használatával

### <a name="remove-the-managed-cache-service-configuration"></a>A Managed Cache Service konfigurációjának eltávolítása
Mielőtt az ügyfélalkalmazások konfigurálhatók az Azure cache-hez a Redis-hez, el kell távolítani a meglévő Managed Cache Service konfigurációt és szerelvény-hivatkozásokat a Managed Cache Service NuGet csomag eltávolításával.

A Managed Cache Service NuGet-csomag eltávolításához kattintson a jobb gombbal a **megoldáskezelő** , majd válassza a **NuGet-csomagok kezelése**lehetőséget. Válassza ki a **telepített csomagok** csomópontot, és írja be a W**IndowsAzure. caching** kifejezést a keresés telepített csomagokba mezőbe. Válassza a **Windows** **Azure cache** (vagy a **Windows** **Azure gyorsítótárazása** a NuGet-csomag verziójától függően) lehetőséget, és kattintson az **Eltávolítás**gombra, majd a **Bezárás**gombra.

![Az Azure Managed Cache Service NuGet csomag eltávolítása](./media/cache-migrate-to-redis/IC757666.jpg)

A Managed Cache Service NuGet-csomag eltávolítása eltávolítja a Managed Cache Service szerelvényeket és a Managed Cache Service bejegyzéseket az ügyfélalkalmazás app. config vagy web. config fájljában. Mivel előfordulhat, hogy egyes testreszabott beállítások nem távolíthatók el a NuGet-csomag eltávolításakor, nyissa meg a web. config vagy az app. config fájlt, és győződjön meg arról, hogy a következő elemek el lesznek távolítva.

Győződjön meg arról, hogy a `dataCacheClients` bejegyzés el lett távolítva a `configSections` elemből. Ne távolítsa el a teljes `configSections` elemet; Ha a jelen van, távolítsa el a `dataCacheClients` bejegyzést.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Győződjön meg arról, hogy a `dataCacheClients` szakasz el van távolítva. A `dataCacheClients` szakasz az alábbi példához hasonló lesz.

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

A Managed Cache Service konfigurációjának eltávolítása után a gyorsítótár-ügyfelet a következő szakaszban leírtak szerint állíthatja be.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Gyorsítótár-ügyfél konfigurálása a StackExchange. Redis NuGet-csomag használatával
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Managed Cache Service-kód migrálása
A StackExchange. Azure cache for Redis-ügyfél API-je hasonló a Managed Cache Servicehoz. Ez a szakasz áttekintést nyújt a különbségekről.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Kapcsolódás a gyorsítótárhoz a ConnectionMultiplexer osztály használatával
Managed Cache Service a gyorsítótárral létesített kapcsolatokat a `DataCacheFactory` és a `DataCache` osztályok kezelik. A Redis-hez készült Azure cache-ben ezeket a kapcsolatokat a `ConnectionMultiplexer` osztály kezeli.

Adja hozzá a következő using utasítást bármely olyan fájl elejéhez, amelyről el szeretné érni a gyorsítótárat.

```csharp
using StackExchange.Redis
```

Ha ez a névtér nem oldható fel, győződjön meg róla, hogy hozzáadta a StackExchange. Redis NuGet-csomagot a gyors útmutató [: az Azure cache használata a Redis .net-alkalmazással](cache-dotnet-how-to-use-azure-redis-cache.md)című cikkben leírtak szerint.

> [!NOTE]
> Vegye figyelembe, hogy a StackExchange. Redis ügyfélhez a .NET-keretrendszer 4-es vagy újabb verziójára van szükség.
> 
> 

Ha csatlakozni szeretne egy Azure-gyorsítótárhoz a Redis-példányhoz, hívja meg a statikus `ConnectionMultiplexer.Connect` metódust, és adja meg a végpontot és a kulcsot. Az alkalmazásban egy `ConnectionMultiplexer` példány megosztására egy lehetséges módszer, ha létrehoz egy statikus tulajdonságot, amely egy csatlakoztatott példányt ad vissza, a következő példához hasonlóan. Ez a módszer egy adott csatlakoztatott `ConnectionMultiplexer`-példány inicializálását teszi lehetővé. Ebben a példában a `abortConnect` hamis értékre van állítva, ami azt jelenti, hogy a hívás akkor is sikeres lesz, ha nem jön létre a gyorsítótárhoz való kapcsolódás. A `ConnectionMultiplexer` egyik fontos szolgáltatása, hogy automatikusan visszaállítja a kapcsolatot a gyorsítótárral, amint a hálózati problémák vagy egyéb hibák elhárulnak.

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

A gyorsítótár-végpont, a kulcsok és a portok a gyorsítótár-példány Redis paneljének **Azure cache** -ről szerezhetők be. További információkért lásd: [Az Azure cache a Redis tulajdonságaihoz](cache-configure.md#properties).

A kapcsolódás után a `ConnectionMultiplexer.GetDatabase` metódus meghívásával visszaküldi a Redis-adatbázishoz tartozó Azure cache-re mutató hivatkozást. A `GetDatabase` metódussal visszaadott objektum egy egyszerűsített továbbított objektum, amelyet nem kell tárolni.

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

A StackExchange. Redis ügyfél a `RedisKey` és `RedisValue` típusokat használja a gyorsítótárban lévő elemek eléréséhez és tárolásához. Ezek a típusok a legkezdetlegesebb nyelvi típusokra mutatnak, beleértve a sztringet, és gyakran nem közvetlenül használják. A Redis karakterláncok a legalapvetőbb Redis-értékek, és számos adattípust tartalmazhatnak, beleértve a szerializált bináris adatfolyamokat, és nem közvetlenül a típust használja, hanem a névben lévő `String`t tartalmazó metódusokat is használhat. A legkezdetlegesebb adattípusok esetében a `StringSet` és az `StringGet` metódussal tárolhatja és beolvashatja a gyorsítótárban lévő elemeket, kivéve, ha gyűjtemények vagy más Redis-adattípusok tárolását tárolja a gyorsítótárban. 

`StringSet` és `StringGet` hasonlóak az Managed Cache Service `Put` és `Get` metódusokhoz, és az egyik legnagyobb különbség az, hogy a .NET-objektumok a gyorsítótárba való beszerzése előtt először szerializálni kell azt. 

Ha a `StringGet`meghívásakor az objektum létezik, a rendszer visszaadja, és ha nem, akkor null értéket ad vissza. Ebben az esetben az értéket lekérheti a kívánt adatforrásból, és a gyorsítótárban tárolhatja későbbi használatra. Ezt a mintát a gyorsítótár-feltöltési mintának nevezzük.

Egy elem lejáratának megadásához a gyorsítótárban használja a `TimeSpan` `StringSet` paraméterét.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

A Redis készült Azure cache .NET-objektumokat és primitív adattípusokat is képes működni, de a .NET-objektumok gyorsítótárazása előtt szerializálni kell. Ez a szerializálás az alkalmazás-fejlesztő feladata, és a fejlesztői rugalmasságot biztosít a szerializáló választásában. További információ és mintakód: [.net-objektumok használata a gyorsítótárban](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>A ASP.NET munkamenet-állapotának áttelepítése és a kimeneti gyorsítótárazás az Azure cache-be a Redis-ben
A Redis-hez készült Azure cache a ASP.NET munkamenet-állapotához és az oldal kimeneti gyorsítótárazásához is rendelkezik szolgáltatókkal. Ha a szolgáltatók Managed Cache Service verzióját használó alkalmazást szeretné áttelepíteni, először távolítsa el a meglévő szakaszt a web. config fájlból, majd konfigurálja az Azure cache-t a szolgáltatók Redis verzióihoz. Az Azure cache Redis ASP.NET-szolgáltatókhoz való használatáról további útmutatásért lásd: [ASP.NET munkamenet-szolgáltató az Azure cache-hez a Redis](cache-aspnet-session-state-provider.md) és a [ASP.NET kimeneti gyorsítótár-szolgáltatója az Azure cache for Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>További lépések
Ismerje meg az Azure cache-t az oktatóanyagok, minták, videók és egyéb [Redis dokumentációjában](https://azure.microsoft.com/documentation/services/cache/) .


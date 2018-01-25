---
title: "Az Azure Redis Cache használata | Microsoft Docs"
description: "Információk arról, hogy miként javítható az Azure-alkalmazásai teljesítménye az Azure Redis Cache segítségével"
services: redis-cache,app-service
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/27/2017
ms.author: wesmc
ms.openlocfilehash: a9276eaa4c7d8b11891d7dfade475316ffac5f71
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache"></a>Az Azure Redis Cache használata
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Ez az útmutató segítséget nyújt az első lépések megtételéhez az **Azure Redis Cache** használatakor. A Microsoft Azure Redis Cache a nyílt forráskódú és népszerű Redis Cache-re épül. Hozzáférést biztosít egy biztonságos, dedikált Redis Cache gyorsítótárhoz, amelyet a Microsoft felügyel. Az Azure Redis Cache használatával létrehozott gyorsítótárak a Microsoft Azure összes alkalmazásából elérhetőek.

A Microsoft Azure Redis Cache a következő szinteken érhető el:

* **Alapszintű** – Egyetlen csomópont. Többféle méret legfeljebb 53 GB-ig.
* **Standard** – Két csomópontból álló elsődleges/replika. Többféle méret legfeljebb 53 GB-ig. 99,9%-os SLA.
* **Prémium** – Két csomópontból álló elsődleges/replika, legfeljebb 10 szegmenssel. Többféle méret 6 GB és 530 GB között. Tartalmazza a Standard szint összes szolgáltatását és továbbiakat, beleértve a [Redis-fürtök](cache-how-to-premium-clustering.md), a [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) és az [Azure Virtual Network](cache-how-to-premium-vnet.md) támogatását. 99,9%-os SLA.

Az egyes szintek szolgáltatási feltételei és díjszabása eltérő. További díjszabási információk: [Díjszabás – Redis Cache][Cache Pricing Details].

Ez az útmutató ismerteti, hogyan használhatja a [StackExchange.Redis][StackExchange.Redis] ügyfelet C\# kóddal. Az ismertetett forgatókönyvek között megtalálható **gyorsítótár létrehozása és konfigurálása**, **gyorsítótárügyfelek konfigurálása** és **objektumok hozzáadása és eltávolítása a gyorsítótárból**. Az Azure Redis Cache használatával kapcsolatos további információkat a [Következő lépések][Next Steps] című szakaszban talál. Részletes útmutató ASP.NET MVC webalkalmazás felépítéséhez Redis Cache használatával: [How to create a Web App with Redis Cache](cache-web-app-howto.md) (Webalkalmazás létrehozása Redis Cache használatával).

<a name="getting-started-cache-service"></a>

## <a name="get-started-with-azure-redis-cache"></a>Bevezetés az Azure Redis Cache használatába
Az első lépések az Azure Redis Cache-ben egyszerűek. Először hozzon létre és konfiguráljon egy gyorsítótárat. Ezután konfigurálja a gyorsítótárügyfeleket, hogy hozzáféréssel rendelkezzenek a gyorsítótárhoz. A gyorsítótárügyfelek konfigurálása után megkezdheti a használatukat.

* [A gyorsítótár létrehozása][Create the cache]
* [A gyorsítótárügyfelek konfigurálása][Configure the cache clients]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>A gyorsítótár elérése a létrehozás után
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

További információk a gyorsítótár konfigurálásáról: [How to configure Azure Redis Cache](cache-configure.md) (Az Azure Redis Cache konfigurálása).

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>A gyorsítótárügyfelek konfigurálása
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Miután az ügyfélprojektet konfigurálta gyorsítótárazásra, a következő szakaszokban ismertetett módszereket is alkalmazhatja a gyorsítótár használatakor.

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>A gyorsítótárak használata
Az ebben a szakaszban szereplő lépések a gyorsítótárral végzett gyakori feladatok végrehajtását mutatják be.

* [Csatlakozás a gyorsítótárhoz][Connect to the cache]
* [Objektumok hozzáadása és lekérése a gyorsítótárból][Add and retrieve objects from the cache]
* [.NET-objektumok használata a gyorsítótárban](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>

## <a name="connect-to-the-cache"></a>Csatlakozás a gyorsítótárhoz
Annak érdekében, hogy programozott módon használja a gyorsítótárat, szüksége van egy, a gyorsítótárra mutató hivatkozásra. Adja a következőt bármely olyan fájl tetejéhez, amelyből a StackExchange.Redis ügyféllel szeretne hozzáférni egy Azure Redis Cache-hez.

    using StackExchange.Redis;

> [!NOTE]
> A StackExchange.Redis ügyfél használatához a .NET-keretrendszer 4-es vagy újabb verziója szükséges.
> 
> 

Az Azure Redis Cache-sel való kapcsolatot a `ConnectionMultiplexer` osztály kezeli. Ennek az osztálynak megoszthatónak és ismét felhasználhatónak kell lennie az ügyfélalkalmazásban, hogy ne kelljen műveletenként létrehozni. 

Az Azure Redis Cache-hez való csatlakozáshoz, valamint hogy a rendszer egy csatlakoztatott `ConnectionMultiplexer`-példányt adjon vissza, hívja meg a statikus `Connect` metódust, és adja meg a gyorsítótár végpontját és a kulcsot. Használja az Azure Portalon létrehozott kulcsot password paraméterként.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

> [!IMPORTANT]
> Figyelmeztetés: Soha ne tároljon hitelesítő adatokat a forráskódban. Annak érdekében, hogy a minta egyszerű maradjon, a forráskódban mutatom meg őket. A hitelesítő adatok tárolásával kapcsolatos információkért tekintse meg [az alkalmazás-karakterláncok és a kapcsolati karakterláncok működését][How Application Strings and Connection Strings Work] ismertető cikket.
> 
> 

Ha nem szeretne SSL-t használni, állítsa be az `ssl=false` értéket, vagy hagyja ki az `ssl` paramétert.

> [!NOTE]
> A nem SSL port az új gyorsítótárakhoz alapértelmezés szerint le van tiltva. Útmutató a nem SSL port engedélyezéséhez: [Portok elérése](cache-configure.md#access-ports).
> 
> 

Az alkalmazásban egy `ConnectionMultiplexer` példány megosztására egy lehetséges módszer, ha létrehoz egy statikus tulajdonságot, amely egy csatlakoztatott példányt ad vissza, a következő példához hasonlóan. Ez a megközelítés egy szálbiztos módszert biztosít egyetlen csatlakoztatott `ConnectionMultiplexer`-példány inicializálásához. Ezekben a példákban az `abortConnect` értéke false, ami azt jelenti, hogy a hívás akkor is sikeres lesz, ha nincs kapcsolat az Azure Redis Cache-sel. A `ConnectionMultiplexer` egyik fontos szolgáltatása, hogy automatikusan visszaállítja a kapcsolatot a gyorsítótárral, amint a hálózati problémák vagy egyéb hibák elhárulnak.

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

További információt a speciális kapcsolatkonfigurációs lehetőségekről a [StackExchange.Redis konfigurációs modellt][StackExchange.Redis configuration model] ismertető cikkben talál.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

A kapcsolat létrejötte után küldjön vissza egy hivatkozást a redis cache-adatbázisnak a `ConnectionMultiplexer.GetDatabase` metódus meghívásával. A `GetDatabase` metódussal visszaadott objektum egy egyszerűsített továbbított objektum, amelyet nem kell tárolni.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Az Azure Redis Cache-ek adatbázisainak száma konfigurálható (alapértelmezés szerint 16). Ezek az adatbázisok a Redis Cache-ben található adatok logikai szétválasztására használhatóak. További információ: [Mik azok a Redis-adatbázisok?](cache-faq.md#what-are-redis-databases) és [A Redis-kiszolgáló alapértelmezett konfigurációja](cache-configure.md#default-redis-server-configuration).

Most, hogy megismerte, hogyan csatlakozhat egy Azure Redis Cache-példányhoz és küldhet vissza egy hivatkozást a gyorsítótár-adatbázisnak, ismerkedjen meg közelebbről a gyorsítótár használatával.

<a name="add-object"></a>

## <a name="add-and-retrieve-objects-from-the-cache"></a>Objektumok hozzáadása és lekérése a gyorsítótárból
Elemek a `StringSet` és a `StringGet` metódussal tárolhatók a gyorsítótárban, illetve kérhetők le onnan.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

A Redis a legtöbb adatot Redis-karakterláncokként tárolja, azonban ezek a karakterláncok több adattípust is tartalmazhatnak, például szerializált bináris adatokat, amelyek akkor használhatók, ha .NET-objektumokat tárol a gyorsítótárban.

A `StringGet` hívásakor ha az objektum létezik, a rendszer visszaadja, ha pedig nem létezik, akkor a `null` értéket adja vissza. Ha `null` értéket ad vissza, az értéket lekérheti a kívánt adatforrásból, és a gyorsítótárban tárolhatja későbbi használatra. Ez a használati minta az úgynevezett gyorsítótár-feltöltő mód.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

A `RedisValue` is használható az alábbi példában látható módon. A `RedisValue` implicit operátorokkal rendelkezik az egész szám típusú adatokkal történő munkavégzéshez, ami akkor lehet hasznos, ha a `null` egy gyorsítótárazott elem várt értéke.


    RedisValue value = cache.StringGet("key1");
    if (!value.HasValue)
    {
        value = GetValueFromDataSource();
        cache.StringSet("key1", value);
    }


Egy elem lejáratának megadásához a gyorsítótárban használja a `TimeSpan` `StringSet` paraméterét.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>.NET-objektumok használata a gyorsítótárban
Az Azure Redis Cache .NET-objektumokat és primitív adattípusokat is képes gyorsítótárazni, de a .NET-objektumokat a gyorsítótárazásuk előtt szerializálni kell. Ez a .NET-objektumszerializálás az alkalmazásfejlesztők feladata, akik így rugalmasan kiválaszthatják a szerializálót.

Az objektumok szerializálásának egy egyszerű módja, ha a [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) témakörben található `JsonConvert` szerializálási módszereket használja és JSON formátumból JSON formátumba szerializál. Az alábbi példában egy `Employee`-objektumpéldány használatával végrehajtott get és set művelet látható.

    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az alapokat, az alábbi hivatkozásokra kattintva tudhat meg többet az Azure Redis Cache-ről.

* Tekintse meg az Azure Redis Cache ASP.NET-szolgáltatóit.
  * [Az Azure Redis munkamenetállapot-szolgáltatója](cache-aspnet-session-state-provider.md)
  * [Az Azure Redis Cache ASP.NET kimenetigyorsítótár-szolgáltatója](cache-aspnet-output-cache-provider.md)
* [Engedélyezze a gyorsítótár-diagnosztikát,](cache-how-to-monitor.md#enable-cache-diagnostics) hogy [megfigyelhesse](cache-how-to-monitor.md) a gyorsítótár állapotát. A mérőszámokat megtekintheti az Azure Portalon, illetve többféle eszközzel is [letöltheti és áttekintheti](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) őket.
* Tekintse meg a [StackExchange.Redis gyorsítótárügyfél dokumentációját][StackExchange.Redis cache client documentation].
  * Az Azure Redis Cache számos Redis-ügyfélből és fejlesztési nyelvből elérhető. További információ: [http://redis.io/clients][http://redis.io/clients].
* Az Azure Redis Cache olyan harmadik féltől származó szolgáltatásokkal és eszközökkel is használható, mint a Redsmin vagy a Redis Destkop Manager.
  * További információt a Redsminről [az Azure Redis kapcsolati karakterlánc lekérését és a Redsminnel történő használatát][How to retrieve an Azure Redis connection string and use it with Redsmin] ismertető cikkben talál.
  * Az Azure Redis Cache-ben tárolt adatait a [RedisDesktopManagert](https://github.com/uglide/RedisDesktopManager) használó grafikus felhasználó felületen érheti el és vizsgálhatja meg.
* Tekintse meg a [Redis][redis] dokumentációját, ahol olvashat a [Redis-adattípusokról][redis data types], valamint áttekintheti [a Redis-adattípusok tizenöt perces bemutatását][a fifteen minute introduction to Redis data types].

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Create the cache]: #create-cache
[Configure the cache]: #enable-caching
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[Connect to the cache]: #connect-to-cache
[Add and retrieve objects from the cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session


<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png







<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[How to retrieve an Azure Redis connection string and use it with Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis configuration model]: https://stackexchange.github.io/StackExchange.Redis/Configuration

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache Pricing Details]: http://www.windowsazure.com/pricing/details/cache/
[Azure portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis cache client documentation]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis data types]: http://redis.io/topics/data-types
[a fifteen minute introduction to Redis data types]: http://redis.io/topics/data-types-intro

[How Application Strings and Connection Strings Work]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/



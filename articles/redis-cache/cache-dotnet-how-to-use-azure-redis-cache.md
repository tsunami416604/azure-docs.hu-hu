<properties 
    pageTitle="Az Azure Redis Cache használata | Microsoft Azure" 
    description="Információk arról, hogy miként javítható az Azure-alkalmazásai teljesítménye az Azure Redis Cache segítségével" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="sdanie"/>

# Az Azure Redis Cache használata

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Ez az útmutató segítséget nyújt az első lépések megtételéhez az **Azure Redis Cache** használatakor. A Microsoft Azure Redis Cache a nyílt forráskódú és népszerű Redis Cache-re épül. Hozzáférést biztosít egy biztonságos, dedikált Redis Cache gyorsítótárhoz, amelyet a Microsoft felügyel. Az Azure Redis Cache használatával létrehozott gyorsítótárak a Microsoft Azure összes alkalmazásából elérhetőek.

A Microsoft Azure Redis Cache a következő szinteken érhető el:

-   **Alapszintű** – Egyetlen csomópont. Többféle méret legfeljebb 53 GB-ig.
-   **Standard** – Két csomópontból álló elsődleges/replika. Többféle méret legfeljebb 53 GB-ig. 99,9%-os SLA.
-   **Prémium** – Két csomópontból álló elsődleges/replika, legfeljebb 10 szegmenssel. Többféle méret 6 GB és 530 GB között (további információkért vegye fel velünk a kapcsolatot). Tartalmazza a Standard szint összes szolgáltatását és továbbiakat, beleértve a [Redis-fürtök](cache-how-to-premium-clustering.md), a [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) és az [Azure Virtual Network](cache-how-to-premium-vnet.md) támogatását. 99,9%-os SLA.

Az egyes szintek szolgáltatási feltételei és díjszabása eltérő. További díjszabási információk: [Díjszabás – Redis Cache][].

Ez az útmutató ismerteti, hogyan használhatja a [StackExchange.Redis][] ügyfelet C\# kóddal. Az ismertetett forgatókönyvek között megtalálható **gyorsítótár létrehozása és konfigurálása**, **gyorsítótárügyfelek konfigurálása** és **objektumok hozzáadása és eltávolítása a gyorsítótárból**. Az Azure Redis Cache használatával kapcsolatos további információkat a [Következő lépések][] szakaszban talál. Részletes útmutató ASP.NET MVC webalkalmazás felépítéséhez Redis Cache használatával: [How to create a Web App with Redis Cache](cache-web-app-howto.md) (Webalkalmazás létrehozása Redis Cache használatával).

<a name="getting-started-cache-service"></a>
## Bevezetés az Azure Redis Cache használatába

Az első lépések az Azure Redis Cache-ben egyszerűek. Először hozzon létre és konfiguráljon egy gyorsítótárat. Ezután konfigurálja a gyorsítótárügyfeleket, hogy hozzáféréssel rendelkezzenek a gyorsítótárhoz. A gyorsítótárügyfelek konfigurálása után megkezdheti a használatukat.

-   [A gyorsítótár létrehozása][]
-   [A gyorsítótárügyfelek konfigurálása][]

<a name="create-cache"></a>
## Gyorsítótár létrehozása

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### A gyorsítótár elérése a létrehozás után

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

További információk a gyorsítótár konfigurálásáról: [How to configure Azure Redis Cache](cache-configure.md) (Az Azure Redis Cache konfigurálása).

<a name="NuGet"></a>
## A gyorsítótárügyfelek konfigurálása

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Miután az ügyfélprojektet konfigurálta gyorsítótárazásra, a következő szakaszokban ismertetett módszereket is alkalmazhatja a gyorsítótár használatakor.

<a name="working-with-caches"></a>
## A gyorsítótárak használata

Az ebben a szakaszban szereplő lépések a gyorsítótárral végzett gyakori feladatok végrehajtását mutatják be.

-   [Csatlakozás a gyorsítótárhoz][]
-   [Objektumok hozzáadása és lekérése a gyorsítótárból][]
-   [.NET-objektumok használata a gyorsítótárban](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## Csatlakozás a gyorsítótárhoz

Annak érdekében, hogy programozott módon használja a gyorsítótárat, létre kell hoznia egy, a gyorsítótárra mutató hivatkozást. Adja a következőt bármely olyan fájl tetejéhez, amelyből a StackExchange.Redis ügyféllel szeretne hozzáférni egy Azure Redis Cache-hez.

    using StackExchange.Redis;

>[AZURE.NOTE] A StackExchange.Redis ügyfél használatához a .NET-keretrendszer 4-es vagy újabb verziója szükséges.

Az Azure Redis Cache-sel való kapcsolatot a `ConnectionMultiplexer` osztály kezeli. Ez az osztály úgy van kialakítva, hogy megosztható és ismét felhasználható legyen az ügyfélalkalmazásában, és nem kell létrehozni műveletenként. 

Az Azure Redis Cache-hez való csatlakozáshoz, valamint hogy a rendszer egy csatlakoztatott `ConnectionMultiplexer`-példányt adjon vissza, hívja meg a statikus `Connect` metódust, és a gyorsítótár végpontját és a kulcsot adja meg az alábbi példának megfelelően. Használja az Azure portálon létrehozott kulcsot password paraméterként.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Figyelmeztetés: Soha ne tároljon hitelesítő adatokat a forráskódban. Annak érdekében, hogy a minta egyszerű maradjon, a forráskódban mutatom meg őket. A hitelesítő adatok tárolásával kapcsolatos információk: [Az alkalmazás-karakterláncok és a kapcsolati karakterláncok működése][].

Ha nem szeretne SSL-t használni, állítsa be az `ssl=false` értéket, vagy hagyja ki az `ssl` paramétert.

>[AZURE.NOTE] A nem SSL port az új gyorsítótárakhoz alapértelmezés szerint le van tiltva. Útmutató a nem SSL port engedélyezéséhez: [Portok elérése](cache-configure.md#access-ports).

Az alkalmazásban egy `ConnectionMultiplexer` példány megosztására egy lehetséges módszer, ha létrehoz egy statikus tulajdonságot, amely egy csatlakoztatott példányt ad vissza, a következő példához hasonlóan. Ez egy szálbiztos módszer csak egyetlen csatlakoztatott `ConnectionMultiplexer`-példány inicializálásához. Ezekben a példákban az `abortConnect` értéke false, ami azt jelenti, hogy a hívás akkor is sikeres lesz, ha nincs kapcsolat az Azure Redis Cache-sel. A `ConnectionMultiplexer` egyik fontos szolgáltatása, hogy automatikusan visszaállítja a kapcsolatot a gyorsítótárral, amint a hálózati problémák vagy egyéb hibák elhárulnak.

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

További információ a speciális kapcsolatkonfigurációs lehetőségekről: [StackExchange.Redis konfigurációs modell][].

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

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

Most, hogy megismerte, hogyan csatlakozhat egy Azure Redis Cache-példányhoz és küldhet vissza egy hivatkozást a gyorsítótár-adatbázisnak, vessünk egy pillantást a gyorsítótár használatára.

<a name="add-object"></a>
## Objektumok hozzáadása és lekérése a gyorsítótárból

Elemek a `StringSet` és a `StringGet` metódussal tárolhatók a gyorsítótárban, illetve kérhetők le onnan.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

A Redis a legtöbb adatot Redis-karakterláncokként tárolja, azonban ezek a karakterláncok több adattípust is tartalmazhatnak, például szerializált bináris adatokat, amelyek akkor használhatók, ha .NET-objektumokat tárol a gyorsítótárban.

A `StringGet` hívásakor ha az objektum létezik, a rendszer visszaadja, ha pedig nem létezik, akkor a `null` értéket adja vissza. Ebben az esetben az értéket lekérheti a kívánt adatforrásból, és a gyorsítótárban tárolhatja későbbi használatra. Ez az úgynevezett gyorsítótár-feltöltő mód.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Egy elem lejáratának megadásához a gyorsítótárban használja a `TimeSpan` `StringSet` paraméterét.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## .NET-objektumok használata a gyorsítótárban

Az Azure Redis Cache .NET-objektumokat és primitív adattípusokat is képes gyorsítótárazni, de a .NET-objektumokat a gyorsítótárazásuk előtt szerializálni kell. Ez az alkalmazásfejlesztők feladata, akik így rugalmasan kiválaszthatják a szerializálót.

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
## Következő lépések

Most, hogy megismerte az alapokat, az alábbi hivatkozásokra kattintva tudhat meg többet az Azure Redis Cache-ről.

-   Tekintse meg az Azure Redis Cache ASP.NET-szolgáltatóit.
    -   [Az Azure Redis munkamenetállapot-szolgáltatója](cache-aspnet-session-state-provider.md)
    -   [Az Azure Redis Cache ASP.NET kimenetigyorsítótár-szolgáltatója](cache-aspnet-output-cache-provider.md)
-   [Engedélyezze a gyorsítótár-diagnosztikát,](cache-how-to-monitor.md#enable-cache-diagnostics) hogy [megfigyelhesse](cache-how-to-monitor.md) a gyorsítótár állapotát. A mérőszámokat megtekintheti az Azure portálon, illetve többféle eszközzel [letöltheti és áttekintheti](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) őket.
-   Tekintse meg a [StackExchange.Redis gyorsítótárügyfél dokumentációjában][].
    -   Az Azure Redis Cache számos Redis-ügyfélből és fejlesztési nyelvből elérhető. További információ: [http://redis.io/clients][].
-   Az Azure Redis Cache olyan harmadik féltől származó szolgáltatásokkal és eszközökkel is használható, mint a Redsmin vagy a Redis Destkop Manager.
    -   További információk a Redsminről: [How to retrieve an Azure Redis connection string and use it with Redsmin][] (Azure Redis kapcsolati karakterlánc lekérése és használata a Redsminnel).
    -   Az Azure Redis Cache-ben tárolt adatait a [RedisDesktopManagert](https://github.com/uglide/RedisDesktopManager) használó grafikus felhasználó felületen érheti el és vizsgálhatja meg.
-   Tekintse meg a [Redis][] dokumentációt, ahol olvashat a [Redis-adattípusokról][], valamint áttekintheti [A Redis-adattípusok tizenöt perces bemutatását][].



<!-- INTRA-TOPIC LINKS -->
[Következő lépések]: #next-steps
[Az Azure Redis Cache bemutatása (videó)]: #video
[Mi az az Azure Redis Cache?]: #what-is
[Azure Cache létrehozása]: #create-cache
[Melyik gyorsítótárazási típus megfelelő a számomra?]: #choosing-cache
[Visual Studio-projektek előkészítése az Azure Caching használatára]: #prepare-vs
[Alkalmazások konfigurálása gyorsítótárazás használatára]: #configure-app
[Bevezetés az Azure Redis Cache használatába]: #getting-started-cache-service
[A gyorsítótár létrehozása]: #create-cache
[A gyorsítótár konfigurálása]: #enable-caching
[A gyorsítótárügyfelek konfigurálása]: #NuGet
[A gyorsítótárak használata]: #working-with-caches
[Csatlakozás a gyorsítótárhoz]: #connect-to-cache
[Objektumok hozzáadása és lekérése a gyorsítótárból]: #add-object
[Specify the expiration of an object in the cache (Objektum lejáratának megadása a gyorsítótárban)]: #specify-expiration
[Store ASP.NET session state in the cache (ASP.NET munkamenet-állapot tárolása a gyorsítótárban)]: #store-session

  
<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Gyorsítótárak]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png






   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache (Fejlesztés más nyelveken az Azure Redis Cache-hez)]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[How to retrieve an Azure Redis connection string and use it with Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Az Azure Redis munkamenetállapot-szolgáltatója]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically (Útmutató: A gyorsítótár-ügyfél szoftveres konfigurálása)]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache (Az Azure Cache munkamenetállapot-szolgáltatója)]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State (Azure AppFabric Cache: munkamenet-állapot gyorsítótárazása)]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache (Az Azure Cache kimeneti gyorsítótár-szolgáltatója)]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Csapatblog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes (Virtuális gép méretének konfigurálása)]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations (Azure Caching kapacitástervezési szempontok)]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively (Útmutató: Egy ASP.NET-lap gyorsítótárazhatóságának beállítása deklarációval)]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically (Útmutató: Lap gyorsítótárazhatóságának szoftveres beállítása)]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache (Gyorsítótár konfigurálása az Azure Redis Cache-ben)]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis konfigurációs modell]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[.NET-objektumok használata a gyorsítótárban]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation (A NuGet Package Manager telepítése)]: http://go.microsoft.com/fwlink/?LinkId=240311
[Díjszabás – Redis Cache]: http://www.windowsazure.com/pricing/details/cache/
[Azure Portal]: https://portal.azure.com/

[Overview of Azure Redis Cache (Az Azure Redis Cache áttekintése)]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache (Áttelepítés Azure Redis Cache-re)]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples (Azure Redis Cache-minták)]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez)]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis gyorsítótárügyfél dokumentációjában]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis-adattípusok]: http://redis.io/topics/data-types
[A Redis-adattípusok tizenöt perces bemutatását]: http://redis.io/topics/data-types-intro

[Az alkalmazás-karakterláncok és a kapcsolati karakterláncok működése]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/





<!--HONumber=sep16_HO1-->



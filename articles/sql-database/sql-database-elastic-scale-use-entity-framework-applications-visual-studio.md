---
title: Elastic database-ügyfélkódtár használatával az Entity Framework |} A Microsoft Docs
description: Elastic Database-ügyfélkódtár és az Entity Framework használata adatbázisok kódolása
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 58b109651408a51ca7505c92d3875de63aae2cc6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261927"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Rugalmas adatbázis-ügyfélkódtárnak az Entity Framework
Ez a dokumentum az Entity Framework-alkalmazásba való integrálásához szükséges változásait jeleníti meg a [rugalmas adatbáziseszközöket](sql-database-elastic-scale-introduction.md). A fókusz a összeállítása van [szilánkleképezés-kezelés](sql-database-elastic-scale-shard-map-management.md) és [Adatfüggő útválasztásnak](sql-database-elastic-scale-data-dependent-routing.md) az Entity Framework- **Code First** megközelítést. A [kód először – új adatbázis](https://msdn.microsoft.com/data/jj193542.aspx) oktatóanyag az EF ebben a dokumentumban futó példaként szolgálja ki. A jelen dokumentum kísérő mintakódot is, a Visual Studio-Kódminták beállítása elastic database-eszközök részét képezi.

## <a name="downloading-and-running-the-sample-code"></a>Letöltésével és futtatásával a mintakód
Ebben a cikkben a kód letöltése:

* A Visual Studio 2012 vagy újabb szükség. 
* Töltse le a [Elastic DB eszközök az Azure SQL - Entity Framework-integráció minta](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) MSDN-ről. Csomagolja ki a minta egy tetszőleges helyre.
* Indítsa el a Visual Studiót. 
* A Visual Studióban válassza ki a fájl -> Nyissa meg a projekt/megoldás. 
* Az a **nyissa meg a projekt** párbeszédpanelen keresse meg a letöltött mintát, és válassza ki **EntityFrameworkCodeFirst.sln** , nyissa meg a mintát. 

A minta futtatásához szüksége három üres adatbázis létrehozása az Azure SQL Database-ben:

* Szegmenstérkép-kezelő adatbázis
* Szilánkleképezés 1 adatbázis
* 2 szegmenstérkép-adatbázis

Miután létrehozta az adatbázisok, adja meg a hely tulajdonosainak **Program.cs** az Azure SQL Database-kiszolgáló neve, az adatbázis nevét és az adatbázisokhoz való csatlakozáshoz a hitelesítő adatait. A Visual Studióban a megoldás felépítéséhez. A Visual Studio letölti a szükséges NuGet-csomagok az elastic database-ügyfélkódtár, az Entity Framework és az átmeneti hibák kezelése a létrehozási folyamat részeként. Győződjön meg arról, hogy a megoldás NuGet-csomagok visszaállítása engedélyezve. Ezzel a beállítással engedélyezheti a a megoldásfájlt a Visual Studio Megoldáskezelőben kattintson a jobb gombbal. 

## <a name="entity-framework-workflows"></a>Entity Framework munkafolyamatai
Entity Framework a fejlesztők a következő négy munkafolyamatok alkalmazásokat hozhat létre, és annak biztosítása érdekében az alkalmazás objektumának adatmegőrzés egyik támaszkodik: 

* **Code First (új adatbázis)**: az EF-fejlesztői az alkalmazás kódjának a modellt hoz létre, és ezután EF hoz létre az adatbázist. 
* **Code First (meglévő adatbázis)**: A fejlesztői lehetővé teszi, hogy az alkalmazás kódjának a modell létrehozásához egy meglévő adatbázis EF.
* **Első modell**: A fejlesztői hoz létre a modell az EF-Tervező, és ezután EF hoz létre az adatbázist a modellből.
* **Adatbázis-első**: A fejlesztői eszközök célszámítógéppel a modellt a meglévő adatbázis EF használja. 

Ezek a módszerek a DbContext osztályt transzparens módon kezelheti az adatbázis-kapcsolatok és a egy alkalmazás adatbázisséma támaszkodnak. A DbContext alaposztály különböző konstruktorok különböző szintű kézben kapcsolat létrehozásakor, rendszerindításra adatbázis és séma létrehozását teszik lehetővé. Elsősorban az a tény, hogy a kapcsolatot a megadott Adatfüggő útválasztási felületek funkcióival metszi a adatbázis-kapcsolat EF által biztosított felügyeleti kihívások által az elastic database-ügyfélkódtár merülnek fel. 

## <a name="elastic-database-tools-assumptions"></a>Rugalmas eszközök feltételezések
Kifejezés-definíciókat, lásd: [rugalmas adatbáziseszközökkel](sql-database-elastic-scale-glossary.md).

Az elastic database-ügyfélkódtár meghatározhatja az alkalmazásadatok shardlet nevű partíció. Shardlet horizontális skálázási kulcs azonosítja, és adott adatbázisokra van leképezve. Előfordulhat, hogy egy alkalmazás annyi adatbázisokat, igény szerint, és elegendő kapacitás és teljesítmény aktuális üzleti követelmények shardlet adatain terjesztése. Horizontális skálázási kulcs értékeit az adatbázisokhoz való leképezése horizontálispartíció-térkép a rugalmas adatbázis-ügyfél API-k által biztosított tárol. Ez a funkció neve **Szilánkleképezés-kezelés**, vagy röviden SMM. A horizontális skálázási térképet az adatbázis-kapcsolatok a kéréseket, amelyek olyan szegmenskulcsot átvitelszervezőként is szolgál. Ez a képesség más néven **Adatfüggő útválasztásnak**. 

A szilánkleképezés-kezelő felhasználók megvédi az inkonzisztens nézetek shardlet adatait, amely akkor fordulhat elő, amikor párhuzamos shardlet management műveletekhez (pl. adatok áthelyezése az egyik adatszilánkba író a másikra) történik. Ehhez a szegmenstérképet kezeli az ügyfél könyvtár broker az adatbázis-kapcsolatok az alkalmazáshoz. Ez lehetővé teszi az adatbázis-kapcsolat automatikusan leállítása, ha a szegmensek felügyeleti műveletek hatással lehet az, hogy a kapcsolat létre lett hozva shardlet szegmens térkép funkcióit. Ez a megközelítés integrálása az egyes EF a funkciókat, például új kapcsolatok létrehozása egy meglévő adatbázis létezik-e keresni kell. Általánosságban véve a megfigyelési lett, hogy a szabványos DbContext konstruktorok csak munkahelyi megbízhatóan lezárt adatbázis mintáját biztonságosan klónozható-kapcsolatok esetében működik. Rugalmas adatbázis tervezési alapelvét inkább, hogy csak a megnyitott kapcsolatok közvetíteni. Az egyik hiszi, hogy egy, az EF DbContext átadását megelőzően az ügyféloldali kódtár által felügyelt kapcsolat bezárása előfordulhat, hogy a probléma megoldásához. Azonban lezárja a kapcsolatot, és nyissa meg újra az EF hagyatkoznia, egy foregoes az a könyvtár érvényesítése és a konzisztencia elvégzett. Az áttelepítések-funkció az EF, azonban ezek a kapcsolatok használja az alapul szolgáló adatbázisséma úgy, hogy az átlátható az alkalmazás kezeléséhez. Ideális esetben fog megőrizni, és minden képességek a rugalmas adatbázis-klienskódtár és a EF ugyanabban az alkalmazásban kapcsolja össze. A következő szakaszban ezek a tulajdonságok és részletes követelményeket ismerteti. 

## <a name="requirements"></a>Követelmények
A rugalmas adatbázis-klienskódtár és a Entity Framework API-k használata, ha meg szeretné őrizni az alábbi tulajdonságokat: 

* **Horizontális felskálázás**: hozzáadásához, vagy eltávolíthat adatbázisokat a szilánkos alkalmazás kapacitásigény szükséges az alkalmazás adatrétegének. Ez azt jelenti, létrehozását és törlését, az adatbázisok és a rugalmas adatbázis szilánkleképezés-kezelővel API-k segítségével kezelheti az adatbázisok és shardlet leképezéseit felett. 
* **Konzisztencia**: az alkalmazás horizontális skálázást alkalmaz, és használja az ügyféloldali kódtár Adatfüggő útválasztás képességeit. Sérülés vagy hibás lekérdezési eredmények elkerülése érdekében kapcsolatok keresztül a szilánkleképezés-kezelő által felügyelt. Ez akkor is megtartja, érvényesítése és a konzisztencia.
* **Code First**: megőrzi az EF a kód első paradigm, a kényelem érdekében. A Code First a osztályok, az alkalmazás transzparens módon vannak leképezve az alapul szolgáló adatbázis struktúrák. Az alkalmazás kódjában, hogy maszkolja a legtöbb területét az alapul szolgáló adatbázis-feldolgozási részt DbSets kommunikál.
* **Séma**: entitás-keretrendszer kezdeti adatbázis sémájának létrehozása és későbbi séma fejlődést szem előtt tartva keresztül áttelepítések kezeli. Szerint megőrzése ezeket a képességeket, az alkalmazás gyakorlatainak az egyszerű, az adatok haladásával. 

A következő útmutatást arra utasítja, hogy ezek megfelelnek a rugalmas Adatbáziseszközök használatáról Code First alkalmazások hogyan. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Adatfüggő útválasztás EF DbContext használata
Az Entity Framework adatbázis-kapcsolatok általában felügyelt alosztályának **DbContext**. Hozzon létre ezen alosztályok kapcsolatból származtatott kapcsolatot **DbContext**. Ez az, amikor Ön határozza meg a **DbSets** , amely végrehajtja a CLR-beli objektumok az alkalmazás adatbázis-alapú gyűjteményei. Adatfüggő útválasztás környezetében azonosíthatja, amely nem feltétlenül rendelkeznek más EF code első alkalmazás-forgatókönyvek esetében számos hasznos tulajdonságai: 

* Az adatbázis már létezik, és regisztrálva van a rugalmas adatbázisok horizontális skálázási térképet. 
* A séma az alkalmazás már telepítve lett az adatbázishoz (alább részletesen). 
* Adatfüggő útválasztás kapcsolatok az adatbázishoz a szegmenstérkép szerint vannak felügyelt. 

Integrálható **DbContexts** a Adatfüggő útválasztás a horizontális felskálázás:

1. Hozzon létre a szilánkleképezés-kezelővel, fizikai adatbázis-kapcsolatok révén a rugalmas adatbázis ügyféloldali felületei 
2. A kapcsolat burkolása a **DbContext** alosztálya
3. Adja át a kapcsolat azokat a **DbContext** osztályokat, győződjön meg arról is történik, az EF oldalán a feldolgozás alapján. 

Az alábbi példakód azt szemlélteti, hogy ez a megközelítés. (Ez a kód van is a hozzájuk tartozó Visual Studio-projekt)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data-dependent routing. This call opens a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call fails for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>Fő szempontja
* Egy új konstruktort helyettesíti az alapértelmezett konstruktort a DbContext alosztály 
* Adatfüggő útválasztás – rugalmas adatbázis ügyfélkönyvtárának szükséges argumentumokat veszi fel, az új konstruktort:
  
  * a horizontális skálázási térképet Adatfüggő útválasztási felületek eléréséhez
  * a horizontális skálázási kulcs a shardlet azonosításához
  * a hitelesítő adatokat a szegmenshez Adatfüggő útválasztás kapcsolat kapcsolati karakterláncot. 
* Az alaposztály konstruktorának hívása egy detour figyelembe veszi, hogy az összes lépést hajt végre statická metoda szükséges Adatfüggő útválasztásnak. 
  
  * Egy megnyitott kapcsolatot létesíteni a horizontális skálázási térképet használ a OpenConnectionForKey hívás az a rugalmas adatbázis ügyféloldali felületei.
  * Horizontális skálázási térképet hoz létre a szegmenshez, amely a megadott horizontális skálázási kulcs a shardlet tárol a nyitott kapcsolat.
  * Ez a kapcsolat megnyitása vissza azt jelzi, hogy a kapcsolat helyett, ami lehetővé teszi az EF automatikusan hozzon létre egy új kapcsolatot EF által használandó DbContext alaposztály konstruktorának kerülnek. Ezzel a módszerrel a kapcsolat címkével ellátott a rugalmas adatbázis-ügyfél API-t, hogy garantálható a szegmensek térkép felügyeleti műveletek keretében konzisztencia.

Az új konstruktort használata helyett az alapértelmezett konstruktort, a kódban a DbContext alosztályát. Például: 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

Az új konstruktort megnyitja a kapcsolatot, amely a értéke által azonosított shardlet adatait tartalmazza a szegmens **tenantid1**. A kód a **használatával** block Access változatlan marad a **DbSet** EF használatával a horizontális blogokhoz **tenantid1**. Az a kód a például, hogy az összes adatbázis-műveletek hatóköre mostantól az egyik adatszilánkba író blokkolni szemantika változik ahol **tenantid1** marad. Például egy LINQ-lekérdezésekre keresztül az őt megjelenítő blogokon **DbSet** csak adna vissza, blogok, a jelenlegi szegmens tárolja, de nem a többi szegmens tárolja azokat.  

#### <a name="transient-faults-handling"></a>Az átmeneti hibák kezelése
A Microsoft Patterns és gyakorlatokkal foglalkozó csoportja közzé a [az átmeneti-kezelési Alkalmazásblokk](https://msdn.microsoft.com/library/dn440719.aspx). A könyvtár a rugalmas méretezésű ügyfélkódtárral EF együtt használatos. Győződjön meg azonban arról, hogy minden átmeneti kivételként egy helyen, ahol biztosíthatja, hogy az új konstruktort használatban van egy átmeneti hibák után, hogy minden új kapcsolódási kísérlet használatával jön létre az Ön tweaked konstruktorok adja vissza. Ellenkező esetben a megfelelő szegmensre kapcsolat nem garantált, és nincsenek nincs kapcsolat fenn lesz tartva, megváltozik a szilánkleképezésbe biztosítékokat. 

A következő példakód azt szemlélteti, egy SQL újrapróbálkozási szabályzat segítségével hogyan biztosítható az új körül **DbContext** alosztályát konstruktorok: 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy** a fenti kód számít, ha egy **SqlDatabaseTransientErrorDetectionStrategy** újrapróbálkozás-számot 5 másodperc és 10 várjon az újrapróbálkozások között eltelt idő. Ez a megközelítés hasonlít az útmutató EF és a felhasználó által kezdeményezett tranzakció (lásd: [végrehajtási stratégiák újrapróbálkozásainak (EF6-től) korlátozások](https://msdn.microsoft.com/data/dn307226). Mindkét esetben szükséges, hogy az alkalmazás szabályozza a hatókör, amelyhez a átmeneti kivételt adja vissza: Nyissa meg újra a tranzakciót, vagy (amint) hozza létre újra a környezetet, a megfelelő konstruktor, amely használja az elastic database ügyfélkódtár.

Szabályozhatja, ahol átmeneti kivételek is velünk vissza hatókörében kell is kizárja a beépített használatát **SqlAzureExecutionStrategy** EF, amely tartalmaz. **SqlAzureExecutionStrategy** lenne nyissa meg újra a kapcsolat, de nem használ **OpenConnectionForKey** részeként végrehajtott összes ellenőrző ezért megkerülve a **OpenConnectionForKey**hívja. Ehelyett a kódminta használ-e a beépített **DefaultExecutionStrategy** , amely EF is tartalmaz. Nem pedig **SqlAzureExecutionStrategy**, és az átmeneti hibák kezelésével az újrapróbálkozási szabályzat együttes alkalmazásával megfelelően működik. A végrehajtási házirend értéke a **ElasticScaleDbConfiguration** osztály. Vegye figyelembe, hogy döntöttünk, hogy nem használja az **DefaultSqlExecutionStrategy** használatát javasolja, mivel **SqlAzureExecutionStrategy** átmeneti kivételek esetén – amelyek vezetne nem megfelelő viselkedésének írtaknak. Az eltérő újrapróbálkozási szabályzatok és az EF további információkért lásd: [kapcsolat rugalmassága az EF](https://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Konstruktor újraírások
A fenti hitelesítésikód-példák bemutatják, hogy az alapértelmezett konstruktor újra ír az Entity Framework Adatfüggő útválasztás használatához az alkalmazás számára szükséges. Az alábbi táblázat általánosítja a többi konstruktorok megközelítést. 

| Aktuális konstruktor | Adatok Átdolgozta konstruktor | Alap konstruktor | Megjegyzések |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, bool) |A kapcsolat kell lennie a szegmenstérkép és a Adatfüggő útválasztás kulcs függvénye. EF hálózatukról automatikus kapcsolat létrehozásának kell, és helyette a szegmenstérkép használatával közvetítse a kapcsolatot. |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, bool) |A kapcsolat a szegmenstérkép és a Adatfüggő útválasztás kulcs függvénye. Egy rögzített adatbázis neve vagy a kapcsolati karakterlánc nem működik, azok által a szegmenstérkép hálózatukról érvényesítése. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |A kapcsolat az adott szegmens térkép és horizontális skálázási kulcs jön létre a megadott modell. A lefordított modell az alap c'tor átkerül az. |
| MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, logikai) |DbContext (DbConnection, bool) |A kapcsolat kell nelze odvodit z a szegmenstérkép és a kulcsot. Ez nem adható meg bemenetként (kivéve, ha a bemenetet a szegmenstérkép és a kulcs már használja). A logikai átkerül az. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |A kapcsolat kell nelze odvodit z a szegmenstérkép és a kulcsot. Ez nem adható meg bemenetként (kivéve, ha a bemenetet a szegmenstérkép és a kulcs lett használatával). A lefordított modell átkerül az. |
| MyContext (ObjectContext, bool) |ElasticScaleContext (ShardMap TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |Az új konstruktort meg kell győződnie arról, hogy az adatként az ObjectContext objektumban található összes kapcsolat irányítva egy rugalmasan méretezhető által felügyelt kapcsolat. ObjectContexts szóló részletes leírást van ez a dokumentum nem foglalkozik. |
| MyContext(DbConnection, DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |A kapcsolat kell nelze odvodit z a szegmenstérkép és a kulcsot. A kapcsolat nem adható meg bemenetként (kivéve, ha a bemenetet a szegmenstérkép és a kulcs már használja). Modell és a logikai értéket rendszer átadja az alaposztály konstruktorának. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>EF-áttelepítés – a szegmensben séma üzembe helyezése
Automatikus séma az Entity Framework által biztosított kényelem. Rugalmas Adatbáziseszközök használatáról alkalmazások kontextusában szeretné megőrizni ezt a funkciót, az újonnan létrehozott szegmensek-séma automatikus kiépítésére, amikor a szilánkos alkalmazás felvett adatbázisok. Az elsődleges használati eset, hogy növeli a kapacitást az adatszinten EF használó horizontálisan skálázott alkalmazások számára. EF a séma felügyeleti funkciókat a függő csökkenti az adatbázis-felügyeleti erőfeszítés EF épülő horizontálisan skálázott alkalmazással. 

EF-áttelepítés – a séma üzembe helyezése a legalkalmasabb az **kapcsolatok olvasatlan**. Ez a szakembereket Adatfüggő útválasztás a forgatókönyv, amely a megnyitott kapcsolat, a rugalmas adatbázis-ügyfél API által biztosított támaszkodik. Egy másik különbség a konzisztencia követelmény: az összes Adatfüggő útválasztás kapcsolathoz egyidejű szegmens térkép adatkezelési ellen védelmet biztosító konzisztencia biztosításához szükséges, miközben már nem jelent problémát egy kezdeti séma üzembe helyezése egy új adatbázis még nincs regisztrálva a horizontális skálázási térképet, és le lett foglalva, amely tárolja a shardlet még nem rendelkezik. Ezért ebben az esetben Adatfüggő útválasztásnak ellentétben rendszeres adatbázis-kapcsolatainak támaszkodnak.  

Ez vezet megközelítést, ahol EF-áttelepítés – a séma üzembe helyezése a szorosan összekapcsolt az új adatbázis regisztrációját, az alkalmazás horizontális skálázási térképet a szegmensek. Ez az alábbi előfeltételek támaszkodik: 

* Az adatbázis már létre van hozva. 
* Az adatbázis üres – nincs felhasználói séma- és nincsenek felhasználói adatok magánál tartja.
* Az adatbázis nem még keresztül érhető el a rugalmas adatbázis-ügyfél API-k Adatfüggő útválasztás. 

Ezek az előfeltételek teljesülnek, létrehozhat egy normál nem megnyitott **SqlConnection** felhőplatformos termékeiért EF-áttelepítés a séma üzembe helyezése. Az alábbi kódmintában ezt a megközelítést mutatja be. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 


Ez a példa bemutatja a metódus **RegisterNewShard** , amely a szegmens regisztrálja a szegmenstérkép az EF-áttelepítés – a séma üzembe helyezi és tárolja a többi olyan szegmenskulcsot térképét. Egy konstruktorának támaszkodik a **DbContext** alosztályát (**ElasticScaleContext** a mintában), amely bemenetként egy SQL-kapcsolati karakterlánc szükséges. Ez a konstruktor kódja lépünk, az alábbi példában látható módon: 

        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // You want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 

Előfordulhat, hogy az egyik használt a konstruktort az alaposztálytól verzióját. De a kód meg kell győződnie arról, hogy a csatlakozáskor használt-e az alapértelmezett inicializáló mintáját. Ezért a rövid detour be a statikus metódus kapcsolati alaposztály konstruktorának hívása előtt. Vegye figyelembe, hogy a regisztráció szegmens fusson-e az eltérő alkalmazástartományból vagy folyamat, győződjön meg arról, hogy ne ütközzenek EF inicializáló beállításait. 

## <a name="limitations"></a>Korlátozások
Az a jelen dokumentumban vázolt módszerek jár néhány korlátozás: 

* EF-alkalmazásokat, amelyek használják **LocalDb** elastic database-ügyfélkódtár használata előtt a szokásos SQL Server-adatbázis áttelepítése, először. Horizontális felskálázás egy alkalmazást a horizontális skálázás rugalmas méretezéssel nem lehetséges a **LocalDb**. Vegye figyelembe, hogy továbbra is használhatják az fejlesztési **LocalDb**. 
* Minden szegmensre az EF-áttelepítés meg kell módosítania az alkalmazást, amelyek azt sugallják adatbázis sémaváltozások. A mintakód a dokumentum nem bemutatják, hogyan lehet ezt. Fontolja meg az adatbázis-frissítés ConnectionString paraméterrel ciklustevékenység minden szegmensre; és nem fejtheti vissza a függőben lévő migrálás, adatbázis-frissítés használatával a T-SQL parancsfájl - szkripttel lehetőséget, majd a alkalmazni a T-SQL parancsfájl a szegmensek.  
* Adja meg a kérést, feltételezzük, hogy az összes hozzá tartozó adatbázis-feldolgozási belül található egyetlen szegmens a horizontális skálázási kulcs a kérés által megadott által azonosított. Azonban ezt a feltételezést nem mindig rendelkezik igaz. Például, ha nincs lehetőség a horizontális skálázási kulcs elérhetővé. Ennek a-klienskódtára biztosítja az **MultiShardQuery** egy kapcsolat absztrakciós a lekérdezés a több szegmensben implementáló osztályt. Használatának elsajátítása az **MultiShardQuery** EF együtt van az túlmutat a jelen dokumentum

## <a name="conclusion"></a>Összegzés
Ebben a dokumentumban leírt lépések, EF alkalmazások használhatja az elastic database ügyfélkódtár képesség Adatfüggő útválasztásnak konstruktora újrabontás által a **DbContext** alosztályok az EF alkalmazásban használt. Ez korlátozza, hogy a helyek szükséges módosításokat, **DbContext** osztályok már léteznek. Emellett EF-alkalmazások továbbra is kihasználhatják a automatikus séma üzembe helyezése a lépéseket, amelyek aktiválják a szükséges EF-áttelepítés regisztrációját az új szegmensek és a horizontális skálázási térképet leképezések kombinálásával. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png

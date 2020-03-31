---
title: Rugalmas adatbázis-ügyfélkódtár használata entitáskeretrendszerrel
description: Rugalmas adatbázis-ügyféltár és entitáskeretrendszer használata adatbázisok kódolásához
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 1653a904875964d86864c59c718603a6dacdcbda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087188"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Rugalmas adatbázis-ügyféltár entitáskeretrendszerrel

Ez a dokumentum az entitáskeret-alkalmazások azon módosításait mutatja be, amelyek a [rugalmas adatbázis-eszközökkel](sql-database-elastic-scale-introduction.md)való integrációhoz szükségesek. A hangsúly a [shard térképkezelés](sql-database-elastic-scale-shard-map-management.md) és [az adatoktól függő útválasztás](sql-database-elastic-scale-data-dependent-routing.md) összeállítása az Entitás **keretkód első** megközelítés. A [Code First – New Database](https://msdn.microsoft.com/data/jj193542.aspx) oktatóanyag az EF szolgál a futó példa ebben a dokumentumban. Az ezt a dokumentumot kísérő mintakód a Visual Studio kódmintáiban található rugalmas adatbázis-eszközök mintakészletének része.

## <a name="downloading-and-running-the-sample-code"></a>A mintakód letöltése és futtatása

A cikk kódjának letöltése:

* Visual Studio 2012-es vagy újabb verziók szükségesek. 
* Töltse le az [Azure SQL rugalmas DB-eszközeit – Entitáskeretrendszer-integrációs minta.](https://github.com/Azure/elastic-db-tools/) Csomagolja ki a mintát az Ön által választott helyre.
* Indítsa el a Visual Studiót. 
* A Visual Studióban válassza a File -> Open Project/Solution (Fájl -> Projekt/Megoldás megnyitása) lehetőséget. 
* A **Projekt megnyitása** párbeszédpanelen keresse meg a letöltött mintát, és válassza **az EntityFrameworkCodeFirst.sln** lehetőséget a minta megnyitásához. 

A minta futtatásához három üres adatbázist kell létrehoznia az Azure SQL Database-ben:

* Shard Map Manager adatbázis
* Shard 1 adatbázis
* Shard 2 adatbázis

Miután létrehozta ezeket az adatbázisokat, töltse ki a helyőrzők **Program.cs** az Azure SQL DB-kiszolgáló nevét, az adatbázis nevét és a hitelesítő adatokat az adatbázisokhoz való csatlakozáshoz. Készítse el a megoldást a Visual Studio-ban. A Visual Studio a létrehozási folyamat részeként letölti a rugalmas adatbázis-ügyfélkódtárhoz, az Entitáskeretrendszerhez és az Átmeneti hibakezeléshez szükséges NuGet-csomagokat. Győződjön meg arról, hogy a NuGet-csomagok visszaállítása engedélyezve van a megoldáshoz. Ezt a beállítást úgy engedélyezheti, hogy a jobb gombbal a Visual Studio Solution Explorer megoldásfájljára kattint. 

## <a name="entity-framework-workflows"></a>Entitáskeret-munkafolyamatok

Az Entitáskeretrendszer fejlesztői az alábbi négy munkafolyamat egyikére támaszkodnak az alkalmazások létrehozásához és az alkalmazásobjektumok megőrzésének biztosításához:

* **Kód első (Új adatbázis):** Az EF fejlesztő létrehozza a modellt az alkalmazáskódban, majd az EF létrehozza belőle az adatbázist. 
* **Kód első (meglévő adatbázis)**: A fejlesztő lehetővé teszi az EF számára, hogy a modell alkalmazáskódját egy meglévő adatbázisból hozza létre.
* **Első modell**: A fejlesztő létrehozza a modellt az EF-tervezőben, majd az EF létrehozza az adatbázist a modellből.
* **Első adatbázis**: A fejlesztő EF-eszközökkel következtet a modellből egy meglévő adatbázisból. 

Mindezek a megközelítések a DbContext osztályra támaszkodnak az alkalmazás adatbázis-kapcsolatainak és adatbázissémának transzparens kezeléséhez. A DbContext alaposztály különböző konstruktorai lehetővé teszik a kapcsolat létrehozása, az adatbázis-rendszerindítás és a séma létrehozása különböző szintű vezérlését. A kihívások elsősorban abból erednek, hogy az EF által biztosított adatbázis-kapcsolatkezelés metszi a rugalmas adatbázis-ügyfélkódtár által biztosított adatfüggő útválasztási összeköttetések kapcsolatkezelési képességeit. 

## <a name="elastic-database-tools-assumptions"></a>Rugalmas adatbázis-eszközök feltételezései

A kifejezésdefiníciókról az [Elastic Database tools szószedet című témakörben található.](sql-database-elastic-scale-glossary.md)

Rugalmas adatbázis-ügyfélkódtár, az alkalmazásadatok shardlets nevű partícióit definiálja. Shardlets egy szilánkos kulcs azonosítja, és meghatározott adatbázisokhoz vannak rendelve. Egy alkalmazás lehet, hogy annyi adatbázist, amennyi szükséges, és a shardlets terjesztéséhez elegendő kapacitást vagy teljesítményt az aktuális üzleti követelményeknek megfelelően. A szegmensek kulcsértékeinek leképezése az adatbázisoktárolja a rugalmas adatbázis-ügyfél API-k által biztosított shard térkép. Ez a képesség az úgynevezett **Shard Map Management**, vagy SMM röviden. A shard térkép is szolgál, mint a közvetítő az adatbázis-kapcsolatok a szilánkos kulcsot tartalmazó kérelmek. Ezt a funkciót **adatfüggő útválasztásnak nevezzük.** 

A shard map manager védi a felhasználókat az inkonzisztens nézetek shardlet adatok, amelyek akkor fordulhatelő, ha egyidejű shardlet felügyeleti műveletek (például adatok áthelyezése az egyik szegmensből a másikba) történik. Ehhez az ügyféltár által felügyelt szegmensleképezések egy alkalmazás adatbázis-kapcsolatait közvetítik. Ez lehetővé teszi, hogy a shard térkép funkció automatikusan leáll egy adatbázis-kapcsolat, ha shard felügyeleti műveletek hatással lehet a shardlet, amely a kapcsolat hozták létre. Ennek a megközelítésnek integrálnia kell az EF néhány funkciójával, például új kapcsolatokat kell létrehoznia egy meglévőről, hogy ellenőrizze az adatbázis létezését. Általánosságban elmondható, hogy a standard DbContext konstruktorok csak megbízhatóan működnek a zárt adatbázis-kapcsolatok, amelyek biztonságosan klónozható EF munkát. A rugalmas adatbázis tervezési elve ehelyett az, hogy csak a megnyitott kapcsolatok közvetítője. Azt gondolhatnánk, hogy az ügyfélkönyvtár által közvetített kapcsolat bezárása, mielőtt átadná az EF DbContext-nek, megoldhatja ezt a problémát. Azonban a kapcsolat bezárásával és az EF újbóli megnyitásával az egyik lemond a tár által végzett érvényesítési és konzisztencia-ellenőrzésekről. Az EF áttelepítési funkciója azonban ezeket a kapcsolatokat használja az alapul szolgáló adatbázisséma olyan módon történő kezelésére, amely az alkalmazás számára átlátható módon történik. Ideális esetben megtartja és egyesíti ezeket a képességeket mind a rugalmas adatbázis-ügyfélkódtárból, mind az EF ugyanabban az alkalmazásban. A következő szakasz részletesebben ismerteti ezeket a tulajdonságokat és követelményeket. 

## <a name="requirements"></a>Követelmények

Ha a rugalmas adatbázis-ügyfélkódtárral és az entitáskeretrendszer API-kkal is dolgozik, meg szeretné őrizni a következő tulajdonságokat: 

* **Horizontális felskálázás**: Adatbázisok hozzáadása vagy eltávolítása a szilánkos alkalmazás adatréteg, ha szükséges az alkalmazás kapacitásigénye. Ez azt jelenti, hogy szabályozhatja az adatbázisok létrehozását és törlését, és a rugalmas adatbázis-leképezéskezelő API-k használatával kezelheti az adatbázisokat és a shardlets leképezéseit. 
* **Konzisztencia**: Az alkalmazás szilánkolást alkalmaz, és az ügyféltár adatfüggő útválasztási képességeit használja. A sérülés vagy a helytelen lekérdezési eredmények elkerülése érdekében a kapcsolatok a shard map manager en keresztül lesznek közvetítve. Ez is megtartja az érvényesítés és a konzisztencia.
* **Kód Először**: Az EF kódjának kényelmének megőrzése az első paradigma. A Code First-ben az alkalmazás osztályai transzparens módon vannak leképezve az alapul szolgáló adatbázisstruktúrákhoz. Az alkalmazáskód együttműködik a DbSets készletekkel, amelyek elfedik az alapul szolgáló adatbázis-feldolgozásban részt vevő legtöbb szempontot.
* **Séma**: Az entitáskeretrendszer kezeli a kezdeti adatbázisséma létrehozását és az azt követő séma-létrehozást az áttelepítéseken keresztül. Ezeknek a képességeknek a megtartásával az alkalmazás adaptálása az adatok fejlődésével egyszerű. 

Az alábbi útmutató azt ismerteti, hogyan felelmeg ezeknek a követelményeknek a Code First alkalmazások rugalmas adatbázis-eszközök használatával. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Adatfüggő útválasztás az EF DbContext használatával

Az Entitáskeretrendszerrel létesített adatbázis-kapcsolatok kezelése általában a DbContext alosztályain **keresztül történik.** Ezeket az alosztályokat a **DbContext**ből származó . Itt határozhatja meg a **DbSets,** amely megvalósítja az adatbázis által támogatott gyűjtemények CLR-objektumok az alkalmazáshoz. Az adatfüggő útválasztás környezetében számos hasznos tulajdonságot azonosíthat, amelyek nem feltétlenül rendelkeznek más EF-kód első alkalmazási forgatókönyveivel: 

* Az adatbázis már létezik, és regisztrálva van a rugalmas adatbázis shard térképen. 
* Az alkalmazás sémája már telepítve van az adatbázisban (az alábbiakban ismertetett). 
* Az adatbázisadat-függő útválasztási kapcsolatokat a szegmenstérkép közvetíti. 

A **DbContexts integrálása** adatfüggő útválasztással a horizontális felskálázáshoz:

1. Hozzon létre fizikai adatbázis-kapcsolatokat a shard map manager rugalmas adatbázis-ügyfélfelületein keresztül, 
2. A Kapcsolat tördelése a **DbContext** alosztállyal
3. Adja át a kapcsolatot a **DbContext** alaposztályokba, hogy az EF-oldalon lévő összes feldolgozás is megtörténjen. 

A következő példa erre a megközelítésre mutat. (Ez a kód a mellékelt Visual Studio projektben is szerepel)

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

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
```

## <a name="main-points"></a>Főbb pontok

* Egy új konstruktor helyettesíti a DbContext alosztály alapértelmezett konstruktorait 
* Az új konstruktor veszi az okat, amelyek szükségesek az adatoktól függő útválasztás rugalmas adatbázis-ügyfélkódtár:
  
  * a szegmenstérkép az adatfüggő útválasztási összeköttetések eléréséhez,
  * a szilánkos kulcs a shardlet azonosításához,
  * a kapcsolati karakterlánc a hitelesítő adatoktól függő útválasztási kapcsolat a szegmenshez. 
* Az alaposztály konstruktorának hívása egy statikus metódusba kerül, amely végrehajtja az adatfüggő útválasztáshoz szükséges összes lépést. 
  
  * A rugalmas adatbázis-ügyfélfelületek OpenConnectionForKey hívását használja a shard térképen egy nyitott kapcsolat létrehozásához.
  * A shard térkép létrehozza a nyílt kapcsolatot a shard, amely tartalmazza a shardlet a megadott szilánkos kulcs.
  * Ez a nyitott kapcsolat visszakerül a DbContext alaposztálykonstruktorához, jelezve, hogy ezt a kapcsolatot az EF használja ahelyett, hogy az EF automatikusan új kapcsolatot hozna létre. Így a kapcsolat van címkézve a rugalmas adatbázis-ügyfél API-t, hogy garantálja a konzisztenciát a shard map felügyeleti műveletek.

Használja az új konstruktor a DbContext alosztály helyett az alapértelmezett konstruktor a kódban. Például: 

```csharp
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
```

Az új konstruktor megnyitja a kapcsolatot a shard, amely tartalmazza az adatokat a shardlet által azonosított értéke **tenantid1**. A kód a **használatával** blokk változatlan marad, hogy a **DbSet** a blogok segítségével EF a shard a **tenantid1**. Ez megváltoztatja a szemantika a kód a használatával blokk, így az összes adatbázis-műveletek most hatóköre az egyik szegmens, ahol **tenantid1** van tárolva. Például egy LINQ-lekérdezést a **DbSet** blogokon keresztül csak az aktuális szegmensben tárolt blogokat adja vissza, de a más szegmenseken tároltakat nem.  

#### <a name="transient-faults-handling"></a>Átmeneti hibák kezelése

A Microsoft Patterns & Practices csapata közzétette az [Átmeneti hibakezelési alkalmazásblokkot.](https://msdn.microsoft.com/library/dn440719.aspx) A könyvtár rugalmas méretezési ügyfélkódtárral és EF-vel kombinálva használatos. Azonban győződjön meg arról, hogy minden átmeneti kivétel visszatér egy olyan helyre, ahol biztosíthatja, hogy az új konstruktor használata után egy átmeneti hiba, hogy minden új csatlakozási kísérlet történik a konstruktorok módosított. Ellenkező esetben a megfelelő shard kapcsolat nem garantált, és nincs garancia a kapcsolat megmarad, ha a shard térkép módosításai bekövetkeznek. 

A következő kódminta bemutatja, hogyan használható az SQL újrapróbálkozási házirend az új **DbContext** alosztály konstruktorok körül: 

```csharp
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
```

A fenti kódban található **SqlDatabaseDatabaseUtils.SqlRetryPolicy** egy **SqlDatabaseTransientErrorDetectionStrategy** definíciója, amelynek újrapróbálkozásszáma 10, és 5 másodperces várakozási idő az újrapróbálkozások között. Ez a megközelítés hasonló az EF-re és a felhasználó által kezdeményezett tranzakciókra vonatkozó útmutatáshoz (lásd: Korlátozások a [végrehajtási stratégiák újbóli megkísérléséhez (EF6-tól)](https://msdn.microsoft.com/data/dn307226). Mindkét helyzetben megköveteli, hogy az alkalmazás program szabályozza a hatókört, amelyre az átmeneti kivétel visszatér: vagy újra megnyitja a tranzakciót, vagy (ahogy látható) újra a környezetet a megfelelő konstruktor, amely a rugalmas adatbázis-ügyfélkódtár.

Annak szabályozására, ahol az átmeneti kivételek visszavisznek minket a hatókörbe, szintén kizárja az EF beépített **SqlAzureExecutionStrategy** használatát. **Az SqlAzureExecutionStrategy** újra megnyit egy kapcsolatot, de nem használja az **OpenConnectionForKey-t,** és ezért megkerüli az **OpenConnectionForKey** hívás részeként végrehajtott összes érvényesítést. Ehelyett a kódminta a beépített **DefaultExecutionStrategy** stratégiát használja, amely az EF-hez is tartozik. Az **SqlAzureExecutionStrategy**programmal ellentétben megfelelően működik az átmeneti hibakezelés újrapróbálkozási házirendjével kombinálva. A végrehajtási házirend az **ElasticScaleDbConfiguration** osztályban van beállítva. Vegye figyelembe, hogy úgy döntöttünk, hogy nem használjuk **a DefaultSqlExecutionStrategy,mivel** azt javasolja, **hogy sqlAzureExecutionStrategy** használata esetén átmeneti kivételek fordulnak elő , ami rossz viselkedéshez vezetne, ahogy azt tárgyalta. A különböző újrapróbálkozási házirendekkel és EF-ekkel kapcsolatos további információkért lásd: [Csatlakozás rugalmasság a EF-ben.](https://msdn.microsoft.com/data/dn456835.aspx)     

#### <a name="constructor-rewrites"></a>A konstruktor átírja

A fenti kódpéldák bemutatják az alapértelmezett konstruktor újraírása szükséges az alkalmazás az entitás keretrendszer adatfüggő útválasztás használata érdekében. Az alábbi táblázat általánosítja ezt a megközelítést más konstruktorok számára. 

| Jelenlegi konstruktor | Újraírva konstruktor adatokhoz | Alapkonstruktor | Megjegyzések |
| --- | --- | --- | --- |
| Saját környezet() |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection; bool) |A kapcsolatnak a szegmenstérkép és az adatfüggő útválasztási kulcs függvényének kell lennie. Meg kell haladnia az AUTOMATIKUS kapcsolat létrehozása az EF, és ehelyett a shard térkép et kell használnia a kapcsolat közvetítéséhez. |
| Sajatkörnyezet(karakterlánc) |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection; bool) |A kapcsolat a shard térkép és az adatfüggő útválasztási kulcs függvénye. A rögzített adatbázis név vagy kapcsolati karakterlánc nem működik, mivel a shard térkép által imitálási műveletek. |
| Sajatkörnyezet(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection; DbCompiledModel, bool) |A kapcsolat jön létre az adott szegmens térkép és a modell a megadott modellt. A lefordított modell átkerül az alap c'tor. |
| SajatKörnyezet(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection; bool) |A kapcsolatot a shard térképből és a kulcsból kell kikövetkeztetni. Nem adható meg bemenetként (kivéve, ha ez a bemenet már használta a szegmenstérképet és a kulcsot). A logikai adták tovább. |
| SajatKörnyezet(karakterlánc; DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection; DbCompiledModel, bool) |A kapcsolatot a shard térképből és a kulcsból kell kikövetkeztetni. Nem adható meg bemenetként (kivéve, ha ez a bemenet a szegmenstérképet és a kulcsot használta). A lefordított modell továbbadható. |
| Sajatkörnyezet(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext; bool) |Az új konstruktornak gondoskodnia kell arról, hogy az ObjectContext bemenetként átadott bármely kapcsolatát átirányítsa a Rugalmas méretezés által kezelt kapcsolatra. Az ObjectContexts részletes megvitatása nem tartozik a dokumentum hatálya alá. |
| SajatKörnyezet(DbConnection, DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |A kapcsolatot a shard térképből és a kulcsból kell kikövetkeztetni. A kapcsolat nem adható meg bemenetként (kivéve, ha ez a bemenet már használta a szegmenstérképet és a kulcsot). A modell és a logikai érték átkerülnek az alaposztály konstruktorához. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Shard séma telepítése EF-áttelepítéseken keresztül

Az automatikus sémakezelés az entitáskeretrendszer kényelmét biztosítja. Rugalmas adatbázis-eszközök használatával alkalmazások környezetében szeretné megőrizni ezt a képességet, hogy automatikusan kiépíti a sémát az újonnan létrehozott szegmensek, amikor adatbázisokat adnak hozzá a szilánkos alkalmazáshoz. Az elsődleges használati eset az, hogy növeli a kapacitást az adatrétegen az EF használatával szilánkos alkalmazások. Támaszkodva az EF sémakezelési képességeire támaszkodva csökkenti az adatbázis-felügyeleti erőfeszítéseket az EF-re épülő szilánkos alkalmazással. 

A séma telepítése az EF-áttelepítéseken keresztül a **bontatlan kapcsolatokon**működik a legjobban. Ez ellentétben áll az adatfüggő útválasztás forgatókönyvével, amely a rugalmas adatbázis-ügyfél API által biztosított megnyitott kapcsolatra támaszkodik. Egy másik különbség a konzisztencia követelmény: Bár kívánatos, hogy biztosítsa a konzisztencia minden adatfüggő útválasztási kapcsolatok elleni egyidejű shard térkép manipuláció, ez nem aggályos a kezdeti séma központi egy új adatbázisba, amely még nincs regisztrálva a shard térképen, és még nincs kiosztva a shardlets tárolására. Ezért ebben a forgatókönyvben a rendszeres adatbázis-kapcsolatok, szemben az adatfüggő útválasztás.  

Ez egy olyan megközelítéshez vezet, amelyben a séma üzembe helyezése az EF-áttelepítéseken keresztül szorosan kapcsolódik az új adatbázis az alkalmazás shard térképének shard ként történő regisztrációjához. Ez a következő előfeltételekre támaszkodik: 

* Az adatbázis már létrejött. 
* Az adatbázis üres - nem tartalmaz felhasználói sémát és felhasználói adatokat.
* Az adatbázis még nem érhető el a rugalmas adatbázis-ügyfél API-k on keresztül az adatfüggő útválasztás. 

Ezekkel az előfeltételekkel létrehozhat egy rendszeres, meg nem nyitott **SqlConnection-t,** hogy elindíthassa az EF-áttelepítéseket a séma telepítéséhez. A következő kódminta ezt a megközelítést mutatja be. 

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema 

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
```

Ez a minta a **RegisterNewShard** metódust jeleníti meg, amely regisztrálja a szegmenst a szegmenstérképen, telepíti a sémát az EF-áttelepítéseken keresztül, és egy szegmenskulcs leképezését tárolja. Támaszkodik a **DbContext** alosztály **(ElasticScaleContext** a mintában), amely egy SQL-kapcsolati karakterlánc bemenetként támaszkodik. A konstruktor kódja egyenesen előre halad, amint azt a következő példa mutatja: 

```csharp
// C'tor to deploy schema and migrations to a new shard 
protected internal ElasticScaleContext(string connectionString) 
    : base(SetInitializerForConnection(connectionString)) 
{ 
} 

// Only static methods are allowed in calls into base class c'tors 
private static string SetInitializerForConnection(string connectionString) 
{ 
    // You want existence checks so that the schema can get deployed 
    Database.SetInitializer<ElasticScaleContext<T>>( 
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

    return connectionString; 
} 
```

Lehet, hogy az alaposztálytól örökölt konstruktor verzióját használta. De a kódnak biztosítania kell, hogy az EF alapértelmezett inicializálója legyen használva a csatlakozáskor. Ezért a rövid kitérőt a statikus metódusbe, mielőtt behívna az alaposztály konstruktorát a kapcsolati karakterlánccal. Vegye figyelembe, hogy a szegmensek regisztrációjának egy másik alkalmazástartományban vagy folyamatban kell futnia annak érdekében, hogy az EF inicializáló beállításai ne ütközzenek. 

## <a name="limitations"></a>Korlátozások

Az e dokumentumban vázolt megközelítések néhány korlátozással járnak: 

* A **LocalDb-t** használó EF-alkalmazásoknak először át kell térniük egy normál SQL Server-adatbázisba, mielőtt rugalmas adatbázis-ügyfélkódtárat használnának. A **LocalDb**segítségével nem lehet horizontális skálázással kibővíteni egy alkalmazást. Ne feledje, hogy a fejlesztés továbbra is használhatja **LocalDb**. 
* Az alkalmazás minden olyan módosítása, amely az adatbázis-séma módosításait jelenti, az összes szegmensen keresztül kell végrehajtania az EF-áttelepítéseket. A dokumentum mintakódja nem mutatja be ennek módját. Fontolja meg az Update-Database egy ConnectionString paraméterrel az összes szegmens en történő iterálását; vagy bontsa ki a T-SQL-parancsfájlt a függőben lévő áttelepítéshez az Update-Database használatával a -Script beállítással, és alkalmazza a T-SQL parancsfájlt a szegmensekre.  
* Egy kérelem esetén feltételezhető, hogy az összes adatbázis-feldolgozás egyetlen szegmensben található, a kérelem által biztosított szegmenskulcs által azonosított. Ez a feltételezés azonban nem mindig igaz. Például ha nem lehetséges, hogy egy szilánkos kulcs elérhetővé. Ennek megoldásához az ügyféltár biztosítja a **MultiShardQuery** osztályt, amely egy kapcsolatasztrakciót valósít meg több szegmensen keresztüli lekérdezéshez. A **MultiShardQuery** EF-el való együttes használatának elsajátítása nem tartozik a jelen dokumentum hatálya alá

## <a name="conclusion"></a>Összegzés

Az ebben a dokumentumban ismertetett lépések révén az EF-alkalmazások használhatják a rugalmas adatbázis-ügyfélkódtár adatfüggő útválasztásra való képességét az EF-alkalmazásban használt **DbContext** alosztályok konstruktorainak átbontásával. Ez korlátozza a szükséges módosításokat azokra a helyekre, ahol **a DbContext** osztályok már léteznek. Emellett az EF-alkalmazások továbbra is élvezhetik az automatikus sémaüzembe helyezés előnyeit a szükséges EF-áttelepítések meghívására és a szegmensek és leképezések shard térképen történő regisztrációjával kapcsolatos lépések kombinálásával. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png

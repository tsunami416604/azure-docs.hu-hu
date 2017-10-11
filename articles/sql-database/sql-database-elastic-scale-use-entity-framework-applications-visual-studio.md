---
title: "A rugalmas adatbázis ügyféloldali kódtár használata Entity Framework |} Microsoft Docs"
description: "Elastic Database ügyféloldali kódtár és Entity Framework használja az adatbázisok kódolása"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: b9c3065b-cb92-41be-aa7f-deba23e7e159
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: torsteng
ms.openlocfilehash: 2f0bff394c1e11a270cb324be5a1a45e9e531d7f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Az Entity Framework rugalmas adatbázis ügyféloldali kódtár
Ez a dokumentum láthatók, amelyek szükségesek ahhoz, hogy integrálható az Entity Framework alkalmazásban a [skálázáshoz rugalmas adatbáziseszközöket](sql-database-elastic-scale-introduction.md). A elsősorban összeállítása [shard térkép felügyeleti](sql-database-elastic-scale-shard-map-management.md) és [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md) az Entity Framework rendelkező **Code First** megközelítést. A [Code először - új adatbázis](http://msdn.microsoft.com/data/jj193542.aspx) EF útmutató futó példánkban ez a dokumentum funkcionál. Ez a dokumentum kísérő mintakód beállítása a Visual Studio-Kódminták található minták rugalmas adatbázis eszközök részét képezi.

## <a name="downloading-and-running-the-sample-code"></a>Töltsön le és futtasson példakód
Ez a cikk kódjának letöltése:

* A Visual Studio 2012 vagy újabb rendszer szükséges. 
* Töltse le a [rugalmas adatbázis-eszközök az Azure SQL - entitás Keretrendszeri integrációját minta](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) msdn. Bontsa ki a minta a helyre.
* Indítsa el a Visual Studiót. 
* A Visual Studio alkalmazásban válassza ki a fájl -> Nyissa meg a projekt/megoldás. 
* Az a **nyissa meg a projekt** párbeszédpanel, nyissa meg a letöltött minta, és válassza ki **EntityFrameworkCodeFirst.sln** a minta megnyitásához. 

A minta futtatásához szükséges három üres adatbázisok létrehozására az Azure SQL Database:

* A shard térkép Manager-adatbázis
* A shard 1 adatbázis
* A shard 2 adatbázis

Miután létrehozta ezeket az adatbázisokat, töltse ki a hely tartozó felhasználók számára a **Program.cs** az Azure SQL adatbázis-kiszolgáló neve, az adatbázis nevét, és csatlakozzon az adatbázishoz a hitelesítő adatait. A Visual Studio megoldás felépítéséhez. A Visual Studio letölti a szükséges NuGet-csomagok az elastic database ügyféloldali kódtár Entity Framework, és átmeneti hiba kezelésére szolgál az összeállítási folyamat részeként. Győződjön meg arról, hogy a megoldás NuGet-csomagok visszaállítására engedélyezve. Kattintson a jobb gombbal a megoldás fájlra a Visual Studio Solution Explorer engedélyezheti ezt a beállítást. 

## <a name="entity-framework-workflows"></a>Entity Framework munkafolyamatai
Entitás-keretrendszer fejlesztői egyet a következő négy munkafolyamatok olyan alkalmazásokat hozhat létre és alkalmazás objektumának megőrzését támaszkodnak: 

* **Első (új adatbázis) code**: az EF fejlesztői hoz létre a modell az alkalmazáskódban, és akkor EF hoz létre az adatbázist. 
* **Első (meglévő adatbázis) code**: A fejlesztői lehetővé teszi, hogy az alkalmazás kódjának a modell létrehozása egy létező adatbázisból EF.
* **A modell első**: A fejlesztői hoz létre a modell az EF-tervezőben, és ezután EF hoz létre az adatbázist a modellből.
* **Adatbázis-első**: A fejlesztő EF tooling eszköz, amelyből megállapítható a modell egy meglévő adatbázist. 

Ezek a módszerek a DbContext osztályt transzparens módon kezelheti a Helyadatbázis-kapcsolatot, és az alkalmazás adatbázisséma támaszkodnak. Ismertetik részletesebben a dokumentum későbbi szakaszában a DbContext alaposztályban különböző konstruktorok lehetővé teszik a különböző szintű kapcsolat létrehozásakor, ellenőrzésre adatbázis rendszerindítása és séma létrehozásához. Kihívást erednek elsősorban az, hogy az adatbázis-kapcsolat EF által biztosított kezelése az adatok függő útválasztási felületek kapcsolat kezelési lehetőségeinek metszi a rugalmas adatbázis ügyféloldali kódtár által. 

## <a name="elastic-database-tools-assumptions"></a>A rugalmas adatbázis eszközök Előfeltételek
Kifejezés-definíciók, lásd: [rugalmas adatbázis eszközök szószedet](sql-database-elastic-scale-glossary.md).

A rugalmas adatbázis ügyféloldali kódtár az alkalmazásadatok shardlets nevű partíciója határozza meg. Shardlets egy horizontális skálázási kulcs azonosítják, és konkrét adatbázisaihoz van leképezve. Egy alkalmazás előfordulhat, hogy annyi adatbázis igény szerint, és ossza ki a shardlets elég kapacitás vagy a teljesítmény aktuális üzleti követelmények biztosításához. Az adatbázisok horizontális kulcsértékei leképezése egy API-k rugalmas adatbázis-ügyfél által megadott shard leképezés tárolja. Ez a funkció hívása **Shard térkép felügyeleti**, vagy röviden SMM. A szilánkok térkép is szolgál az átvitelszervező adatbázis-kapcsolatok a kéréseket, amelyben egy horizontális skálázási kulcsot. Ezt a képességet, hivatkozunk **adatok függő útválasztási**. 

A szilánkok térkép-kezelő felhasználók védi a inkonzisztens nézetek, amely akkor fordulhat elő, amikor párhuzamos shardlet felügyeleti műveletek (például az adatok áthelyezése egy shard a másik) hogy shardlet adatokká. Ehhez a shard maps felügyeli az ügyfél könyvtár broker a Helyadatbázis-kapcsolatot az alkalmazás. Ez lehetővé teszi a shard leképezés funkció automatikusan leállítani az adatbázis-kapcsolat, ha shard felügyeleti műveletek jelentős hatással lehet a shardlet, amely a kapcsolat jött létre a(z). Ezt a módszert kell néhány EF-EK funkciókat, például új kapcsolatok létrehozására, az adatbázis létezését keressen egy meglévő integrálható. Általában a megfigyelési lett, hogy működik-e a szabványos DbContext konstruktorok csak munkahelyi megbízhatóan EF biztonságosan klónozható lezárt adatbázis-kapcsolatok esetében. Rugalmas kialakítás elve-kapcsolatok csak replikaszervező helyette hoz. Egy hiszi, hogy az ügyfél kódtára előtt továbbításáért azt az EF DbContext közvetítőalapú kapcsolat bezárása előfordulhat, hogy a probléma megoldására. Azonban lezárja a kapcsolatot, és nyissa meg újra az EF hagyatkoznia, egy foregoes az érvényesítési és a konzisztencia ellenőrzések végzi el a könyvtárban. A áttelepítések funkciói EF, azonban ezek a kapcsolatok kezelésére használ az alapul szolgáló adatbázisséma oly módon, hogy az alkalmazás átlátható. Ideális esetben szeretnénk és az összes ezeket a képességeket a rugalmas adatbázis ügyféloldali kódtár és az EF ugyanabban az alkalmazásban kombinálni. Az alábbi szakasz ismerteti, ezek a tulajdonságok és részletes követelményeket. 

## <a name="requirements"></a>Követelmények
Az elastic database ügyféloldali kódtárának mind az Entity Framework API-k használatakor szeretnénk tartsa meg az alábbi tulajdonságokat: 

* **Kibővített**: hozzáadásához vagy eltávolításához adatbázisok alkalmazásának a szilánkos a kapacitás iránti igények kielégítése érdekében a megfelelő az alkalmazás az adatok réteg alapján. Ez azt jelenti, hogy vezérlő keresztül a létrehozását és törlését az adatbázisok és a rugalmas adatbázis shard használatával leképezése manager API-k, adatbázisok és shardlets leképezéseit kezeléséhez. 
* **Konzisztencia**: az alkalmazás horizontális alkalmazza, és adatok függő útválasztási szolgáltatásait használja az ügyféloldali kódtár. Sérült vagy helytelen lekérdezési eredmények elkerülése érdekében kapcsolatok keresztül a shard térkép manager által felügyelt. Ez megőrzi a érvényesítése és a konzisztencia is.
* **Első Code**: EF-EK kód első paradigma kényelmi megőrzését. A kód első a osztályok az alkalmazás átlátható módon vannak leképezve az alapul szolgáló adatbázis felépítését. Az alkalmazás kódjában, amely a legtöbb területét az alapul szolgáló adatbázis-feldolgozási részt maszkolnia DbSets kommunikál.
* **Séma**: Entity Framework kezdeti adatbázis-séma létrehozása és későbbi séma alakulása áttelepítések keresztül kezeli. Megtartja ezeket a képességeket, az alkalmazás testreszabásához az egyszerű az adatok fejlődésének. 

A következő útmutatást arra utasítja, hogy hogyan ezeknek a követelményeknek rugalmas adatbáziseszközöket Code First alkalmazásokhoz. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Az adatok függő útválasztás EF DbContext használata
Adatbázis-kapcsolatok az Entity Framework általában kezelhetők alosztályokat **DbContext**. Hozzon létre a alosztályok származó **DbContext**. Itt adhatja meg azt a **DbSets** amelyek megvalósítják az adatbázis biztonsági objektumok gyűjteményeit adják CLR az alkalmazáshoz. Az adatok függő útválasztási környezetben azt azonosíthatja a számos hasznos tulajdonság nem feltétlenül vonatkozik az egyéb EF code első alkalmazás forgatókönyvek, amelyek: 

* Az adatbázis már létezik, és a rugalmas adatbázis shard térkép regisztrálva van. 
* A séma, az alkalmazás már telepítve van az adatbázishoz (alábbi leírásban is). 
* Adatok függő útválasztási kapcsolatok az adatbázis által a shard térkép vannak közvetítőalapú. 

Integrálni **DbContexts** kibővített az útválasztási adatok függő:

1. A szilánkok térkép-kezelő, a rugalmas adatbázis ügyfél felületeken keresztül fizikai adatbázis-kapcsolatok létrehozása 
2. A kapcsolat burkolása a **DbContext** alosztálya lesz
3. Adja a kapcsolat át az a **DbContext** EF oldalán összes feldolgozása történik, valamint hogy osztályok alapjául. 

Az alábbi példakód mutatja be ezt a módszert használja. (Ez a kód egyben a hozzá tartozó Visual Studio-projekt)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
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

## <a name="main-points"></a>Főbb pontjai
* Egy új konstruktort a felváltja az alapértelmezett konstruktort a a DbContext alosztálya lesz 
* Az új konstruktor argumentummal szükséges adatok függő útválasztási elastic database ügyféloldali kódtárának keresztül:
  
  * a szilánkok térkép elérni az adatok függő útválasztási felületek
  * a horizontális kulcs a shardlet azonosításához
  * a kapcsolati karakterlánc az adatok függő a shard útválasztási kapcsolat hitelesítő adatait. 
* Az alaposztály konstruktor hívása veszi egy detour statikus metódus, amely a lépéseket végzi el az adatok függő útválasztási szükséges. 
  
  * Nyitott kapcsolatot létrehozni a shard térképen használ a rugalmas adatbázis-ügyfél adapterek OpenConnectionForKey hívása.
  * A szilánkok térkép a shard, amely tárolja a megadott horizontális skálázási kulcs shardlet a nyitott kapcsolatot hoz.
  * A megnyitott kapcsolat vissza azt jelzi, hogy a kapcsolat ahelyett, hogy automatikusan hozzon létre egy új kapcsolatot EF EF által használandó DbContext alaposztály konstruktorának átadott. Így a kapcsolat címkézett a rugalmas adatbázis-ügyfél API-t, hogy garantálják, hogy a shard térkép felügyeleti műveletek konzisztencia.

Az új konstruktor használata helyett az alapértelmezett konstruktort a kódban a DbContext alosztálya. Például: 

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

Az új konstruktor megnyitja a kapcsolatot a shard értékének által azonosított shardlet használt adatokat tartalmazó **tenantid1**. A kód a **használatával** blokk hozzáférés változatlan marad a **DbSet** az EF szalaghasználata a shard blogok **tenantid1**. Ez módosítja szemantikáját be a kódját letiltása például, hogy az összes művelet hatóköre mostantól a következő egy shard ahol **tenantid1** maradnak. Például a blogok keresztül a LINQ lekérdezés **DbSet** csak alakítanák vissza, az aktuális shard tárolt blogok, de nem más szilánkok tárolja azokat.  

#### <a name="transient-faults-handling"></a>Átmeneti hibák kezelése
A Microsoft Patterns & eljárások csapat közzé a [az átmeneti hiba kezelési alkalmazás blokk](https://msdn.microsoft.com/library/dn440719.aspx). A könyvtár a rugalmas bővítést ügyféloldali kódtár EF együtt használatos. Azonban ügyeljen arra, hogy átmeneti kivételen egy olyan helyre, ahol azt is győződjön meg arról, hogy az új konstruktor használatban van egy átmeneti hiba után, hogy minden új kapcsolódási kísérlet történik a konstruktorok azt rendelkezik tweaked használatával adja vissza. Ellenkező esetben a megfelelő shard kapcsolat nem garantált, és nincs változás a shard leképezés a kapcsolat megmarad biztosítékok van. 

A következő példakód azt szemlélteti, hogyan SQL újrapróbálkozási házirendje használható-e az új körül **DbContext** alosztály konstruktorok: 

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

**SqlDatabaseUtils.SqlRetryPolicy** a kódban van definiálva, egy **SqlDatabaseTransientErrorDetectionStrategy** újrapróbálkozás-szám 10-es és 5 másodperc várjon az újrapróbálkozások között eltelt idő. Ez a megközelítés hasonlít az útmutató EF és a felhasználó által kezdeményezett tranzakció (lásd: [korlátozásai a végrehajtási stratégiák újrapróbálkozás (EF6 és újabb verziók esetében)](http://msdn.microsoft.com/data/dn307226). Mindkét esetben szükséges, hogy az alkalmazás szabályozza a hatókör, amelyhez a átmeneti kivételt adja vissza: Nyissa meg újra a tranzakciót, vagy (ahogy) hozza létre újra a megfelelő konstruktor a környezetben, amely a rugalmas adatbázis ügyféloldali kódtár használ.

Történő ellenőrzése, ahol átmeneti kivételek a hálózatról, biztonsági hatókör is kizárja a beépített használatát **SqlAzureExecutionStrategy** az EF elérhető lesz. **SqlAzureExecutionStrategy** volna nyissa meg újra a kapcsolatot, de nem használható **OpenConnectionForKey** , és ezért az érvényesítési részeként végrehajtott műveletek a **OpenConnectionForKey** hívja. Ehelyett a kódminta használja a beépített **DefaultExecutionStrategy** az EF is elérhető lesz. Helyett **SqlAzureExecutionStrategy**, megfelelően működik az újrapróbálkozási házirendet kezeljen átmeneti hiba együtt. A végrehajtási házirend beállítása a **ElasticScaleDbConfiguration** osztály. Vegye figyelembe, hogy nem használandó döntöttünk **DefaultSqlExecutionStrategy** óta használandó javasol **SqlAzureExecutionStrategy** átmeneti kivételek esetén – amely vezetne nem megfelelő viselkedésének című szakaszban leírtaknak megfelelően. A házirendek a különböző újrapróbálkozásos és EF további információkért lásd: [kapcsolati rugalmasságot az EF](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Konstruktor újraírások
A fenti példák bemutatják, hogy az alapértelmezett konstruktor átírja adatok függő az Entity Framework útválasztással használatához az alkalmazáshoz szükséges. A következő táblázat a más konstruktorok megközelítése használatúvá. 

| Aktuális konstruktor | Az adatok egy átírt konstruktor | Alap konstruktor | Megjegyzések |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |A kapcsolat kell lennie a shard leképezés és az adatok függő útválasztási kulcsot. Kihagyva automatikus kapcsolat létrehozásának EF kell, és helyette a shard térkép segítségével replikaszervező a kapcsolatot. |
| MyContext(string) |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |A szilánkok leképezés és az adatok függő útválasztási kulcs létrejön a kapcsolat. A rögzített adatbázis neve vagy a kapcsolati karakterlánc nem fognak működni azok által a shard térkép kihagyva érvényesítése. |
| MyContext(DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, logikai) |A kapcsolat lesz létrehozása a megadott shard térkép és horizontális kulcs bemutatott minta. A kiinduló c'tor a lefordított modell fog átadásra. |
| MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, logikai) |DbContext (DbConnection, bool) |A kapcsolat kell következtethető ki a shard térkép és a kulcsot. Azt nem adható meg bemenetként (kivéve, ha a bemenetet a shard térkép és a kulcs már használja). A logikai értékek átadásra kerül. |
| MyContext (karakterlánc, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, logikai) |A kapcsolat kell következtethető ki a shard térkép és a kulcsot. Azt nem adható meg bemenetként (kivéve, ha a bemenetet a shard térkép és a kulcs lett használatával). A lefordított modell átadásra kerül. |
| MyContext (ObjectContext, bool) |ElasticScaleContext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |Gondoskodjon arról, hogy a kapcsolat a adatként az ObjectContext objektumban található átirányítását kezeli a rugalmas bővítést kapcsolatra kell az új konstruktor. Részletes leírását az ObjectContexts nem a jelen dokumentum terjed. |
| MyContext (DbConnection, DbCompiledModel, logikai) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection, DbCompiledModel, logikai); |A kapcsolat kell következtethető ki a shard térkép és a kulcsot. A kapcsolat nem adható meg bemenetként (kivéve, ha a bemenetet a shard térkép és a kulcs már használja). Modell és a logikai átadott a alaposztály konstruktor. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>A shard séma telepítési keresztül EF-áttelepítések
Automatikus séma kezelésére szolgál az Entity Framework által biztosított, a könnyebb elérhetőség érdekében. A rugalmas adatbázis-eszközt használó alkalmazások a környezetben azt szeretnénk megőrizni ezt a képességet, hogy automatikusan kiépítést biztosítson a sémát, hogy az újonnan létrehozott szilánkok adatbázisok a szilánkos alkalmazás hozzáadásakor. Az elsődleges használati eset az adatréteg szilánkos alkalmazásokhoz EF-kapacitás növelése érdekében. Séma felügyeleti képességeket EF-EK hagyatkoznia csökkenti az adatbázis-felügyeleti elérhető EF épülő szilánkos alkalmazással. 

Séma telepítési EF áttelepítések keresztül működik a legjobban a **ilyen formában kapcsolatok**. Ez az ellentétben a forgatókönyvet, az adatok függő útválasztási, amely a megnyitott kapcsolat, a rugalmas adatbázis-ügyfél API által megadott támaszkodik. Egy másik különbség a konzisztencia-követelményt: az összes adat-függő útválasztási kapcsolathoz egyidejű shard térkép adatkezelési elleni védelem érdekében konzisztencia biztosításához szükséges, miközben még nincs egy probléma, amely az új adatbázist, amelyen még nincs regisztrálva a shard leképezés, és még nincs lefoglalva a shardlets tartsa a kezdeti séma központi telepítéshez. Ezért azt is támaszkodnak rendszeres adatbázis-kapcsolatok a forgatókönyvek esetén adatok függő útválasztási szemben.  

Ennek eredménye egy megközelítésre ahol séma telepítési keresztül EF áttelepítések szorosan együtt használja a regisztrációját, az új adatbázis, az alkalmazás shard leképezés a szilánkcímtárban. Ez a következő előfeltételek támaszkodik: 

* Az adatbázis már létezik. 
* Az adatbázis - magánál tartja nincs felhasználói séma és a nem felhasználói adatokat.
* Az adatbázis nem még elérhetőek a rugalmas adatbázis-ügyfél API-k az adatok függő továbbításához. 

Az előfeltételek teljesülnek, a szokványos létrehozhatjuk megbízhatatlan megnyitott **SqlConnection** az EF áttelepítések séma telepítéshez indítsa. A következő példakód azt szemlélteti, hogy ezt a módszert használja. 

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


Ez a példa bemutatja a metódus **RegisterNewShard** , amely a shard regisztrálja a shard térkép, telepíti a séma EF áttelepítések és egy egy horizontális skálázási kulccsal, hogy a szilánkok leképezése tárolja. A konstruktor támaszkodnak a **DbContext** alosztály (**ElasticScaleContext** minta) bemenetként SQL kapcsolati karakterlánc időt vesz igénybe. Ez a konstruktor kódját a következő egyszerű feladat, az alábbi példában látható módon: 

        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 

Előfordulhat, hogy egy használt a konstruktort az alaposztály öröklődik verzióját. De a kódot kell ellenőrizze, hogy az alapértelmezett inicializáló EF a kapcsolódáskor használja-e. Ezért a rövid detour be a statikus metódus az alaposztály konstruktornak a megadott kapcsolati karakterlánccal hívása előtt. Vegye figyelembe, hogy a regisztráció a szilánkok futtasson-e az eltérő alkalmazástartományból vagy folyamat végrehajtásával biztosítható, hogy nem ütköznek-EF inicializáló beállításait. 

## <a name="limitations"></a>Korlátozások
A jelen dokumentumban ismertetett módszerekkel néhány korlátozásokkal jár: 

* EF használó alkalmazások **LocalDb** először át kell térnie az SQL Server-adatbázis rendszeres elastic database ügyféloldali kódtár használata előtt. A rugalmas bővítést ki egy alkalmazást a horizontális skálázás nincs lehetőség a **LocalDb**. Vegye figyelembe, hogy fejlesztési továbbra is használhatja **LocalDb**. 
* A módosításokat a, amelyek azt sugallják adatbázis sémamódosítások érdekében át kell haladnia EF áttelepítések minden szegmensben osztják meg. A mintakód a dokumentum nem bemutatják, hogyan lehet ezt. Érdemes lehet az adatbázis-frissítés a ConnectionString paraméterrel az összes szilánkok; ismétlés a T-SQL-parancsfájlt a függőben lévő áttelepítési adatbázis-frissítés használatával, illetve a - parancsfájllal lehetőséget, majd a T-SQL-parancsfájl a szilánkok vonatkoznak.  
* A kérelemben megadott, feltételezzük, hogy az adatbázis-feldolgozási mindegyikét tartalmazza egyetlen shard a kérelem által biztosított horizontális kulcs által meghatározott. Azonban ez a feltételezés nem mindig tartsa igaz. Például, ha nincs lehetőség egy horizontális skálázási kulcs elérhetővé. A probléma orvoslása érdekében az ügyféloldali kódtár biztosít a **MultiShardQuery** osztály, amely a kapcsolat absztrakciós több szilánkok keresztül lekérdezése. Teszi használatát a **MultiShardQuery** EF együtt nem terjed a jelen dokumentum

## <a name="conclusion"></a>Összegzés
Ebben a dokumentumban leírt lépéseket, keresztül EF alkalmazások a rugalmas adatbázis ügyféloldali kódtár funkció segítségével az adatok függő konstruktora újrabontása útválasztást a **DbContext** alosztályok az EF alkalmazásban használt. Ez csökkenti a helyek szükséges módosításokat ahol **DbContext** osztályok már léteznek. Emellett EF alkalmazások továbbra is a lépéseket, amelyek a szükséges EF áttelepítések regisztrációját új szilánkok és a shard térkép tartalmazó meghívása kombinálásával automatikus séma telepítési kihasználhassa. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png

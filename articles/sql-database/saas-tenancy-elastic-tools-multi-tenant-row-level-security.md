---
title: Több-bérlős alkalmazások rls-t és a rugalmas Adatbáziseszközök |} A Microsoft Docs
description: Elastic database-eszközök használata a sorszintű biztonság egy jól skálázható adatréteggel rendelkező alkalmazás létrehozását.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 24375ca3fec50c1a9e194918ac4f824ab6fa81be
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568260"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Az elastic database-eszközökkel és a sorszintű biztonság több-bérlős alkalmazások

[Elastic database-eszközök](sql-database-elastic-scale-get-started.md) és [sorszintű biztonság (RLS)] [ rls] együttműködnek az Azure SQL Database több-bérlős alkalmazás adatrétegének skálázás engedélyezése. Ezek a technológiák együtt egy jól skálázható adatréteggel rendelkező alkalmazást készíthet segítségével. Az adatréteg több-bérlős szilánkok támogatja, és használja **ADO.NET SqlClient** vagy **Entity Framework**. További információkért lásd: [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database-](saas-tenancy-app-design-patterns.md).

- **Elastic database-eszközök** horizontális felskálázása az adatréteg standard alapuló, horizontális particionálási eljárásokkal, .NET-kódtárakkal és az Azure-szolgáltatás sablonok használatával a fejlesztők. A szegmensek történő kezelésével a [Elastic Database-Ügyfélkódtár] [ s-d-elastic-database-client-library] segít automatizálhatja és korszerűsítheti számos, a horizontális skálázás általában társított infrastrukturális feladatokat.
- **Sorszintű biztonság** lehetővé teszi a fejlesztők számára, hogy biztonságosan tárolva az adatok több bérlő ugyanabban az adatbázisban. Az RLS-biztonsági házirendek szűrése ki a sorokat, amely a lekérdezést végrehajtó bérlőhöz tartozik. Az adatbázisban a szűrő logika központosítása egyszerűbbé teszi a karbantartási, és csökkenti a biztonsági hiba kockázatát. A biztonság kényszerítése minden Ügyfélkód hagyatkoznia a kockázatos esetben.

A funkciók használatával együtt, egy alkalmazás adatok tárolhatók ugyanabban a szegmensben az adatbázisban több bérlő számára. Ez kevesebb költséggel jár bérlőnként, amikor a bérlők osztoznak egy adatbázist. Még ugyanazt az alkalmazást is is letilthatja a prémium szintű bérlők a saját dedikált egybérlős szegmens kellene fizetnie. Egybérlős elkülönítési egyik előnye nagyobb fokú biztonság érdekében teljesítményre vonatkozó garanciákat. Egy egybérlős adatbázis nincs semmilyen más bérlővel versengő erőforrások.

A cél az, hogy a rugalmas adatbázis ügyfélkönyvtárának [Adatfüggő útválasztásnak](sql-database-elastic-scale-data-dependent-routing.md) API-k automatikusan csatlakozhatnak a megfelelő szegmensre adatbázis egyes adott bérlő. Csak az egyik adatszilánkba író adott TenantId érték tartalmazza az adott bérlő esetében. A tenantid a *horizontális skálázási kulcs*. A kapcsolat létrejötte után a az RLS-biztonsági szabályzat az adatbázison belül biztosítja, hogy az adott bérlő férhetnek csak ezen adatok a sorokat a TenantId.

> [!NOTE]
> A bérlőazonosító egynél több oszlopot tartalmazhatnak. A kényelem vitafórumban, más feltételezzük, hogy egy egyoszlopos TenantId.

![Blogplatform app-architektúra][1]

## <a name="download-the-sample-project"></a>Töltse le a mintaprojektet

### <a name="prerequisites"></a>Előfeltételek

- Használja a Visual Studio (2012 vagy újabb)
- Három Azure SQL-adatbázisok létrehozása
- Töltse le a mintaprojektet: [Az Azure SQL - több-Bérlős szilánkok elastic DB eszközök](https://go.microsoft.com/?linkid=9888163)
  - Adja meg az adatokat az adatbázisok elején **Program.cs** 

Ez a projekt kibővíti a leírt egy [Elastic DB eszközök az Azure SQL - Entity Framework-integráció](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) támogatásával a több-bérlős bontott adatbázissal. A projekt egy egyszerű, blogok és hozzászólások létrehozására szolgáló konzolalkalmazást épít fel. A projekt négy bérlővel, valamint két több-bérlős bontott adatbázisokat tartalmazza. Ez a konfiguráció a fenti ábrán látható. 

Hozhat létre, és futtassa az alkalmazást. Futtatni az elastic database-eszközök szilánkleképezés-kezelővel csatlakoztatja, és a következő teszteket hajt végre: 

1. Entity Framework és a LINQ használatával, hozzon létre egy új blog, és jelenítheti meg a blogok az egyes bérlők számára
2. Egy bérlő használja az ADO.NET SqlClient, jeleníti meg a blogok
3. Győződjön meg arról, hogy egy hiba lépett fel a megfelelő bérlő blog beszúrásakor  

Figyelje meg, hogy rls-t még nem engedélyezett a szilánkleképezés-adatbázisokban lévő, mert ezek a tesztek mindegyike tárja fel a problémát: bérlők látni, amely nem tartozik hozzájuk blogok, és az alkalmazás nem akadályozza blog Beszúrás rossz bérlőhöz. Ez a cikk további része ismerteti, hogyan lehet az ilyen problémák megoldásához a bérlők elkülönítésére az RLS által. Két lépésből áll: 

1. **Alkalmazásrétegek**: Mindig az aktuális bérlő azonosítója a munkamenetben beállított, az alkalmazás kódjának módosítása\_helyi kapcsolat megnyitása után. A mintaprojekt már állítja be a bérlő azonosítója: Ezzel a módszerrel. 
2. **Adatszint**: Az RLS-biztonsági házirend létrehozása az egyes szegmensek adatbázisokban tárolt munkamenet TenantId alapján sorok szűrése\_környezetben. Szabályzat létrehozása a szilánkleképezés-adatbázisok mindegyike esetében, ellenkező esetben a több-bérlős szilánkok sorok nem vannak szűrve. 

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. Alkalmazásrétegek: A munkamenetben beállított TenantId\_környezet

Először csatlakozzon a szegmenstérkép-adatbázis a Adatfüggő útválasztás API-k használatával, az elastic database ügyfélkódtár. Az alkalmazás továbbra is kell tudniuk az adatbázis melyik TenantId a kapcsolatot használ. A TenantId arra utasítja az RLS-biztonsági házirendet melyik sort kell kiszűrte meghatározásoknak megfelelően a többi bérlőtől. Az aktuális TenantId Store a [munkamenet\_környezet](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) a kapcsolat.

MUNKAMENET helyett\_környezet használatára [környezet\_INFO](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). De munkamenet\_környezet nem jobb megoldás. MUNKAMENET\_környezet használata egyszerűbb, NULL alapértelmezés szerint adja vissza, és a kulcs-érték párok támogatja.

### <a name="entity-framework"></a>Entity Framework

Az alkalmazások Entity Framework használatával, a legegyszerűbb megközelítés, hogy állítsa be a munkamenet\_KÖRNYEZETÉBEN található a leírt ElasticScaleContext felülbírálás [Adatfüggő útválasztás használatával EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Hozzon létre, és hajtsa végre, amely beállítja a TenantId a munkamenet egy SqlCommand\_a a kapcsolathoz megadott shardingKey KÖRNYEZETÉT. Ezután adja vissza a Adatfüggő útválasztásnak keresztül felügyelt kapcsolat. Ezzel a módszerrel csak kell kód megírását, állítsa be a munkamenet\_környezetben. 

```csharp
// ElasticScaleContext.cs 
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection 
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
} 
// ... 
```

Most már a munkamenet\_környezet automatikus beállítása a megadott Bérlőazonosító, amikor ElasticScaleContext meghívásainak: 

```csharp
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
    {     
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
        foreach (var item in query)     
        {       
            Console.WriteLine(item.Name);     
        }   
    } 
}); 
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

ADO.NET SqlClient használó alkalmazásokhoz hozzon létre egy burkoló funkció ShardMap.OpenConnectionForKey metódus körül. Az automatikusan a munkamenetben beállított TenantId burkoló rendelkezik\_az aktuális TenantId kapcsolat visszaküldése előtt környezetben. Annak érdekében, hogy a munkamenet\_környezetben mindig be van-e állítva, ez a burkoló funkció segítségével kapcsolatok csak nyisson meg.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Adatszint: Sorszintű biztonság szabályzat létrehozása

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Hozzon létre egy biztonsági szabályzatot az egyes bérlők hozzáférhetnek sorok szűrése

Most, hogy az alkalmazás munkamenet állít\_előtt lekérdezése az aktuális TenantId-környezet, az RLS-biztonsági házirend szűrheti, lekérdezések és a egy másik TenantId tartalmazó kizárási sorok.  

Rls-t a Transact-SQL-ben van megvalósítva. Egy felhasználó által definiált függvény meghatározza az adatelérési logikáját, és egy biztonsági házirend van kötve Ez a funkció számos táblát. Ehhez a projekthez:

1. A függvény ellenőrzi, hogy az alkalmazás csatlakozik az adatbázishoz, és, hogy a TenantId tárolja a munkamenet\_környezet megfelel az adott sor TenantId.
    - Az alkalmazás csatlakoztatva van, ahelyett, hogy néhány más SQL-felhasználó.

2. Egy SZŰRŐPREDIKÁTUMNAK egy struktúraoszlopot lehetővé teszi, hogy a sorokat, amelyek megfelelnek a TenantId szűrő a SELECT, UPDATE, áthaladását, és törli a lekérdezéseket.
    - A blokkpredikátumok megakadályozza, hogy a sorokat, amelyek a szűrő beszúrt vagy frissített legyenek sikertelenek.
    - Ha a munkamenet\_környezet nincs beállítva, a függvény NULL értéket ad vissza, és nincsenek sorai nem látható vagy képes lehet beszúrni. 

Ahhoz, hogy minden szegmensre az rls-t, hajtsa végre a következő T-SQL Visual Studio (SSDT), az SSMS vagy az projekt tartalmazza a PowerShell-parancsfájl használatával. Vagy ha használ [Elastic Database-feladatok](sql-database-elastic-jobs-overview.md), automatizálhatja a T-SQL az összes szegmensek végrehajtását.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
    RETURNS TABLE     
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO 
```

> [!TIP]
> Szüksége lehet a predikátum hozzáadása a táblák több száz összetett projekt, amely lehet fárasztó feladat. Nincs segítő tárolt eljárást, amely automatikusan létrehoz egy biztonsági szabályzatot, és hozzáadja a predikátum egy sémát az összes táblához. További információkért tekintse meg a következő blogbejegyzésben található: [sorszintű biztonság alkalmazása az összes táblához - segítő parancsfájl (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).

Most ismét a mintaalkalmazás futtatásakor, a bérlők meg csak a hozzájuk tartozó sorokat. Ezenkívül az alkalmazás nem szúrható be más, mint a jelenleg kapcsolódik a szegmenstérkép-adatbázis bérlőkhöz tartozó sorokat. Az alkalmazás emellett azokat a sorokat, láthatja a TenantId nem lehet frissíteni. Ha az alkalmazás megpróbálja teheti, egy DbUpdateException jelenik meg.

Később hozzáadhat egy új táblát, ha MÓDOSÍTJA a szűrő és a BLOCK predikátumok hozzáadásához kattintson az új táblázat a biztonsági szabályzatot.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Adja hozzá az alapértelmezett korlátozásokban automatikusan feltölti az esetében a beszúrások

Minden tábla automatikusan feltölti a TenantId munkamenet jelenleg tárolt érték az alapértelmezett korlátozásban helyezheti\_sor beszúrásakor környezetben. A következő egy példa. 

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs     
    ADD CONSTRAINT df_TenantId_Blogs      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts     
    ADD CONSTRAINT df_TenantId_Posts      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO 
```

Most az alkalmazást nem kell megadnia a TenantId sor beszúrásakor: 

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);     
        db.SaveChanges();   
    } 
}); 
```

> [!NOTE]
> Ha az alapértelmezett korlátozásokban használ az Entity Framework-projektben, ajánlott, amikor *nem* tartalmazza a TenantId oszlopot az EF-adatmodellben. Ez a javaslat azért, mert az Entity Framework automatikusan lekérdezi az ellátási alapértelmezett értékeket, amelyek felülírják a T-SQL-ben létrehozott alapértelmezett korlátozásokban munkamenet használó\_környezetben.
> A mintaprojekt használandó alapértelmezett korlátozásokban, például kell TenantId eltávolítása DataClasses.cs (és futtatási Add-áttelepítési a Csomagkezelő konzol) és a T-SQL használatával győződjön meg arról, hogy a mező csak az adatbázistáblák létezik. Ezzel a módszerrel EF automatikusan megadhatja az olyan nem megfelelő alapértelmezett értékek adatok beszúrásakor.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Nem kötelező) Engedélyezze a *superuser* el az összes sor

Egyes alkalmazások érdemes létrehozni egy *superuser* férhet hozzá az összes sor. Superuser minden szegmensben lévő összes bérlőre kiterjedő jelentéskészítés sikerült engedélyezéséhez. Vagy superuser sikerült felosztási-egyesítési műveletek végrehajtása a szegmensben bérlői sorok adatbázisok közötti áthelyezése érintő.

Superuser engedélyezéséhez hozzon létre egy új SQL-felhasználó (`superuser` ebben a példában) minden egyes szegmens adatbázisban. Ezután módosítja a biztonsági házirendet, és új predikátum függvény, amely lehetővé teszi, hogy a felhasználó elérheti az összes sor. Egy adott függvény a következő van megadva.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Karbantartás

- **Hozzáadás, új szegmensekre**: Hajtsa végre a T-SQL parancsfájlt engedélyezése bármely új szegmensekre az rls-t, ellenkező esetben ezekben a szegmensekben lévő lekérdezések nem vannak szűrve.
- **Új táblázatok hozzáadása**: Adja hozzá egy SZŰRŐT, és letiltja a predikátum a biztonsági házirendet minden szegmensben, amikor létrejön egy új tábla. Ellenkező esetben az új tábla-lekérdezéseket is nem lesznek szűrve. A Hozzáadás automatizálható a DDL-triggerek használatával leírtak szerint [sorszintű biztonság alkalmazása automatikusan az újonnan létrehozott táblák (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Összegzés

Elastic database-eszközökkel és a sorszintű biztonság együtt használandó horizontális felskálázási adatrétegbeli alkalmazás-mindkét több-bérlős támogatással és egybérlős szegmensek lehet. Több-bérlős szilánkok hatékonyabban adatok tárolására használható. Ez a hatékonyság ejtsd van, ahol a bérlők nagy számú csak néhány sornyi adatot rendelkezik. Egybérlős szegmensek szigorúbb teljesítmény és az elkülönítési követelmények prémium szintű bérlők esetén is támogatja.  További információkért lásd: [sorszintű biztonság referencia][rls].

## <a name="additional-resources"></a>További források

- [Mi az az Azure rugalmas készlet?](sql-database-elastic-pool.md)
- [Horizontális felskálázás az Azure SQL Database segítségével](sql-database-elastic-scale-introduction.md)
- [Tervezési minták az Azure SQL Database-t használó több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md)
- [Hitelesítés a több-bérlős alkalmazásokban az Azure AD és az OpenID Connect segítségével](../guidance/guidance-multitenant-identity-authenticate.md)
- [A Tailspin Surveys-alkalmazás](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Kérdések és a Funkciókérések

Ha kérdése van, lépjen kapcsolatba velünk az a [SQL Database fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). És bármely szolgáltatással kapcsolatos kéréseit, adja hozzá a [SQL Database-visszajelzési fórumon](https://feedback.azure.com/forums/217321-sql-database/).


<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md


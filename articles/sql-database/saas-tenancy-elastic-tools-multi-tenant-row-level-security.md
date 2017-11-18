---
title: "Az RLS és rugalmas adatbáziseszközöket több-bérlős alkalmazásokhoz |} Microsoft Docs"
description: "A sorszintű biztonsággal rendelkező skálázáshoz rugalmas adatbáziseszközöket használhat egy kiválóan méretezhető réteghez alkalmazás létrehozásához."
metakeywords: azure sql database elastic tools multi tenant row level security rls
services: sql-database
documentationcenter: 
manager: jhubbard
author: tmullaney
ms.assetid: e72d3cfe-e9be-4326-b776-9c6d96c0a18e
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: thmullan;torsteng
ms.openlocfilehash: f874800e8647eac6ed94945d79e904df86207b0f
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Több-bérlős alkalmazásokhoz a rugalmas adatbáziseszközöket és sorszintű biztonsággal

[Rugalmas adatbáziseszközöket](sql-database-elastic-scale-get-started.md) és [sorszintű biztonságot (RLS)] [ rls] együttműködik az adatréteg egy több-bérlős alkalmazás az Azure SQL Database skálázás engedélyezése. Együtt ezek a technológiák segítségével, amely rendelkezik egy kiválóan méretezhető adatrétegbeli alkalmazás létrehozásához. Az adatréteg támogatja a több-bérlős szilánkok, és használja **ADO.NET SqlClient** vagy **Entity Framework**. További információkért lásd: [Tervminták több-bérlős SaaS-alkalmazásokhoz az Azure SQL Database](saas-tenancy-app-design-patterns.md).

- **Rugalmas adatbáziseszközöket** a fejlesztők horizontális felskálázás az adatréteg szabványos horizontális eljárásokat, a .NET-kódtárakra és az Azure-szolgáltatások sablon használatával. A szilánkok kezelése a [Elastic Database ügyféloldali kódtár] [ s-d-elastic-database-client-library] segítségével automatizálhatja és egyszerűsítésére általában társított horizontális infrastrukturális műveleteket.
- **A sorszintű biztonsági** lehetővé teszi a fejlesztők számára, hogy biztonságosan tárolva az adatok több bérlő ugyanabban az adatbázisban. RLS biztonsági házirendek azon sorait, amelyek nem tartoznak a bérlő lekérdezése szűrik. A szűrő logika belül az adatbázis központosítása egyszerűbbé teszi a karbantartási, és csökkenti a biztonsági hiba kockázatát. Az összes Ügyfélkód enfore biztonsági hagyatkoznia kockázatos esetben.

Ezek a funkciók együtt használva az alkalmazás adatait tárolhatja az ugyanazt a shard adatbázisban több bérlő számára. Azt a költségek kisebb bérlőnként a bérlők számára az adatbázis megosztása során. Még ugyanazt az alkalmazást is kínálhat a prémium szintű bérlők a saját dedikált single-bérlő shard fizető lehetőséget. Single-bérlők elszigetelésére egyik előnye nagyobb fokú biztonság érdekében teljesítményt garantálja. Single-bérlő adatbázisban nincs nincs más bérlői erőforrások használják.

A cél az, hogy a rugalmas adatbázis ügyféloldali kódtár [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md) API-k minden egyes megadott bérlői automatikusan csatlakozzanak a megfelelő shard adatbázis. Csak egy shard adott TenantId érték az adott bérlő tartalmazza. A tenantid a *horizontális kulcs*. A kapcsolat létrejötte után az adatbázis az RLS biztonsági házirend biztosítja, hogy az adott bérlő is el csak ezen adatokat a sorokat a TenantId.

> [!NOTE]
> A bérlői azonosító több oszlopot tartalmazhatnak. A kényelem ismertető, főleg feltételezzük, hogy egy egy oszlop a TenantId.

![Bloggolás app architektúrája][1]

## <a name="download-the-sample-project"></a>A minta-projekt letöltése

### <a name="prerequisites"></a>Előfeltételek

- Használja a Visual Studio (2012 vagy újabb) 
- Hozzon létre három Azure SQL-adatbázisok 
- A minta-projekt letöltése: [az Azure SQL - több-Bérlős szilánkok rugalmas adatbázis-eszközök](http://go.microsoft.com/?linkid=9888163)
  - Töltse ki az információkat az adatbázisokhoz elején **Program.cs** 

Ez a projekt bővíti a leírt egy [rugalmas adatbázis-eszközök az Azure SQL - entitás Keretrendszeri integrációját](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) a több-bérlős shard adatbázisok támogatásának hozzáadásával. A projekt létrehozásához blogok és hozzászólásokat egyszerű konzolalkalmazásként alkot. A projekt négy bérlők, mellett két, több-bérlős shard adatbázis is. Ez a konfiguráció a fenti ábrán látható. 

Hozza létre, és futtassa az alkalmazást. Ehhez a futtató a rugalmas adatbázis-eszközt a shard térkép manager betöltéséhez, és a következő teszteket hajt végre: 

1. Az Entity Framework és a LINQ, hozzon létre egy új blog, és akkor jelenít meg az egyes bérlők számára a blogok
2. A bérlő a blogok ADO.NET SqlClient használ, megjelenítése
3. Győződjön meg arról, hogy hiba történt a megfelelő bérlő blog beszúrásakor  

Figyelje meg, hogy RLS még nincs engedélyezve a shard adatbázisokban, mert e vizsgálatok során probléma: bérlők láthatók, amelyek nem tartoznak hozzájuk blogok, és az alkalmazás nem akadályozza a megfelelő bérlői blog beszúrni. Ez a cikk fennmaradó ismerteti, hogyan lehet a problémák megoldásához úgy RLS a bérlők elkülönítésére. Két lépésben történik: 

1. **Alkalmazás réteg**: módosítsa az alkalmazás kódjában, hogy mindig beállította a munkamenet a jelenlegi TenantId\_környezeti kapcsolat megnyitása után. A minta-projekt már beállítja a TenantId ily módon. 
2. **Adatszint**: RLS biztonsági szabályzat létrehozása minden shard adatbázis munkamenet tárolja a TenantId alapján sorok szűrése\_környezetben. Hozzon létre egy házirendet a shard adatbázisok mindegyike esetében, ellenkező esetben a több-bérlős szilánkok sorok nem van szűrve. 

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. Alkalmazás réteg: a munkamenet Set TenantId\_környezetben

Először csatlakozik egy shard adatbázis az adatok függő útválasztási API-kat a rugalmas adatbázis ügyféloldali kódtár segítségével. Az alkalmazás továbbra is kell kérje meg az adatbázis melyik TenantId használ a kapcsolat. A TenantId be van állítva a Sorszintű biztonsági házirendet melyik sort kell kiszűri, hogy más bérlők tartoznak. Az aktuális TenantId tárolja a [munkamenet\_környezetben](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) a kapcsolat.

Ahelyett, hogy a munkamenet\_környezet használatára [környezetben\_információ](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). De a munkamenet\_környezet jobb megoldás. MUNKAMENET\_környezetben való használatának egyszerűbbé, hogy NULL értéket ad vissza alapértelmezés szerint, és támogatja kulcs-érték párok.

### <a name="entity-framework"></a>Entity Framework

Az Entity Framework használó alkalmazások, a legegyszerűbb módszer is, hogy a munkamenet\_KÖRNYEZETÉBEN található ismertetett ElasticScaleContext felülbírálás [adatok függő útválasztás EF DbContext használata](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Hozzon létre és hajtható végre, amely beállítja a TenantId a munkamenet egy SqlCommand\_a shardingKey a kapcsolathoz megadott környezet. Térjen vissza a kapcsolat által felügyelt adatok függő útválasztási keresztül. Ezzel a módszerrel Ön csak kódot kell írnia egyszer beállítása a munkamenet\_környezetben. 

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

Most már a munkamenet\_környezet automatikusan azzal van megadva, a megadott TenantId amikor ElasticScaleContext meghívták: 

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

### <a name="adonet-sqlclient"></a>Az ADO.NET SqlClient

Az ADO.NET SqlClient használó alkalmazások körül metódus ShardMap.OpenConnectionForKey burkoló függvény létrehozása. A TenantId automatikusan a MUNKAMENETBEN beállított burkoló rendelkezik\_kapcsolatot visszatérése előtt az aktuális TenantId környezet. Annak érdekében, hogy a munkamenet\_környezetben mindig be van-e állítva, csak meg kell nyitnia a burkoló funkcióval kapcsolatok.

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

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Adatszint: sorszintű biztonsági szabályzat létrehozása

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Az egyes bérlői hozzáférhessenek sorok szűrése-biztonsági szabályzat létrehozása

Most, hogy az alkalmazás állít munkamenet\_előtt lekérdezése az aktuális TenantId a környezetben, az RLS-biztonsági házirend végezhet, lekérdezések és kizárási a sorokat, egy másik TenantId.  

RLS Transact-SQL valósul meg. A felhasználó által definiált függvény határozza meg az access programot, és egy biztonsági házirend Ez a funkció kötődik korlátlan számú táblát. Ebben a projektben:

1. A függvény ellenőrzi, hogy az alkalmazás csatlakoztatva van az adatbázis, és hogy a TenantId tárolja a munkamenet\_környezet megfelel az adott sor a TenantId.
    - Az alkalmazás ahelyett, hogy néhány más SQL-felhasználó csatlakozik.

2. A szűrőpredikátum lehetővé teszi, hogy azon sorait, amelyek megfelelnek a TenantId szűrő az áthaladás, válassza ki, a frissítés, és törli a lekérdezéseket.
    - A blokkpredikátumok megakadályozza, hogy a sorok, amelyekre a szűrő nem beszúrt vagy frissített sikertelen.
    - Ha a munkamenet\_sorkörnyezete nem lett beállítva, a függvény NULL értéket ad vissza, és sorok látható vagy tud beszúrni. 

Ahhoz, hogy az összes szilánkok RLS, hajtsa végre a következő T-SQL Visual Studio (SSDT), SSMS, vagy a projekt tartalmazza a PowerShell-parancsfájl használatával. Vagy ha használ [rugalmas adatbázis-feladatok](sql-database-elastic-jobs-overview.md), automatizálhatja azokat az összes szilánkok a T-SQL végrehajtása.

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
> Szükség lehet a predikátum hozzáadása a táblák több száz összetett projekt, amely lehet fárasztó. Nincs olyan segítő tárolt eljárást, amely automatikusan hoz létre egy olyan biztonsági házirendet, és hozzáadja a predikátum egy sémában összes táblán. További információkért tekintse meg a következő blogbejegyzésben található [sorszintű biztonság alkalmazása az összes táblához - segítő parancsfájl (blogbejegyzés)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).

Most futtassa újra a mintaalkalmazást, ha a bérlők csak a hozzájuk tartozó sorok tekintse meg. Ezenkívül az alkalmazás nem szúrható be sorok, amelyekre a bérlők számára más, mint a jelenleg kapcsolódik a shard adatbázishoz tartozik. Az alkalmazás is, az egyetlen sort sem az felismerje a TenantId nem frissíthető. Ha az alkalmazás megpróbálja módszerekkel, egy DbUpdateException következik be.

Ha később ad hozzá egy új tábla, MÓDOSÍTSA a biztonsági házirend és a BLOCK predikátumok hozzáadásához kattintson az új táblázat.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Adja hozzá automatikusan feltölti a TenantId beszúrása az alapértelmezett korlátozásokban

Minden tábla automatikusan feltölti a TenantId munkamenet jelenleg tárolt értékét az alapértelmezett megkötés helyezhet el\_környezetben, amikor sort beszúrni. A következő egy példa. 

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

Most az alkalmazást nem kell megadnia a TenantId sorok beszúráskor: 

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
> Ha használja az Entity Framework projektek alapértelmezett korlátozásokban, javasoljuk, hogy *nem* tartalmazza a TenantId az oszlopot a EF adatmodell. Ez a javaslat azért, mert az Entity Framework lekérdezi automatikusan adjon meg alapértelmezett értékek, amelyek felülírják a T-SQL-ben létrehozott alapértelmezett korlátozásokban munkamenet használó\_környezetben.
> A minta projekt használandó alapértelmezett korlátozásokban, például meg kell DataClasses.cs (és futtatási Add-áttelepítés a Csomagkezelő konzol) eltávolítása a TenantId és T-SQL használatával győződjön meg arról, hogy a mező csak megtalálható a táblák. Ezzel a módszerrel EF automatikusan helytelen alapértelmezett értéket ad adatok beszúráskor.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Választható) Engedélyezze a *felügyelő* minden sor eléréséhez

Egyes alkalmazások érdemes létrehozni egy *felügyelő* ki érje el az összes sort. Felügyelő létrehozása lehetővé teszi egyetlen bérlő számára az összes szilánkok között reporting. Vagy felügyelőt hajthat végre vegyes egyesítéses műveleteket a szilánkok, például az adatbázisok közötti áthelyezése a bérlő sorokat.

Ahhoz, hogy a rendszeradminisztrátor, hozzon létre egy új SQL-felhasználó (`superuser` ebben a példában) minden shard adatbázisban. Majd módosítsa a biztonsági házirendet, és egy új predikátum függvény, amely lehetővé teszi a felhasználó elérheti az összes sort. Ilyen függvényt a következő elbírálása.

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

- **Új szilánkok hozzáadása**: a T-SQL parancsfájlt ahhoz, hogy minden új szilánkok az RLS, ellenkező esetben ezek a szilánkok lekérdezései nem van szűrve.
- **Új táblák hozzáadásáról**: és a BLOCK predicate hozzáadása a biztonsági házirendet a összes szilánkok, amikor egy új tábla létrehozása. Ellenkező esetben az új táblázat lekérdezései vannak nem lesznek szűrve. A hozzáadást a DDL-eseményindítót segítségével automatizálható, a [sorszintű biztonság alkalmazása automatikusan az újonnan létrehozott táblához (blogbejegyzés)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Összefoglalás

Rugalmas adatbáziseszközöket és sorszintű biztonsággal együtt használt, egy alkalmazás adatszintjének mindkét több-bérlős támogatással bővíteni és single-bérlő szilánkok lehet. Több-bérlős szilánkok hatékonyabban adatok tárolására használható. Ez a hatékonyság hangsúlyozottan van, ahol a bérlők nagy számú rendelkezik csak néhány sornyi adatot. Single-bérlő szilánkok támogathatja a prémium szintű bérlők, amely szigorúbb teljesítmény és elszigetelési követelményei vannak.  További információkért lásd: [sorszintű biztonság hivatkozás][rls].

## <a name="additional-resources"></a>További források

- [Mi az Azure rugalmas készletek?](sql-database-elastic-pool.md)
- [Horizontális felskálázás az Azure SQL Database segítségével](sql-database-elastic-scale-introduction.md)
- [Tervezési minták az Azure SQL Database-t használó több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md)
- [Hitelesítés a több-bérlős alkalmazásokban az Azure AD és az OpenID Connect segítségével](../guidance/guidance-multitenant-identity-authenticate.md)
- [A Tailspin Surveys-alkalmazás](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Kérdések és funkciókra vonatkozó kérések

A kérdésekhez, lépjen velünk kapcsolatba az a [SQL-adatbázis fórum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). És bármely szolgáltatás kérelmek adja hozzá a [SQL adatbázis-visszajelzési fórumon](https://feedback.azure.com/forums/217321-sql-database/).


<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md


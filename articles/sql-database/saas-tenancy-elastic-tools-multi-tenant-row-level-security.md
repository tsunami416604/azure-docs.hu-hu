---
title: "Több-bérlős alkalmazásokhoz a rugalmas adatbáziseszközöket és sorszintű biztonsággal"
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
ms.date: 05/27/2016
ms.author: thmullan;torsteng
ms.openlocfilehash: b85673c7f0e8176a1700e55fe2ce33b7c191828a
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Több-bérlős alkalmazásokhoz a rugalmas adatbáziseszközöket és sorszintű biztonsággal
[Rugalmas adatbáziseszközöket](sql-database-elastic-scale-get-started.md) és [sorszintű biztonságot (RLS)](https://msdn.microsoft.com/library/dn765131) egy hatékony rugalmasan és hatékony méretezést az adatréteg egy több-bérlős alkalmazás az Azure SQL Database képességeket kínál. További információkért lásd: [Tervminták több-bérlős SaaS-alkalmazásokhoz az Azure SQL Database](saas-tenancy-app-design-patterns.md) további információt. 

Ez a cikk bemutatja, hogyan használandó ezek a technológiák együttesen egy kiválóan méretezhető réteghez, amely támogatja a több-bérlős szilánkok használó alkalmazás létrehozásához **ADO.NET SqlClient** és/vagy **Entity Framework**.  

* **Rugalmas adatbáziseszközöket** a fejlesztők a adatok réteg az alkalmazások szabványos horizontális eljárások használatával a .NET-kódtárakra és az Azure-szolgáltatások sablon használatával. A rugalmas adatbázis ügyféloldali kódtár használata szilánkok kezelése segítségével automatizálhatja és egyszerűsítésére általában társított horizontális infrastrukturális műveleteket. 
* **A sorszintű biztonsági** lehetővé teszi a fejlesztők a biztonsági házirendeket használó kiszűrését a sorok, amelyekre a lekérdezés végrehajtása a bérlő nem tartoznak ugyanazon adatbázis több bérlő adatok tárolására. Központi hozzáférési logika az RLS belül az adatbázis helyett az alkalmazásban leegyszerűsíti a karbantartás, és csökkenti a hiba kockázatát, mint az alkalmazás csomagazonosítóját kódbázis növekszik. 

Együtt használja ezeket a funkciókat, egy alkalmazás is kihasználhatja le költség megtakarítások és a hatékonyság növekedését adatokat tárol a ugyanazt a shard adatbázisban több bérlő számára. Egy időben az alkalmazás még a rugalmasságot tesz elkülönített, egyetlen-bérlő szilánkok "prémium" bérlők számára, akik szigorúbb teljesítmény garanciák igényel, mivel a több-bérlős szilánkok nem garantálják egyenlő erőforrás terjesztési bérlők között.  

Rövid, a rugalmas adatbázis-e az ügyféloldali kódtár [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md) API-k bérlők automatikusan csatlakoznak a megfelelő shard adatbázis tartalmazó a horizontális kulcsával (általában a "TenantId"). A csatlakozás után a az RLS-biztonsági házirend, az adatbázis biztosítja, hogy a bérlők csak hozzáférhet a TenantId tartalmazó sorok. Feltételezzük, hogy minden olyan táblát tartalmaz-e a TenantId oszlopot annak jelzésére, hogy mely sorai minden bérlőhöz tartozik. 

![Bloggolás app architektúrája][1]

## <a name="download-the-sample-project"></a>A minta-projekt letöltése
### <a name="prerequisites"></a>Előfeltételek
* Használja a Visual Studio (2012 vagy újabb) 
* Hozzon létre három Azure SQL-adatbázisok 
* A minta-projekt letöltése: [az Azure SQL - több-Bérlős szilánkok rugalmas adatbázis-eszközök](http://go.microsoft.com/?linkid=9888163)
  * Töltse ki az információkat az adatbázisokhoz elején **Program.cs** 

Ez a projekt bővíti a leírt egy [rugalmas adatbázis-eszközök az Azure SQL - entitás Keretrendszeri integrációját](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) a több-bérlős shard adatbázisok támogatásának hozzáadásával. Blogok és hozzászólásokat, négy bérlők és a fenti ábrán szemléltetett két több-bérlős shard adatbázis való létrehozásának egyszerű konzolalkalmazásként-buildekről nyújtanak. 

Hozza létre, és futtassa az alkalmazást. Ez a rugalmas adatbázis-eszközt a shard térkép manager betöltéséhez, és a következő tesztek futtatása: 

1. Az Entity Framework és a LINQ, hozzon létre egy új blog, és akkor jelenít meg az egyes bérlők számára a blogok
2. A bérlő a blogok ADO.NET SqlClient használ, megjelenítése
3. Győződjön meg arról, hogy hiba történt a megfelelő bérlő blog beszúrásakor  

Figyelje meg, hogy RLS még nincs engedélyezve a shard adatbázisokban, mert e vizsgálatok során probléma: bérlők láthatók, amelyek nem tartoznak hozzájuk blogok, és az alkalmazás nem akadályozza a megfelelő bérlői blog beszúrni. Ez a cikk fennmaradó ismerteti, hogyan lehet a problémák megoldásához úgy RLS a bérlők elkülönítésére. Két lépésben történik: 

1. **Alkalmazás réteg**: mindig be az aktuális TenantId a SESSION_CONTEXT kapcsolat megnyitása után az alkalmazás kódjának megváltoztatására. A minta-projekt már megtörtént a. 
2. **Adatszint**: RLS biztonsági szabályzat létrehozása minden shard adatbázis sorok szűrése a TenantId SESSION_CONTEXT tárolt alapján. Az egyes a shard adatbázist, a több-bérlős szilánkok nem szűri a szolgáltatásba, másként sorok ehhez szüksége. 

## <a name="step-1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. lépés) alkalmazás réteg: állítsa be a TenantId a SESSION_CONTEXT a
A rugalmas adatbázis ügyféloldali kódtár adatok függő útválasztási API-k használatával shard-adatbázishoz szeretne csatlakozni, miután az alkalmazás továbbra is kell kérje meg az adatbázis, mely a TenantId van kapcsolatot használó, hogy az RLS-biztonsági házirend kiszűrheti tartozó sorok más bérlők. A javasolt ezt az információt továbbítja módja az adott kapcsolathoz az aktuális TenantId tárolja a [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806.aspx). (Megjegyzés: Másik megoldásként használhat [CONTEXT_INFO](https://msdn.microsoft.com/library/ms180125.aspx), de SESSION_CONTEXT jobb megoldás könnyebben használható, mert alapértelmezés szerint NULL értéket ad vissza, és támogatja a kulcs-érték párok.)

### <a name="entity-framework"></a>Entity Framework
Az Entity Framework használó alkalmazások, a legegyszerűbb módszer is, hogy a leírt ElasticScaleContext felülbírálás belül SESSION_CONTEXT [adatok függő útválasztás EF DbContext használata](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Vissza a kapcsolat által felügyelt adatok függő útválasztási keresztül, előtt hozzon létre, és hajtsa végre a "TenantId" megadó SqlCommand számára az adott kapcsolathoz megadott shardingKey SESSION_CONTEXT. Ezzel a módszerrel Ön csak kódot kell írnia egyszer a SESSION_CONTEXT beállításához. 

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data-dependent routing. This call opens a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call fails for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
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

Most már a SESSION_CONTEXT automatikusan azzal van megadva, a megadott TenantId amikor ElasticScaleContext meghívták: 

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
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
Az ADO.NET SqlClient használó alkalmazások az ajánlott módszer, hogy hozzon létre egy burkoló függvényt, amely automatikusan beállítja a TenantId"a SESSION_CONTEXT, hogy helyes-e a TenantId kapcsolatot visszatérése előtt ShardMap.OpenConnectionForKey() körül. Győződjön meg arról, hogy SESSION_CONTEXT mindig van beállítva, hogy csak a burkoló funkcióval kapcsolatok nyissa meg.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that SESSION_CONTEXT is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
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

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs are listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="step-2-data-tier-create-row-level-security-policy"></a>2. lépés) az adatok réteg: sorszintű biztonsági szabályzat létrehozása
### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Az egyes bérlői hozzáférhessenek sorok szűrése-biztonsági szabályzat létrehozása
Most, hogy az alkalmazás és az aktuális TenantId SESSION_CONTEXT állít előtt kérdez le, az RLS-biztonsági házirend lekérdezések szűréséhez, és egy másik TenantId tartalmazó sorok kihagyása.  

RLS megvalósítása a T-SQL: egy felhasználó által definiált függvény határozza meg az access programot, és egy biztonsági házirend Ez a funkció kötődik korlátlan számú táblát. Ebben a projektben a függvény ellenőrzi, hogy az alkalmazás (ahelyett, hogy néhány más SQL-felhasználó) az adatbázishoz kapcsolódik, és, hogy a "TenantId" tárolja a SESSION_CONTEXT megegyezik az adott sor a TenantId. A szűrőpredikátum lehetővé teszi, hogy a sorokat, amelyek megfelelnek az áthaladás, a szűrő SELECT, UPDATE és DELETE lekérdezések; ezek a feltételek és egy blokkpredikátumok megakadályozza, hogy ezek a feltételek nem beszúrt vagy frissített megsértő sorok. Ha SESSION_CONTEXT nincs megadva, az értéket ad vissza, NULL értékű, és a sor nem látható vagy szúrható be tudja. 

Ahhoz, hogy az RLS, végrehajtani a következő T-SQL Visual Studio (SSDT), a szolgáltatáshoz az SSMS vagy a PowerShell-parancsfájlt a projekt tartalmazza az összes szilánkok (vagy használatakor [rugalmas adatbázis-feladatok](sql-database-elastic-jobs-overview.md), ennek végrehajtása automatizálásához használhatja Az összes szilánkok T-SQL): 

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
    RETURNS TABLE     
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult          
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [!TIP]
> Hozzá kell adnia a predikátum a táblák több száz összetettebb projektek használhatja a segítő tárolt eljárás, amely automatikusan hoz létre egy biztonsági házirend predikátum ad hozzá a séma összes tábla. További információkért lásd: [sorszintű biztonság alkalmazása az összes táblához - segítő parancsfájl (blogbejegyzés)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).  
> 
> 

Most futtassa újra a mintaalkalmazást, ha a bérlők csak a hozzájuk tartozó sorok tekintse meg. Ezenkívül az alkalmazás nem szúrható be azon sorait, amelyek csak a jelenleg kapcsolódik a shard adatbázis, és nem tudja frissíteni szeretné, hogy egy másik TenantId látható sorok bérlők tartozik. Ha az alkalmazás megpróbálja módszerekkel, egy DbUpdateException következik be.

Ha később a naplóbejegyzések új táblában, a biztonsági házirend módosítására és szűrő hozzáadása, és az új táblán predikátumok letiltása: 

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Adja hozzá automatikusan feltölti a TenantId beszúrása az alapértelmezett korlátozásokban
Minden tábla automatikusan feltölti a TenantId jelenleg tárolt SESSION_CONTEXT sorok beszúráskor értékét az alapértelmezett megkötés helyezhet el. Példa: 

```
-- Create default constraints to auto-populate TenantId with the value of SESSION_CONTEXT for inserts 
ALTER TABLE Blogs     
    ADD CONSTRAINT df_TenantId_Blogs      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO

ALTER TABLE Posts     
    ADD CONSTRAINT df_TenantId_Posts      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO 
```

Most az alkalmazást nem kell megadnia a TenantId sorok beszúráskor: 

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
        db.Blogs.Add(blog);     
        db.SaveChanges();   
    } 
}); 
```

> [!NOTE]
> Ha az Entity Framework projektek használja alapértelmezett korlátozásokban, javasolt, hogy nem adja meg a TenantId oszlop az EF adatmodell. Ennek oka az Entity Framework lekérdezések automatikusan, amelyek felülírják a T-SQL-ben létrehozott alapértelmezett korlátozásokban SESSION_CONTEXT használó alapértelmezett értéket ad meg. A minta projekt használandó alapértelmezett korlátozásokban, például meg kell DataClasses.cs (és futtatási Add-áttelepítés a Csomagkezelő konzol) eltávolítása a TenantId és T-SQL használatával győződjön meg arról, hogy a mező csak megtalálható a táblák. Ezzel a módszerrel EF automatikusan helytelen alapértelmezett értéket ad adatok beszúráskor. 
> 
> 

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Választható) Minden sor elérésére "felügyelőt" engedélyezése
Egyes alkalmazások érdemes létrehozni egy "felügyelő" ki férhet hozzá az összes sort, például, ahhoz, hogy egyetlen bérlő számára az összes szilánkok között reporting, vagy a szilánkok, például az adatbázisok közötti áthelyezése a bérlő sorok felosztása/egyesítés műveletek végrehajtásához. Ennek engedélyezéséhez, minden egyes shard adatbázis érdemes létrehozni egy új SQL-felhasználó (ebben a példában "felügyelő" jelöli). Ezután módosítsa a biztonsági házirendet, és egy új predikátum függvény, amely lehetővé teszi a felhasználó elérheti az összes sort:

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### <a name="maintenance"></a>Karbantartás
* **Új szilánkok hozzáadása**: a T-SQL parancsfájlt ahhoz, hogy minden új szilánkok az RLS, ellenkező esetben ezek a szilánkok lekérdezései nem van szűrve.
* **Új táblák hozzáadásáról**: hozzáadása az új táblázat egyéb lekérdezései, a biztonsági házirendet a összes szilánkok, amikor egy új tábla létrehozása és a blokk predikátum nem szűri. Ez automatizálható a DDL-eseményindítót használ a [sorszintű biztonság alkalmazása automatikusan az újonnan létrehozott táblához (blogbejegyzés)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Összefoglalás
Rugalmas adatbáziseszközöket és sorszintű biztonsággal együtt használt, egy alkalmazás adatszintjének mindkét több-bérlős támogatással bővíteni és single-bérlő szilánkok lehet. Több-bérlős szilánkok segítségével adattárolás hatékonyabbá (különösen olyan esetekben, ahol nagyszámú bérlők csak néhány sornyi adatot), egyetlen-bérlő közben szilánkok segítségével szigorúbb teljesítmény és elkülönítési prémium bérlők támogatása követelmények.  További információkért lásd: [sorszintű biztonság hivatkozás](https://msdn.microsoft.com/library/dn765131). 

## <a name="additional-resources"></a>További források
* [Mi az Azure rugalmas készletek?](sql-database-elastic-pool.md)
* [Horizontális felskálázás az Azure SQL Database segítségével](sql-database-elastic-scale-introduction.md)
* [Tervezési minták az Azure SQL Database-t használó több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md)
* [Hitelesítés a több-bérlős alkalmazásokban az Azure AD és az OpenID Connect segítségével](../guidance/guidance-multitenant-identity-authenticate.md)
* [A Tailspin Surveys-alkalmazás](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Kérdések és funkciókra vonatkozó kérések
A kérdésekhez kapcsolatba velünk a a [SQL-adatbázis fórum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) , a szolgáltatás kéréseket, hozzáadhatja őket a a [SQL adatbázis-visszajelzési fórumon](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->



---
title: Több-bérlős alkalmazások RLS-sel és rugalmas adatbázis-eszközökkel
description: A sorszintű biztonsággal rendelkező rugalmas adatbázis-eszközökkel nagymértékben méretezhető adatréteggel rendelkező alkalmazást hozhat létre.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 4cf260620d4e907fdb9190a052155fa22f1c7985
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398331"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Több-bérlős alkalmazások rugalmas adatbázis-eszközökkel és sorszintű biztonsággal

[Rugalmas adatbázis-eszközök](sql-database-elastic-scale-get-started.md) és [a sorszintű biztonság (RLS)][rls] együttműködnek, hogy lehetővé tegye a több-bérlős alkalmazás adatrétegének méretezése az Azure SQL Database használatával. Ezek a technológiák együttesen egy jól méretezhető adatréteggel rendelkező alkalmazást hozhat létre. Az adatréteg támogatja a több-bérlős szegmensek, és **használja ADO.NET SqlClient** vagy **entitás keretrendszer.** További információ: [Tervezési minták több-bérlős SaaS-alkalmazások az Azure SQL Database.](saas-tenancy-app-design-patterns.md)

- **Rugalmas adatbázis-eszközök** lehetővé teszik a fejlesztők számára, hogy a .NET-kódtárak és az Azure-szolgáltatássablonok használatával szabványos horizontális eljárásokkal horizontálissá tegyék az adatréteget. A szegmensek kezelése a [rugalmas adatbázis-ügyfélkódtár][s-d-elastic-database-client-library] használatával segít automatizálni és egyszerűsíteni számos, általában a szilánkok társított infrastrukturális feladatok.
- **A sorszintű biztonság** lehetővé teszi a fejlesztők számára, hogy biztonságosan tárolják az adatokat több bérlő számára ugyanabban az adatbázisban. Az RLS biztonsági házirendjei kiszűrik azokat a sorokat, amelyek nem tartoznak a lekérdezést végrehajtó bérlőhöz. A szűrőlogika központosítása az adatbázison belül leegyszerűsíti a karbantartást, és csökkenti a biztonsági hibák kockázatát. Az alternatív a támaszkodva az összes ügyfélkódot a biztonság érvényesítéséhez kockázatos.

Ezeket a funkciókat együtt használva egy alkalmazás több bérlő adatait tárolhatja ugyanabban a szegmens-adatbázisban. Bérlőnként kevesebbe kerül, ha a bérlők megosztják az adatbázist. Mégis ugyanazt az alkalmazást is kínál a prémium bérlők a lehetőséget, hogy a saját dedikált egybérlős shard fizetni. Az egybérlős elkülönítés egyik előnye a határozottabb teljesítménygaranciák. Egy egybérlős adatbázisban nincs más bérlő, amely verseng az erőforrásokért.

A cél az, hogy a rugalmas [adatbázis-ügyfélkódtár adatfüggő útválasztási API-k](sql-database-elastic-scale-data-dependent-routing.md) automatikusan csatlakoztassa az egyes bérlők a megfelelő shard adatbázis. Csak egy szegmens tartalmaz adott TenantId értéket az adott bérlőhöz. A TenantId a *skálázási kulcs.* A kapcsolat létrejötte után az RLS biztonsági házirend az adatbázisban biztosítja, hogy az adott bérlő csak azokat az adatsorokat, amelyek tartalmazzák a TenantId.

> [!NOTE]
> A bérlői azonosító több oszlopból is állhat. Az egyszerűség kedvéért ez a vita, mi informálisan vállalja egy egyoszlopos TenantId.

![Blogoló alkalmazás architektúrája][1]

## <a name="download-the-sample-project"></a>A mintaprojekt letöltése

### <a name="prerequisites"></a>Előfeltételek

- A Visual Studio használata (2012-es vagy újabb verzió)
- Három Azure SQL-adatbázis létrehozása
- Mintaprojekt letöltése: [Rugalmas DB-eszközök az Azure SQL-hez – több-bérlős szegmensek](https://go.microsoft.com/?linkid=9888163)
  - Töltse ki az adatbázisok adatait a **Program.cs** elején

Ez a projekt kiterjeszti az [Azure SQL rugalmas DB-eszközeiben](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) leírtat a több-bérlős shard-adatbázisok támogatásának hozzáadásával. A projekt egy egyszerű konzolalkalmazást hoz létre blogok és bejegyzések létrehozásához. A projekt négy bérlőt, valamint két több-bérlős shard-adatbázist tartalmaz. Ezt a konfigurációt az előző ábra szemlélteti.

Hozza létre és futtassa az alkalmazást. Ez a futtatási rendszera rugalmas adatbázis-eszközök shard map manager, és végrehajtja a következő teszteket:

1. Az Entitáskeret rendszerkeret és a LINQ használatával hozzon létre egy új blogot, majd jelenítse meg az egyes bérlők összes blogját
2. Az SqlClient ADO.NET használatával jelenítse meg a bérlő összes blogját
3. Próbáljon meg beszúrni egy blogot a rossz bérlő, hogy ellenőrizze, hogy a hiba történt

Figyelje meg, hogy mivel az RLS még nincs engedélyezve a shard adatbázisokban, ezek a tesztek mindegyike problémát tár fel: a bérlők láthatják a nem hozzájuk tartozó blogokat, és az alkalmazás nem akadályozza meg a blog beillesztését a rossz bérlőszámára. A cikk további témakör ismerteti, hogyan oldhatja meg ezeket a problémákat a bérlők elkülönítésének kényszerítésével rls. Két lépésből áll:

1. **Alkalmazásszint**: Módosítsa az alkalmazáskódot úgy, hogy a\_kapcsolat megnyitása után mindig állítsa be az aktuális TenantId azonosítót a SESSION CONTEXT-ben. A mintaprojekt már így állítja be a TenantId-et.
2. **Adatréteg:** Hozzon létre egy RLS biztonsági házirendet minden szegmensadatbázisban a\_munkamenet-környezetben tárolt TenantId alapján tárolt sorok szűréséhez. Hozzon létre egy szabályzatot az egyes szegmensadatbázisok, különben a több-bérlős szegmensek sorai nem szűrhető.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. Alkalmazásszint: TenantId beállítása\_a SESSION CONTEXT környezetben

Először a rugalmas adatbázis-ügyfélkódtár adatfüggő útválasztási API-jainak használatával csatlakozik egy szegmensadatbázishoz. Az alkalmazásnak továbbra is meg kell mondania az adatbázisnak, hogy melyik TenantId használja a kapcsolatot. A TenantId közli az RLS biztonsági házirendet, hogy mely sorokat kell kiszűrni más bérlőkhöz tartozóként. Az aktuális TenantId tárolása a kapcsolat [session\_környezetében.](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql)

A SESSION\_CONTEXT egy másik alternatívája a [CONTEXT\_INFO](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql)használata. De\_session context egy jobb választás. A\_SESSION CONTEXT használata könnyebb, alapértelmezés szerint NULL értéket ad vissza, és támogatja a kulcs-érték párokat.

### <a name="entity-framework"></a>Entity Framework

Az Entitáskeretrendszert használó alkalmazások esetében a legegyszerűbb\_módszer a SESSION CONTEXT beállítása az EF DbContext használatával az [adatfüggő útválasztásban](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext)leírt ElasticScaleContext felülírásban. Hozzon létre és hajtson végre egy\_SqlCommand, amely beállítja tenantId a SESSION context a kapcsolathoz megadott shardingkey. Ezután adja vissza az adatfüggő útválasztáson keresztül felügyelt kapcsolatot. Így csak egyszer kell kódot írnia a\_SESSION CONTEXT beállításához.

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

Most a\_SESSION CONTEXT automatikusan be van állítva a megadott TenantId-del, amikor elasticScaleContext meghívásra kerül:

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

Az SqlClient ADO.NET használó alkalmazások esetén hozzon létre egy burkolófüggvényt a ShardMap.OpenConnectionForKey metódus körül. A kapcsolat visszaadása előtt a\_burkoló automatikusan állítsa be a TenantId-et a SESSION CONTEXT-ben az aktuális TenantId-re. Annak érdekében, hogy a SESSION\_CONTEXT mindig be legyen állítva, csak ezzel a burkoló függvénnyel nyisson meg kapcsolatokat.

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

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Adatszint: Sorszintű biztonsági házirend létrehozása

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Hozzon létre egy biztonsági házirendet az egyes bérlők által elérhető sorok szűréséhez

Most, hogy az\_alkalmazás a lekérdezés előtt a SESSION CONTEXT-t az aktuális TenantId azonosítóval állítja be, az RLS biztonsági házirendje szűrheti a lekérdezéseket, és kizárhatja a más TenantId azonosítóval rendelkező sorokat.

Az RLS a Transact-SQL-ben van megvalósítva. A felhasználó által definiált függvény határozza meg a hozzáférési logikát, és egy biztonsági házirend tetszőleges számú táblához köti ezt a függvényt. Ehhez a projekthez:

1. A függvény ellenőrzi, hogy az alkalmazás csatlakozik-e az adatbázishoz,\_és hogy a SESSION CONTEXT-ben tárolt TenantId megegyezik-e egy adott sor TenantId azonosítójával.
    - Az alkalmazás csatlakoztatva van, nem pedig egy másik SQL-felhasználó.

2. A FILTER predikátum lehetővé teszi, hogy a TenantId szűrőnek megfelelő sorok áthaladjanak a SELECT, UPDATE és DELETE lekérdezéseken.
    - A BLOCK predikátum megakadályozza, hogy a szűrő torkaként elzáró dúsvagy updated sorokat tartalmazó sorokat inserted vagy updated.
    - Ha\_a SESSION CONTEXT nincs beállítva, a függvény NULL értéket ad vissza, és nem látható vagy beszúrható sorok.

Az RLS engedélyezéséhez az összes szegmensek, hajtsa végre a következő T-SQL segítségével vagy visual studio (SSDT), SSMS vagy a PowerShell-parancsfájl a projektben. Vagy rugalmas [adatbázis-feladatok](elastic-jobs-overview.md)használata esetén automatizálhatja a T-SQL végrehajtását az összes szegmensen.

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
> Egy összetett projektben szükség lehet az állítmány hozzáadására több száz asztalhoz, ami fárasztó lehet. Van egy segítő tárolt eljárás, amely automatikusan létrehoz egy biztonsági házirendet, és predikátumot ad a séma összes táblájához. További információt a [Sorszintű biztonság alkalmazása az összes táblára című](https://techcommunity.microsoft.com/t5/sql-server/apply-row-level-security-to-all-tables-helper-script/ba-p/384360)blogbejegyzésben talál .

Most, ha újra futtatja a mintaalkalmazást, a bérlők csak a hozzájuk tartozó sorokat látják. Emellett az alkalmazás nem szúrhat be olyan sorokat, amelyek nem a szegmensadatbázishoz jelenleg csatlakoztatott bérlőkhöz tartoznak. Emellett az alkalmazás nem tudja frissíteni a TenantId bármely sorban látható. Ha az alkalmazás bármelyiket megpróbálja megtenni, a rendszer egy DbUpdateException-et emel ki.

Ha később új táblát ad hozzá, módosítsa a biztonsági házirendet, hogy szűrőt és BLOKK predikátumokat adjon az új táblához.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Alapértelmezett megkötések hozzáadása az INSERT-k bérlőazonosítójának automatikus feltöltéséhez

Az egyes táblázatokra alapértelmezett megkötést helyezhet, amely automatikusan feltölti a TenantId azonosítót a SESSION\_CONTEXT-ben jelenleg tárolt értékkel a sorok beszúrásakor. Egy példa következik.

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

Most az alkalmazásnak nem kell megadnia a TenantId-et a sorok beszúrásakor:

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
> Ha egy entitáskeret-projektalapértelmezett megkötéseit használja, ajánlott, hogy *ne* foglalja bele a TenantId oszlopot az EF-adatmodellbe. Ennek a javaslatnak az az oka, hogy az Entitáskeretrendszer-lekérdezések\_automatikusan olyan alapértelmezett értékeket adnak meg, amelyek felülbírálják a T-SQL-ben a SESSION CONTEXT függvényt használó alapértelmezett megkötéseket.
> Ha például alapértelmezett korlátozásokat szeretne használni a mintaprojektben, távolítsa el a TenantId azonosítót DataClasses.cs (és futtassa az Add-Migration parancsot a Csomagkezelő konzolon), és a T-SQL használatával győződjön meg arról, hogy a mező csak az adatbázistáblákban található. Ily módon az EF automatikusan helytelen alapértelmezett értékeket ad meg az adatok beszúrásakor.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Nem kötelező) A *rendszergazda* hozzáférésének engedélyezése az összes sorhoz

Előfordulhat, hogy egyes alkalmazások olyan *rendszergazdai felhasználót* szeretnének létrehozni, aki minden sort el tud érni. Egy rendszergazda engedélyezheti a jelentések et az összes bérlő minden szilánkok. Vagy egy rendszergazda végezhet felosztásos egyesítési műveleteket a szegmensek, amelyek magukban foglalják a bérlői sorok áthelyezése adatbázisok között.

Rendszergazdai felhasználó engedélyezéséhez hozzon létre`superuser` egy új SQL-felhasználót (ebben a példában) minden szegmensadatbázisban. Ezután módosítsa a biztonsági házirendet egy új predikátumfunkcióval, amely lehetővé teszi a felhasználó számára az összes sor elérését. Ez a funkció a következő.

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

- **Új szegmensek hozzáadása:** A T-SQL-parancsfájl végrehajtása az RLS engedélyezéséhez minden új szegmensek, különben lekérdezések ezeken a szegmenseken nem szűrhető.
- **Új táblák hozzáadása:** Adjon hozzá egy szűrőt és blokk állítmányt az összes szegmens biztonsági házirendjéhez, amikor új táblát hoz létre. Ellenkező esetben az új tábla lekérdezései nem lesznek szűrve. Ez a kiegészítés ddl-eseményindító használatával automatizálható, ahogy azt a [Sorszintű biztonság automatikus alkalmazása automatikusan az újonnan létrehozott táblákra (blogra)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx)című részben leírtak szerint ismerteti.

## <a name="summary"></a>Összefoglalás

Rugalmas adatbázis-eszközök és a sorszintű biztonság együtt az alkalmazás adatrétegének horizontális felskálázása több-bérlős és egy-bérlős szegmensek támogatásával. Több-bérlős szilánkok adatok hatékonyabb tárolására használható. Ez a hatékonyság akkor hangsúlyos, ha nagy számú bérlő csak néhány sornyi adatkal rendelkezik. Az egybérlős szegmensek támogathatják a prémium szintű bérlőket, amelyek szigorúbb teljesítmény- és elkülönítési követelményekkel rendelkeznek. További információt a [Sorszintű biztonsági útmutató című témakörben talál.][rls]

## <a name="additional-resources"></a>További források

- [Mi az az Azure rugalmas készlet?](sql-database-elastic-pool.md)
- [Scaling out with Azure SQL Database (Horizontális felskálázás az Azure SQL Database segítségével)](sql-database-elastic-scale-introduction.md)
- [Tervezési minták az Azure SQL Database-t használó több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md)
- [Hitelesítés a több-bérlős alkalmazásokban az Azure AD és az OpenID Connect segítségével](../guidance/guidance-multitenant-identity-authenticate.md)
- [A Tailspin Surveys-alkalmazás](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Kérdések és szolgáltatáskérések

Kérdéseivel forduljon hozzánk az [SQL Database fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). És adjunk hozzá minden funkció kéri, hogy az [SQL Database visszajelzés fórum](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md

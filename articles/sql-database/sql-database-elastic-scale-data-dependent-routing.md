---
title: Adatfüggő útválasztás Azure SQL Databasesal | Microsoft Docs
description: A ShardMapManager osztály használata a .NET-alkalmazásokban az Adatfüggő útválasztáshoz, a többrétegű adatbázisok szolgáltatásához Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 3f0ce4f3bdf3159e991bfd72590882dfa7412ee3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568484"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Lekérdezés továbbítása a megfelelő adatbázishoz az Adatfüggő útválasztás használatával

Az Adatfüggő **Útválasztás** lehetővé teszi, hogy egy lekérdezésben lévő, a kérést a megfelelő adatbázishoz irányítsa. Az adatokon alapuló útválasztás alapvető minta a többrétegű adatbázisok használatakor. A kérelem környezete a kérelem továbbítására is használható, különösen akkor, ha a horizontális Felskálázási kulcs nem része a lekérdezésnek. Egy alkalmazásban az Adatfüggő útválasztást használó minden egyes lekérdezés vagy tranzakció esetében csak egy adatbázis férhet hozzá egy kérelemhez. Az Azure SQL Database rugalmas eszközök esetében ez az Útválasztás a **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) osztály használatával érhető el.

Az alkalmazásnak nem kell nyomon követnie a különböző típusú adatszeletekhez társított különböző kapcsolódási karakterláncokat vagy adatbázis-tárolóhelyeket. Ehelyett a szegmenses [Térkép kezelője](sql-database-elastic-scale-shard-map-management.md) szükség esetén nyitja meg a megfelelő adatbázisokhoz való kapcsolódást, és az alkalmazás kérelmének céljaként szolgáló horizontális Felskálázási kulcs értékét. A kulcs általában a *ügyfél*, a *tenant_id*, a *date_key*vagy valamilyen más, az adatbázis-kérelem alapvető paraméterét képező azonosító.

További információ: SQL Server horizontális [Felskálázása az adatoktól függő útválasztással](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Az ügyféloldali kódtár letöltése

Letöltés:

* A könyvtár Java-verziója: [Maven Central adattár](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A könyvtár .NET-verziója: [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>ShardMapManager használata Adatfüggő útválasztási alkalmazásokban

Az alkalmazásoknak az inicializálás során kell létrehozniuk a **ShardMapManager** a Factory Call **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) használatával. Ebben a példában a **ShardMapManager** és a benne található adott **ShardMap** is inicializálva van. Ez a példa a GetSqlShardMapManager és a GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.net](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))) metódusokat mutatja be.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>A szegmens Térkép beszerzéséhez a legalacsonyabb jogosultsági szintű hitelesítő adatok használata lehetséges

Ha egy alkalmazás nem kezeli magát a szegmenst, a gyári metódusban használt hitelesítő adatoknak írásvédett engedélyekkel kell rendelkezniük a globális szegmens- **hozzárendelési** adatbázison. Ezek a hitelesítő adatok általában eltérnek a szegmenses Térkép kezelőjével létesített kapcsolatok megnyitásához használt hitelesítő adatoktól. Lásd még: [az Elastic Database ügyféloldali kódtár eléréséhez használt hitelesítő adatok](sql-database-elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>A OpenConnectionForKey metódus hívása

A **ShardMap. OpenConnectionForKey metódus** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) egy olyan kapcsolódási lehetőséget ad vissza, amely készen áll a megfelelő adatbázisra vonatkozó parancsok kiadására a **kulcs** paraméter értéke alapján. A szegmens adatait a **ShardMapManager**gyorsítótárazza az alkalmazásban, ezért ezek a kérelmek általában nem tartalmaznak adatbázis-keresést a **globális** szegmens-hozzárendelési adatbázison.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* A **Key** paramétert keresési kulcsként használja a rendszer a szegmensben lévő térképre, hogy meghatározza a kérelemhez szükséges adatbázist.
* A **ConnectionString** a kívánt kapcsolathoz tartozó felhasználói hitelesítő adatok továbbítására szolgál. Ez a *ConnectionString* nem tartalmazza az adatbázis nevét vagy a kiszolgáló nevét, mivel a metódus meghatározza az adatbázist és a kiszolgálót a **ShardMap**használatával.
* A **connectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) értékének connectionOptions kell lennie **. Ellenőrizze** , hogy van-e olyan környezet, amelyben a szegmens térképek változhatnak, és a sorok más adatbázisokba is áthelyezhetők a felosztott vagy egyesítési műveletek eredményeképpen. Ez az ellenőrzés egy rövid lekérdezést is magában foglal a céladatbázis helyi szegmensének térképén (nem a globális szegmenses térképre), mielőtt a rendszer megküldi a kapcsolódást az alkalmazáshoz.

Ha a helyi szegmens leképezésének érvényesítése meghiúsul (jelezve, hogy a gyorsítótár helytelen), a szegmenses Térkép kezelője lekérdezi a globális szegmenses térképet, hogy beszerezze a kereséshez szükséges új helyes értéket, frissíti a gyorsítótárat, és beolvassa és visszaadja a megfelelő adatbázis-kapcsolat értékét. .

A **connectionOptions. None** értéket csak akkor használja, ha egy alkalmazás online állapotban van, és a szegmens-hozzárendelések módosítása nem várható. Ebben az esetben előfordulhat, hogy a gyorsítótárazott értékek mindig helyesek, és a célként megadott adatbázisra irányuló extra oda-és visszaút-ellenőrzési hívás is biztonságosan kihagyható. Ez csökkenti az adatbázis-forgalmat. A **connectionOptions** egy konfigurációs fájlban lévő értékkel is beállítható annak jelzésére, hogy az adott időszakra vonatkozó változások várhatóak-e vagy sem.  

Ez a példa egy Integer Key **Vevőkód**értékét használja, amely egy **customerShardMap**nevű **ShardMap** objektumot használ.  

```Java
int customerId = 12345;
int productId = 4321;
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

// Connect to the shard for that customer ID. No need to call a SqlConnection
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
{
    // Execute a simple command.
    SqlCommand cmd = conn.CreateCommand();
    cmd.CommandText = @"UPDATE Sales.Customer
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID";

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
    cmd.ExecuteNonQuery();
}  
```

A **OpenConnectionForKey** metódus egy új, már nyitott kapcsolódást ad vissza a megfelelő adatbázishoz. Az ily módon használt kapcsolatok teljes mértékben kihasználják a kapcsolatok készletezését.

Az **OpenConnectionForKeyAsync metódus** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) akkor is elérhető, ha az alkalmazás aszinkron programozást használ.

## <a name="integrating-with-transient-fault-handling"></a>Integrálás az átmeneti hibák kezelésére

Az adatelérési alkalmazások felhőben történő fejlesztésének bevált gyakorlata, hogy az alkalmazás átmeneti hibákat észlel, és a műveletek többször is többször is próbálkoznak a hiba eldobása előtt. A felhőalapú alkalmazások átmeneti hibáinak kezelését az átmeneti hibák kezelésére ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.net](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))) tárgyaljuk.

Az átmeneti hibák kezeléséhez természetesen az Adatfüggő útválasztási minta is használható. A legfontosabb követelmény, hogy próbálja megismételni a teljes adatelérési kérelmet, beleértve az Adatfüggő útválasztási kapcsolatot bekérő blokk **használatával** . Az előző példa a következőképpen írható át.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Példa – az Adatfüggő útválasztás átmeneti hibák kezelésével

```Java
int customerId = 12345;
int productId = 4321;
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

Configuration.SqlRetryPolicy.ExecuteAction(() =&gt;
{
    // Connect to the shard for a customer ID.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
    {
        // Execute a simple command
        SqlCommand cmd = conn.CreateCommand();

        cmd.CommandText = @"UPDATE Sales.Customer
                            SET PersonID = @newPersonID
                            WHERE CustomerID = @customerID";

        cmd.Parameters.AddWithValue("@customerID", customerId);
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
        cmd.ExecuteNonQuery();

        Console.WriteLine("Update completed");
    }
});
```

Az átmeneti hibakezelés megvalósításához szükséges csomagokat a rendszer automatikusan letölti a rugalmas adatbázis-minta alkalmazás létrehozásakor.

## <a name="transactional-consistency"></a>Tranzakciós konzisztencia

A tranzakciós tulajdonságok minden, a szegmensen belüli művelet esetében garantáltak. Az Adatfüggő útválasztáson keresztül küldött tranzakciók például a kapcsolatok cél szegmensének hatókörén belül lesznek végrehajtva. Jelenleg nincs lehetőség arra, hogy több kapcsolat kerüljön be egy tranzakcióba, ezért a szegmenseken végrehajtott műveletekhez nem biztosítunk tranzakciós garanciát.

## <a name="next-steps"></a>További lépések

A szegmensek leválasztásához vagy a szegmensek újbóli csatolásához lásd: [a recoverymanager osztállyal osztály használata](sql-database-elastic-database-recovery-manager.md) a szegmenses Térkép problémák megoldásához

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
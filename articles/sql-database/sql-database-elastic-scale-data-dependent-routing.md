---
title: Adatfüggő útválasztás
description: A ShardMapManager osztály használata a .NET-alkalmazásokban adatfüggő útválasztáshoz, amely az Azure SQL Database szilánkos adatbázisainak egyik szolgáltatása
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
ms.openlocfilehash: fbdf8e316368be02ebd0c4bfd320917c20d80777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069455"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Lekérdezés továbbítása a megfelelő adatbázisba adatfüggő útválasztással

**Az adatfüggő útválasztás** az a képesség, hogy a lekérdezésben lévő adatok segítségével a kérést egy megfelelő adatbázisba irányítsa. Az adatoktól függő útválasztás alapvető minta a szilánkos adatbázisok munka során. A kérelem környezetben is használható a kérelem továbbítására, különösen akkor, ha a szilánkos kulcs nem része a lekérdezésnek. Az adatoktól függő útválasztást használó alkalmazások ban minden egyes lekérdezés vagy tranzakció kérésenként csak egy adatbázis elérésére korlátozódik. Az Azure SQL Database Elastic eszközök esetében ez az útválasztás a **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) osztályhasználatával történik.

Az alkalmazásnak nem kell nyomon követnie a különböző kapcsolati karakterláncokat vagy db-helyeket a szilánkos környezetben lévő különböző adatszeletekhez társítva. Ehelyett a [Shard Map Manager](sql-database-elastic-scale-shard-map-management.md) megnyitja a kapcsolatokat a megfelelő adatbázisokhoz, ha szükséges, a szegmenstérkép adatai és az alkalmazás kérésének célként szolgáló szegmenskulcs értéke alapján. A kulcs általában a *customer_id*, *tenant_id*, *date_key*vagy más konkrét azonosító, amely az adatbázis-kérelem alapvető paramétere.

További információt az [SQL Server kiskálázása adatfüggő útválasztással című témakörben talál.](https://technet.microsoft.com/library/cc966448.aspx)

## <a name="download-the-client-library"></a>Az ügyféltár letöltése

Letöltés:

* A könyvtár Java-verziója, [lásd: Maven Központi Adattár](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A tár .NET verziója, [lásd: NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>ShardMapManager használata adatfüggő útválasztási alkalmazásban

Az alkalmazásoknak az inicializálás során a **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), .NET ) gyári hívással kell meghozniuk a **ShardMapManager-t.** [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) Ebben a példában egy **ShardMapManager** és egy adott **ShardMap,** amely tartalmazza az inicializálása. Ez a példa a GetSqlShardMapManager és a GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))) metódusokat mutatja be.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>A lehető legalacsonyabb jogosultsági szintű hitelesítő adatok használata a shard térkép leképezéséhez

Ha egy alkalmazás nem módosítja magát a szegmenstérképet, a gyári módszerben használt hitelesítő adatoknak csak olvasható engedélyekkel kell rendelkezniük a **globális shard** térkép-adatbázishoz. Ezek a hitelesítő adatok általában eltérnek a shard térképkezelővel létesített kapcsolatok megnyitásához használt hitelesítő adatoktól. Lásd [még: A rugalmas adatbázis-ügyfélkódtár eléréséhez használt hitelesítő adatok.](sql-database-elastic-scale-manage-credentials.md)

## <a name="call-the-openconnectionforkey-method"></a>Az OpenConnectionForKey metódus felhívása

A **ShardMap.OpenConnectionForKey metódus** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) a **kulcsparaméter** értéke alapján parancsok kiadására kész kapcsolatot ad vissza a megfelelő adatbázisnak. Shard információk gyorsítótárazott az alkalmazásban a **ShardMapManager,** így ezek a kérelmek általában nem járnak a **globális shard térkép** adatbázis adatbázis.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* A **kulcsparaméter** a shard térkép keresőkulcsként szolgál a kérelem megfelelő adatbázisának meghatározásához.
* A **connectionString** csak a kívánt kapcsolat felhasználói hitelesítő adatainak a megadására szolgál. Ebben a *kapcsolati karakterláncban* nem szerepel adatbázisnév vagy kiszolgálónév, mivel a módszer a **ShardMap**segítségével határozza meg az adatbázist és a kiszolgálót.
* A **connectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) beállítást a **ConnectionOptions.Validate** értékre kell állítani, ha olyan környezetben, ahol a szegmensleképezések változhatnak, és a sorok felosztási vagy egyesítési műveletek eredményeként más adatbázisokba is átkerülhetnek. Ez az ellenőrzés magában foglalja egy rövid lekérdezést a helyi shard térkép a céladatbázisban (nem a globális shard térkép) a kapcsolat kézbesítése előtt.

Ha a helyi shard térkép en való érvényesítés sikertelen (jelezve, hogy a gyorsítótár helytelen), a Shard Map Manager lekérdezi a globális shard térképet a globális helyes érték megszerzéséhez, a gyorsítótár frissítéséhez, valamint a megfelelő adatbázis-kapcsolat beszerzéséhez és visszaadásához .

**Használja connectionoptions.None** csak akkor, ha shard leképezési módosítások nem várható, amíg egy alkalmazás online állapotban van. Ebben az esetben a gyorsítótárazott értékek mindig helyesnek tekinthetők, és a céladatbázishoz intézett extra oda-vissza érvényesítési hívás biztonságosan kihagyható. Ez csökkenti az adatbázis forgalmát. A **connectionOptions** egy konfigurációs fájlban lévő értéken keresztül is beállítható annak jelzésére, hogy a szilánkok módosítása várható-e egy adott időszakban.  

Ez a példa egy **ügyfélazonosító**egész kulcs értékét használja egy **CustomerShardMap**nevű **ShardMap** objektum használatával.  

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

Az **OpenConnectionForKey** metódus új, már megnyitott kapcsolatot ad vissza a megfelelő adatbázishoz. Az ily módon használt kapcsolatok továbbra is teljes mértékben kihasználják a kapcsolatkészletezés előnyeit.

Az **OpenConnectionForKeyAsync metódus** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) akkor is elérhető, ha az alkalmazás aszinkron programozást használ.

## <a name="integrating-with-transient-fault-handling"></a>Integrálás átmeneti hibakezeléssel

A felhőben lévő adatelérési alkalmazások fejlesztésének ajánlott gyakorlata annak biztosítása, hogy az alkalmazás átmeneti hibákat észleljen, és hogy a műveleteket többször is megpróbálja újra, mielőtt hibát dobna. A felhőalapú alkalmazások átmeneti hibakezelését az Átmeneti hibakezelés[(Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))) tárgyalja.

Az átmeneti hibakezelés természetesen együtt létezhet az adatfüggő útválasztási mintával. A legfontosabb követelmény a teljes adatelérési kérelem újrapróbálkozása, beleértve az adatfüggő útválasztási kapcsolatot kapó **blokkhasználatát** is. Az előző példa a következőképpen írható át.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Példa - adatfüggő útválasztás átmeneti hibakezeléssel

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

Configuration.SqlRetryPolicy.ExecuteAction(() -> {

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

Az átmeneti hibakezelés megvalósításához szükséges csomagok automatikusan letöltődnek a rugalmas adatbázis-mintaalkalmazás létrehozásakor.

## <a name="transactional-consistency"></a>Tranzakciós konzisztencia

Tranzakciós tulajdonságok garantált a shard helyi műveletek. Például az adatfüggő műveletterven keresztül küldött tranzakciók a kapcsolat célszegmensének hatókörén belül futnak. Jelenleg nincsenek olyan képességek, amelyek több kapcsolatot egy tranzakcióba való besorozásához biztosítanak, ezért nincsenek tranzakciós garanciák a szegmensek között végrehajtott műveletekhez.

## <a name="next-steps"></a>További lépések

Shard leválasztásához vagy egy shard újbóli csatolásához [lásd: A RecoveryManager osztály használata a szegmenstérkép-problémák megoldásához](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

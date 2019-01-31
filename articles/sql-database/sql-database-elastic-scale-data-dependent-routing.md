---
title: Adatfüggő útválasztás az Azure SQL Database |} A Microsoft Docs
description: Adatfüggő útválasztás, szilánkokra osztott adatbázisok az Azure SQL Database szolgáltatás használata a ShardMapManager osztály a .NET-alkalmazások
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: fe9098592fcfde2d5e23b78a3e33f2b4ebb9e2dc
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468648"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Adatfüggő útválasztás irányíthatja a megfelelő adatbázis-lekérdezés használata

**Adatfüggő útválasztás** képessége, hogy az adatok segítségével egy lekérdezésben irányítsa át a kérést a megfelelő adatbázishoz. Egy alapvető mintája adatok függő útválasztás akkor, ha horizontálisan skálázott adatbázisok használata. A kérelem környezetéből is használható, irányítsa át a kérést, különösen akkor, ha a horizontális skálázási kulcs nem szerepel a lekérdezésben. Minden egyes lekérdezés vagy a tranzakció egy alkalmazásban Adatfüggő útválasztás használatával korlátozódik kérelmenként egy adatbázis elérése közben. Az Azure SQL Database rugalmas eszközök, az Útválasztás segítségével lehet megvalósítani a **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) osztály.

Az alkalmazás nem kell nyomon követni a különböző kapcsolati karakterláncok vagy a horizontálisan skálázott környezetben adatok eltérő szeletek társított DB helyeket. Ehelyett a [Szilánkleképezés-kezelővel](sql-database-elastic-scale-shard-map-management.md) megnyílik kapcsolatok a megfelelő adatbázisokhoz, amikor szükséges, hogy a szegmenstérkép és az alkalmazásigénylés célja a horizontális skálázási kulcs értékét az adatok alapján. A kulcs: általában az *customer_id*, *tenant_id*, *date_key*, vagy valamilyen más specifikus azonosító, amely az adatbázis-kérelem alapvető paraméter.

További információkért lásd: [méretezés ki SQL Server Adatfüggő útválasztásnak](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Az ügyféloldali kódtár letöltése

Letöltése:

* A Java-verzió a könyvtár, lásd: [Maven központi tárházból](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A könyvtár a .NET verzióját, lásd: [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Adatfüggő útválasztás alkalmazásban egy ShardMapManager használatával

Alkalmazások példányosítania kell a **ShardMapManager** az inicializálás során az előállító hívás használatával **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). Ebben a példában is egy **ShardMapManager** és a egy adott **ShardMap** benne van inicializálva. Ez a példa bemutatja a GetSqlShardMapManager és GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))) módszereket.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Legkisebb lehetséges szintű hitelesítő adatokat használni a horizontális skálázási térképet beolvasása

Ha egy alkalmazás nem van módosítása a szegmenstérkép magát, a gyári metódusban használt hitelesítő adatokat csak olvasható engedélyek kell rendelkeznie a **globális Szegmenstérkép** adatbázis. Ezeket a hitelesítő adatokat általában eltérnek a szilánkleképezés-kezelővel való kapcsolat megnyitásához használt hitelesítő adatokat. Lásd még: [az Elastic Database ügyfélkódtár eléréséhez használt hitelesítő adatok](sql-database-elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>A OpenConnectionForKey metódus hívása

A **ShardMap.OpenConnectionForKey metódus** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) adja vissza a kapcsolat készen áll a parancsok kiadása értéke alapján a megfelelő adatbázishoz a **kulcs** paraméter. Szilánkleképezés-adatbázisban tárolja az alkalmazás által a **ShardMapManager**, így ezek a kérelmek jellemzően nem járnak irányuló adatbázis keresés a **globális Szegmenstérkép** adatbázis.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* A **kulcs** paraméter keresési kulcsként a szegmenstérkép be meghatározására szolgál a kéréshez a megfelelő adatbázishoz.
* A **connectionString** be csak a felhasználó hitelesítő adatait a kívánt kapcsolatot. Nincs adatbázis vagy a kiszolgáló neve tartalmazza a jelen *connectionString* óta a módszer az adatbázis és a kiszolgáló használatával határozza meg a **ShardMap**.
* A **connectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) értékre kell állítani **ConnectionOptions.Validate** Ha olyan környezetben, ahol szilánkleképezések május változás- és sorok más adatbázisok felosztása és egyesítése műveletek miatt előfordulhat, hogy át. Ellenőrzés magában foglalja a cél helyi horizontális skálázási térképet rövid lekérdezés adatbázis (nem a globális szegmenstérkép), mielőtt megkapná a kapcsolatot az alkalmazás.

Ha a helyi szegmenstérkép sémaellenőrzésen sikertelen (azt jelzi, hogy a gyorsítótár nem megfelelő), a Szilánkleképezés-kezelővel lekérdezi a globális szegmenstérkép szerezze be az új megfelelő érték a kereséshez, frissítse a gyorsítótárat, és szerezze be és adja vissza a megfelelő adatbázis-kapcsolat .

Használat **ConnectionOptions.None** csak amikor szegmens leképezési módosításokat nem várt közben egy alkalmazás online állapotban. Ebben az esetben a gyorsítótárban lévő értékeket is feltételezhető, hogy mindig a megfelelő lehet, és a céladatbázis extra oda-vissza érvényesítési hívása biztonságosan kimarad. Amely csökkenti az adatbázis-forgalom. A **connectionOptions** is beállítható, keresztül értéket jelzi-e a horizontális skálázási módosítások várhatóan egy konfigurációs fájlban vagy egy bizonyos idő alatt nem.  

Ebben a példában egy egész kulcsnak az értéke **CustomerID**révén egy **ShardMap** nevű objektum **customerShardMap**.  

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

A **OpenConnectionForKey** metódus az új már nyitott kapcsolat a megfelelő adatbázishoz adja vissza. Kapcsolatok használt fel, így továbbra is teljes mértékben kihasználhatja a kapcsolatkészletezést.

A **OpenConnectionForKeyAsync metódus** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) egyben érhető el, ha az alkalmazás aszinkron programozás használja.

## <a name="integrating-with-transient-fault-handling"></a>Integrálás az átmeneti hibák kezelése

A data access alkalmazások felhőbeli fejlesztéséhez ajánlott eljárás, hogy győződjön meg arról, hogy az átmeneti hibák az alkalmazás által észlelt, és a műveletek előtt értesítő hiba megismétlődnek több alkalommal. Átmeneti hibák a felhőalapú alkalmazások a következő cikkben: átmeneti hibák kezelésével ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))).

Adatfüggő útválasztás mintával rendelkező természetes módon párhuzamosan átmeneti hibák kezelése. A fő követelmény, hogy ismételje meg a teljes hozzáférési kérelem többek között a **használatával** szerezni a Adatfüggő útválasztás kapcsolat blokkolása. Az előző példában a következő sikerült kell írni.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Példa – Adatfüggő útválasztás az átmeneti hibák kezelése

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

Végre kell hajtani az átmeneti hibák kezelése a csomagok automatikusan letöltődnek a rugalmas adatbázis-mintaalkalmazás készítése során.

## <a name="transactional-consistency"></a>Tranzakció-konzisztencia

Tranzakciós tulajdonságok garantáltan minden művelet helyi szegmenshez való hozzáadásukkor. Adatfüggő útválasztás keresztül elküldött tranzakciók például hajtható végre, a kapcsolat a célként megadott szegmens hatókörén belül. Jelenleg nincsenek a felvétel be egy tranzakció több kapcsolat biztosított képességek találhatók, és ezért garanciát nem jelentenek tranzakciós a szegmensek között végrehajtott műveletek.

## <a name="next-steps"></a>További lépések

Válassza le a szegmensek, vagy csatlakoztassa újból szegmensek, lásd: [szilánkleképezési problémák javítása a RecoveryManager osztállyal](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
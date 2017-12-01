---
title: "Függő az Azure SQL Database útválasztási adatok |} Microsoft Docs"
description: "A ShardMapManager osztály használata a .NET-alkalmazásokban az adatok függő útválasztási, szilánkos adatbázisokat az Azure SQL-adatbázis szolgáltatása"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: cad09e15-5561-4448-aa18-b38f54cda004
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: ddove
ms.openlocfilehash: ef88d6072cfa95842703c31ec8e95ff4664f91ff
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="data-dependent-routing"></a>Adatfüggő útválasztás
**Adatok függő útválasztási** képessége, hogy a lekérdezés az adatok használatával továbbítja a kérelmet a megfelelő adatbázishoz. Ez az alapvető mintát, az szilánkos adatbázisok használatakor. A kérés környezete is használható a kérelem továbbításához, különösen akkor, ha a horizontális kulcs része nem a lekérdezés. Minden egyedi lekérdezés vagy a tranzakció az adatok függő útválasztási használó alkalmazások elérése egy önálló adatbázis kérelmenként korlátozódik. Az Azure SQL Database rugalmas eszközök, az Útválasztás segítségével történik a **ShardMapManager** ([.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx), [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager)) osztály.

Az alkalmazás nem kell különböző kapcsolati karakterláncok és az adatok a szilánkos környezetben más szeletek társított DB helyek követheti nyomon. Ehelyett a [Shard térkép Manager](sql-database-elastic-scale-shard-map-management.md) a helyes adatbázis szükség esetén megnyílik-kapcsolatok a shard és az alkalmazás kérelem céljaként a horizontális kulcsnak az értéke adatok alapján. A kulcs van általában a *customer_id*, *tenant_id*, *date_key*, vagy az adatbázis-kérelem alapvető paraméter néhány más egyedi azonosító). 

További információkért lásd: [skálázás, SQL Server Data függő útválasztási](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Töltse le az ügyféloldali kódtár
Letöltése:
* a könyvtár a .NET változata: [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).
* a Java verzióját a könyvtárban, lásd: [Maven központi tárházban](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Egy ShardMapManager használata adatok függő útválasztási alkalmazásokban
Alkalmazások példányt kell létrehozni a **ShardMapManager** az inicializálás során használja a gyári hívás **GetSQLShardMapManager** ([.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx), [Java ](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager)). Ebben a példában mind a **ShardMapManager** és egy adott **ShardMap** benne foglalt inicializálása. Ez a példa bemutatja a GetSqlShardMapManager és GetRangeShardMap ([.NET](https://msdn.microsoft.com/library/azure/dn824173.aspx), [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getrangeshardmap)) módszerek.

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>A szilánkok leképezés első legkisebb lehetséges jogosultságokat biztosító hitelesítő adatok használata
Ha egy alkalmazás nem van kezelésére a shard térkép magát, a gyári metódusban használt hitelesítő adatok csak olvasási engedélyekkel kell rendelkezniük a **globális Shard térkép** adatbázis. Ezek a hitelesítő adatok általában eltérnek a shard térkép Manager kapcsolatok megnyitásához használt hitelesítő adatok. Lásd még: [az Elastic Database ügyféloldali kódtár eléréséhez használt hitelesítő adatok](sql-database-elastic-scale-manage-credentials.md). 

## <a name="call-the-openconnectionforkey-method"></a>A OpenConnectionForKey metódus hívása
A **ShardMap.OpenConnectionForKey metódus** ([.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx), [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkey)) adja vissza a kapcsolat készen áll a parancsok kiadása a megfelelő adatbázisba értéke alapján a **kulcs** paraméter. A shard információkat tárolja a rendszer az alkalmazás által a **ShardMapManager**, így ezek a kérelmek általában nem tartalmaznak egy adatbázis-lekérdezés alapján a **globális Shard térkép** adatbázis. 

```csharp
// Syntax: 
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* A **kulcs** paraméterrel keresési kulcsként azokat a shard térkép határozza meg a megfelelő adatbázis a kérelemhez. 
* A **connectionString** csak a felhasználói hitelesítő adatok továbbítása a kívánt kapcsolat használatával. Nincs adatbázis vagy a kiszolgáló neve szerepelnek a *connectionString* óta metódus határozza meg, hogy az adatbázis és a kiszolgáló használja a **ShardMap**. 
* A **connectionOptions** ([.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx), [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._connection_options)) értékre kell állítani **ConnectionOptions.Validate** Ha egy olyan környezetben, ahol a shard leképezhető előfordulhat, hogy változás- és sorok más adatbázisok, megosztott vagy merge műveletek miatt előfordulhat, hogy áthelyezése. Ebbe beletartozik a helyi shard leképezés a cél rövid lekérdezés adatbázist (nem a globális shard térkép) előtt a kapcsolatot a rendszer az alkalmazás. 

Ha a helyi shard térkép elvégzett sémaellenőrzésen sikertelen (Ez azt jelzi, hogy a gyorsítótár nem megfelelő), a Shard térkép Manager le fogja kérdezni a globális shard térkép szerezze be az új megfelelő értéket a keresési frissíteni a gyorsítótárat, és szerezze be és térjen vissza a megfelelő adatbázis a kapcsolat. 

Használjon **ConnectionOptions.None** csak amikor szilánkok leképezése módosítások esetén nem követelmény, míg az alkalmazás online. Ebben az esetben a gyorsítótárazott értékeket is feltételezhető, hogy mindig a megfelelő lehet, és a céladatbázis extra körbejárási érvényesítési hívása biztonságosan kihagyja. Amely csökkenti az adatbázis-forgalom. A **connectionOptions** is beállítható, keresztül értéket annak jelzésére, hogy horizontális módosítások várható a konfigurációs fájlban vagy egy adott időn belül során nem.  

Ez a példa egy egész kulcsnak az értéke **CustomerID**használatával egy **ShardMap** nevű objektum **customerShardMap**.  

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

A **OpenConnectionForKey** metódus visszaadja az új kapcsolat már nyitva a megfelelő adatbázishoz. Ily módon be kapcsolatok továbbra is teljes körű kihasználása kapcsolatkészletezést. 

A **OpenConnectionForKeyAsync metódus** ([.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx), [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkeyasync)) akkor is használható, ha az alkalmazás lehetővé teszi az aszinkron programozás használja az ADO.Net.

## <a name="integrating-with-transient-fault-handling"></a>Átmeneti hiba kezelési integrálása
Adatok felhőalapú alkalmazásokat fejleszt ajánlott annak biztosítására, hogy átmeneti hibák az alkalmazás által észlelt, és, hogy a műveletek előtt hibát jelez a többször van újra. Átmeneti hiba kezelése a felhőalapú alkalmazások tárgyalt: átmeneti hiba kezelése ([.NET](https://msdn.microsoft.com/library/dn440719\(v=pandp.60\).aspx), [Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling)). 

Átmeneti hiba kezelési egyszerre is használható természetesen az adatok függő útválasztási mintával. A kulcs mérete, majd ismételje meg a teljes hozzáférés kérelem, például a **használatával** lekért adatok függő útválasztási kapcsolat blokkolása. (Megjegyzés: a kijelölt módosítása) az alábbi módon sikerült kell írni a fenti példa. 

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Példa - adatok függő útválasztással átmeneti hiba kezelése
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


A rugalmas adatbázis-minta alkalmazás építésekor átmeneti hiba kezelési végrehajtásához szükséges csomagok automatikusan letöltődnek. 

## <a name="transactional-consistency"></a>Tranzakciós konzisztencia
Tranzakciós tulajdonságai garantáltan minden műveletnél helyi, a szilánkcímtárban. Például tranzakciók adatok függő útválasztási keresztül hajtható végre, a kapcsolat a cél shard hatókörén belül. Jelenleg nincsenek történő besorolásakor több kapcsolatot egy tranzakcióban megadott képességek, és ezért nincsenek nem tranzakciós garanciák műveletekhez szilánkok keresztül zajlik.

## <a name="next-steps"></a>Következő lépések
Válassza le a shard, vagy egy shard újracsatolni [shard térkép problémák elhárításának a RecoveryManager osztály használatával](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


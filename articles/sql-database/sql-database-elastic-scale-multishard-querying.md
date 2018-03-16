---
title: "Horizontálisan skálázott Azure SQL-adatbázis lekérdezése |} Microsoft Docs"
description: "A rugalmas adatbázis ügyféloldali kódtár segítségével szilánkok lekérdezések futtatása."
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/28/2017
ms.author: sstein
ms.openlocfilehash: 2712968f2929c48318e781fa846a8de525a0ef0c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="multi-shard-querying"></a>Több shard lekérdezése
## <a name="overview"></a>Áttekintés
Az a [skálázáshoz rugalmas adatbáziseszközöket](sql-database-elastic-scale-introduction.md), szilánkos adatbázis megoldásokat hozhat létre. **Több shard lekérdezése** feladatokhoz, mint például több szilánkok gyűjtemény/jelentő lekérdezés futtatása igénylő átnyúljon használja. (A való ellentétben [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md), egyetlen shard összes munkahelyi végzi, amely.) 

1. Első egy **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) vagy **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET ](https://msdn.microsoft.com/library/azure/dn807370.aspx)) használatával a **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), a **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), vagy a **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) metódust. Lásd:  **[hozhat létre egy ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)**  és  **[egy RangeShardMap vagy ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)**.
2. Hozzon létre egy **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_connection), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)) objektum.
3. Hozzon létre egy **MultiShardStatement vagy MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)). 
4. Állítsa be a **a CommandText tulajdonság** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)) számára a T-SQL-parancsot.
5. A parancs meghívásával hajtható végre a **ExecuteQueryAsync vagy ExecuteReader** ([Java](), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)) metódust.
6. Megtekintheti az eredményeket a **MultiShardResultSet vagy MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_result_set), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)) osztály. 

## <a name="example"></a>Példa
Az alábbi kód bemutatja az lekérdezése használatával több shard használatát egy adott **ShardMap** nevű *myShardMap*. 

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString)) 
{ 
    using (MultiShardCommand cmd = conn.CreateCommand())
    { 
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
        cmd.CommandType = CommandType.Text; 
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

        using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
        { 
            while (sdr.Read())
            { 
                var c1Field = sdr.GetString(0); 
                var c2Field = sdr.GetFieldValue<int>(1); 
                var c3Field = sdr.GetFieldValue<Int64>(2);
            } 
        } 
    } 
} 
```

A fő különbség több shard kapcsolatok létrehozása. Ha **SqlConnection** egy önálló adatbázis működik a **MultiShardConnection** időt vesz igénybe egy ***szilánkok gyűjteménye*** bemenetként. Töltse fel a szilánkok egy shard leképezés. A lekérdezés végrehajtása majd használatával szilánkok gyűjteménye **UNION ALL** össze egy teljes eredmény szemantikáját. Másik lehetőségként a shard, ahol a sor származik neve lehet hozzáadni a kimenethez használt a **ExecutionOptions** parancs tulajdonságát. 

Vegye figyelembe a hívást **myShardMap.GetShards()**. Ez a módszer lekéri az összes szilánkok shard leképezés, és könnyedén közötti összes megfelelő adatbázis-lekérdezés futtatható. Szilánkok gyűjteménye több shard lekérdezés kifinomultabb lehet további által a gyűjtemény keresztül hajtja végre a LINQ lekérdezés által visszaadott hívása **myShardMap.GetShards()**. A részleges eredmények házirend együtt, több shard lekérdezése az aktuális képesség úgy tervezték, és több tíz, akár több száz szilánkok működéséhez.

A többszörös shard lekérdezése korlátozása jelenleg érvényesíteni a következő szilánkok és, hogy a rendszer megkérdezi a shardlets hiánya. Amíg az adatok függő útválasztási ellenőrzi, hogy egy adott shard része a shard leképezés lekérdezése időpontjában, több shard lekérdezések ne hajtsa végre ezt az ellenőrzést. Ez több shard lekérdezések, amelyek el lettek távolítva a shard leképezés adatbázisokon vezethet.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Több shard lekérdezések és a felosztott-merge műveletek
Több shard lekérdezések nem ellenőrizhető, hogy shardlets a lekérdezett adatbázis részt vegyes-egyesítés folyamatban lévő műveletek. (Lásd: [méretezés, a rugalmas adatbázis vegyes egyesítéses eszközzel](sql-database-elastic-scale-overview-split-and-merge.md).) Ez vezethet inkonzisztenciát ahol szerepel, az azonos shardlet sorát több adatbázis ugyanabban a több shard lekérdezésben. Ezek a korlátozások figyelembe vennie, és fontolja meg a vegyes egyesítéses kiürítési folyamatban lévő műveletek és a shard térkép módosításainak több shard lekérdezések végrehajtása közben.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]



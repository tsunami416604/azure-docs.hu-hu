---
title: Szilánkokra osztott Azure SQL-adatbázisok lekérdezéséhez |} A Microsoft Docs
description: Az elastic database ügyfélkódtár használatával szegmensek közötti lekérdezéseket futtathat.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 93408b266a239e897b49ab2482818a5221742685
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870402"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Többszegmenses lekérdezés a rugalmas Adatbáziseszközök használatáról

## <a name="overview"></a>Áttekintés

Az a [rugalmas adatbáziseszközöket](sql-database-elastic-scale-introduction.md), szilánkokra osztott adatbázis megoldásokat hozhat létre. **Többszegmenses lekérdezés** feladatok szolgál, például a gyűjtemény/jelentő egy lekérdezés futtatásának igénylő húzódó több szegmens között. (A, ellentétben [Adatfüggő útválasztásnak](sql-database-elastic-scale-data-dependent-routing.md), amely az összes munkát végez egy szegmenst.) 

1. Get- **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) vagy **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET ](https://msdn.microsoft.com/library/azure/dn807370.aspx)) használatával a **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), a **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), vagy a **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) metódust. Lásd: **[hozhat létre, amely egy ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)** és  **[egy RangeShardMap vagy ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)**.
2. Hozzon létre egy **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_connection), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)) objektum.
3. Hozzon létre egy **MultiShardStatement vagy MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)). 
4. Állítsa be a **vlastnost CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)), a T-SQL paranccsal.
5. A parancs meghívásával hajtható végre a **ExecuteQueryAsync vagy Executereadert** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement.executeQueryAsync), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)) metódust.
6. Tekintse meg az eredményeket használja a **MultiShardResultSet vagy MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_result_set), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)) osztály. 

## <a name="example"></a>Példa

Az alábbi kód bemutatja a használatát több horizontális partíciós lekérdezések használatával egy adott **ShardMap** nevű *myShardMap*. 

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

A fő különbség többszegmenses kapcsolatok építését. Ahol **SqlConnection** egy önálló adatbázis működik a **MultiShardConnection** vesz igénybe egy ***szegmensek gyűjteményét*** bemenetként. Adja meg a gyűjtemény horizontálispartíció-térkép a szegmens. A lekérdezés majd hajtja végre a szegmensek gyűjteményét **UNION ALL** szemantika egy teljes eredmény összegyűjtése. Szükség esetén a szegmens, ahol a sor származik nevét a kimeneti történő is hozzáadhatók a **ExecutionOptions** parancssori tulajdonságot. 

Vegye figyelembe a hívást **myShardMap.GetShards()**. Ez a módszer minden szegmensre lekéri a szegmenstérkép, és minden megfelelő adatbázis-lekérdezés futtatható egy egyszerűbb megoldást kínál. A gyűjtemény szegmens többszegmenses lekérdezés finomított is lehet további keresztül a gyűjtemény egy LINQ-lekérdezésekre elvégzésével által visszaadott hívása **myShardMap.GetShards()**. És a részleges eredményeket szabályzat együttes alkalmazásával a jelenlegi képességről többszegmenses lekérdezés esetén működik megfelelően a szegmensek több száz legfeljebb tíz úgy lett kialakítva.

Egy, a több szegmensre vonatkozó lekérdezésekkel kapcsolatos korlátozás jelenleg szegmens és shardlet, hogy a rendszer megkérdezi a érvényesítése hiánya. Adatfüggő útválasztás ellenőrzi, hogy egy adott szegmens a szegmenstérkép része a lekérdezés időpontjában, amíg több horizontális partíciós lekérdezések ne végezze el ezt az ellenőrzést. Több horizontális partíciós lekérdezések adatbázisokon, amelyek a szegmenstérkép el lettek távolítva vezethet.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Több horizontális partíciós lekérdezések és műveletek szétválasztás és egyesítés esetén

Több horizontális partíciós lekérdezések ne ellenőrizze-e a lekérdezett adatbázison shardlet résztvevő szétválasztás és egyesítés folyamatban lévő műveletek. (Lásd: [az Elastic Database felosztási-egyesítési eszközének használatával végzett skálázást bemutató](sql-database-elastic-scale-overview-split-and-merge.md).) Ez vezethet inkonzisztenciákat, ugyanaz a shardlet tárolhatja sorait több adatbázis ugyanazon többszegmenses lekérdezés megjelenítése. Vegye figyelembe ezeket a korlátozásokat, és fontolja meg a kiürítési folyamatos felosztási-egyesítési műveletek használatát és a szegmenstérkép módosítások több horizontális partíciós lekérdezések végrehajtása közben.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
---
title: Szilánkos adatbázisok lekérdezése
description: Lekérdezések szilánkok között a rugalmas adatbázis-ügyfélkódtár használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: ae14a9fd8fc8479eac596fb694e12e3e0a9027f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067314"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Többszegmenses lekérdezés rugalmas adatbázis-eszközök használatával

## <a name="overview"></a>Áttekintés

A [rugalmas adatbázis-eszközökkel](sql-database-elastic-scale-introduction.md)szilánkos adatbázis-megoldásokat hozhat létre. **A többszegmenses lekérdezés** olyan feladatokhoz használatos, mint például az adatgyűjtés/jelentéskészítés, amelyek több szegmensen átnyúló lekérdezés futtatását igénylik. (Ezt hasonlítsa össze [az adatfüggő útválasztással,](sql-database-elastic-scale-data-dependent-routing.md)amely egyetlen shardon hajtja végre az összes munkát.)

1. A(z)[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap) **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) vagy **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap) [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) beszereznie a **TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) vagy a **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) metódushasználatával. **TryGetListShardMap** Lásd: [ShardMapManager építése](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) és [RangeShardMap vagy ListShardMap beszerezése.](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)
2. Hozzon létre egy **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)) objektumot.
3. Hozzon létre egy **MultiShardStatement vagy MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Állítsa a **CommandText tulajdonságot** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) T-SQL parancsra.
5. A parancs végrehajtása az **ExecuteQueryAsync vagy az ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) metódus meghívásával.
6. Az eredmények megtekintése a **MultiShardResultSet vagy a MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)) osztályhasználatával.

## <a name="example"></a>Példa

A következő kód bemutatja a használatát több shard lekérdezési használatával egy adott **ShardMap** nevű *myShardMap.*

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

A legfontosabb különbség a többshard kapcsolatok építése. Ha **az SqlConnection** egy adott adatbázison működik, a **MultiShardConnection** ***szegmensek gyűjteményét*** veszi fel bemenetként. A szilánkok gyűjteményének feltöltése egy szegmenstérképről. A lekérdezés ezután végrehajtja a szegmensek gyűjtését **union all** szemantika egyetlen átfogó eredmény összeállításához. Szükség esetén a shard neve, ahonnan a sor származik, hozzáadható a kimenethez a **Command ExecutionOptions** tulajdonság használatával.

Megjegyzés: a hívás **myShardMap.GetShards()**. Ez a módszer lekéri az összes szilánkok a shard térképről, és egyszerű módja annak, hogy egy lekérdezést az összes releváns adatbázisok között. A többszegmenses lekérdezés szegmenseinek gyűjteménye tovább finomítható egy LINQ-lekérdezés végrehajtásával a **myShardMap.GetShards()** hívásból visszaadott gyűjteményfelett. A részleges eredmények házirend, a jelenlegi képesség a több szegmenslekérdezési úgy tervezték, hogy jól működik a tíz akár több száz szilánkok.

A többshard lekérdezési korlátozás jelenleg a lekérdezésre lekérdezett szegmensek és shardlets érvényesítésének hiánya. Míg az adatfüggő útválasztási ellenőrzi, hogy egy adott szegmens része-e a szegmenslekérdezik a lekérdezés időpontjában, több shard lekérdezések nem hajtják végre ezt az ellenőrzést. Ez azt eredményezheti, hogy több shard lekérdezések futó adatbázisok, amelyek eltávolították a shard térképen.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Többszegmenses lekérdezések és felosztott egyesítési műveletek

A többszegmenses lekérdezések nem ellenőrzik, hogy a lekérdezett adatbázis shardletjei folyamatban lévő felosztásos egyesítési műveletekben vesznek-e részt. (Lásd: [Méretezés a Rugalmas adatbázis felosztásos egyesítése eszközzel.)](sql-database-elastic-scale-overview-split-and-merge.md) Ez inkonzisztenciákhoz vezethet, ahol ugyanabból a shardletből származó sorok ugyanazon több szegmenses lekérdezés több adatbázisát mutatják. Vegye figyelembe ezeket a korlátozásokat, és fontolja meg a folyamatos felosztásos egyesítési műveletek és a szegmenstérkép módosításai kiürítését a többszegmenses lekérdezések végrehajtása közben.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
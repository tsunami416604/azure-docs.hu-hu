---
title: Adatok átalakítása a tárolt eljárási tevékenység az Azure Data Factory használatával |} Microsoft Docs
description: Ismerteti, hogyan lehet meghívni a Data Factory-folyamat az Azure SQL adatbázis/Data Warehouse tárolt eljárást az SQL Server tárolt eljárási tevékenység.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 25329d65861dc31e67bba146e15a6446a6449e83
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620588"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Adatok átalakítása a SQL Server tárolt eljárási tevékenység az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-stored-proc-activity.md)
> * [2. verzió – Előzetes verzió](transform-data-using-stored-procedure.md)


Adatok átalakítása tevékenységek használata egy adat-előállítóban [csővezeték](concepts-pipelines-activities.md) átalakító és előrejelzéseket és elemzések nyers adatok feldolgozása. A tárolt eljárási tevékenység, amely támogatja a Data Factory átalakítása tevékenységek egyike. Ez a cikk épít, a [adatok átalakítása](transform-data.md) cikk, amelynek során az adatok átalakítása és a Data Factory támogatott átalakítása tevékenységek általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [tárolt eljárási tevékenység a V1](v1/data-factory-stored-proc-activity.md).
> 
> Ha most ismerkedik az Azure Data Factory, olvassa végig [Bevezetés az Azure Data Factory](introduction.md) hajtsa végre az oktatóanyag: [oktatóanyag: adatok](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

A tárolt eljárási tevékenység segítségével meghívása tárolt eljárás valamelyik a következő adatokat tárolja, a vállalati vagy egy Azure virtuális gépen (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server-adatbázis.  SQL Server használatakor telepíthető önálló üzemeltetett integrációs futásidejű ugyanazon a számítógépen, amelyen az adatbázis vagy egy másik számítógépre, amely hozzáféréssel rendelkezik az adatbázishoz. Önálló üzemeltetett integrációs futásidejű rendszere, egy összetevő, amely összeköti az adatok a helyszínen vagy a Azure VM a cloud serviceshez adatforrásokat felügyelt és biztonságos módon. Lásd: [önállóan üzemel integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben alább.

> [!IMPORTANT]
> Ha az adatok másolása az Azure SQL Database vagy az SQL Server, konfigurálhatja a **SqlSink** a másolási tevékenység tárolt eljárás használatával meghívni a **sqlWriterStoredProcedureName** tulajdonság. A tulajdonság kapcsolatos tudnivalókért lásd az alábbi összekötő cikkek: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Adatok másolása az Azure SQL Data Warehouse a másolási tevékenység során a tárolt eljárás meghívása nem támogatott. De a tárolt eljárási tevékenység segítségével az SQL Data Warehouse tárolt eljárás hívása. 
>
> Ha az adatok másolása az Azure SQL Database vagy az SQL Server vagy az Azure SQL Data Warehouse, konfigurálhatja a **SqlSource** a másolási tevékenység meghívni egy tárolt eljárás a forrás-adatbázis használatával adatokat olvasni az **sqlReaderStoredProcedureName** tulajdonság. További információkért tekintse meg a következő összekötő-cikkek: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Szintaxis részletei
A tárolt eljárási tevékenység meghatározásához a JSON formátum a következő:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

A következő táblázat ismerteti ezeket a JSON-tulajdonságokat:

| Tulajdonság                  | Leírás                              | Szükséges |
| ------------------------- | ---------------------------------------- | -------- |
| név                      | A tevékenység neve.                     | Igen      |
| leírás               | Mire használható a tevékenységet leíró szöveg | Nem       |
| type                      | A tárolt eljárási tevékenység, a tevékenység típusa nem **SqlServerStoredProcedure** | Igen      |
| linkedServiceName         | Hivatkozás a **Azure SQL Database** vagy **Azure SQL Data Warehouse** vagy **SQL Server** adat-előállítóban csatolt szolgáltatásként regisztrálva. A csatolt szolgáltatással kapcsolatos további tudnivalókért lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) cikk. | Igen      |
| storedProcedureName       | Adja meg a tárolt eljárás a meghívni kívánt nevét. | Igen      |
| storedProcedureParameters | Adja meg a tárolt eljárás paraméter értékét. Használjon `"param1": { "value": "param1Value","type":"param1Type" }` felelt meg a paraméterértékek és azok típusát, az adatforrás által támogatott. Ha az egyik paraméter null értéket átadni van szüksége, `"param1": { "value": null }` (összes kisbetű). | Nem       |

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkekben talál, amely ismerteti az adatok más módon: 

* [U-SQL Activity](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [A Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce művelethez](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [A Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [A tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)

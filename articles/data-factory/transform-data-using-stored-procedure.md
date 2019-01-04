---
title: Adatok átalakítása a tárolt eljárási tevékenység használatával az Azure Data Factoryban |} A Microsoft Docs
description: Azt ismerteti, hogyan használhatja az SQL Server tárolt eljárási tevékenység egy tárolt eljárást az Azure SQL Database/Data Warehouse a Data Factory-folyamatok meghívásához.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: douglasl
ms.openlocfilehash: 9a724f8e319e652b85941810a6312c35a5036120
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025725"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Adatok átalakítása az Azure Data Factoryban az SQL Server tárolt eljárási tevékenység használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-stored-proc-activity.md)
> * [Aktuális verzió](transform-data-using-stored-procedure.md)

Adat-átalakítási tevékenységeket használ egy Data factoryben [folyamat](concepts-pipelines-activities.md) átalakításához, és a nyers adatok feldolgozása az előrejelzési díjat és az elemzések. A tárolt eljárási tevékenység, amely támogatja a Data Factory az Adatátalakítási tevékenységek egyike. Ez a cikk épül, amely a [adatátalakítás](transform-data.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek a Data Factory általános áttekintése.

> [!NOTE]
> Ha most ismerkedik az Azure Data Factory, olvassa el az [az Azure Data Factory bemutatását](introduction.md) és végezze el az oktatóanyag: [Oktatóanyag: adatok átalakítása](tutorial-transform-data-spark-powershell.md) Ez a cikk elolvasása előtt. 

A tárolt eljárási tevékenység használatával a következő adattárakat egyikét a tárolt eljárás meghívása a vállalati vagy egy Azure virtuális gépen (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- Az SQL Server-adatbázis.  Ha SQL Servert használ, telepítse a saját üzemeltetésű integrációs modul ugyanarra a gépre, amelyen az adatbázis vagy egy külön számítógépen, amely hozzáféréssel rendelkezik az adatbázishoz. Saját üzemeltetésű integrációs modul, egy összetevő, amely kapcsolódik az adatokat a helyszíni és az Azure VM cloud services használatával adatforrások egy biztonságos és felügyelt módon. Lásd: [saját üzemeltetésű integrációs modul](create-self-hosted-integration-runtime.md) részleteivel.

> [!IMPORTANT]
> Ha az adatok másolása az Azure SQL Database vagy SQL Server, konfigurálhatja úgy a **SqlSink** a másolási tevékenység meghívni egy tárolt eljárás használatával a **sqlWriterStoredProcedureName** tulajdonság. A tulajdonság kapcsolatos információkért lásd az alábbi összekötő cikkek: [Az Azure SQL Database](connector-azure-sql-database.md), [az SQL Server](connector-sql-server.md). Tárolt eljárás meghívása közben az adatok másolása az Azure SQL Data Warehouse-bA a másolási tevékenység használatával nem támogatott. De a tárolt eljárási tevékenység használatával egy SQL Data Warehouse a tárolt eljárás meghívása. 
>
> Ha az adatok másolása az Azure SQL Database vagy SQL Server vagy az Azure SQL Data Warehouse, konfigurálhatja a **SqlSource** a másolási tevékenység használatával adatokat olvasni a forrásadatbázis tárolt eljárás meghívása a  **sqlReaderStoredProcedureName** tulajdonság. További információkért tekintse meg a következő összekötő cikkeket: [Az Azure SQL Database](connector-azure-sql-database.md), [az SQL Server](connector-sql-server.md), [az Azure SQL Data warehouse-bA](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Szintaxis részletei
Itt látható a JSON-formátumban, egy tárolt eljárási tevékenység definiálása:

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

A következő táblázat ismerteti a JSON-tulajdonságokról:

| Tulajdonság                  | Leírás                              | Szükséges |
| ------------------------- | ---------------------------------------- | -------- |
| név                      | A tevékenység neve                     | Igen      |
| leírás               | Mire használható a tevékenységet leíró szöveg | Nem       |
| type                      | A tárolt eljárási tevékenység, a tevékenység típusa van **SqlServerStoredProcedure** | Igen      |
| linkedServiceName         | Hivatkozás a **Azure SQL Database** vagy **Azure SQL Data Warehouse** vagy **SQL Server** regisztrálva a Data Factory társított szolgáltatásként. Ezt a társított szolgáltatást kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk. | Igen      |
| storedProcedureName       | Adja meg a tárolt eljárás meghívása nevét. | Igen      |
| storedProcedureParameters | Adja meg a tárolt eljárás paraméterértékeinek értékeit. Használat `"param1": { "value": "param1Value","type":"param1Type" }` átadni a paraméterértékeket, és a a típusuk, az adatforrás által támogatott. Ha át az egyik paraméter null értékű van szüksége, használja a `"param1": { "value": null }` (csupa kisbetű). | Nem       |

## <a name="error-info"></a>Hibainformáció

Tárolt eljárás sikertelen lesz, és hiba adatait adja vissza, ha a hiba adatait közvetlenül a tevékenység kimenete nem rögzíti. Azonban a Data Factory szivattyúk az összes hozzá tartozó tevékenységet a Azure Monitor események. Többek között az eseményeket, hogy a Data Factory az Azure monitornak szivattyúk leküldi a hiba részletei van. Például beállíthat is e-mailes riasztásokhoz származó események. További információ: [riasztás megjelenítése és figyelése az Azure Monitor használatával adat-előállítók](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkek, amelyek bemutatják, hogyan alakíthat át adatokat, egyéb módon: 

* [U-SQL Activity](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce-tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)

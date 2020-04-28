---
title: Adatátalakítás a tárolt eljárási tevékenység használatával
description: A cikk azt ismerteti, hogyan használható SQL Server tárolt eljárás tevékenység egy Data Factory folyamat Azure SQL Database/adattárházában tárolt eljárás meghívásához.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: 57bf653aa3f421ae8897c4be661ceef589fcdc06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418813"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Az adatátalakítást az SQL Server tárolt eljárási tevékenység használatával Azure Data Factory
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-stored-proc-activity.md)
> * [Aktuális verzió](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az Adatátalakítási tevékenységek egy Data Factory folyamaton keresztül alakíthatók át és feldolgozhatók az előrejelzések [és az](concepts-pipelines-activities.md) elemzések során. A tárolt eljárási tevékenység az Data Factory által támogatott átalakítási tevékenységek egyike. Ez [a cikk az](transform-data.md) Adatátalakítási cikkre épül, amely általános áttekintést nyújt az adattranszformációról és a Data Factory támogatott átalakítási tevékenységeiről.

> [!NOTE]
> Ha még nem ismeri a Azure Data Factoryt, olvassa el a [Azure Data Factory bevezetését](introduction.md) , és végezze el az oktatóanyagot: [oktatóanyag: az adatátalakítás](tutorial-transform-data-spark-powershell.md) a cikk elolvasása előtt. 

A tárolt eljárási tevékenységgel egy tárolt eljárást hívhat meg a vállalat vagy egy Azure-beli virtuális gép (VM) alábbi adattárainak egyikében: 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server adatbázis.  Ha SQL Server használ, telepítse a saját üzemeltetésű integrációs modult ugyanarra a gépre, amely az adatbázist üzemelteti, vagy egy különálló gépen, amely hozzáfér az adatbázishoz. A saját üzemeltetésű integrációs modul egy olyan összetevő, amely biztonságos és felügyelt módon csatlakoztatja a helyszíni/Azure-beli virtuális gépen lévő adatforrásokat a Cloud Services szolgáltatással. További részleteket a saját üzemeltetésű [integrációs](create-self-hosted-integration-runtime.md) modulról szóló cikkben talál.

> [!IMPORTANT]
> Az adatok Azure SQL Database vagy SQL Serverba való másolása során beállíthatja, hogy a **SqlSink** a másolási tevékenységben egy tárolt eljárást hívjanak a **sqlWriterStoredProcedureName** tulajdonság használatával. A tulajdonsággal kapcsolatos részletekért tekintse meg a következő összekötő-cikkeket: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Tárolt eljárás meghívása az adatok egy Azure SQL Data Warehouseba másolási tevékenységgel történő másolása során nem támogatott. Azonban a tárolt eljárási tevékenység használatával meghívhat egy tárolt eljárást egy SQL Data Warehouseban. 
>
> Az adatok Azure SQL Database vagy SQL Server vagy Azure SQL Data Warehouseból való másolása esetén a másolási tevékenységben beállíthatja, hogy egy tárolt eljárás meghívja a **SqlSource** a **sqlReaderStoredProcedureName** tulajdonság használatával. További információt a következő összekötő cikkeiben talál: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Szintaxis részletei
Itt látható a tárolt eljárási tevékenység definiálásának JSON-formátuma:

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
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

A következő táblázat ismerteti ezeket a JSON-tulajdonságokat:

| Tulajdonság                  | Leírás                              | Kötelező |
| ------------------------- | ---------------------------------------- | -------- |
| név                      | A tevékenység neve                     | Igen      |
| leírás               | A tevékenység által használt szöveg leírása | Nem       |
| type                      | A tárolt eljárási tevékenységnél a tevékenység típusa **SqlServerStoredProcedure** . | Igen      |
| linkedServiceName         | Hivatkozás **Azure SQL Databasera** , vagy **SQL Server** **Azure SQL Data Warehouse** társított szolgáltatásként regisztrálva van Data Factoryban. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk. | Igen      |
| storedProcedureName       | Adja meg a meghívott tárolt eljárás nevét. | Igen      |
| storedProcedureParameters | A tárolt eljárás paramétereinek értékeinek megadása. A `"param1": { "value": "param1Value","type":"param1Type" }` paraméter értékének és az adatforrás által támogatott típusnak a továbbítására használható. Ha egy paraméternél null értéket kell átadnia, `"param1": { "value": null }` használja a (minden kisbetű) lehetőséget. | Nem       |

## <a name="parameter-data-type-mapping"></a>Paraméter adattípusának leképezése
A paraméterhez megadott adattípus a Azure Data Factory típus, amely a használt adatforrásban lévő adattípushoz van leképezve. Az adattípushoz tartozó leképezések az adatforráshoz az összekötők területen találhatók. Néhány példa

| Adatforrás          | Adattípus-leképezés |
| ---------------------|-------------------|
| Azure SQL Data Warehouse | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Database   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Hiba adatai

Ha egy tárolt eljárás meghiúsul, és a hiba részleteit adja vissza, nem tudja közvetlenül rögzíteni a hibaüzenetet a tevékenység kimenetében. Data Factory pumpálja azonban az összes tevékenységét, hogy Azure Monitor. A szivattyúk Azure Monitor Data Factorya között a hibák részleteit küldi el a rendszer. Beállíthat például e-mailes riasztásokat ezekből az eseményekről. További információ: a riasztás és az adat-előállítók [figyelése Azure monitor használatával](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>További lépések
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Struktúra tevékenysége](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-tevékenység](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach-végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)

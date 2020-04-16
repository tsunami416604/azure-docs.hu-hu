---
title: Adatok átalakítása a Tárolt eljárás tevékenység használatával
description: A cikk azt ismerteti, hogy miként hívhat meg egy tárolt eljárást egy Azure SQL Database/Data Warehouse rendszerében egy Data Factory-folyamatból tárolt eljárás meghívására.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418813"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Adatok átalakítása az SQL Server tárolt eljárási tevékenységének használatával az Azure Data Factoryban
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-stored-proc-activity.md)
> * [Aktuális verzió](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Data [Factory-folyamat](concepts-pipelines-activities.md) adatátalakítási tevékenységeivel átalakíthatja és feldolgozhatja a nyers adatokat előrejelzésekké és elemzési adatokká. A tárolt eljárás tevékenység a Data Factory által támogatott átalakítási tevékenységek egyike. Ez a cikk az [átalakítási adatok](transform-data.md) ról szóló cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a Data Factory támogatott átalakítási tevékenységeiről.

> [!NOTE]
> Ha most jön az Azure Data Factory, olvassa el a Bevezetés az [Azure Data Factory,](introduction.md) és ezt az oktatóanyag: [Oktatóanyag: átalakítása adatok](tutorial-transform-data-spark-powershell.md) elolvasása előtt ezt a cikket. 

A tárolt eljárási tevékenység segítségével meghívhat egy tárolt eljárást a vállalat alábbi adattasztatúráinak egyikében vagy egy Azure virtuális gépen(VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server adatbázis.  SQL Server használata esetén telepítse a saját üzemeltetésű integrációs futásidőt ugyanarra a számítógépre, amelyen az adatbázis található, vagy egy külön gépen, amely hozzáfér az adatbázishoz. A saját üzemeltetésű integrációs futásidejű olyan összetevő, amely biztonságos és felügyelt módon kapcsolja össze a helyszíni/Azure-beli virtuális gépek adatforrásait a felhőszolgáltatásokkal. A részleteket [a saját üzemeltetésű integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben találja.

> [!IMPORTANT]
> Amikor adatokat másol az Azure SQL Database vagy az SQL Server, beállíthatja az **SqlSink** másolási tevékenység meghívására tárolt eljárás használatával **az sqlWriterStoredProcedureName** tulajdonság. A tulajdonságról a következő összekötőcikkekben talál: [Azure SQL Database](connector-azure-sql-database.md), SQL [Server](connector-sql-server.md). Nem támogatott a tárolt eljárás meghívása az Azure SQL Data Warehouse-ba másolása közben egy másolási tevékenység használatával. De használhatja a tárolt eljárás tevékenység meghívására egy tárolt eljárást egy SQL Data Warehouse. 
>
> Amikor adatokat másol az Azure SQL Database vagy az SQL Server vagy az Azure SQL Data Warehouse, beállíthatja **sqlSource** másolási tevékenység meghívására tárolt eljárást adatok at a forrás adatbázisból az **sqlReaderStoredProcedureName** tulajdonság használatával. További információt a következő összekötő cikkekben talál: [Azure SQL Database](connector-azure-sql-database.md), SQL [Server](connector-sql-server.md), Azure SQL [Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Szintaxis részletei
Itt van a JSON formátum a tárolt eljárási tevékenység meghatározásához:

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

Az alábbi táblázat a következő JSON-tulajdonságokat ismerteti:

| Tulajdonság                  | Leírás                              | Kötelező |
| ------------------------- | ---------------------------------------- | -------- |
| név                      | A tevékenység neve                     | Igen      |
| leírás               | A tevékenységet leíró szöveg | Nem       |
| type                      | Tárolt eljárási tevékenység esetén a tevékenység típusa **SqlServerStoredProcedure** | Igen      |
| linkedServiceName         | Hivatkozás az **Azure SQL Database** vagy az Azure SQL Data **Warehouse** vagy az **SQL Server** regisztrált, mint egy kapcsolt szolgáltatás data factory. A csatolt szolgáltatásról a [Csatolt szolgáltatások számítási cikkében](compute-linked-services.md) olvashat. | Igen      |
| storedProcedureName       | Adja meg a meghívni kívánt tárolt eljárás nevét. | Igen      |
| storedProcedureParameters | Adja meg a tárolt eljárásparamétereinek értékeit. A `"param1": { "value": "param1Value","type":"param1Type" }` paraméterértékek és az adatforrás által támogatott típusuk átadhatók. Ha null értéket kell átadnia `"param1": { "value": null }` egy paraméterhez, használja (minden kisbetű). | Nem       |

## <a name="parameter-data-type-mapping"></a>Paraméteradattípus-hozzárendelés
A paraméterhez megadott adattípus az Azure Data Factory-típus, amely leképezi a használt adatforrás adattípusát. Az adatforrás adattípus-leképezéseit az összekötők területén találhatja meg. Néhány példa a

| Adatforrás          | Adattípus-leképezés |
| ---------------------|-------------------|
| Azure SQL Data Warehouse | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Database   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Hibainformáció

Ha egy tárolt eljárás sikertelen, és hibarészleteket ad vissza, a hibaadatokat nem rögzítheti közvetlenül a tevékenység kimenetében. A Data Factory azonban az Azure Monitorba pumpálja az összes tevékenységfuttatási eseményét. A Data Factory által az Azure Monitorba szivattyúzott események között hibarészleteket hoz le. Beállíthatja például az ilyen eseményekből érkező e-mail-értesítéseket. További információ: [Riasztás és figyelés i adatgyárak az Azure Monitor használatával.](monitor-using-azure-monitor.md)

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben elmagyarázhatja, hogyan alakíthatja át más módon az adatokat: 

* [U-SQL tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Sertés tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming tevékenység](transform-data-using-hadoop-streaming.md)
* [Szikratevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Gépi tanulási Bach végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)

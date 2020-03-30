---
title: Másolási tevékenység hibatűrése az Azure Data Factoryban
description: Megtudhatja, hogyan adhat hozzá hibatűrést a másolási tevékenységhez az Azure Data Factoryban az inkompatibilis sorok kihagyásával.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: 42c637839172dab09a8721a93a67785a748afd2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75708902"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Másolási tevékenység hibatűrése az Azure Data Factoryban
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuális verzió](copy-activity-fault-tolerance.md)

Az Azure Data Factory másolási tevékenysége két lehetőséget kínál az inkompatibilis sorok kezelésére a forrás- és fogadóadattárak közötti másoláskor:

- A másolási tevékenység megszakadhat, és sikertelen lehet, ha nem kompatibilis adatokat (alapértelmezett viselkedés) tapasztal.
- Az összes adat másolása hibatűrés hozzáadásával és inkompatibilis adatsorok kihagyásával folytatódhat. Emellett naplózhatja a nem kompatibilis sorokat az Azure Blob storage vagy az Azure Data Lake Store. Ezután megvizsgálhatja a naplót a hiba okának megismeréséhez, javíthatja az adatforráson lévő adatokat, majd próbálkozzon újra a másolási tevékenységgel.

## <a name="supported-scenarios"></a>Támogatott esetek
A Másolási tevékenység három forgatókönyvet támogat a nem kompatibilis adatok észlelésére, kihagyására és naplózására:

- **Inkompatibilitás a forrásadattípus és a fogadó natív típusa között.** 

    Például: Adatok másolása egy CSV-fájlból a Blob storage-ban egy SQL-adatbázisba egy sémadefinícióval, amely három INT-típusú oszlopot tartalmaz. A numerikus adatokat (például 123 456 789) tartalmazó CSV-fájlsorok másolása sikeresen bekerül a fogadótárolóba. A nem numerikus értékeket (például 123 456) tartalmazó sorokat azonban a rendszer nem kompatibilisként észleli, és kihagyja.

- **A forrás és a fogadó közötti oszlopok számának eltérése.**

    Például: Adatok másolása egy CSV-fájlból a Blob storage-ban egy SQL-adatbázisba egy hat oszlopot tartalmazó sémadefinícióval. A hat oszlopot tartalmazó CSV-fájlsorok másolása sikeresen bekerül a fogadótárolóba. A hatnál több oszlopot tartalmazó CSV-fájlsorok nem kompatibilisként észlelhetők, és kimaradnak.

- **Elsődleges kulcsmegsértése az SQL Server/Azure SQL Database/Azure Cosmos DB írásakor.**

    Például: Adatok másolása SQL-kiszolgálóról SQL-adatbázisba. Az elsődleges kulcs definiálva van a fogadó SQL-adatbázisában, de ilyen elsődleges kulcs nincs definiálva a forrás SQL-kiszolgálón. A forrásban létező duplikált sorok nem másolhatók a fogadóba. A Másolási tevékenység csak a forrásadatok első sorát másolja a fogadóba. A következő forrássorok, amelyek tartalmazzák a duplikált elsődleges kulcs értékét, nem kompatibilisként észlelhetők, és kimaradnak.

>[!NOTE]
>- Az SQL Data Warehouse-ba a PolyBase használatával történő adatbetöltéséhez konfigurálja a PolyBase natív hibatűrési beállításait úgy, hogy a másolási tevékenységben a "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" utasítással adja meg az elutasítási házirendeket. Továbbra is engedélyezheti a PolyBase inkompatibilis sorok blobba vagy ADLS-be való átirányítását az alábbiak szerint.
>- Ez a funkció nem alkalmazható, ha a másolási tevékenység az [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)meghívására van konfigurálva.
>- Ez a szolgáltatás nem alkalmazható, ha a másolási tevékenység úgy van beállítva, hogy [egy SQL-fogadóból tárolt eljárást hívjanak meg.](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink)

## <a name="configuration"></a>Konfiguráció
A következő példa egy JSON-definíciót tartalmaz a másolási tevékenység nem kompatibilis sorainak kihagyásához:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Itt adható meg, hogy kihagyja-e a nem kompatibilis sorokat a másolás során. | True (Igaz)<br/>False (alapértelmezett) | Nem
redirectInkompatibilisRowSettings | Tulajdonságok csoportja, amely akkor adható meg, ha naplózni szeretné a nem kompatibilis sorokat. | &nbsp; | Nem
linkedServiceName | Az Azure [Storage](connector-azure-blob-storage.md#linked-service-properties) vagy az [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) összekapcsolt szolgáltatása a kihagyott sorokat tartalmazó napló tárolásához. | A naplófájl `AzureStorage` `AzureDataLakeStore` tárolásához használni kívánt példányra hivatkozó vagy típuskapcsolt szolgáltatás neve. | Nem
path | A kihagyott sorokat tartalmazó naplófájl elérési útja. | Adja meg a nem kompatibilis adatok naplózásához használni kívánt elérési utat. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót. | Nem

## <a name="monitor-skipped-rows"></a>Kihagyott sorok figyelése
A másolási tevékenység futásának befejezése után láthatja a kihagyott sorok számát a másolási tevékenység kimenetében:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Ha a nem kompatibilis sorok naplózására konfigurálja, a naplófájlt ezen az elérési úton találja: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

A naplófájlok csak a csv fájlok lehetnek. A kihagyott eredeti adatokat szükség esetén vesszővel naplózza a rendszer oszlophatárolóként. A naplófájlban lévő eredeti forrásadatokhoz további két "ErrorCode" és "ErrorMessage" oszlopot adunk hozzá, ahol láthatja az inkompatibilitás kiváltó okát. Az ErrorCode és errorMessage idézőjelek. 

A naplófájl tartalmára példa a következő:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>További lépések
Lásd a többi Másolási tevékenység cikkeket:

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)
- [Tevékenységteljesítmény másolása](copy-activity-performance.md)



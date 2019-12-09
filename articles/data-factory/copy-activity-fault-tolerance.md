---
title: Másolási tevékenység hibatűrése az Azure Data Factoryban
description: A nem kompatibilis sorok kihagyásával megtudhatja, hogyan adhat hozzá hibatűrést Azure Data Factory másolási tevékenységhez.
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
ms.openlocfilehash: b874c0dcc1f394866e74b45e6bc335a25ce24499
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930017"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Másolási tevékenység hibatűrése az Azure Data Factoryban
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuális verzió](copy-activity-fault-tolerance.md)

A Azure Data Factory másolási tevékenysége két módszert kínál a nem kompatibilis sorok kezelésére, amikor Adatmásolást végez a forrás-és fogadó adattárolók között:

- A másolási tevékenység megszakítható, ha nem kompatibilis adat észlelhető (alapértelmezett viselkedés).
- Az összes adattal folytathatja a hibatűrés hozzáadásával és a nem kompatibilis adatsorok kihagyásával. Emellett naplózhatja a nem kompatibilis sorokat az Azure Blob Storage-ban vagy a Azure Data Lake Storeban. Ezután megvizsgálhatja a naplót, hogy megismerje a hiba okát, javítsa ki az adatforráson lévő információkat, majd próbálja megismételni a másolási tevékenységet.

## <a name="supported-scenarios"></a>Támogatott esetek
A másolási tevékenység három olyan forgatókönyvet támogat, amelyek nem kompatibilis adatok észlelésére, kihagyására és naplózására alkalmasak:

- Nem **kompatibilis a forrás adattípusa és a fogadó natív típusa között**. 

    Példa: adatok másolása CSV-fájlból a blob Storage-ból egy SQL Database-be egy olyan séma-definícióval, amely három INT típusú oszlopot tartalmaz. A numerikus adatértékeket tartalmazó CSV-fájlok sorai, például az 123 456 789-es sorok sikeres másolása a fogadó tárolóba. Azonban a nem numerikus értékeket tartalmazó sorok, például az 123 456, az ABC nem kompatibilisként és kimarad.

- A **forrás és a fogadó közötti oszlopok száma nem egyezik**.

    Például: adatok másolása egy CSV-fájlból a blob Storage-ban egy SQL Database-be olyan séma-definícióval, amely hat oszlopot tartalmaz. A hat oszlopot tartalmazó CSV-fájl sorainak másolása sikeresen megtörtént a fogadó tárolóba. A több vagy kevesebb, mint hat oszlopot tartalmazó CSV-fájl sorai nem kompatibilisként észlelhetők, és a rendszer kihagyja őket.

- **Az elsődleges kulcs megsértése SQL Server/Azure SQL Database/Azure Cosmos db írásakor**.

    Például: adatok másolása egy SQL-kiszolgálóról egy SQL-adatbázisba. Elsődleges kulcs van definiálva a fogadó SQL-adatbázisban, de nincs ilyen elsődleges kulcs definiálva a forrás SQL-kiszolgálón. A forrásban található duplikált sorok nem másolhatók a fogadóba. A másolási tevékenység csak a forrásadatok első sorát másolja a fogadóba. A duplikált elsődleges kulcs értékét tartalmazó következő forrásfájlokat inkompatibilisként észleli a rendszer, és kihagyja.

>[!NOTE]
>- Az adatoknak a SQL Data Warehouse használatával történő betöltéséhez a "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" beállítással állítsa be a natív hibatűrési beállításokat úgy, hogy a másolási tevékenységben megadta a "visszautasítási házirendek" beállítást. A lent látható módon továbbra is engedélyezheti a "Base inkompatibilis" sorok átirányítását a blob-vagy ADLS.
>- Ez a funkció nem érvényes, ha a másolási tevékenység úgy van konfigurálva, hogy meghívja az [Amazon vöröseltolódásának eltávolítását](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Ez a funkció nem érvényes, ha a másolási tevékenység konfigurálva van egy tárolt eljárás meghívására [egy SQL-](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink)fogadóból.

## <a name="configuration"></a>Konfiguráció
A következő példa egy JSON-definíciót biztosít a nem kompatibilis sorok másolási tevékenységbe való kihagyásának konfigurálásához:

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

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Meghatározza, hogy a nem kompatibilis sorok kihagyása a másolás során vagy sem. | Igaz<br/>False (alapértelmezett) | Nem
redirectIncompatibleRowSettings | A nem kompatibilis sorok naplózásához megadható tulajdonságok csoportja. | &nbsp; | Nem
linkedServiceName | Az [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) vagy [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) társított szolgáltatása, amely a kihagyott sorokat tartalmazó naplót tárolja. | Egy `AzureStorage` vagy `AzureDataLakeStore` típusú társított szolgáltatás neve, amely arra a példányra hivatkozik, amelyet a naplófájl tárolására kíván használni. | Nem
elérési út | A kihagyott sorokat tartalmazó naplófájl elérési útja. | Itt adhatja meg a nem kompatibilis adatfájlok naplózásához használni kívánt elérési utat. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót. | Nem

## <a name="monitor-skipped-rows"></a>A kihagyott sorok figyelése
A másolási tevékenység futtatása után a másolási tevékenység kimenetében a kihagyott sorok száma látható:

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
Ha úgy konfigurálja, hogy naplózza a nem kompatibilis sorokat, a naplófájlt a következő elérési úton találja: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

A naplófájlok csak a CSV-fájlok lehetnek. Ha szükséges, a kihagyott eredeti adatértékek vesszővel elválasztott oszlopként lesznek naplózva. A naplófájlban az eredeti forrásadatok további két oszlopát ("ErrorCode" és "ErrorMessage") is hozzáadjuk, ahol megtekintheti az inkompatibilitás kiváltó okát. A ErrorCode és a ErrorMessage idézőjelek alapján történik. 

A naplófájl tartalmának példája a következő:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Következő lépések
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Másolási tevékenység teljesítménye](copy-activity-performance.md)



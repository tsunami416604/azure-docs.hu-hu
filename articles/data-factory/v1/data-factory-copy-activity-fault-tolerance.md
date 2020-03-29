---
title: Hibatűrés hozzáadása az Azure Data Factory Copy Activity szolgáltatásban a nem kompatibilis sorok kihagyásával
description: Megtudhatja, hogy miként adhat hozzá hibatűrést az Azure Data Factory Copy Activity szolgáltatásban a nem kompatibilis sorok másolás közbeni kihagyásával
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8ffaee75154fd5fe025bdb683c89f16799d6e86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926152"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Hibatűrés hozzáadása a másolási tevékenységhez a nem kompatibilis sorok kihagyásával

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-copy-activity-fault-tolerance.md)
> * [2-es verzió (aktuális verzió)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [Data Factory másolási tevékenységének hibatűrését.](../copy-activity-fault-tolerance.md)

Az Azure Data Factory [Copy Activity](data-factory-data-movement-activities.md) kétféle képpen kezelhető a forrás- és a fogadó adattárak közötti adatátárak másolásakor:

- A másolási tevékenység megszakadhat, és sikertelen lehet, ha nem kompatibilis adatokat (alapértelmezett viselkedés) tapasztal.
- Az összes adat másolása hibatűrés hozzáadásával és inkompatibilis adatsorok kihagyásával folytatódhat. Emellett naplózhatja a nem kompatibilis sorokat az Azure Blob storage-ban. Ezután megvizsgálhatja a naplót a hiba okának megismeréséhez, javíthatja az adatforráson lévő adatokat, majd próbálkozzon újra a másolási tevékenységgel.

## <a name="supported-scenarios"></a>Támogatott esetek
A Másolási tevékenység három forgatókönyvet támogat a nem kompatibilis adatok észlelésére, kihagyására és naplózására:

- **Inkompatibilitás a forrás adattípusa és a fogadó natív típusa között.**

    Például: Adatok másolása egy CSV-fájlból a Blob storage-ban egy SQL-adatbázisba egy sémadefinícióval, amely három **INT-típusú** oszlopot tartalmaz. A CSV-fájlsorok, amelyek numerikus adatokat tartalmaznak, például `123,456,789` a rendszer sikeresen átmásolja a fogadótárolóba. A nem numerikus értékeket tartalmazó sorok `123,456,abc` azonban nem kompatibilisként észlelhetők, és kimaradnak.

- **Eltérés az oszlopok számában a forrás és a fogadó között.**

    Például: Adatok másolása egy CSV-fájlból a Blob storage-ban egy SQL-adatbázisba egy hat oszlopot tartalmazó sémadefinícióval. A hat oszlopot tartalmazó CSV-fájlsorok másolása sikeresen bekerül a fogadótárolóba. A rendszer a legfeljebb hat oszlopot tartalmazó CSV-fájlsorokat nem kompatibilisként észleli, és kihagyja.

- **Elsődleges kulcs megsértése az SQL Serverre/Azure SQL Database-be/Azure Cosmos DB-be történő íráskor**

    Például: Adatok másolása SQL-kiszolgálóról SQL-adatbázisba. Az elsődleges kulcs definiálva van a fogadó SQL-adatbázisában, de ilyen elsődleges kulcs nincs definiálva a forrás SQL-kiszolgálón. A forrásban létező duplikált sorok nem másolhatók a fogadóba. A Másolási tevékenység csak a forrásadatok első sorát másolja a fogadóba. A következő forrássorok, amelyek tartalmazzák a duplikált elsődleges kulcs értékét, nem kompatibilisként észlelhetők, és kimaradnak.

>[!NOTE]
>Ez a funkció nem alkalmazható, ha a másolási tevékenység külső adatbetöltési mechanizmus, például [az Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) vagy az Amazon [Redshift Unload](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift)meghívására van konfigurálva. Az SQL Data Warehouse-ba a PolyBase használatával történő adatok betöltéséhez használja a PolyBase natív hibatűrési támogatását a "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" másolási tevékenységben való megadásával.

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Engedélyezze a nem kompatibilis sorok kihagyását másolás közben vagy sem. | True (Igaz)<br/>False (alapértelmezett) | Nem |
| **redirectInkompatibilisRowSettings** | Tulajdonságok csoportja, amely akkor adható meg, ha naplózni szeretné a nem kompatibilis sorokat. | &nbsp; | Nem |
| **linkedServiceName** | Az Azure Storage összekapcsolt szolgáltatása a kihagyott sorokat tartalmazó napló tárolására. | Egy [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) csatolt szolgáltatás neve, amely a naplófájl tárolására használni kívánt tárolási példányra hivatkozik. | Nem |
| **Elérési út** | A kihagyott sorokat tartalmazó naplófájl elérési útja. | Adja meg a blob tárolási elérési útját, amelyet a nem kompatibilis adatok naplózásához kíván használni. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót. | Nem |

## <a name="monitoring"></a>Figyelés
A másolási tevékenység futásának befejezése után a figyelési szakaszban láthatja a kihagyott sorok számát:

![Kihagyott inkompatibilis sorok figyelője](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Ha az inkompatibilis sorok naplózására konfigurálja, a naplófájlt `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` ezen az elérési úton találja: A naplófájlban láthatja a kihagyott sorokat és az inkompatibilitás okát.

Mind az eredeti adatok, mind a megfelelő hiba a fájlba kerül. A naplófájl tartalmára példa a következő:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az Azure Data Factory Copy Activity szolgáltatásról, olvassa el az [Adatok áthelyezése a Másolási tevékenység használatával című témakört.](data-factory-data-movement-activities.md)

---
title: "A másolási tevékenység során az Azure Data Factory hibatűrés |} Microsoft Docs"
description: "Ismerje meg a nem kompatibilis sorok kihagyása másolása az Azure Data Factory tevékenység hibatűrést hozzáadása."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: b7ca3f6da104da16bd64db042a2a13f593a393b6
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>A másolási tevékenység során az Azure Data Factory hibatűrés
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-copy-activity-fault-tolerance.md)
> * [2. verzió – Előzetes verzió](copy-activity-fault-tolerance.md)

A másolási tevékenység során az Azure Data Factory szeretné kezelni a forrás és a fogadó adattároló közötti másolás nem kompatibilis sorok két lehetőséget kínál:

- Megszakítja, és sikertelen lesz a másolási tevékenység nem kompatibilis adat esetén történt (alapértelmezés).
- Továbbra is másolhatja az összes adat hozzáadásával a hibatűrést, és nem kompatibilis adat sorok kihagyása. Ezenkívül bejelentkezhet a nem kompatibilis sorok Azure Blob-tároló vagy az Azure Data Lake Store. Ismerje meg a hiba okát, javítsa ki az adatokat az adatforrás, és ismételje meg a másolási tevékenység napló majd ellenőrizheti.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [tevékenység hibatűrést másolja a V1](v1/data-factory-copy-activity-fault-tolerance.md).


 ## <a name="supported-scenarios"></a>Támogatott esetek
Másolási tevékenység észlelésekor, kihagyása és naplózási adatok nem kompatibilis három forgatókönyveket teszi lehetővé:

- **A forrás adattípus és a fogadó natív típusa között kompatibilitási**. 

    Például: adatok másolása az a Blob-tároló CSV-fájl az SQL-adatbázis egy séma-definícióval, amely három INT típusú oszlopot tartalmaz. Numerikus adatok, például 123,456,789 tartalmazó CSV-fájl sorok sikeresen másolta a fogadó tárolójába. Azonban 123,456, például a nem numerikus értékeket tartalmazó sorok abc észlelhetők a nem kompatibilis, és kimarad.

- **A forrás- és a fogadó között oszlopok száma nem egyezik**.

    Például: adatok másolása az a Blob-tároló CSV-fájl az SQL-adatbázis egy séma-definícióval, amely hat oszlopokat tartalmaz. A fogadó tároló hat oszlopokat tartalmazó CSV-fájl sorok sikeresen lesz másolva. A több vagy kevesebb, mint hat oszlopot tartalmazó CSV-fájl sorok észlelhetők a nem kompatibilis, és kimarad.

- **Elsődleges kulcs megsértése egy relációs adatbázis írásakor**.

    Például: adatok másolása az SQL server az SQL-adatbázis. A fogadó SQL-adatbázis elsődleges kulcs van definiálva, de nincs ilyen elsődleges kulcs van definiálva a forrás SQL-kiszolgálón. A duplikált sorokat, amely szerepel a forrás nem lehet másolni a fogadó. Másolási tevékenység során az adatok csak az első sor a fogadó másolja. Ismétlődő elsődleges kulcs értéke a következő adatforrás a sorokat a rendszer észleli a rendszer nem kompatibilis, és kimarad a.

>[!NOTE]
>Ez a funkció nem vonatkozik a másolási tevékenység során konfigurált-mechanizmus többek között külső Adatbetöltési meghívása [Azure SQL Data Warehouse PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) vagy [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift). Adatok betöltése az SQL Data Warehouse PolyBase használatával, használja a PolyBase által natív hiba tolerancia támogatási megadásával "[kapcsolódó polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" a másolási tevékenység.

## <a name="configuration"></a>Konfiguráció
A következő példa egy JSON-definícióból, a másolási tevékenység nem kompatibilis sorok kihagyása konfigurálása biztosítja:

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
enableSkipIncompatibleRow | Megadja, hogy nem kompatibilis sorok kihagyása másolása során, vagy nem. | True (Igaz)<br/>Hamis (alapértelmezés) | Nem
redirectIncompatibleRowSettings | Egy csoport, amely tulajdonságok meg, ha azt szeretné, a nem kompatibilis sorok bejelentkezni. | &nbsp; | Nem
linkedServiceName | A társított szolgáltatás [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) vagy [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) a naplót, hogy a rendszer kihagyta sorokat tartalmaz-e tárolni. | A neve egy `AzureStorage` vagy `AzureDataLakeStore` írja be a társított szolgáltatás, amely ahhoz a példányhoz, a naplófájl tárolási használni kívánt hivatkozik. | Nem
elérési út | A rendszer kihagyta sorokat tartalmaz-e a naplófájl elérési útja | Adja meg az útvonalat, amelyet a nem kompatibilis adatokat naplózhatnak használni kívánt. Ha nem ad meg egy elérési utat, a szolgáltatás létrehoz egy tárolót. | Nem

## <a name="monitor-skipped-rows"></a>A rendszer kihagyta sorok figyelése
A másolási tevékenység során futtatása után megtekintheti a másolási tevékenység kimenet kihagyott sorok száma:

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
Ha a nem kompatibilis sorok bejelentkezni konfigurálja, a naplófájl ezen az elérési úton található: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

A naplófájlok csak a csv-fájlok lehetnek. Az eredeti adatokkal, hogy a rendszer kihagyja kerül naplózásra vesszővel válassza el egymástól elválasztó oszlop szükség esetén. Jelenleg két további oszlopok hozzáadása "ErrorCode" és "ErrorMessage" a kiegészítő az eredeti forrás adatokat a naplófájlban, ahol láthatja a legfelső szintű a kompatibilitási okát. Az ErrorCode és hibaüzenet fog szerepelhetnek idézőjelek között dupla idézőjelek közé. 

A napló fájl tartalma például a következőképpen történik:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>További lépések
A másolási tevékenység cikkekben talál:

- [Tevékenység – áttekintés](copy-activity-overview.md)
- [A másolási tevékenység](copy-activity-performance.md)



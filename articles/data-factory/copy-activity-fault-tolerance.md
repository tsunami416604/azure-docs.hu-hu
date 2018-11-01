---
title: Hibatűrés másolási tevékenységgel az Azure Data Factoryban |} A Microsoft Docs
description: További információ a másolási tevékenység az Azure Data Factory által az inkompatibilis sorok kihagyása hibatűrés hozzáadása.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: jingwang
ms.openlocfilehash: 3f207cdb3af3f7e328cd5843053240bbbe15980e
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418343"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Az Azure Data Factory másolási tevékenysége a hibatűrés
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuális verzió](copy-activity-fault-tolerance.md)

A másolási tevékenység az Azure Data Factory kezeli az inkompatibilis sorok, példatípust az adatok forrásaként és fogadó adattár közötti adatáthelyezéshez két lehetőséget kínál:

- Megszakítja, és nem sikerül a másolási tevékenység, amikor nem kompatibilis adatok során (alapértelmezés).
- Továbbra is másolhatja az összes, az adatok hozzáadásával a hibatűrést, és nem kompatibilis adatokat sorok kihagyása. Emellett az inkompatibilis sorok bejelentkezhet az Azure Blob storage vagy az Azure Data Lake Store. Ismerje meg a hiba okát, javítsa ki az adatokat az adatforrásban, és ismételje meg a másolási tevékenység a napló majd ellenőrizheti.

## <a name="supported-scenarios"></a>Támogatott esetek
A másolási tevékenység, a rendszer kihagyja, és nem kompatibilis adatok naplózása három forgatókönyv támogatja:

- **Az adattípusra és a fogadó nativní typ között inkompatibilitás**. 

    Példa: adatok másolása Blob storage-ban CSV-fájlból egy SQL Database egy séma-definícióval, amely három INT típusú oszlopokat tartalmaz. A numerikus adatok, például 123,456,789 tartalmazó CSV-fájl sorok sikeresen lesz másolva a fogadótároló. Azonban 123,456, például a nem numerikus értékeket tartalmazó sorok abc észlelhetők a nem kompatibilis, és a rendszer kihagyja.

- **A forrás és a fogadó oszlopok száma nem egyezik**.

    Példa: adatok másolása Blob storage-ban CSV-fájlból egy SQL Database egy séma-definícióval, amely hat oszlopokat tartalmazza. A hat oszlopokat tartalmazó CSV-fájl sorok sikeresen lesz másolva a fogadótároló. Több vagy kevesebb, mint 6 oszlopokat tartalmazó CSV-fájl sorok észlelhetők a nem kompatibilis, és a rendszer kihagyja.

- **Elsődleges kulcsok protokollmegsértési írásakor az SQL Server vagy az Azure SQL Database vagy az Azure Cosmos DB**.

    Példa: adatok másolása az SQL-kiszolgáló SQL-adatbázishoz. A fogadó SQL database-ben meg van adva egy elsődleges kulcs, de nincs ilyen elsődleges kulcs van definiálva a forrás SQL-kiszolgálón. A duplikált sorokat, amelyek szerepelnek a forrás nem lehet másolni a fogadó. Másolási tevékenység az adatok csak az első sor mobilkészülékét a lefolyóba másol. Az ismétlődő elsődleges kulcs értékét a következő forrás a sorokat észlelhetők a nem kompatibilis, és a rendszer kihagyja.

>[!NOTE]
>- Az adatok betöltése az SQL Data Warehouse-bA a PolyBase, beállításokat kell megadni a PolyBase a natív tartalék tolerancia visszautasítási szabályzatokat megadásával "[kapcsolódó polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" a másolási tevékenység. Átirányítás a PolyBase inkompatibilis sorok Blobhoz vagy a szokásos módon, ahogy az alábbi ADLS továbbra is engedélyezheti.
>- Ez a funkció nem vonatkozik a másolási tevékenység meghívásához konfigurálásakor [Amazon Redshift eltávolítása](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).


## <a name="configuration"></a>Konfiguráció
Az alábbi példában egy JSON-definíciót a másolási tevékenység az inkompatibilis sorok kihagyása konfigurálása biztosítja:

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
enableSkipIncompatibleRow | Megadja, hogy az inkompatibilis sorok kihagyása másolása során, vagy sem. | True (Igaz)<br/>FALSE (alapértelmezett) | Nem
redirectIncompatibleRowSettings | Egy csoport tulajdonságok, amelyek a megadott való bejelentkezéshez az inkompatibilis sorok. | &nbsp; | Nem
linkedServiceName | A társított szolgáltatás [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) vagy [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) tárolja a naplót, hogy a rendszer kihagyta sorokat tartalmaz-e. | Neve az `AzureStorage` vagy `AzureDataLakeStore` írja be a társított szolgáltatás, amely a napló tárolásához használni kívánt szolgáltató példányt jelenti. | Nem
elérési út | A rendszer kihagyta sorokat tartalmaz-e a naplófájl elérési útja. | Adja meg, hogy a nem kompatibilis adatok naplózása használni kívánt elérési. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót az Ön számára. | Nem

## <a name="monitor-skipped-rows"></a>A rendszer kihagyta sorok figyelése
A másolási tevékenység futtatási befejeződése után láthatja a kimenetben látható a másolási tevékenység kihagyva sorok száma:

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
Ha konfigurálja az inkompatibilis sorok bejelentkezni, Észreveheti, hogy ezt az elérési utat a naplófájl: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

A naplófájlok a csv-fájlokat csak lehet. Kihagyta az eredeti adatok bekerülnek a naplóba vesszővel válassza el egymástól oszlophatároló, ha szükséges. A Microsoft két további oszlopok hozzáadása "ErrorCode" és "ErrorMessage" további az eredeti forrás adatok naplófájlban, ahol megtekintheti a legfelső szintű a kompatibilitási okát. Az ErrorCode és ErrorMessage ajánlatokban idézőjelekkel együtt. 

A log fájl tartalma például a következőképpen történik:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>További lépések
A másolási tevékenység egyéb cikkekben talál:

- [Másolási tevékenység áttekintése](copy-activity-overview.md)
- [Másolási tevékenység](copy-activity-performance.md)



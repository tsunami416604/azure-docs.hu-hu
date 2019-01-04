---
title: Hibatűrés hozzáadása az Azure Data Factory másolási tevékenység által inkompatibilis sorok kihagyása |} A Microsoft Docs
description: A hibatűrés felvétele az Azure Data Factory másolási tevékenység által másolása során inkompatibilis sorok kihagyása
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 44703a2547f685aaa0b6c583c39c08ef6a570d56
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016528"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Adja hozzá a hibatűrés a másolási tevékenység által inkompatibilis sorok kihagyása

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-copy-activity-fault-tolerance.md)
> * [2-es verzió (aktuális verzió)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [hibatűrés a Data Factory másolási tevékenység](../copy-activity-fault-tolerance.md).

Az Azure Data Factory [másolási tevékenység](data-factory-data-movement-activities.md) két különböző módon kezelni a forrás- és fogadó adattárak közötti másolást inkompatibilis sorok kínálja:

- Megszakítja, és nem sikerül a másolási tevékenység, amikor nem kompatibilis adatok során (alapértelmezés).
- Továbbra is másolhatja az összes, az adatok hozzáadásával a hibatűrést, és nem kompatibilis adatokat sorok kihagyása. Emellett az inkompatibilis sorok bejelentkezhet az Azure Blob storage-ban. Ismerje meg a hiba okát, javítsa ki az adatokat az adatforrásban, és ismételje meg a másolási tevékenység a napló majd ellenőrizheti.

## <a name="supported-scenarios"></a>Támogatott esetek
A másolási tevékenység, a rendszer kihagyja, és nem kompatibilis adatok naplózása három forgatókönyv támogatja:

- **Az adattípusra és a fogadó nativní typ között inkompatibilitás**

    Példa: Adatok másolása Blob storage-ban CSV-fájlból egy SQL Database egy séma-definícióval, amely tartalmazza a három **INT** típusú oszlop. Például numerikus adatokat tartalmazó CSV-fájl sorok `123,456,789` lesz másolva a fogadótároló sikeresen megtörtént. Azonban a sorok tartalmaznak, nem numerikus értékeket, mint például `123,456,abc` inkompatibilis adatforrásként vannak észlelve és a rendszer kihagyja.

- **A forrás és a fogadó oszlopok száma nem egyezik**

    Példa: Adatok másolása Blob storage-ban CSV-fájlból egy SQL Database egy séma-definícióval, amely hat oszlopokat tartalmazza. A hat oszlopokat tartalmazó CSV-fájl sorok sikeresen lesz másolva a fogadótároló. Több vagy kevesebb, mint 6 oszlopokat tartalmazó CSV-fájl sorok észlelhetők a nem kompatibilis, és a rendszer kihagyja.

- **Elsődleges kulcsok protokollmegsértési írásakor az SQL Server vagy az Azure SQL Database vagy az Azure Cosmos DB**

    Példa: Adatmásolás az SQL-kiszolgáló SQL-adatbázishoz. A fogadó SQL database-ben meg van adva egy elsődleges kulcs, de nincs ilyen elsődleges kulcs van definiálva a forrás SQL-kiszolgálón. A duplikált sorokat, amelyek szerepelnek a forrás nem lehet másolni a fogadó. Másolási tevékenység az adatok csak az első sor mobilkészülékét a lefolyóba másol. Az ismétlődő elsődleges kulcs értékét a következő forrás a sorokat észlelhetők a nem kompatibilis, és a rendszer kihagyja.

>[!NOTE]
>Ez a funkció nem vonatkozik a másolási tevékenység külső adatok betöltése, beleértve a mechanizmus meghívásához konfigurálásakor [Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) vagy [Amazon Redshift eltávolítása](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Adatok betöltése a PolyBase az SQL Data Warehouse-ba, használja a PolyBase a natív tartalék tolerancia támogatási megadásával "[kapcsolódó polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" a másolási tevékenység.

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Vagy nem engedélyezi az inkompatibilis sorok kihagyása másolása során. | True (Igaz)<br/>FALSE (alapértelmezett) | Nem |
| **redirectIncompatibleRowSettings** | Egy csoport tulajdonságok, amelyek a megadott való bejelentkezéshez az inkompatibilis sorok. | &nbsp; | Nem |
| **linkedServiceName** | Tárolja a naplót, hogy a rendszer kihagyta sorokat tartalmaz-e az Azure Storage társított szolgáltatás. | Neve az [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) társított szolgáltatás, amely a napló tárolásához használni kívánt tárolási szolgáltató példányt jelenti. | Nem |
| **path** | A rendszer kihagyta sorokat tartalmaz-e a naplófájl elérési útja. | Adja meg a Blob elérési útja, amely a nem kompatibilis adatok naplózása használni kívánt. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót az Ön számára. | Nem |

## <a name="monitoring"></a>Figyelés
A másolási tevékenység futtatási befejeződése után láthatja a figyelés szakaszban, a rendszer kihagyta sorok száma:

![A figyelő az inkompatibilis sorok kihagyva](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Konfigurálja az inkompatibilis sorok bejelentkezni, ha a naplófájl az elérési úton található: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` A naplófájlban láthatja, hogy a sorokat, amelyek ki lettek hagyva, és a kompatibilitási oka.

A fájl az eredeti adatok és a hiba jelentkezett be. A log fájl tartalma például a következőképpen történik:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>További lépések
Az Azure Data Factory másolási tevékenységgel kapcsolatos további információkért lásd: [adatok áthelyezése másolási tevékenységgel](data-factory-data-movement-activities.md).

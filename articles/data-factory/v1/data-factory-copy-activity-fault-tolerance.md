---
title: "A hibatűrés hozzáadása az Azure Data Factory másolási tevékenység nem kompatibilis sorok kihagyása |} Microsoft Docs"
description: "Megtudhatja, hogyan hibatűrést hozzáadása az Azure Data Factory másolási tevékenység nem kompatibilis sorok kihagyása másolása során"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5c32d4ac2c1179a83a82bd5deb41047b82e43b7e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>A hibatűrés hozzáadása a másolási tevékenység nem kompatibilis sorok kihagyása
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [hibatűrés a Data Factory 2-es verzióját a másolási tevékenység](../copy-activity-fault-tolerance.md).

Az Azure Data Factory [másolási tevékenység](data-factory-data-movement-activities.md) kétféleképpen Ön szeretné kezelni a forrás és a fogadó adattároló közötti másolás nem kompatibilis sorok:

- Megszakítja, és sikertelen lesz a másolási tevékenység nem kompatibilis adat esetén történt (alapértelmezés).
- Továbbra is másolhatja az összes adat hozzáadásával a hibatűrést, és nem kompatibilis adat sorok kihagyása. Ezenkívül bejelentkezhet a nem kompatibilis sorok Azure Blob Storage tárolóban. Ismerje meg a hiba okát, javítsa ki az adatokat az adatforrás, és ismételje meg a másolási tevékenység napló majd ellenőrizheti.

## <a name="supported-scenarios"></a>Támogatott esetek
Másolási tevékenység észlelésekor, kihagyása és naplózási adatok nem kompatibilis három forgatókönyveket teszi lehetővé:

- **A forrás adattípus és a fogadó natív típusa között inkompatibilitás**

    Például: adatok másolása CSV-fájl Blob Storage egy séma-definícióval, amely tartalmazza a három SQL-adatbázis **INT** típusú oszlop. Például numerikus adatokat tartalmazó CSV-fájl sorok `123,456,789` sikeresen a fogadó tárolóba kerülnek. Azonban a sorokat tartalmazó nem numerikus értékeket, például `123,456,abc` észlelhetők a nem kompatibilis, és kimarad.

- **A forrás- és a fogadó között oszlopok száma nem egyezik**

    Például: adatok másolása az a Blob-tároló CSV-fájl az SQL-adatbázis egy séma-definícióval, amely hat oszlopokat tartalmaz. A fogadó tároló hat oszlopokat tartalmazó CSV-fájl sorok sikeresen lesz másolva. A több vagy kevesebb, mint hat oszlopot tartalmazó CSV-fájl sorok észlelhetők a nem kompatibilis, és kimarad.

- **Elsődleges kulcs megsértése egy relációs adatbázis írásakor**

    Például: adatok másolása az SQL server az SQL-adatbázis. A fogadó SQL-adatbázis elsődleges kulcs van definiálva, de nincs ilyen elsődleges kulcs van definiálva a forrás SQL-kiszolgálón. A duplikált sorokat, amely szerepel a forrás nem lehet másolni a fogadó. Másolási tevékenység során az adatok csak az első sor a fogadó másolja. Ismétlődő elsődleges kulcs értéke a következő adatforrás a sorokat a rendszer észleli a rendszer nem kompatibilis, és kimarad a.

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Vagy nem engedélyezheti a nem kompatibilis sorok kihagyása másolása során. | True (Igaz)<br/>Hamis (alapértelmezés) | Nem |
| **redirectIncompatibleRowSettings** | Egy csoport, amely tulajdonságok meg, ha azt szeretné, a nem kompatibilis sorok bejelentkezni. | &nbsp; | Nem |
| **linkedServiceName** | A napló, a rendszer kihagyta sorokat tartalmazó tárolásához Azure Storage társított szolgáltatás. | A neve egy [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) társított szolgáltatás, amely a tárolási példányon, amely a naplófájl tárolási használni kívánt vonatkozik. | Nem |
| **elérési út** | A rendszer kihagyta sorokat tartalmaz-e a naplófájl elérési útja | Adja meg a Blob storage elérési utat, amely a nem kompatibilis adatokat naplózhatnak használni kívánt. Ha nem ad meg egy elérési utat, a szolgáltatás létrehoz egy tárolót. | Nem |

## <a name="monitoring"></a>Figyelés
A másolási tevékenység során futtatása után megtekintheti a figyelési szakaszban kihagyott sorok száma:

![A figyelő nem kompatibilis sorok kihagyva](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Ha a nem kompatibilis sorok bejelentkezni konfigurálja, a naplófájl ezen az elérési úton található: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` , amelyek ki lettek hagyva a sorok és a kompatibilitási okának láthatja a naplófájlban.

A fájl az eredeti adatok és a hiba jelentkezett be. A napló fájl tartalma például a következőképpen történik:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Következő lépések
Azure Data Factory másolási tevékenység kapcsolatos további információkért lásd: [adatok áthelyezése a másolási tevékenység segítségével](data-factory-data-movement-activities.md).
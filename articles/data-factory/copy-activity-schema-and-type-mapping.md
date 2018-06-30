---
title: A másolási tevékenység séma-hozzárendeléséhez |} Microsoft Docs
description: További információk a hogyan képezi le a másolási tevékenység során az Azure Data Factoryben az adatok gyűjtése, ha az adatok másolásának forrásadatok sémák és adattípusok.
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
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 16275ddc4d4ad85bdac54244ceeec568603fdfef
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112099"
---
# <a name="schema-mapping-in-copy-activity"></a>A másolási tevékenység séma-hozzárendelése
Ez a cikk ismerteti, hogyan működik az Azure Data Factory másolási tevékenység során a séma-hozzárendelése és a forrásadatok adatok gyűjtésének adattípus-leképezés amikor hajtsa végre az adatok másolását.

## <a name="column-mapping"></a>Oszlop leképezése

Alapértelmezés szerint a másolási tevékenység **forrás adatok gyűjtésének által oszlopnevek leképezése**, kivéve, ha [explicit oszlopleképezés](#explicit-column-mapping) van konfigurálva. Pontosabban a másolási tevékenység:

1. Az adatok olvasása a forrásból, és határozza meg az adatforrás sémája

    * Az adatok tárolási/fájlformátumban, például adatbázisok/fájlok metaadatokkal (Avro/ORC/Parquet/szöveg fejlécet), előre definiált séma adatforrások adatforrásséma kinyert a lekérdezés eredménye, vagy a fájl-metaadatok.
    * Rugalmas séma, például Azure tábla/Cosmos DB adatforrások adatforrásséma van következtetni a lekérdezés eredménye. Felülírja a "structure" adatkészletben levő biztosítása.
    * Szöveges fájlt fejléc nélküli alapértelmezett oszlopnevek akkor jönnek létre, mintával "Prop_0", "Prop_1"... Felülírja a "structure" adatkészletben levő biztosítása.
    * A Dynamics adatforrás meg kell adnia a sémaadatok, az adatkészlet "structure" szakaszban.

2. Explicit oszlopleképezés alkalmazni, ha meg van adva.

3. Az adatok gyűjtésének írása

    * Az adattároló, előre definiált séma az adatokat írni az oszlopokat ugyanazzal a névvel.
    * Adattároló nélkül rögzített sémájába és fájlformátum az oszlop neve/metadata jön létre a forrás séma alapján.

### <a name="explicit-column-mapping"></a>Explicit oszlop leképezése

Megadhat **columnMappings** a a **typeProperties** explicit oszlopleképezés ehhez a másolási tevékenység szakasza. Ebben az esetben "structure" szakasz szükség a bemeneti és a kimeneti adathalmazokat. Oszlop leképezése támogatja **összes leképezési vagy a forrás adatkészletben "structure" a "structure" fogadó adatkészlet összes oszlopa oszlopok csoportja,**. Hiba feltételek, amelyek kivételt a következők:

* Forrás adattárolási lekérdezés eredménye nem rendelkezik olyan oszlopnevet, amely a bemeneti adatkészlet "structure" szakaszban van megadva.
* A fogadó adattár (ha az előre definiált séma) nem rendelkezik olyan oszlopnevet, amely a kimeneti adatkészlet "structure" szakaszban van megadva.
* Kevesebb oszlopot vagy több oszlop szerepel a "structure" fogadó adatkészlet mint a leképezésben megadott.
* Ismétlődő leképezés.

#### <a name="explicit-column-mapping-example"></a>Explicit oszlop leképezése-példa

Ez a példa a bemeneti táblájának struktúrája, és a helyszíni SQL-adatbázisban egy tábla mutat.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Ez a példa a kimeneti táblához tartozik egy struktúra, és egy Azure SQL adatbázis egyik táblája mutat.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

A következő JSON egy folyamaton belül határozza meg a másolási tevékenység. A forrás oszlop szerepel a fogadó leképezve oszlopokat (**columnMappings**) használatával a **fordító** tulajdonság.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings": 
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Ha használta szintaxisa a következő `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` oszlopleképezés megadásához továbbra is támogatott,-van.

**Oszlop-hozzárendelési folyamat:**

![Oszlop-hozzárendelési folyamat](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="data-type-mapping"></a>Adattípus-leképezés

Másolási tevékenység hajtja végre a következő 2. lépés – a módszert leképezett gyűjtése típusait:

1. Azure Data Factory ideiglenes adattípusok natív eseményforrás-típusnak konvertálása
2. Az Azure Data Factory ideiglenes adattípusok átalakítása natív a fogadó típusa

A natív típus minden összekötő témakör "Adattípus leképezés" szakaszának ideiglenes típusra való leképezése található.

### <a name="supported-data-types"></a>Támogatott adattípusokat

Adat-előállítót a következő ideiglenes adattípusokat támogatja: alatt az értékeket is megadhat, írja be az adatokat nyújtásakor [adatkészlet-szerkezetekben](concepts-datasets-linked-services.md#dataset-structure) konfiguráció:

* Byte]
* Logikai
* Dátum és idő
* Datetimeoffset
* Decimális
* Dupla
* GUID
* Int16
* Int32
* Int64
* Önálló
* Sztring
* Időtartomány

### <a name="explicit-data-type-conversion"></a>Explicit az adattípus átalakítása

A rögzített séma, például az SQL Server/Oracle, az adatok másolásának adatokká tárolja, amikor a forrás és a fogadó másik típussal rendelkezik ugyanezen az oszlopon, ha a típus explicit konverzió deklarálása a forrás oldalon:

* A forrás például CSV vagy Avro, a típuskonverziós kell nyilvánítani forrás struktúrával, amelynek teljes oszloplistát (ügyféloldali oszlop nevét és a fogadó oldali Forrástípus) keresztül
* A relációs adatforrás (például SQL vagy Oracle) a típuskonverziós a lekérdezési utasítást explicit típust formába kell megvalósítani.

## <a name="when-to-specify-dataset-structure"></a>Ha a dataset "structure" adja meg

Az alábbi forgatókönyvek esetén "structure" adatkészletben levő szükség:

* Alkalmazása [explicit az adattípus átalakítása](#explicit-data-type-conversion) fájl források (bemeneti adatkészletet) másolása során
* Alkalmazása [explicit oszlopleképezés](#explicit-column-mapping) (mindkettő bemeneti és kimeneti adatkészlet) másolása során
* Dynamics 365 (bemeneti adatkészletet) / CRM forrásból másolása
* Ha a forrás nincs (kimeneti adatkészlet) JSON-fájlok beágyazott objektumként Cosmos DB másolása

Az alábbi forgatókönyvek esetén "structure" adatkészletben levő javasolt:

* Másolás szövegfájlból nélkül fejléc (bemeneti adatkészlet). Megadhatja az oszlopok neveit szöveges fájlt a megfelelő fogadó oszlopokkal menti a explicit oszlopleképezés biztosító igazítása.
* Adatok másolása tárolja a rugalmas séma, például Azure-tábla/Cosmos DB (bemeneti adatkészletet), a várt adatokat (oszlop) keresztül történő másolásakor lehetővé másolás helyett biztosításához tevékenység amelyből megállapítható a séma felső sor(ok) alapján minden tevékenység futtatása során.


## <a name="next-steps"></a>További lépések
A másolási tevékenység cikkekben talál:

- [Tevékenység – áttekintés](copy-activity-overview.md)
- [Tevékenység hibatűrést másolása](copy-activity-fault-tolerance.md)
- [A másolási tevékenység](copy-activity-performance.md)

---
title: A másolási tevékenység séma hozzárendelése |} A Microsoft Docs
description: Ismerje meg hogyan másolási tevékenységgel az Azure Data Factoryban leképezi a sémák és adattípusok forrásadatok adatok gyűjtése, ha másol adatokat.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9108f83e854b51720c64c5a74a828543cc5e7688
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875804"
---
# <a name="schema-mapping-in-copy-activity"></a>Séma-hozzárendelés másolási tevékenységben

Ez a cikk bemutatja, hogyan történik az Azure Data Factory másolási tevékenység a séma-hozzárendelés és a forrásadatok a fogadó-adatok adattípus-leképezés végrehajtása közben az adatok másolását.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Az adatok másolása a fogadó-forrás oszlop-hozzárendelés vonatkozik. Alapértelmezés szerint a másolási tevékenység **képezze le a forrásadatok a fogadó által az oszlopnevek**. Megadhat [explicit leképezés](#explicit-mapping) Az oszlopleképezés igény szerint testreszabhatja. Pontosabban a másolási tevékenység:

1. Az adatok olvasása a forrásból, és döntse el, a forrás sémája
2. Használja az alapértelmezett oszlop leképezése oszlopok leképezésére név alapján, vagy explicit oszlopleképezés alkalmazni, ha meg van adva.
3. A fogadó az adatok írása

### <a name="explicit-mapping"></a>Explicit leképezés

Megadhatja az oszlopok leképezésére, a másolási tevékenység -> `translator`  ->  `mappings` tulajdonság. Az alábbi példa meghatározza a másolási tevékenység elválasztójellel tagolt szöveges adatokat másol az Azure SQL Database egy folyamatban.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

A következő tulajdonságok támogatottak alatt `translator`  ->  `mappings` -> objektum `source` és `sink`:

| Tulajdonság | Leírás                                                  | Szükséges |
| -------- | ------------------------------------------------------------ | -------- |
| név     | A forrás és fogadó oszlop neve.                           | Igen      |
| Sorszám  | Az oszlopindex. Indítsa el az 1. <br>Alkalmazása és megadása kötelező, ha használatával tagolt szöveg fejlécsort nélkül. | Nem       |
| elérési út     | Az egyes mezőkhöz a kinyerni vagy leképezése JSON-útvonalának kifejezését. Hierarchikus adatokra vonatkozik például mongodb-hez, illetve a REST.<br>A gyökérobjektum alatti mezők esetében a JSON-útvonal elindítja a gyökér $; által kiválasztott tömbben lévő mezők esetében `collectionReference` tulajdonság, a tömbelem indul JSON-útvonalhoz. | Nem       |
| type     | Data Factory közbenső a forrás és fogadó oszlop adattípusát. | Nem       |
| kulturális környezet  | A forrás és fogadó oszlopban kultúrájának. <br>Típus esetén a alkalmazni `Datetime` vagy `Datetimeoffset`. A mező alapértelmezett értéke: `en-us`. | Nem       |
| Formátum   | Formázó karakterlánc típus esetén használandó `Datetime` vagy `Datetimeoffset`. Tekintse meg [egyéni dátum- és időformátum karakterláncokat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) a dátum és idő formázása. | Nem       |

A következő tulajdonságok támogatottak alatt `translator`  ->  `mappings` mellett objektum `source` és `sink`:

| Tulajdonság            | Leírás                                                  | Szükséges |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Támogatott, csak ha hierarchikus adatokkal például mongodb-hez, illetve a REST forrás.<br>Ha szeretné-e ismételt futtatásával és az adatok kinyerése az objektumok **egy tömbmezőben található** ugyanazt a mintát és convert objektumonkénti soronként cross-alkalmazásához a tömb JSON elérési útjának megadásához. | Nem       |

### <a name="alternative-column-mapping"></a>Alternatív oszlop-hozzárendelés

Megadhatja a másolási tevékenység -> `translator`  ->  `columnMappings` leképezésére táblázatos alakú adatok között. Ebben az esetben "struktúra" szakasz is a bemeneti és kimeneti adatkészleteket szükség. Oszlop leképezése támogatja **összes leképezést vagy a forrásadatkészlet "struktúrát", "struktúra" fogadó-adatkészlet összes oszlopa az oszlopok**. A kivétel eredményező hiba feltételek a következők:

* Forrásadatok tárolja a lekérdezés eredménye nem rendelkezik olyan oszlopnevet, amely a bemeneti adatkészlet "struktúra" szakaszban van megadva.
* Fogadó adattár (ha az előre definiált sémával) nem rendelkezik olyan oszlopnevet, amely a kimeneti adatkészlet "struktúra" szakaszban van megadva.
* Kevesebb oszlopot vagy több oszlop szerepel a "szerkezete" fogadó-adatkészlet, mint a leképezésben megadott.
* Kettős hozzárendelés.

A következő példában a bemeneti adatkészlet struktúrája, és a egy helyszíni Oracle database egyik táblájába mutat.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Ebben a példában a kimeneti adatkészlet struktúrája, és a egy Salesfoce táblára mutat.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

A következő JSON egy másolási tevékenységet a folyamat határozza meg. Az oszlopok fogadó oszlopai leképezve a forrásból a **translator** -> **leképezésekben** tulajdonság.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
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

Ha szintaxisát használja `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` oszlop-hozzárendelés megadása, hogy továbbra is támogatott-van.

### <a name="alternative-schema-mapping"></a>Alternatív séma-hozzárendelés

Megadhatja a másolási tevékenység -> `translator`  ->  `schemaMapping` leképezésére hierarchikus alakú és táblázatos alakú adatok között, például másolása a mongodb-hez, illetve a REST szövegfájl és az Oracle másolása az Azure Cosmos DB API a mongodb-hez. A következő tulajdonságokat a másolási tevékenység támogatott `translator` szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység translator type tulajdonsága értékre kell állítani: **TabularTranslator** | Igen |
| schemaMapping | Kulcs-érték párok, egy gyűjteményt, amely jelöli az leképezési kapcsolat **a fogadó oldali forrás oldaláról**.<br/>- **Kulcs:** forrás jelöli. A **táblázatos adatforrás**, adja meg az oszlop neve; adatkészletszerkezet a meghatározott **hierarchikus forrás**, adja meg az egyes mezők kinyerése és képezze le a JSON-útvonalának kifejezését.<br>- **Érték:** fogadó jelöli. A **táblázatos fogadó**, adja meg az oszlop neve; adatkészletszerkezet a meghatározott **hierarchikus fogadó**, adja meg az egyes mezők kinyerése és képezze le a JSON-útvonalának kifejezését. <br>Hierarchikus adatokkal dolgozik, a, a gyökérobjektum alatti mezők esetén JSON-útvonal elindítja a gyökér $; által kiválasztott tömbben lévő mezők esetében `collectionReference` tulajdonság, a tömbelem indul JSON-útvonalhoz.  | Igen |
| collectionReference | Ha szeretné-e ismételt futtatásával és az adatok kinyerése az objektumok **egy tömbmezőben található** ugyanazt a mintát és convert objektumonkénti soronként cross-alkalmazásához a tömb JSON elérési útjának megadásához. Ez a tulajdonság csak a forrás hierarchikus adatok esetén támogatott. | Nem |

**Példa: Oracle MongoDB-ből másolja:**

Ha például a MongoDB-dokumentumot, az alábbi tartalommal:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

és szeretné másolni egy Azure SQL-táblába az alábbi formátumban, a tömbben lévő adatok egybesimításával *(order_pd és order_price)* és csatlakozik a szolgáltatáshoz a közös gyökérinformációval közötti *(szám, dátum és város)* :

| orderNumber | rendelés dátuma | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

A séma-leképezési szabály konfigurálása a következő másolási tevékenység JSON-mintát:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number",
                "orderDate": "$.date",
                "order_pd": "prod",
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Adattípus-leképezés

A másolási tevékenység forrástípusok a típusok az alábbi 2 megközelítését tartalmazó hozzárendelés fogadó hajtja végre:

1. Azure Data Factory közbenső adattípusok natív forrástípusok átalakítása
2. Az Azure Data Factory közbenső adattípusok átalakítása natív fogadó típusa

Nativní typ "Adattípus leképezés" szakaszában található minden egyes összekötőt témakör közbenső típusra közötti leképezést találja.

### <a name="supported-data-types"></a>Támogatott adattípusok

A Data Factory a következő, köztes adattípusokat támogatja: Írja be az adatokat konfigurálásakor ad meg alábbi értékek [adatkészlet-szerkezetekben](concepts-datasets-linked-services.md#dataset-structure-or-schema) konfiguráció:

* Byte[]
* Boolean
* DateTime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Időtartomány

## <a name="next-steps"></a>További lépések
A másolási tevékenység egyéb cikkekben talál:

- [Másolási tevékenység áttekintése](copy-activity-overview.md)

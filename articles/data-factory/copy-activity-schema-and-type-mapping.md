---
title: Séma-hozzárendelés másolási tevékenységben
description: Megtudhatja, hogy az Azure Data Factory ban végzett tevékenységek másolása hogyan képezi le a sémákat és adattípusokat a forrásadatokból az adatok másolásakor lévő adatokra.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 9ae07e2a471cc417b467092a2616a5a0cdafb1fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260812"
---
# <a name="schema-mapping-in-copy-activity"></a>Séma-hozzárendelés másolási tevékenységben

Ez a cikk azt ismerteti, hogy az Azure Data Factory másolási tevékenység hogyan séma- és adattípus-leképezést végez a forrásadatokról a fogadóadatokra az adatmásolás végrehajtásakor.

## <a name="schema-mapping"></a>Sémaleképezés

Az oszlopleképezés az adatok forrásból fogadóba történő másolásakor érvényes. Alapértelmezés szerint másolja a **tevékenységleképezés forrásadatait oszlopnevek szerint.** Explicit [leképezést](#explicit-mapping) adhat meg az oszlopleképezés igény szerint való testreszabásához. Pontosabban, másolási tevékenység:

1. Az adatok beolvasása a forrásból és a forrásséma meghatározása
2. Az alapértelmezett oszlopleképezéssel név szerint képezheti az oszlopokat, vagy explicit oszlopleképezést alkalmazhat, ha meg van adva.
3. A fogadóhoz írt adatok írása

### <a name="explicit-mapping"></a>Explicit leképezés

Megadhatja azokat az oszlopokat, `translator`  ->  `mappings` amelyeket a ->-es másolási tevékenységben szeretne leképezni. A következő példa egy másolási tevékenységet határoz meg egy folyamatban, amely adatokat másol a tagolt szövegből az Azure SQL Database-be.

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

A következő tulajdonságok `translator`  ->  `mappings` a -> `source` `sink`objektum és : alatt támogatottak:

| Tulajdonság | Leírás                                                  | Kötelező |
| -------- | ------------------------------------------------------------ | -------- |
| név     | A forrás- vagy fogadóoszlop neve.                           | Igen      |
| Ordinális  | Oszlopindex. Kezdjük 1-el. <br>Alkalmazza és kötelező, ha fejlécsor nélküli tagolt szöveget használ. | Nem       |
| path     | JSON-görbekifejezés minden egyes mezőhöz, amelyet ki szeretne bontani vagy le képezni. Hierarchikus adatokra, pl. MongoDB/REST-re pályázzon.<br>A gyökérobjektum alatti mezők esetében a JSON elérési út $; a tulajdonság által `collectionReference` választott tömbön belüli mezők esetében a JSON elérési út a tömbelemtől indul. | Nem       |
| type     | A forrás- vagy fogadóoszlop köztes adattípusa. | Nem       |
| Kultúra  | A forrás- vagy mosogatóoszlop kultúrája. <br>Alkalmazza, ha `Datetime` `Datetimeoffset`a szöveg vagy a . A mező alapértelmezett értéke: `en-us`. | Nem       |
| Formátum   | A szöveg vagy a `Datetime` szöveg `Datetimeoffset`használata esetén használandó karakterlánc formázása A datetime formázásáról az [Egyéni dátum- és időformátum-karakterláncok hivatkozásra](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) hivatkozik. | Nem       |

Az `translator`  ->  `mappings` objektum `source` és a mellett a `sink`következő tulajdonságok támogatottak:

| Tulajdonság            | Leírás                                                  | Kötelező |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Csak akkor támogatott, ha hierarchikus adatok, pl. A MongoDB/REST forrás.<br>Ha azonos mintával rendelkező **tömbmezőben lévő** objektumokból szeretne adatokat kinyerni és kinyerni, és objektumonként soronkénti átalakításra konvertálni, adja meg a tömb JSON elérési útját a keresztalkalmazáshoz. | Nem       |

### <a name="alternative-column-mapping"></a>Alternatív oszlopleképezés

Megadhatja a -> `translator`  ->  `columnMappings` másolási tevékenységet a táblázatos alakú adatok közötti leképezéshez. Ebben az esetben a "struktúra" szakasz szükséges mind a bemeneti és kimeneti adatkészletek. Az oszlopleképezés támogatja **a forrásadatkészlet "struktúra" oszlopainak egy vagy egy részét a "struktúra" fogadó adatkészlet összes oszlopához való hozzárendelését.** A következők a kivételt eredményező hibafeltételek:

* A forrásadattár lekérdezésének eredménye nem rendelkezik a bemeneti adatkészlet "struktúra" szakaszában megadott oszlopnévvel.
* A fogadó adattárának (ha előre definiált sémával van elsajátított a séma) nem rendelkezik a kimeneti adatkészlet "struktúra" szakaszában megadott oszlopnévvel.
* Vagy kevesebb oszlop, vagy több oszlop a fogadó adatkészlet "struktúrájában", mint a leképezésben megadott.
* Duplikált leképezés.

A következő példában a bemeneti adatkészlet rendelkezik egy struktúrával, és egy helyszíni Oracle-adatbázis táblájára mutat.

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

Ebben a példában a kimeneti adatkészlet rendelkezik egy struktúrával, és a Salesfoce egy táblájára mutat.

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

A következő JSON egy másolási tevékenységet határoz meg egy folyamatban. A **forrásoszlopok** -> leképezése a fogadó oszlopaihoz a fordító**columnMappings** tulajdonság használatával.

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

Ha oszlopleképezés `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` megadására használja a szintaxist, akkor továbbra is támogatott.

### <a name="alternative-schema-mapping"></a>Alternatív sémaleképezés

Megadhatja a másolási `translator`  ->  `schemaMapping` tevékenység -> a hierarchikus alakú adatok és a táblázatos alakú adatok leképezéséhez, például a MongoDB/REST fájlból a szövegfájlba, és az Oracle-ből az Azure Cosmos DB MongoDB-hoz készült API-jába történő másolása. A másolási tevékenység `translator` szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A copy activity translator típustulajdonságának a következőre kell állítani: **TabularTranslator** | Igen |
| sémaleképezés | Kulcs-érték párok gyűjteménye, amely a **forrásoldalról**a fogadó oldalra tartozó leképezési kapcsolatot jelöli.<br/>- **Kulcs:** a forrást jelöli. Táblázatos **forrás**esetén adja meg az oszlop nevét az adatkészlet szerkezetében meghatározottmódon; **hierarchikus forrásesetén**adja meg a kibontandó és leképezésre szánt mezők JSON-görbekifejezését.<br>- **Érték:** a fogadót jelöli. Táblázatos **fogadó**esetén adja meg az oszlop nevét az adatkészlet szerkezetében meghatározottmódon; **hierarchikus fogadó**esetén adja meg a Kibontandó és leképezésre szánt egyes mezők JSON-görbekifejezését. <br>Hierarchikus adatok esetén a gyökérobjektum alatti mezők esetében a JSON elérési út $- gyökérrel kezdődik; a tulajdonság által `collectionReference` választott tömbön belüli mezők esetében a JSON elérési út a tömbelemtől indul.  | Igen |
| collectionReference | Ha azonos mintával rendelkező **tömbmezőben lévő** objektumokból szeretne adatokat kinyerni és kinyerni, és objektumonként soronkénti átalakításra konvertálni, adja meg a tömb JSON elérési útját a keresztalkalmazáshoz. Ez a tulajdonság csak akkor támogatott, ha a hierarchikus adatok forrása. | Nem |

**Példa: másolás a MongoDB-ról az Oracle-re:**

Ha például a MongoDB dokumentum a következő tartalommal rendelkezik:

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

és szeretné másolni egy Azure SQL-táblába a következő formátumban, a tömbön belüli adatok összeolvasztásával *(order_pd és order_price),* és keresztbe a közös gyökéradatokkal *(szám, dátum és város):*

| orderNumber (rendelésszáma) | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Konfigurálja a sémaleképezési szabályt a következő JSON másolási tevékenységként:

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
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Adattípus-leképezés

A másolási tevékenység forrástípusokat hajt végre a következő kétlépéses megközelítéssel:

1. Konvertálás natív forrástípusokról Azure Data Factory köztes adattípusokra
2. Átalakítás az Azure Data Factory köztes adattípusaiból natív fogadótípussá

A natív típus köztes típus közötti leképezés t az egyes összekötőtémakör "Adattípus-leképezés" szakaszában találhatja meg.

### <a name="supported-data-types"></a>Támogatott adattípusok

A Data Factory a következő köztes adattípusokat támogatja: Az alábbi értékeket adhatja meg az [adatkészletszerkezet-konfigurációban](concepts-datasets-linked-services.md#dataset-structure-or-schema) a típusadatok konfigurálásakor:

* Bájt[]
* Logikai
* Datetime
* Dátumidő-eltolás
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Egyirányú
* Sztring
* Időtartomány

## <a name="next-steps"></a>További lépések
Lásd a többi Másolási tevékenység cikkeket:

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)

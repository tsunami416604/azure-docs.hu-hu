---
title: Séma-hozzárendelés másolási tevékenységben
description: Ismerje meg, hogy a másolási tevékenység hogyan Azure Data Factory Maps-sémákban és adattípusokban a forrásadatok között az adatok másolásakor.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jingwang
ms.openlocfilehash: 9f04955fb910a6159dc09ac40a87a398e67d59d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414118"
---
# <a name="schema-mapping-in-copy-activity"></a>Séma-hozzárendelés másolási tevékenységben
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogy a Azure Data Factory másolási tevékenység hogyan végzi el a séma-hozzárendelést és az adattípust a forrásadatok és az Adatmásolás során az adatok elfogadásához.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Az oszlop-hozzárendelés akkor érvényes, ha a forrásról a fogadóba másol Adatmásolást. Alapértelmezés szerint a másolási tevékenység **leképezése a forrásadatok oszlop neve alapján történő**elfogadására. Az oszlop-hozzárendelés igény szerinti testreszabásához [explicit leképezést](#explicit-mapping) is megadhat. Pontosabban a másolási tevékenység:

1. A forrásból származó adatok beolvasása és a forrásoldali séma meghatározása
2. Az oszlopok leképezése név szerint az alapértelmezett oszlop-hozzárendeléssel, vagy ha meg van adva, explicit oszlop-hozzárendelést alkalmazhat.
3. Az adatgyűjtés megírása a fogadóba

### <a name="explicit-mapping"></a>Explicit leképezés

A másolási tevékenység – > `translator`  ->  `mappings` tulajdonságban megadhatja a leképezni kívánt oszlopokat. Az alábbi példa egy másolási tevékenységet határoz meg egy folyamaton belül, hogy a tagolt szövegből Azure SQL Databaseba másolja az adatok.

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

`translator`  ->  `mappings` A (z) `source` és `sink`a (z) > objektumban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás                                                  | Kötelező |
| -------- | ------------------------------------------------------------ | -------- |
| név     | A forrás vagy a fogadó oszlop neve.                           | Igen      |
| sorszámok  | Oszlop indexe Első lépések: 1. <br>Alkalmazva és kötelező, ha a tagolt szöveg fejléc nélkül van használatban. | Nem       |
| path     | Az egyes mezőkhöz tartozó JSON-elérésiút-kifejezés kibontása vagy leképezése. Hierarchikus adatokra vonatkozik például MongoDB/REST.<br>A root objektum alatti mezők esetében a JSON-útvonal a root $; karakterrel kezdődik. a `collectionReference` tulajdonság által választott tömbben lévő mezők esetében a JSON-útvonal a tömb elemből indul el. | Nem       |
| type     | Data Factory a forrás vagy a fogadó oszlop közbenső adattípusa. | Nem       |
| kulturális környezet  | A forrás vagy a fogadó oszlop kulturális környezete. <br>Akkor alkalmazza, ha `Datetime` a `Datetimeoffset`típus értéke vagy. A mező alapértelmezett értéke: `en-us`. | Nem       |
| formátumban   | A Type vagy `Datetime` `Datetimeoffset`a típushoz használandó formázó sztring. A DateTime formátumának formázásához tekintse meg az [Egyéni dátum-és időformátumot ismertető karakterláncot](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) . | Nem       |

`translator`  ->  `mappings` A (z) `source` és `sink`a (z) objektum mellett a következő tulajdonságokat is támogatja:

| Tulajdonság            | Leírás                                                  | Kötelező |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Csak akkor támogatott, ha a hierarchikus adatok például a MongoDB/REST forrása.<br>Ha szeretné megismételni és kinyerni a **tömbben** lévő objektumokból származó adatmennyiséget ugyanazzal a mintával, és soronként konvertálja az objektumokat, akkor a tömb JSON-elérési útját kell megadnia. | Nem       |

### <a name="alternative-column-mapping"></a>Alternatív oszlop-hozzárendelés

Megadhatja a másolási tevékenység `translator`  ->  `columnMappings` -> a táblázatos adatokat a leképezéshez. Ebben az esetben a "Structure" szakasz szükséges a bemeneti és a kimeneti adatkészletekhez is. Az oszlop-hozzárendelés támogatja a "Structure" **forrás-adatkészlet összes oszlopának vagy részhalmazának leképezését a "Structure" fogadó adatkészlet összes oszlopára**. A következő hibák a kivételt eredményezik:

* A forrás adattároló lekérdezési eredményének nincs olyan oszlopa, amely meg van adva a (z) "Structure" bemeneti adatkészletben.
* A fogadó adattár (ha előre definiált sémával rendelkezik) nem rendelkezik olyan oszloppal, amely meg van adva a "Structure" (kimeneti adatkészlet) szakaszban.
* Kevesebb oszlop vagy több oszlop szerepel a fogadó adatkészlet "szerkezetében", mint a leképezésben megadott érték.
* Ismétlődő leképezés.

A következő példában a bemeneti adatkészlet struktúrát tartalmaz, és egy helyszíni Oracle-adatbázisban lévő táblára mutat.

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

Ebben a példában a kimeneti adatkészlet struktúrát tartalmaz, és egy Salesfoce-táblára mutat.

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

A következő JSON a másolási tevékenységet definiálja egy folyamaton belül. A forrás oszlopai a befogadó oszlopokra vannak leképezve a **Translator** -> **columnMappings** tulajdonság használatával.

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

Ha a szintaxisát `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` használja az oszlopok megfeleltetésének megadásához, akkor továbbra is támogatott.

### <a name="alternative-schema-mapping"></a>Alternatív séma-hozzárendelés

Megadhatja a másolási tevékenység `translator`  ->  `schemaMapping` – > a hierarchikus és a táblázatos adatok között, például a MongoDB/Rest-ből a szövegfájlba való másolással és az Oracle-ből a Azure Cosmos db API-ból történő másolásával a MongoDB. A másolási tevékenység `translator` szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fordítójának Type tulajdonságát a következőre kell beállítani: **TabularTranslator** | Igen |
| schemaMapping | Kulcs-érték párok gyűjteménye, amely a **forrás oldalról a fogadó oldalra való**leképezési kapcsolatot jelöli.<br/>- **Kulcs:** a forrást jelöli. **Táblázatos forrás**esetén adja meg az oszlop nevét az adatkészlet struktúrájában definiált módon. **hierarchikus forrás**esetén adja meg a JSON-elérésiút-kifejezést az egyes mezők kinyeréséhez és leképezéséhez.<br>- **Érték:** a fogadót jelöli. **Táblázatos**fogadó esetén adja meg az oszlop nevét az adatkészlet struktúrájában definiált módon. **hierarchikus**fogadó esetén adja meg a JSON-elérésiút-kifejezést az egyes mezők kinyeréséhez és leképezéséhez. <br>Hierarchikus adat esetén a gyökér objektum alatti mezők esetében a JSON-útvonal a root $; karakterrel kezdődik. a `collectionReference` tulajdonság által választott tömbben lévő mezők esetében a JSON-útvonal a tömb elemből indul el.  | Igen |
| collectionReference | Ha szeretné megismételni és kinyerni a **tömbben** lévő objektumokból származó adatmennyiséget ugyanazzal a mintával, és soronként konvertálja az objektumokat, akkor a tömb JSON-elérési útját kell megadnia. Ez a tulajdonság csak akkor támogatott, ha a hierarchikus adatforrás a forrás. | Nem |

**Példa: másolás a MongoDB-ből az Oracle-be:**

Ha például a következő tartalommal rendelkezik a MongoDB-dokumentummal:

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

és a következő formátumban szeretné átmásolni egy Azure SQL-táblába a tömbben lévő adatok összesimításával *(order_pd és order_price)* , valamint a közös legfelső szintű információkkal való összekapcsolással *(szám, dátum és város)*:

| rendelésszáma | RendelésDátuma | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Konfigurálja a séma-leképezési szabályt a következő másolási tevékenység JSON-mintaként:

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

A másolási tevékenység a következő kétlépéses megközelítéssel hajtja végre a forrás típusait a fogadó típusok leképezéséhez:

1. Átalakítás natív forrásokból Azure Data Factory átmeneti adattípusokra
2. Átalakítás Azure Data Factory köztes adattípusról natív fogadó típusra

A másolási tevékenység a következő közbenső adattípusokat támogatja: 

* Bájt []
* Logikai
* Datetime
* DateTimeOffset
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
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)

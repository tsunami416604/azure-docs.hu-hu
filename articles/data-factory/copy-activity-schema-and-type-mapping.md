---
title: A másolási tevékenység séma hozzárendelése |} A Microsoft Docs
description: Ismerje meg hogyan másolási tevékenységgel az Azure Data Factoryban leképezi a sémák és adattípusok forrásadatok adatok gyűjtése, ha másol adatokat.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: c2f58a3510699cdf74e3150d3ad5882929f4f05b
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358711"
---
# <a name="schema-mapping-in-copy-activity"></a>A másolási tevékenység séma hozzárendelése
Ez a cikk bemutatja, hogyan történik az Azure Data Factory másolási tevékenység a séma-hozzárendelés és a forrásadatok a fogadó-adatok adattípus-leképezés végrehajtása közben az adatok másolását.

## <a name="column-mapping"></a>Oszlopleképezés

Az oszlopleképezés érvényes példatípust az adatok táblázatos alakú adatok között. Alapértelmezés szerint a másolási tevékenység **képezze le a forrásadatok a fogadó által az oszlopnevek**, kivéve, ha [explicit oszlopleképezés](#explicit-column-mapping) van konfigurálva. Pontosabban a másolási tevékenység:

1. Az adatok olvasása a forrásból, és döntse el, a forrás sémája

    * A data store/fájlformátumban, például adatbázisok és fájlokat metaadatokkal (Avro/ORC/Parquet/szöveg fejléccel), előre definiált sémával adatforrások schéma zdroje kinyert a lekérdezés eredménye vagy a fájl metaadatait.
    * Rugalmas sémájú, például az Azure tábla/Cosmos DB, adatforrások schéma zdroje fióktól vette a beállításait, a lekérdezés eredménye. Felülírja a "szerkezet" konfigurálása az adatkészlet.
    * Fejléc nélküli szöveges fájlt minta "Prop_0", "Prop_1", az alapértelmezett oszlopnevek jönnek létre... Felülírja a "szerkezet" konfigurálása az adatkészlet.
    * Dynamics-adatforráshoz akkor adja meg a séma adatokat az adatkészlet "struktúra" szakaszban.

2. Explicit oszlopleképezés alkalmazni, ha meg van adva.

3. A fogadó az adatok írása

    * Előre definiált sémával olyan adattárakban az adatok írása az oszlopokat ugyanazzal a névvel.
    * Rögzített sémát nélkül adattárak és fájlformátumok esetében a nevek/metaadatok oszlop jön létre, a forrás sémája alapján.

### <a name="explicit-column-mapping"></a>Explicit oszlop-hozzárendelés

Megadhat **leképezésekben** a a **typeProperties** szakasz látható a másolási tevékenység oszlopok explicit leképezés elvégzésére. Ebben az esetben "struktúra" szakaszban szükség a mind a bemeneti és kimeneti adatkészleteket. Oszlop leképezése támogatja **összes leképezést vagy a forrásadatkészlet "struktúrát", "struktúra" fogadó-adatkészlet összes oszlopa az oszlopok**. A kivétel eredményező hiba feltételek a következők:

* Forrásadatok tárolja a lekérdezés eredménye nem rendelkezik olyan oszlopnevet, amely a bemeneti adatkészlet "struktúra" szakaszban van megadva.
* Fogadó adattár (ha az előre definiált sémával) nem rendelkezik olyan oszlopnevet, amely a kimeneti adatkészlet "struktúra" szakaszban van megadva.
* Kevesebb oszlopot vagy több oszlop szerepel a "szerkezete" fogadó-adatkészlet, mint a leképezésben megadott.
* Kettős hozzárendelés.

#### <a name="explicit-column-mapping-example"></a>Explicit oszlopleképezés – példa

Ebben a példában a bemeneti tábla struktúrája, és a egy helyszíni SQL database egyik táblájába mutat.

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

Ebben a példában a kimeneti tábla egy struktúrája, és az Azure SQL Database egyik táblájába mutat.

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

A következő JSON egy másolási tevékenységet a folyamat határozza meg. Az oszlopok hozzárendelve fogadó oszlopai forrásból (**leképezésekben**) használatával a **translator** tulajdonság.

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

Ha szintaxisát használja `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` oszlop-hozzárendelés megadása, hogy továbbra is támogatott-van.

**Oszlop-hozzárendelési folyamat:**

![Oszlop-hozzárendelési folyamat](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="schema-mapping"></a>Séma-hozzárendelés

Séma-hozzárendelés érvényes, ha a hierarchikus alakú és táblázatos alakú adatait, például a Másolás szövegfájlt a mongodb-hez, illetve a REST és másolás az SQL Azure Cosmos DB MongoDB API-közötti másolást. A következő tulajdonságokat a másolási tevékenység támogatott `translator` szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység translator type tulajdonsága értékre kell állítani: **TabularTranslator** | Igen |
| schemaMapping | Kulcs-érték párok, egy gyűjteményt, amely a leképezési kapcsolat táblázatos oldaláról hierarchikus részére jelöli.<br/>- **Kulcs:** táblázatos adatok, az oszlop neve meg van adva adatkészlet-szerkezetekben.<br/>- **Érték:** egyes mezők kinyerése és képezze le a JSON-útvonalának kifejezését. A gyökérobjektum alatti mezők esetében kezdjen a gyökér $ értékkel. A `collectionReference` tulajdonság által kiválasztott tömbben lévő mezők esetében kezdjen a tömbelemmel.  | Igen |
| collectionReference | Ha szeretné-e ismételt futtatásával és az adatok kinyerése az objektumok **egy tömbmezőben található** ugyanazt a mintát és convert objektumonkénti soronként cross-alkalmazásához a tömb JSON elérési útjának megadásához. Ez a tulajdonság csak a forrás hierarchikus adatok esetén támogatott. | Nem |

**Példa: SQL MongoDB-ből másolja:**

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
    "name": "CopyFromMongoDBToSqlAzure",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "SqlSink"
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

A Data Factory a következő, köztes adattípusokat támogatja: Írja be az adatokat konfigurálásakor ad meg alábbi értékek [adatkészlet-szerkezetekben](concepts-datasets-linked-services.md#dataset-structure) konfiguráció:

* Byte]
* Logikai
* Dátum és idő
* Datetimeoffset
* Tizedes tört
* Dupla
* GUID
* Int16
* Int32
* Int64
* Önálló
* Karakterlánc
* Időtartomány

### <a name="explicit-data-type-conversion"></a>Explicit adattípus-konverzió

Rögzített sémát, például az SQL Serverről/Oracle, az adatok másolása az adatok tárolja, amikor a forrás- és fogadó rendelkezik más típusú ugyanazt az oszlopot, amikor az explicit típusának átalakítása a forrás oldalon kell deklarálni:

* Fájl forrás például CSV/avro-hoz, a típuskonverziós kell deklarálni forrásstruktúra teljes oszlop listát (ügyféloldali oszlop nevét és a fogadó oldalon Forrástípus) keresztül
* A relációs forrás (például SQL/Oracle) a típuskonverziós a lekérdezési utasítást explicit típus formába kell megvalósítani.

## <a name="when-to-specify-dataset-structure"></a>Mikor érdemes adja meg a "adatkészletszerkezet"

Az alábbi forgatókönyvek esetén "struktúra" adatkészletben levő szükség:

* Alkalmazása [explicit adattípus-konverzió](#explicit-data-type-conversion) fájl források során másolása (a bemeneti adatkészlet)
* Alkalmazása [explicit oszlopleképezés](#explicit-column-mapping) során másolási (mindkettő bemeneti és kimeneti adatkészlet)
* Másolás Dynamics 365 és CRM forrás (a bemeneti adatkészlet)
* Ha az adatforrás nem JSON-fájlok (kimeneti adatkészlet) beágyazott objektumként Cosmos DB-hez másolása

Az alábbi forgatókönyvek esetén "struktúra" adatkészletben levő ajánlott:

* (A bemeneti adatkészlet) fejléc nélküli szöveges fájlból másol. Megadhatja, hogy a megfelelő fogadó oszlopra konfigurálása kifejezett oszlop-hozzárendelés mentése igazodó szöveges fájlt az oszlopok neveit.
* Adatok másolása tárolja rugalmas sémájú, például az Azure-tábla/Cosmos DB (bemeneti adatkészlet), a garantálja a várt adatokat (oszlop) helyett az eszközkorlátozásokkal másolási keresztül másolja a tevékenység kikövetkeztetni a séma alapján a felső sor minden tevékenység futtatása során.


## <a name="next-steps"></a>További lépések
A másolási tevékenység egyéb cikkekben talál:

- [Másolási tevékenység áttekintése](copy-activity-overview.md)
- [Másolja ki a tevékenység a hibatűrés](copy-activity-fault-tolerance.md)
- [Másolási tevékenység](copy-activity-performance.md)

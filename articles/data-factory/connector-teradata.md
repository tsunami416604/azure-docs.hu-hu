---
title: Adatok másolása a Teradata a Azure Data Factory használatával | Microsoft Docs
description: A Data Factory szolgáltatás Teradata-összekötője lehetővé teszi adatok másolását egy Teradata-adatbázisból a Data Factory által támogatott adattárakba mosogatóként.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: jingwang
ms.openlocfilehash: ddce94cab0067c34ad056a40251d79c5470ba460
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996566"
---
# <a name="copy-data-from-teradata-by-using-azure-data-factory"></a>Adatok másolása a Teradata a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
>
> * [1-es verzió](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuális verzió](connector-teradata.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Teradata-adatbázisból történő másolásához. A [másolási tevékenység áttekintésére](copy-activity-overview.md)épül.

## <a name="supported-capabilities"></a>Támogatott képességek

A Teradata-adatbázisból bármely támogatott fogadó adattárba másolhat adatok. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Ez a Teradata-összekötő a következőket támogatja:

- Teradata- **verzió: 14,10, 15,0, 15,10, 16,0, 16,10 és 16,20**.
- Adatok másolása alapszintű vagy **Windows** -hitelesítés használatával.
- Párhuzamos másolás egy Teradata-forrásból. A részletekért tekintse meg a [Parallel másolás a Teradata](#parallel-copy-from-teradata) szakaszát.

> [!NOTE]
>
> A saját üzemeltetésű Integration Runtime v 3.18 megjelenése után Azure Data Factory frissítette a Teradata-összekötőt. Az előző Teradata-összekötőt használó meglévő munkaterhelések továbbra is támogatottak. Új munkaterhelések esetén azonban érdemes az újat használni. Vegye figyelembe, hogy az új elérési útnak eltérő társított szolgáltatást, adatkészletet és másolási forrást kell létrehoznia. A konfigurációs részletekért tekintse meg a következő szakaszokat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Az Integration Runtime a 3,18-es verziótól kezdődően beépített Teradata-illesztőprogramot biztosít. Nem kell manuálisan telepítenie az illesztőprogramot. Az illesztőprogramhoz a saját C++ üzemeltetésű Integration Runtime-gépen a "Visual Redistributable 2012 Update 4" szükséges. Ha még nincs telepítve, töltse le innen. [](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)

Az 3,18-nál korábbi, saját üzemeltetésű Integration Runtime-verziók esetében telepítse a .net-adatszolgáltatót a [Teradata](https://go.microsoft.com/fwlink/?LinkId=278886), 14-ös vagy újabb verzióra az Integration Runtime-gépen. 

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a Teradata-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Teradata társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **Teradata**értékre kell beállítani. | Igen |
| connectionString | Meghatározza a Teradata adatbázis-példányhoz való kapcsolódáshoz szükséges adatokat. Tekintse át a következő mintákat.<br/>A jelszót Azure Key Vaultba is helyezheti, és `password` lekérheti a konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md) című témakört. | Igen |
| username | Adja meg a Teradata-adatbázishoz való kapcsolódáshoz használandó felhasználónevet. Windows-hitelesítés használatakor érvényes. | Nem |
| password | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Azt is megteheti, hogy [hivatkozik Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). <br>A Windows-hitelesítés használatakor, vagy az alapszintű hitelesítéshez Key Vault jelszóra hivatkozik. | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Igen |

**Példa egyszerű hitelesítés használatával**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa Windows-hitelesítés használatára**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> A következő hasznos adatok továbbra is támogatottak. Továbbra is az újat kell használnia.

**Korábbi hasznos adatok:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Ez a szakasz a Teradata adatkészlet által támogatott tulajdonságok listáját tartalmazza. Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [](concepts-datasets-linked-services.md)adatkészletek.

Az adatok Teradata történő másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát be kell állítani `TeradataTable`. | Igen |
| database | A Teradata-adatbázis neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |
| table | A tábla neve a Teradata adatbázisban. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`a Type adatkészlet továbbra is támogatott. Javasoljuk azonban, hogy az új adatkészletet használja.

**Korábbi hasznos adatok:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz a Teradata forrás által támogatott tulajdonságok listáját tartalmazza. A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata forrásként

>[!TIP]
>Az adatok Teradata történő hatékony betöltéséhez az adatparticionálás használatával tudjon meg többet a [párhuzamos másolásról a Teradata](#parallel-copy-from-teradata) szakaszból.

Az adatok Teradata történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre `TeradataSource`kell beállítani:. | Igen |
| query | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`.<br>Ha engedélyezi a particionált terhelést, össze kell kapcsolnia a lekérdezéshez tartozó beépített partíciós paramétereket. Példákat a [párhuzamos másolás Teradata](#parallel-copy-from-teradata) szakaszban talál. | Nem (ha meg van adva table az adatkészletben) |
| partitionOptions | Meghatározza az adatok Teradata való betöltéséhez használt adatparticionálási beállításokat. <br>Értékek engedélyezése: **Nincs** (alapértelmezett), **kivonat** -és **DynamicRange**.<br>Ha engedélyezve van egy partíciós beállítás (azaz nem `None`), konfigurálja a [`parallelCopies`](copy-activity-performance.md#parallel-copy) beállítást is a másolási tevékenységhez. Ez határozza meg az adatok Teradata-adatbázisból való egyidejű betöltésének párhuzamos mértékét. Beállíthatja például a következőt: 4. | Nem |
| partitionSettings | Határozza meg az adatparticionálási beállítások csoportját. <br>Akkor alkalmazza, ha a `None`partíció beállítása nem. | Nem |
| partitionColumnName | Adja meg a forrás oszlop **olyan egész típusú** nevét, amelyet a párhuzamos másolási tartomány particionálásakor fog használni. Ha nincs megadva, a rendszer automatikusan észleli a tábla elsődleges kulcsát, és a partíció oszlopként használja. <br>Akkor alkalmazza, ha a partíció `Hash` beállítás `DynamicRange`értéke vagy. Ha lekérdezést használ a forrásadatok, a Hook `?AdfHashPartitionCondition` vagy `?AdfRangePartitionColumnName` a WHERE záradék beolvasásához. Lásd: példa [párhuzamos másolással a Teradata](#parallel-copy-from-teradata) szakaszból. | Nem |
| partitionUpperBound | Az adatmásolásra szolgáló partíciós oszlop maximális értéke. <br>Akkor alkalmazza, ha a `DynamicRange`partíció lehetőség van. Ha lekérdezést használ a forrásadatok beolvasásához, a `?AdfRangePartitionUpbound` where záradékban lévő hookot. Példaként tekintse meg a [Parallel másolás a Teradata](#parallel-copy-from-teradata) szakaszát. | Nem |
| partitionLowerBound | Az adatmásolásra szolgáló partíciós oszlop minimális értéke. <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, a WHERE záradékban lévő hookot `?AdfRangePartitionLowbound` . Példaként tekintse meg a [Parallel másolás a Teradata](#parallel-copy-from-teradata) szakaszát. | Nem |

> [!NOTE]
>
> `RelationalSource`a típus másolási forrása továbbra is támogatott, de nem támogatja az új, beépített párhuzamos terhelést a Teradata (partíciós beállítások). Javasoljuk azonban, hogy az új adatkészletet használja.

**Példa: adatok másolása egy alapszintű, partíció nélküli lekérdezés használatával**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Párhuzamos másolás a Teradata

A Data Factory Teradata-összekötő beépített adatparticionálást biztosít az adatok párhuzamos Teradata való másolásához. Az adatparticionálási beállítások a másolási tevékenység **forrástábla** tábláján találhatók.

![Képernyőfelvétel a partíciós beállításokról](./media/connector-teradata/connector-teradata-partition-options.png)

A particionált másolás engedélyezésekor a Data Factory párhuzamos lekérdezéseket futtat a Teradata-forráson az adatpartíciók betöltéséhez. A párhuzamos mértéket a másolási [`parallelCopies`](copy-activity-performance.md#parallel-copy) tevékenység beállításai vezérlik. Ha például a négy értékre `parallelCopies` van állítva, Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés a Teradata-adatbázisból származó adatok egy részét kéri le.

Célszerű engedélyezni a párhuzamos másolást az adatparticionálással, különösen akkor, ha nagy mennyiségű adattal tölt be a Teradata-adatbázisból. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációk szerepelnek. Az adatok file-alapú adattárba való másolása során a rendszer úgy helyezi át, hogy több fájlként írjon egy mappába (csak a mappa nevét adja meg), amely esetben a teljesítmény jobb, mint egyetlen fájl írásakor.

| Forgatókönyv                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés a nagyméretű táblából.                                   | **Partíciós beállítás**: Hash. <br><br/>A végrehajtás során a Data Factory automatikusan észleli a PK-oszlopot, alkalmaz egy kivonatot, és az adatokat partíciók szerint másolja. |
| Nagy mennyiségű adattal tölthetők be egyéni lekérdezések használatával.                 | **Partíciós beállítás**: Hash.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Partíciós oszlop**: A kivonatoló partíció alkalmazásához használt oszlop megadása. Ha nincs megadva, a Data Factory automatikusan észleli a Teradata adatkészletben megadott tábla PK oszlopát.<br><br>A végrehajtás során Data Factory helyettesíti `?AdfHashPartitionCondition` a kivonatoló partíció logikáját, és elküldi a Teradata. |
| Nagy mennyiségű adattal tölthetők be egy egyéni lekérdezéssel, amely egy egész számú oszlopból álló, egyenletesen elosztott értékkel rendelkezik a tartomány particionálásához. | **Partíciós beállítások**: Dinamikus tartomány partíciója.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partíciós oszlop**: Az adatparticionáláshoz használt oszlop meghatározása. Az oszlop egész adattípussal is particionálható.<br>**Partíció felső határa** és a **partíció alsó határa**: Adja meg, hogy a partíció oszlop alapján szeretne-e szűrést végezni, hogy csak az alsó és a felső tartomány között legyenek az adatlekérdezések.<br><br>A végrehajtás során a Data Factory `?AdfRangePartitionColumnName`lecseréli `?AdfRangePartitionLowbound` `?AdfRangePartitionUpbound`, és az egyes partíciók tényleges oszlop-és értéktartomány-tartományát, és elküldi a Teradata. <br>Ha például az "ID" partíciós oszlop az 1 értékre van állítva, a felső határ pedig 80, a párhuzamos másolási beállítás értéke 4, Data Factory 4 partíció alapján kérdezi le az adatmennyiséget. Az azonosítóik [1, 20], [21, 40], [41, 60] és [61, 80] között vannak. |

**Példa: lekérdezés kivonatoló partícióval**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Példa: lekérdezés dinamikus tartományú partícióval**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Adattípusok leképezése Teradata

Az adatok Teradata történő másolásakor a következő leképezések érvényesek. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadónak, tekintse meg a [séma-és adattípus](copy-activity-schema-and-type-mapping.md)-leképezéseket.

| Teradata adattípusa | Data Factory közbenső adattípus |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |Sztring |
| Clob |Sztring |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Integer |Int32 |
| Interval Day |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Interval Day To Hour |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Interval Day To Minute |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Interval Day To Second |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Interval Hour |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Interval Hour To Minute |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Interval Hour To Second |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Interval Minute |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Interval Minute To Second |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Interval Month |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Interval Second |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Interval Year |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Interval Year To Month |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Number |Double |
| Időszak (dátum) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időszak (idő) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időtartam (idő zónával) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időszak (timestamp) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időtartam (időbélyegző az időzónával) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |Sztring |
| VarGraphic |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Xml |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |


## <a name="next-steps"></a>További lépések
A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

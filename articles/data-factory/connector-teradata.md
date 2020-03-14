---
title: Adatok másolása a Teradata Vantage-ból Azure Data Factory használatával
description: A Data Factory szolgáltatás Teradata-összekötője lehetővé teszi az adatok másolását a Data Factory által támogatott adattárakba a Teradata.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 5a41d5653de0d8a9f674009904756892ac343609
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281729"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Adatok másolása a Teradata Vantage-ból Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
>
> * [1-es verzió](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuális verzió](connector-teradata.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához a Teradata Vantage-ból. A [másolási tevékenység áttekintésére](copy-activity-overview.md)épül.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Teradata-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Teradata Vantage-ból származó adatok másolása bármely támogatott fogadó adattárba lehetséges. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez a Teradata-összekötő a következőket támogatja:

- Teradata- **verzió: 14,10, 15,0, 15,10, 16,0, 16,10 és 16,20**.
- Adatok másolása **Alapszintű** vagy **Windows** -hitelesítés használatával.
- Párhuzamos másolás egy Teradata-forrásból. A részletekért tekintse meg a [Parallel másolás a Teradata](#parallel-copy-from-teradata) szakaszát.

> [!NOTE]
>
> A saját üzemeltetésű Integration Runtime v 3.18 megjelenése után Azure Data Factory frissítette a Teradata-összekötőt. Az előző Teradata-összekötőt használó meglévő munkaterhelések továbbra is támogatottak. Új munkaterhelések esetén azonban érdemes az újat használni. Vegye figyelembe, hogy az új elérési útnak eltérő társított szolgáltatást, adatkészletet és másolási forrást kell létrehoznia. A konfigurációs részletekért tekintse meg a következő szakaszokat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Az Integration Runtime a 3,18-es verziótól kezdődően beépített Teradata-illesztőprogramot biztosít. Nem kell manuálisan telepítenie az illesztőprogramot. Az illesztőprogramhoz a saját C++ üzemeltetésű Integration Runtime-gépen a "Visual Redistributable 2012 Update 4" szükséges. Ha még nincs telepítve, [töltse le innen.](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)

Az 3,18-nál korábbi, saját üzemeltetésű Integration Runtime-verziók esetében telepítse a [.net-adatszolgáltatót a Teradata](https://go.microsoft.com/fwlink/?LinkId=278886), 14-ös vagy újabb verzióra az Integration Runtime-gépen. 

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a Teradata-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Teradata társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **Teradata**értékre kell beállítani. | Igen |
| connectionString | Meghatározza az Teradata-példányhoz való kapcsolódáshoz szükséges adatokat. Tekintse át a következő mintákat.<br/>A jelszót Azure Key Vaultba is helyezheti, és lekérheti a `password` konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md) című témakört. | Igen |
| felhasználónév | Adja meg a Teradata való kapcsolódáshoz használandó felhasználónevet. Windows-hitelesítés használatakor érvényes. | Nem |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Azt is megteheti, hogy [hivatkozik Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). <br>A Windows-hitelesítés használatakor, vagy az alapszintű hitelesítéshez Key Vault jelszóra hivatkozik. | Nem |
| connectVia | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Igen |

További kapcsolatok tulajdonságai az adott esetben a kapcsolatok karakterláncában állíthatók be:

| Tulajdonság | Leírás | Alapértelmezett érték |
|:--- |:--- |:--- |
| CharacterSet | A munkamenethez használandó karakterkészlet Például `CharacterSet=UTF16`.<br><br/>Ez az érték lehet felhasználó által definiált karakterkészlet, vagy a következő előre definiált karakterkészletek egyike: <br/>– ASCII<br/>-UTF8<br/>– UTF16<br/>– LATIN1252_0A<br/>– LATIN9_0A<br/>– LATIN1_0A<br/>-SHIFT-JIS (Windows, DOS-kompatibilis, KANJISJIS_0S)<br/>-EUC (UNIX-kompatibilis, KANJIEC_0U)<br/>-IBM mainframe (KANJIEBCDIC5035_0I)<br/>– KANJI932_1S0<br/>-BIG5 (TCHBIG5_1R0)<br/>-GB (SCHGB2312_1T0)<br/>– SCHINESE936_6R0<br/>– TCHINESE950_8R0<br/>-NetworkKorean (HANGULKSC5601_2R4)<br/>– HANGUL949_7R0<br/>– ARABIC1256_6A0<br/>– CYRILLIC1251_2A0<br/>– HEBREW1255_5A0<br/>– LATIN1250_1A0<br/>– LATIN1254_7A0<br/>– LATIN1258_8A0<br/>– THAI874_4A0 | Az alapértelmezett érték `ASCII`. |
| MaxRespSize |Az SQL-kérelmekre vonatkozó válasz puffer maximális mérete (kilobájtban) (Tudásbázis). Például `MaxRespSize=‭10485760‬`.<br/><br/>A Teradata-adatbázis 16,00-es vagy újabb verziójához a maximális érték a 7361536. A korábbi verziókat használó kapcsolatok esetében a maximális érték 1048576. | Az alapértelmezett érték `65536`. |

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

Ez a szakasz a Teradata adatkészlet által támogatott tulajdonságok listáját tartalmazza. Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md).

Az adatok Teradata történő másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát `TeradataTable`értékre kell beállítani. | Igen |
| database | Az Teradata-példány neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |
| table | A Teradata-példányban található tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

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
> `RelationalTable` típus adatkészlete továbbra is támogatott. Javasoljuk azonban, hogy az új adatkészletet használja.

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

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát `TeradataSource`értékre kell állítani. | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`.<br>Ha engedélyezi a particionált terhelést, össze kell kapcsolnia a lekérdezéshez tartozó beépített partíciós paramétereket. Példákat a [párhuzamos másolás Teradata](#parallel-copy-from-teradata) szakaszban talál. | Nem (ha meg van adva table az adatkészletben) |
| partitionOptions | Meghatározza az adatok Teradata való betöltéséhez használt adatparticionálási beállításokat. <br>Értékek engedélyezése: **none** (alapértelmezett), **kivonat** -és **DynamicRange**.<br>Ha engedélyezve van egy partíciós beállítás (azaz nem `None`), akkor a Teradata származó adatok párhuzamos betöltésének fokát a másolási tevékenység [`parallelCopies`](copy-activity-performance.md#parallel-copy) beállítása vezérli. | Nem |
| partitionSettings | Határozza meg az adatparticionálási beállítások csoportját. <br>Akkor alkalmazza, ha a partíció beállítása nem `None`. | Nem |
| partitionColumnName | Adja meg annak a forrás oszlopnak a nevét, amelyet a tartomány partíció vagy a kivonatoló partíció használ a párhuzamos másoláshoz. Ha nincs megadva, a rendszer automatikusan észleli a tábla elsődleges indexét, és a partíciós oszlopként használja. <br>Akkor alkalmazza, ha a partíciós beállítás `Hash` vagy `DynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, a Hook `?AdfHashPartitionCondition` vagy `?AdfRangePartitionColumnName` a WHERE záradékban. Lásd: példa [párhuzamos másolással a Teradata](#parallel-copy-from-teradata) szakaszból. | Nem |
| partitionUpperBound | Az adatmásolásra szolgáló partíciós oszlop maximális értéke. <br>Akkor alkalmazza, ha a partíció beállítása `DynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, a Hook `?AdfRangePartitionUpbound` a WHERE záradékban. Példaként tekintse meg a [Parallel másolás a Teradata](#parallel-copy-from-teradata) szakaszát. | Nem |
| partitionLowerBound | Az adatmásolásra szolgáló partíciós oszlop minimális értéke. <br>Akkor alkalmazza, ha a partíció beállítása `DynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, a Hook `?AdfRangePartitionLowbound` a WHERE záradékban. Példaként tekintse meg a [Parallel másolás a Teradata](#parallel-copy-from-teradata) szakaszát. | Nem |

> [!NOTE]
>
> `RelationalSource` típus másolási forrása továbbra is támogatott, de nem támogatja az új, beépített párhuzamos terhelést a Teradata (partíciós beállítások). Javasoljuk azonban, hogy az új adatkészletet használja.

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

A particionált másolás engedélyezésekor a Data Factory párhuzamos lekérdezéseket futtat a Teradata-forráson az adatpartíciók betöltéséhez. A párhuzamos fokot a másolási tevékenység [`parallelCopies`](copy-activity-performance.md#parallel-copy) beállítása vezérli. Ha például úgy állítja be a `parallelCopies`t négyre, Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés a Teradata származó adatok egy részét kéri le.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatok particionálásával, különösen akkor, ha nagy mennyiségű adatmennyiséget tölt be a Teradata. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációk szerepelnek. Az adatok file-alapú adattárba való másolása során a rendszer úgy helyezi át, hogy több fájlként írjon egy mappába (csak a mappa nevét adja meg), amely esetben a teljesítmény jobb, mint egyetlen fájl írásakor.

| Forgatókönyv                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés a nagyméretű táblából.                                   | **Partíciós beállítás**: kivonat. <br><br/>A végrehajtás során a Data Factory automatikusan észleli a PK-oszlopot, alkalmaz egy kivonatot, és az adatokat partíciók szerint másolja. |
| Nagy mennyiségű adattal tölthetők be egyéni lekérdezések használatával.                 | **Partíciós beállítás**: kivonat.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Partíciós oszlop**: a kivonatoló partíció alkalmazásához használt oszlop megadása. Ha nincs megadva, a Data Factory automatikusan észleli a Teradata adatkészletben megadott tábla PK oszlopát.<br><br>A végrehajtás során a Data Factory lecseréli az `?AdfHashPartitionCondition`t a kivonatoló partíciós logikával, és elküldi a Teradata. |
| Nagy mennyiségű adattal tölthetők be egy egyéni lekérdezéssel, amely egy egész számú oszlopból álló, egyenletesen elosztott értékkel rendelkezik a tartomány particionálásához. | **Partíciós beállítások**: dinamikus tartományú partíció.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partíciós oszlop**: az adatparticionáláshoz használt oszlop megadására szolgál. Az oszlop egész adattípussal is particionálható.<br>**Partíció felső határa** és a **partíció alsó határa**: adja meg, hogy a partíció oszlop alapján szeretne-e szűrést végezni, hogy csak az alsó és a felső tartomány között legyenek lekérdezve.<br><br>A végrehajtás során a Data Factory lecseréli a `?AdfRangePartitionColumnName`, a `?AdfRangePartitionUpbound`és az `?AdfRangePartitionLowbound` értéket az egyes partíciók tényleges oszlop-és értéktartomány-tartományával, és elküldi a Teradata. <br>Ha például az "ID" partíciós oszlop az 1 értékre van állítva, a felső határ pedig 80, a párhuzamos másolási beállítás értéke 4, Data Factory 4 partíció alapján kérdezi le az adatmennyiséget. Az azonosítóik [1, 20], [21, 40], [41, 60] és [61, 80] között vannak. |

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

Az adatok Teradata történő másolásakor a következő leképezések érvényesek. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadónak, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md).

| Teradata adattípusa | Data Factory közbenső adattípus |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Bájt |Byte[] |
| ByteInt |Int16 |
| CHAR |Sztring |
| Clob |Sztring |
| Dátum |DateTime |
| tizedes tört |tizedes tört |
| Dupla |Dupla |
| Graphic |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Egész szám |Int32 |
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
| Szám |Dupla |
| Időszak (dátum) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időszak (idő) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időtartam (idő zónával) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időszak (timestamp) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időtartam (időbélyegző az időzónával) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| SmallInt |Int16 |
| Time |Időtartam |
| Time With Time Zone |Időtartam |
| Időbélyeg |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |Sztring |
| VarGraphic |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Xml |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |


## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések
A Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

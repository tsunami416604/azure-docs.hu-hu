---
title: Adatok másolása az Azure Data Factory használatával Teradata |} A Microsoft Docs
description: Ismerje meg a Data Factory szolgáltatás, amely lehetővé teszi a Teradata-összekötő által a Data Factory fogadóként támogatott adattárak Teradata-adatbázisból adatok másolása.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 63f28c8b6eaceed12e1f76e9c0c5984e3b63b500
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561428"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Teradata
> [!div class="op_single_selector" title1="Válassza ki a Data Factory szolgáltatás használ:"]
>
> * [1-es verzió](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuális verzió](connector-teradata.md)

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol egy Teradata-adatbázis használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok bármely támogatott fogadó adattárba másolhatja Teradata-adatbázisból. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

A Teradata-összekötőt, a következőket támogatja:

- Teradata **verzió 14.10, 15.0, 15.10, 16,0, 16.10 és 16.20**.
- Másolja az adatokat az **alapszintű** vagy **Windows** hitelesítést.
- Párhuzamos másolási Teradata-forrásból. Lásd: [Teradata másolatot párhuzamos](#parallel-copy-from-teradata) részletei szakaszban.

> [!NOTE]
>
> Az Azure Data Factory a Teradata-összekötő frissítése óta v3.18 helyi integrációs modul, amely fel egy beépített ODBC-illesztőt, valamint lehetővé teszi a rugalmas csatlakozási lehetőségek, valamint out-of-box párhuzamos másolási teljesítmény növelése érdekében. Meglévő számítási feladatait az előző Teradata-összekötővel lehetőségek .NET-adatszolgáltató a Teradata továbbra is támogatott, mint-van, amíg, a jövőben újat használata javasolt. Vegye figyelembe, hogy az új elérési utat igényli a társított szolgáltatás/adatkészlet/másolási forrásaként külön készletét. További információt a megfelelő szakasz konfigurációs talál.

## <a name="prerequisites"></a>Előfeltételek

Ha a Teradata nem érhető el nyilvánosan, meg kell egy helyi Integration Runtime beállítása. Az integration runtime kapcsolatos további információkért lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md). Az Integration Runtime biztosít beépített Teradata illesztőprogram verziója 3.18 kezdve, ezért nem kell manuálisan telepítenie az összes illesztőprogramot. A meghajtó csak "vizuális C++ újraterjeszthető csomag 2012 Update 4" a helyi integrációs modul gépen töltse le a [Itt](https://www.microsoft.com/en-sg/download/details.aspx?id=30679) Ha még nincs telepítve.

A helyi integrációs modul alacsonyabb, mint 3.18 esetén telepítenie kell a [.NET-adatszolgáltató a teradata rendszerhez](https://go.microsoft.com/fwlink/?LinkId=278886) 14-es verziót vagy újabb a saját üzemeltetésű integrációs gépen. 

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Teradata-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Teradata-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Teradata** | Igen |
| connectionString | Adja meg a Teradata-adatbázis-példányhoz való kapcsolódáshoz szükséges információkat. Tekintse meg a következő minták.<br/>Jelszó az Azure Key Vault és lekéréses is helyezheti a `password` konfigurációs ki a kapcsolati karakterláncot. Tekintse meg [Store hitelesítő adatokat az Azure Key Vaultban](store-credentials-in-key-vault.md) további részleteket a cikkben. | Igen |
| username | Adja meg a felhasználónevet a Teradata-adatbázishoz való csatlakozáshoz. Windows-hitelesítés használata esetén érvényes. | Nem |
| password | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Azt is beállíthatja a [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). <br>Windows-hitelesítés használata esetén alkalmazza, vagy a Key Vault alapszintű hitelesítés jelszava hivatkozik. | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Egy helyi Integration Runtime szükség, az említett [Előfeltételek](#prerequisites). |Igen |

**Például: alapszintű hitelesítést használ**

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

**Példa: Windows-hitelesítés használatával**

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
> Ha .NET-adatszolgáltató a kapcsolódószolgáltatás-lehetőségek Teradata a teradata rendszerhez az alábbi hasznos adattal használja, továbbra is támogatott-van, amíg, a jövőben újat használata javasolt.

**Előző hasznos:**

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a Teradata adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása a Teradata, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **TeradataTable** | Igen |
| database | Teradata-adatbázis neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |
| table | A Teradata-adatbázishoz a tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

> [!NOTE]
>
> Ha "RelationalTable" típusú adatkészlet a következőképpen használta, továbbra is támogatott-van, amíg, a jövőben újat használata javasolt.

**Előző hasznos:**

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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Teradata-adatforrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="teradata-as-source"></a>Teradata forrásként

> [!TIP]
>
> További információkat talál a [Teradata másolatot párhuzamos](#parallel-copy-from-teradata) hogyan lehet adatokat betölteni a Teradata hatékony használata, az adatparticionálás szakaszán.

Teradata adatmásolás a másolási tevékenység a következő tulajdonságok támogatottak **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **TeradataSource** | Igen |
| query | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`.<br>Ha engedélyezi a particionált terhelés, kell megfelelő beépített partíció történt a lekérdezés környezet igénybe vételét. Példák a [Teradata másolatot párhuzamos](#parallel-copy-from-teradata) szakaszban. | Nem (ha az adatkészlet tábla van megadva) |
| partitionOptions | Az adatparticionálási betölteni az adatokat a Teradata használt beállításokat megadja. <br>Lehetővé teszi az értékek a következők: **Nincs** (alapértelmezett), **kivonatoló** és **DynamicRange**.<br>Ha engedélyezve van a partíció lehetőséget (nem "nincs"), állítsa is be **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** beállítása a másolási tevékenység, pl.: 4, amely meghatározza, hogy a párhuzamos párhuzamossági egy időben betölteni az adatokat a teradata rendszerhez az adatbázis. | Nem |
| partitionSettings | Adja meg a csoport, az adatparticionálás beállításait. <br>Alkalmazza, ha partíció lehetőséget nem `None`. | Nem |
| partitionColumnName | Adja meg a forrásoszlop neve **az egész típusú** particionálásával tartomány párhuzamos másolásához használni kívánt. Ha nincs megadva, a tábla elsődleges kulcsa nem észlelt, és szerepel partícióoszlop automatikus. <br>Alkalmazza, ha partíció lehetőséget `Hash` vagy `DynamicRange`. Ha a lekérdezések használatával lekérheti a forrásadatokat, a környezet igénybe vételét `?AdfHashPartitionCondition` vagy `?AdfRangePartitionColumnName` WHERE záradékban. A példa [Teradata másolatot párhuzamos](#parallel-copy-from-teradata) szakaszban. | Nem |
| partitionUpperBound | Az adatokat másolja a partíciós oszlopának legnagyobb értékét. <br>Alkalmazza, ha partíció lehetőséget `DynamicRange`. Ha a lekérdezések használatával lekérheti a forrásadatokat, a környezet igénybe vételét `?AdfRangePartitionUpbound` WHERE záradékban. A példa [Teradata másolatot párhuzamos](#parallel-copy-from-teradata) szakaszban. | Nem |
| partitionLowerBound | Minimális érték a partícióoszlop adatmásolás céljából. <br>Alkalmazza, ha partíció lehetőséget `DynamicRange`. Ha a lekérdezések használatával lekérheti a forrásadatokat, a környezet igénybe vételét `?AdfRangePartitionLowbound` WHERE záradékban. A példa [Teradata másolatot párhuzamos](#parallel-copy-from-teradata) szakaszban. | Nem |

> [!NOTE]
>
> Ha használta a "RelationalSource" típusa másolási forrásaként, továbbra is támogatott-, de nem támogatja az új beépített párhuzamosan töltse be a Teradata (partíció beállításai). Használata javasolt, ez a jövőben újat használatára.

**. Példa: alapszintű lekérdezési partíció nélkül használatával adatok másolása**

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

## <a name="parallel-copy-from-teradata"></a>Teradata párhuzamos másolását.

Data factory Teradata-összekötő használatával adatait átmásolhatja a Teradata nagy teljesítményű párhuzamos particionálás beépített adatokat biztosít. A másolási tevékenység adatok particionálási beállításai -> Teradata-adatforrás található:

![Partíciós beállítások](./media/connector-teradata/connector-teradata-partition-options.png)

Ha engedélyezi a particionált másolási, adat-előállító párhuzamos lekérdezések fut a Teradata forrás partíció betölteni az adatokat. A párhuzamos párhuzamossági van beállítva, és keresztül **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** másolási tevékenység beállítást. Például, ha a beállított `parallelCopies` négy, egyidejűleg állít elő, a data factory és a megadott partíció lehetőséget és a beállításokat, minden a Teradata-adatbázis adatainak lekérése során részének alapján négy lekérdezések futtatása.

Használata javasolt, párhuzamos másolás engedélyezéséhez az adatparticionálás, különösen akkor, ha nagy mennyiségű adat tölthető be, Teradata-adatbázisból. A különböző helyzetekhez javasolt konfigurációk a következők:

| Forgatókönyv                                                     | Ajánlott beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| A nagy méretű tábla teljes terhelés                                   | **Beállítás particionálása**: Kivonat. <br><br/>A futtatás során a data Factory automatikusan észleli oszlophoz, a alkalmazni kivonata alapján és adatok másolása a partíció. |
| Nagy mennyiségű adat használatával egyéni lekérdezés betöltése                 | **Beállítás particionálása**: Kivonat.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Partícióoszlop**: Adja meg az oszlop alkalmaz kivonatoló partíció használt. Ha nincs megadva, az ADF automatikusan észleli az a tábla a Teradata-adatkészletben megadott oszlophoz.<br><br>Data factory cserélje le a futtatás során `?AdfHashPartitionCondition` a kivonat partíció logikai és a Teradata küld. |
| Nagy mennyiségű adat használatával egyéni lekérdezés egyenlően elosztott értékkel tartomány particionálás egészszám-oszloppal rendelkező betöltése | **Beállítások particionálása**: Dinamikus tartományának partíció.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partícióoszlop**: Adja meg az adatok particionálása használt oszlop. Egész szám adattípusú particionáló oszlop ellen.<br>**Partíció felső határérték** és **partíció alsó határérték**: Adja meg, hogy szeretné-e be csak alsó és felső tartományba eső adatokat partícióoszlop szűrést.<br><br>Data factory cserélje le a futtatás során `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, és `?AdfRangePartitionLowbound` a tényleges oszlop nevét és értékét az egyes tartományok particionálja, és Teradata küldje. <br>Például, ha a partíció oszlop "ID" csoportot az alsó határ értékének 1 és 80-as, a párhuzamos készletet, 4, mint a felső határérték ADF adatbeolvasás 4 partíció azonosítójú között [1,20], [21, 40], [41, 60] és [61, 80]. |

**Példa: lekérdezés kivonatoló partíció**

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

**Példa: lekérdezés dinamikus tartományának partíció**

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

## <a name="data-type-mapping-for-teradata"></a>Adattípus-leképezés a teradata rendszerhez

Ha az adatok másolása a Teradata, Azure Data Factory-közbenső adattípusok a következő hozzárendeléseket használtak Teradata adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| Teradata-adattípus | Data factory közbenső adattípus |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Integer |Int32 |
| Interval Day |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Interval Day To Hour |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Interval Day To Minute |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Interval Day To Second |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Interval Hour |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Interval Hour To Minute |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Interval Hour To Second |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Interval Minute |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Interval Minute To Second |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Interval Month |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Interval Second |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Interval Year |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Interval Year To Month |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Szám |Double |
| Időszak (dátum) |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Időszak (idő) |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Időszak (idő időzónával együtt) |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Időszak (Timestamp) |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Időszak (Timestamp időzónával együtt) |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |
| Xml |Nem támogatott. A alkalmazni az adatforrás-lekérdezés explicit átalakítást. |


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

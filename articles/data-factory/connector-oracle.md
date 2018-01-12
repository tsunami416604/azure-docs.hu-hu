---
title: "Adatok másolása az Azure Data Factory használatával Oracle |} Microsoft Docs"
description: "Ismerje meg az adatok másolása az Oracle-adatbázishoz a támogatott forráshierarchiából áruházakból (vagy) az Oracle támogatott fogadó áruházak Data Factory használatával."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: 10db7959396b4ee9927e4272dec9939ac8c13580
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-and-to-oracle-using-azure-data-factory"></a>Másolja az adatokat, a kezdő és a Oracle Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-onprem-oracle-connector.md)
> * [2. verzió – Előzetes verzió](connector-oracle.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory adatok másolása az és Oracle-adatbázishoz. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Oracle-összekötőt a V1](v1/data-factory-onprem-oracle-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok másolása az Oracle-adatbázishoz az egyetlen támogatott fogadó adattár, vagy adatok másolása az egyetlen támogatott forrás adattár az Oracle-adatbázishoz. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az Oracle-összekötő Oracle-adatbázishoz a következő verzióit támogatja, és a Basic vagy az OID hitelesítések támogatja.

- Oracle 12c R1 (12.1)
- Oracle 11g R1 vagy R2 (11.1, 11.2)
- Oracle 10g R1 vagy R2 (10.1, 10,2)
- Oracle 9i R1 vagy R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7-es)

## <a name="prerequisites"></a>Előfeltételek

Az adatok másolása az/Oracle-adatbázishoz, amely nincs nyilvánosan elérhető, akkor be kell állítania egy Self-hosted integrációs futásidejű. Lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) szóló cikkben olvashat integrációs futásidejű. Integrációs futásidejű biztosít egy beépített Oracle-illesztőprogramot, ezért nem, manuálisan kell telepítenie minden olyan illesztőprogram a/az Oracle adatok másolásakor kell.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják megadhatók a Data Factory tartozó entitások Oracle-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Oracle kapcsolódó szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Oracle** | Igen |
| connectionString | Adja meg az Oracle-adatbázispéldány való kapcsolódáshoz szükséges adatokat. Ez a mező megjelölése a SecureString.<br><br>**Kapcsolat típusa támogatott**: szeretne használni **Oracle SID** vagy **Oracle szolgáltatásnév** az adatbázis azonosításához:<br>-SID használata:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Szolgáltatás neve használatával:`Host=<host>;Port=<port>;ServiceName=<sid>;User Id=<username>;Password=<password>;` | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az Oracle-adatkészlet által támogatott tulajdonságokról.

Másolja az adatokat, vagy Oracle, állítsa be a adatkészlet type tulajdonsága **OracleTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **OracleTable** | Igen |
| tableName |A tábla az Oracle-adatbázishoz, amely hivatkozik a társított szolgáltatás neve. | Igen |

**Példa**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Oracle-forrás és a fogadó által támogatott tulajdonságokról.

### <a name="oracle-as-source"></a>Oracle forrásaként

Adatok másolása Oracle, állítsa be a forrás típusa a másolási tevékenység **OracleSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **OracleSource** | Igen |
| oracleReaderQuery | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM MyTable"`. | Nem |

Ha nem adja meg a "oracleReaderQuery", az adatkészletet a "structure" szakaszában meghatározott oszlopokat segítségével olyan lekérdezést (`select column1, column2 from mytable`) futtatásához az Oracle-adatbázishoz. Az adatkészlet-definícióban nem rendelkezik a "structure", ha minden kiválasztott oszlop. a táblából.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>A fogadó, Oracle

Adatok másolása Oracle, állítsa be a fogadó típusa a másolási tevékenység **OracleSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **OracleSink** | Igen |
| WriteBatchSize | Szúr be az SQL-tábla adatokat, amikor a puffer mérete eléri writeBatchSize.<br/>Két érték engedélyezett: egész szám (sorok száma). |Nem (alapértelmezett beállítás 10000) |
| writeBatchTimeout | Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot.<br/>Két érték engedélyezett: Timespan. . Példa: 00:30:00 (30 perc). | Nem |
| preCopyScript | Adjon meg egy SQL-lekérdezést végrehajtani az adatok írása az Oracle minden futtatása előtt a másolási tevékenységhez. Ez a tulajdonság segítségével törölje az előre betöltött adatokat. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Adattípus-leképezés az Oracle rendszerhez

A/az Oracle adatok másolásakor a következő megfeleltetéseket szolgálnak az Oracle típusú adatokat Azure Data Factory ideiglenes adattípusokat. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| Oracle-adattípusra | Data factory ideiglenes adattípus |
|:--- |:--- |
| BFÁJL |Byte] |
| A BLOB |Byte]<br/>(csak támogatja az Oracle 10g és újabb) |
| KARAKTER |Karakterlánc |
| CLOB |Karakterlánc |
| DATE |DateTime |
| LEBEGŐPONTOS |Decimális, karakterlánc (Ha pontosság > 28) |
| EGÉSZ SZÁM |Decimális, karakterlánc (Ha pontosság > 28) |
| HOSSZÚ |Karakterlánc |
| HOSSZÚ NYERS |Byte] |
| NCHAR |Karakterlánc |
| NCLOB |Karakterlánc |
| SZÁM |Decimális, karakterlánc (Ha pontosság > 28) |
| NVARCHAR2 |Karakterlánc |
| NYERS |Byte] |
| ROWID |Karakterlánc |
| IDŐBÉLYEG |DateTime |
| A HELYI IDŐZÓNÁRA IDŐBÉLYEG |Karakterlánc |
| AZ IDŐZÓNA IDŐBÉLYEG |Karakterlánc |
| ELŐJEL NÉLKÜLI EGÉSZKÉNT. |Szám |
| VARCHAR2 |Karakterlánc |
| XML |Karakterlánc |

> [!NOTE]
> Adattípus IDŐKÖZ év, hónap és nap TO IDŐKÖZ második nem támogatottak.


## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).
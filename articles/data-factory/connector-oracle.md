---
title: Adatok másolása és Oracle az Azure Data Factory használatával |} Microsoft Docs
description: Ismerje meg az adatok másolása támogatott forrás áruházakból Oracle-adatbázishoz, vagy a támogatott fogadó áruházak Oracle Data Factory használatával.
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
ms.date: 06/14/2018
ms.author: jingwang
ms.openlocfilehash: 6a232787793f9f4992a4dece821ae0bcc9059afc
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37058918"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Adatok másolása az és Oracle Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuális verzió](connector-oracle.md)

Ez a cikk ismerteti a másolási tevékenység használata az Azure Data Factory-adatok másolása az és Oracle-adatbázishoz. Buildekről nyújtanak a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok bármely támogatott fogadó adattárolóhoz másolhatja Oracle-adatbázishoz. Is másolhatja adatok bármely támogatott forrás adattár Oracle-adatbázishoz. A másolási tevékenység által támogatott adatforrások vagy mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Oracle-összekötő kifejezetten, Oracle-adatbázishoz a következő verzióit támogatja. Basic vagy az OID hitelesítések is támogatja:

- Oracle 12c R1 (12.1)
- Oracle 11g R1 vagy R2 (11.1, 11.2)
- Oracle 10g R1 vagy R2 (10.1, 10,2)
- Oracle 9i R1 vagy R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7-es)

> [!Note]
> Oracle-proxy kiszolgáló nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Adatok másolása az és Oracle-adatbázishoz, amely nem a nyilvánosan elérhető, akkor be kell állítania egy Self-hosted integrációs futásidejű. Integrációs futásidejű kapcsolatos további információkért lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md). Az integrációs futásidejű beépített Oracle illesztőprogram biztosít. Emiatt nem kell manuálisan a kezdő és a Oracle adatok másolása az illesztőprogram telepítéséhez.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory tartozó entitások és az Oracle-összekötő tulajdonságait.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Az Oracle csatolt szolgáltatás a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot meg kell **Oracle**. | Igen |
| connectionString | Adja meg az Oracle-adatbázispéldány való kapcsolódáshoz szükséges adatokat. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md).<br><br>**Kapcsolattípus támogatott**: használható **Oracle SID** vagy **Oracle szolgáltatásnév** az adatbázis azonosításához:<br>– Ha SID használ: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Ha a szolgáltatás a nevet használja: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Igen |
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a rész felsorolja az Oracle-adatkészlet által támogatott tulajdonságokról.

Adatok másolása az és Oracle, állítsa be a type tulajdonságot az adathalmaz **OracleTable**. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani **OracleTable**. | Igen |
| tableName |A neve annak a táblának az Oracle-adatbázishoz, amelyre a társított szolgáltatás hivatkozik. | Igen |

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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a rész felsorolja az Oracle-forrás és a fogadó által támogatott tulajdonságokról.

### <a name="oracle-as-a-source-type"></a>Oracle a forrás típusa

Adatok másolása Oracle, állítsa be a forrás típusa a másolási tevékenység **OracleSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani **OracleSource**. | Igen |
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

### <a name="oracle-as-a-sink-type"></a>Oracle a fogadó típusa

Adatok másolása Oracle, állítsa be a fogadó típusa a másolási tevékenység **OracleSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani **OracleSink**. | Igen |
| writeBatchSize | Szúr be az SQL-tábla adatokat, amikor a puffer mérete eléri writeBatchSize.<br/>Megengedett értékek: egész szám (sorok száma). |Nem (alapértelmezett érték 10 000) |
| writeBatchTimeout | Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot.<br/>Megengedett értékek: Timespan. Egy példa: 00:30:00 (30 perc). | Nem |
| preCopyScript | Adja meg a másolási tevékenység végrehajtása előtt minden egyes futtatásához írt adatok az Oracle egy SQL-lekérdezést. Ez a tulajdonság segítségével törölje az előre betöltött adatokat. | Nem |

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

A kezdő és Oracle a másolt adatok, a következő megfeleltetéseket segítségével Oracle típusú adatokat a Data Factory ideiglenes adattípusokat. Hogyan a másolási tevékenység van leképezve a séma- és adatok típusa a fogadó kapcsolatos további tudnivalókért lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md).

| Oracle-adattípusra | Data Factory ideiglenes adattípus |
|:--- |:--- |
| BFILE |Byte] |
| A BLOB |Byte]<br/>(csak támogatja az Oracle 10g és újabb) |
| KARAKTER |Sztring |
| CLOB |Sztring |
| DATE |DateTime |
| LEBEGŐPONTOS |Decimális, karakterlánc (Ha pontosság > 28) |
| EGÉSZ SZÁM |Decimális, karakterlánc (Ha pontosság > 28) |
| HOSSZÚ |Sztring |
| HOSSZÚ NYERS |Byte] |
| NCHAR |Sztring |
| NCLOB |Sztring |
| SZÁM |Decimális, karakterlánc (Ha pontosság > 28) |
| NVARCHAR2 |Sztring |
| RAW |Byte] |
| ROWID |Sztring |
| IDŐBÉLYEG |DateTime |
| A HELYI IDŐZÓNÁRA IDŐBÉLYEG |Sztring |
| AZ IDŐZÓNA IDŐBÉLYEG |Sztring |
| ELŐJEL NÉLKÜLI EGÉSZKÉNT. |Szám |
| VARCHAR2 |Sztring |
| XML |Sztring |

> [!NOTE]
> Az adattípusok IDŐKÖZ év TO hónap és nap TO IDŐKÖZ második nem támogatottak.


## <a name="next-steps"></a>További lépések
Források és mosdók adat-előállítóban másolási tevékenység által támogatott adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).

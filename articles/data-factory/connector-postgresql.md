---
title: Adatmásolás a PostgreSQL az Azure Data Factory használatával |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a PostgreSQL támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 1142400cee040f40f88bf1f7509fc5dcba3429c6
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658538"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>PostgreSQL adatokat másol az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-onprem-postgresql-connector.md)
> * [Aktuális verzió](connector-postgresql.md)

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol egy PostgreSQL-adatbázis használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

A PostgreSQL-adatbázis adatok másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, a PostgreSQL-összekötő támogatja a PostgreSQL **7.4 verzió vagy újabb**.

## <a name="prerequisites"></a>Előfeltételek

Ha a PostgreSQL-adatbázis nem érhető el nyilvánosan, meg kell egy helyi Integration Runtime beállítása. Saját üzemeltetésű integrációs modulok kapcsolatos további információkért lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) cikk. Az integrációs modul biztosít beépített PostgreSQL illesztőprogram kezdve 3.7-es verziója, ezért nem kell manuálisan telepítenie az összes illesztőprogramot.

A helyi integrációs modul alacsonyabb, mint 3.7 esetén telepítenie kell a [Ngpsql adatszolgáltató a PostgreSQL-hez készült](https://go.microsoft.com/fwlink/?linkid=282716) 2.0.12-es és a saját üzemeltetésű integrációs gépen 3.1.9 közötti verziójával.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások PostgreSQL összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

PostgreSQL-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **PostgreSql** | Igen |
| kapcsolati Sztringje | Az ODBC kapcsolati karakterlánc csatlakozás az Azure Database for postgresql-hez. <br/>Ez a mező jelölhetnek egy SecureString tárolja biztonságos helyen a Data Factoryban. Jelszó az Azure Key Vault és lekéréses is helyezheti a `password` konfigurációs ki a kapcsolati karakterláncot. Tekintse meg a következő minták és [Store hitelesítő adatokat az Azure Key Vaultban](store-credentials-in-key-vault.md) további részleteket a cikkben. | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

Egy tipikus kapcsolati karakterlánc `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. További tulajdonságok beállíthatja, hogy az eset száma:

| Tulajdonság | Leírás | Beállítások | Szükséges |
|:--- |:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| A metódus az illesztőprogram az illesztőprogram és az adatbázis-kiszolgáló közötti adatforgalom titkosítására használja. Például `ValidateServerCertificate=<0/1/6>;`| 0 (nincs titkosítás) **(alapértelmezett)** / (SSL) 1 / 6 (RequestSSL) | Nem |
| ValidateServerCertificate (VSC) | Meghatározza, hogy az illesztőprogram érvényesíti a tanúsítványt, ha az SSL-titkosítás engedélyezve van az adatbázis-kiszolgáló által küldött (titkosítási módszer = 1). Például `ValidateServerCertificate=<0/1>;`| 0 (letiltva) **(alapértelmezett)** / 1 (engedélyezve) | Nem |

**Példa**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: a jelszó tárolásához az Azure Key Vaultban**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Ha az alábbi hasznos adattal társított PostgreSQL szolgáltatás használja, továbbra is támogatott-van, amíg, a jövőben újat használata javasolt.

**Előző hasznos:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a PostgreSQL-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

PostgreSQL adatmásolás állítsa be a type tulajdonság, az adatkészlet **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **RelationalTable** | Igen |
| tableName | A PostgreSQL-adatbázishoz a tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a PostgreSQL-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="postgresql-as-source"></a>PostgreSQL forrásként

Adatok másolása a PostgreSQL, állítsa be a forrás típusaként a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

> [!NOTE]
> Séma-és tábla-és nagybetűk. Tegye őket a `""` (dupla idézőjel) a lekérdezésben.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PostgreSQL input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

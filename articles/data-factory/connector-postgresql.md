---
title: Adatok másolása a PostgreSQL-ből a Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatok a PostgreSQL-ből a támogatott fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0efb884de9deaa2784e160785c26d78179da6567
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966892"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Adatok másolása a PostgreSQL-ből Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-postgresql-connector.md)
> * [Aktuális verzió](connector-postgresql.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban adatok másolásához egy PostgreSQL-adatbázisból. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Az adatok a PostgreSQL-adatbázisból bármilyen támogatott fogadó adattárba másolhatók. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Ez a PostgreSQL-összekötő támogatja a PostgreSQL **7,4-es vagy újabb verzióját**.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

A 3,7-nál kisebb, saját üzemeltetésű IR-verziók esetében telepítenie kell a [PostgreSQL-hez készült Ngpsql](https://go.microsoft.com/fwlink/?linkid=282716) -adatszolgáltatót a 2.0.12 és a 3.1.9 között a Integration Runtime gépen.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a PostgreSQL-összekötőhöz tartozó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A PostgreSQL-hez társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **PostgreSql** | Igen |
| connectionString | Az ODBC kapcsolati karakterlánc csatlakozás az Azure Database for postgresql-hez. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A jelszót a Azure Key Vaultban is elhelyezheti, és `password` lekérheti a konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

Egy tipikus kapcsolati karakterlánc `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. További tulajdonságok beállíthatja, hogy az eset száma:

| Tulajdonság | Leírás | Beállítások | Szükséges |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| A metódus az illesztőprogram az illesztőprogram és az adatbázis-kiszolgáló közötti adatforgalom titkosítására használja. Például `EncryptionMethod=<0/1/6>;`| 0 (nincs titkosítás) **(alapértelmezett)** / (SSL) 1 / 6 (RequestSSL) | Nem |
| ValidateServerCertificate (VSC) | Meghatározza, hogy az illesztőprogram érvényesíti a tanúsítványt, ha az SSL-titkosítás engedélyezve van az adatbázis-kiszolgáló által küldött (titkosítási módszer = 1). Például `ValidateServerCertificate=<0/1>;`| 0 (letiltva) **(alapértelmezett)** / 1 (engedélyezve) | Nem |

**Példa:**

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

**Példa: a Jelszó tárolása Azure Key Vault**

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

Ha a PostgreSQL-hez társított szolgáltatást használta a következő hasznos adattartalommal, akkor továbbra is támogatott, míg a rendszer azt javasolja, hogy az új továbbítást használja.

**Korábbi hasznos adatok:**

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

Az adatok PostgreSQL-ből való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **RelationalTable**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **RelationalTable** | Igen |
| tableName | A PostgreSQL-adatbázisban található tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

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

Az adatok PostgreSQL-ből való másolásához állítsa a forrás típusát a másolás tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **source** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **RelationalSource** | Igen |
| query | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

> [!NOTE]
> A séma és a tábla neve megkülönbözteti a kis-és nagybetűket. Csatolja őket `""` a lekérdezésben (idézőjelek között).

**Példa:**

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

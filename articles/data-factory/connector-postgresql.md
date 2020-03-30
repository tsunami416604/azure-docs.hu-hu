---
title: Adatok másolása a PostgreSQL-ből az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat a PostgreSQL-ből a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: jingwang
ms.openlocfilehash: 38cab21fb38fe171992ec8ce6c48b07f2ea94e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471142"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Adatok másolása a PostgreSQL-ből az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-postgresql-connector.md)
> * [Aktuális verzió](connector-postgresql.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása egy PostgreSQL-adatbázisból. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a PostgreSQL-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A PostgreSQL adatbázisból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Pontosabban, ez a PostgreSQL csatlakozó támogatja a PostgreSQL **7.4-es és újabb verzióját.**

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Az Integrációs futásidő a 3.7-es verziótól kezdve beépített PostgreSQL-illesztőprogramot biztosít, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot sem.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a PostgreSQL-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A PostgreSQL csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **PostgreSql** | Igen |
| connectionString (kapcsolati karakterlánc) | ODBC-kapcsolati karakterlánc a PostgreSQL Azure Database for PostgreSQL-hez való csatlakozáshoz. <br/>Az Azure Key Vaultban is felhelyezhet idúra, és kihúzhatja a `password` konfigurációt a kapcsolati karakterláncból. További részleteket az [Azure Key Vault-cikkben](store-credentials-in-key-vault.md) a következő minták és áruházi hitelesítő adatok című cikkben talál. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

Egy tipikus kapcsolati karakterlánc a `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. További tulajdonságok, amelyeket a tokszerint állíthat be:

| Tulajdonság | Leírás | Beállítások | Kötelező |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Az illesztőprogram által az illesztőprogram és az adatbázis-kiszolgáló között küldött adatok titkosítására használt módszer. Pl.`EncryptionMethod=<0/1/6>;`| 0 (nincs titkosítás) **(alapértelmezett)** / 1 (SSL) / 6 (RequestSSL) | Nem |
| ValidateServerCertificate (VSC) | Azt határozza meg, hogy az illesztőprogram érvényesítse-e az adatbázis-kiszolgáló által küldött tanúsítványt, ha az SSL-titkosítás engedélyezve van (Titkosítási módszer=1). Pl.`ValidateServerCertificate=<0/1>;`| 0 (letiltva) **(alapértelmezett)** / 1 (engedélyezve) | Nem |

**Példa:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: jelszó tárolása az Azure Key Vaultban**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;",
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

Ha a PostgreSQL kapcsolt szolgáltatást a következő hasznos adattal használta, akkor továbbra is támogatott, miközben azt javasoljuk, hogy az újat használja a jövőben.

**Előző hasznos teher:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a PostgreSQL adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok postgreSQL-ből történő másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **PostgreSqlTable** | Igen |
| Séma | A séma neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Használja `schema` `table` és új munkaterheléshez. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "PostgreSqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Ha gépelt adatkészletet használt, `RelationalTable` továbbra is támogatott, amíg van, miközben javasoljuk, hogy használja az újat a jövőben.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a PostgreSQL-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="postgresql-as-source"></a>PostgreSQL mint forrás

A PostgreSQL adatainak másolásához a másolási tevékenység **forrásszakaszában** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **PostgreSqlSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Nem (ha az adatkészletben a "tableName" van megadva) |

> [!NOTE]
> A séma- és táblanevekben a kis- és nagybetűket nem lehet figyelembe. A lekérdezésbe `""` (dupla idézőjelek) mellékeljük őket.

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
                "type": "PostgreSqlSource",
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ha gépelt forrást használt, `RelationalSource` akkor továbbra is támogatott, amíg az újat a jövőben is használhatja.

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.

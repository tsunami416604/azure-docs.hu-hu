---
title: Netezza adatokat másol az Azure Data Factory használatával |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Netezza támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
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
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 8e2b65f83395c9e8991338864d2037d0572dd269
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078006"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Netezza adatokat másol az Azure Data Factory használatával 

Ez a cikk az Azure Data Factory másolási tevékenység használatával adatokat másol a Netezza módját ismerteti. A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat Netezza bármely támogatott fogadó adattárba. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Az Azure Data Factory-kapcsolat beépített illesztőprogramot tartalmaz. Nem kell manuálisan telepítenie az összes illesztőprogram az összekötő használatához.

## <a name="get-started"></a>Bevezetés

Létrehozhat egy folyamatot, amely egy másolási tevékenységet használ a .NET SDK-t, a Python SDK-t, az Azure PowerShell, a REST API vagy egy Azure Resource Manager-sablon használatával. Tekintse meg a [másolási tevékenység oktatóanyagát](quickstart-create-data-factory-dot-net.md) kapcsolatos lépésenkénti útmutatót egy másolási tevékenységgel rendelkező folyamat létrehozása.

A következő szakaszok segítségével konkrétan a Netezza-összekötő a Data Factory-entitások definiálása-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak a Netezza társított szolgáltatást:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **Netezza**. | Igen |
| kapcsolati Sztringje | Az ODBC kapcsolati karakterlánc Netezza csatlakozni. Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. Emellett [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. Választhat egy saját üzemeltetésű integrációs modulok vagy az Azure integrációs modul (ha az adattár nyilvánosan elérhető). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

Egy tipikus kapcsolati karakterlánc `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. A következő táblázat ismerteti, amely lehet további tulajdonságok:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| SecurityLevel | Az adattárban való csatlakozáshoz használja az illesztőprogram (SSL/TLS) biztonsági szintjét. Példa: `SecurityLevel=preferredSecured`. Támogatott értékei a következők:<br/>- **Csak nem biztonságos** (**onlyUnSecured**): az illesztőprogram nem használ SSL.<br/>- **Előnyben részesített nem biztonságos (preferredUnSecured) (alapértelmezett)**: Ha a kiszolgáló lehetővé teszi a választást, az illesztőprogram nem használ SSL. <br/>- **Előnyben részesített (preferredSecured) biztonságos**: Ha a kiszolgáló lehetővé teszi a választást, az illesztőprogramot használja az SSL. <br/>- **Csak a (onlySecured) védett**: az illesztőprogram nem csatlakozzon, hacsak nem érhető el SSL-kapcsolat. | Nem |
| CA-tanúsítványfájl | A kiszolgáló által használt SSL-tanúsítvány teljes elérési útja. Például: `CaCertFile=<cert path>;`| Igen, ha SSL engedélyezve van |

**Példa**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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

Ez a szakasz felsorolja, amely támogatja a Netezza adatkészlet tulajdonságai.

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md). 

Adatok másolása Netezza, állítsa be a **típusa** tulajdonság, az adatkészlet **NetezzaTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **NetezzaTable** | Igen |
| tableName | A tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz felsorolja, amely támogatja a Netezza forrás tulajdonságai.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="netezza-as-source"></a>Netezza forrásként

Adatok másolása Netezza, állítsa be a **forrás** írja be a másolási tevékenység **NetezzaSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrás tulajdonság **NetezzaSource**. | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"` | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>További lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

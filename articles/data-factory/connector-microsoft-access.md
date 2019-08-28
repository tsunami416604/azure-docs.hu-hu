---
title: Adatok másolása a Microsoft Access-forrásokból Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Microsoft hozzáférési forrásaiból egy Azure Data Factory-folyamat másolási tevékenységének használatával támogatott fogadó adattárakba.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: jingwang
ms.openlocfilehash: 79a960b106cc9de4116e8e59b2fa17f48c896221
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061454"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Adatok másolása a és a rendszerből a Microsoft Access-adattárakba Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factory az adatok Microsoft Access-adattárból történő másolásához. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

A Microsoft Access forrásból származó adatok másolása bármely támogatott fogadó adattárba lehetséges. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

## <a name="prerequisites"></a>Előfeltételek

A Microsoft Access Connector használatához a következőket kell tennie:

- Saját üzemeltetésű Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.
- Telepítse a Microsoft Access ODBC-illesztőt az adattárhoz a Integration Runtime gépen.

>[!NOTE]
>Az ODBC-illesztőprogram Microsoft Access 2016-es verziója nem működik ezzel az összekötővel. Használja helyette az 2013-es vagy 2010-os illesztőprogramot.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a Microsoft Access connectorhoz tartozó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Microsoft Access társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **MicrosoftAccess** | Igen |
| connectionString | Az ODBC-kapcsolatok karakterlánca a hitelesítő adatok kivételével. Megadhatja a kapcsolati karakterláncot, vagy használhatja a Integration Runtime gépen beállított rendszeradatforrás-nevet (az adatforrás nevét) (ennek megfelelően a társított szolgáltatás hitelesítő adatait is meg kell adnia).<br>Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md).| Igen |
| authenticationType | A Microsoft Access adattárhoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Engedélyezett értékek a következők: Alapszintű és **Névtelen**. | Igen |
| userName | Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. | Nem |
| password | Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| credential | Az illesztőprogram-specifikus tulajdonság-érték formátumban megadott kapcsolati karakterlánc hozzáférési hitelesítő része. A mező megjelölése SecureString. | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. A saját üzemeltetésű Integration Runtime az [Előfeltételek](#prerequisites)szakaszban említettek szerint kell megadni. |Igen |

**Példa:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a Microsoft Access adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Microsoft Accessből való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **MicrosoftAccessTable** | Igen |
| tableName | A tábla neve a Microsoft Accessben. | Nem forrásként (ha a "Query" a tevékenység forrásában van megadva);<br/>Igen a fogadó számára |

**Példa**

```json
{
    "name": "MicrosoftAccessDataset",
    "properties": {
        "type": "MicrosoftAccessTable",
        "linkedServiceName": {
            "referenceName": "<Microsoft Access linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Microsoft Access Source által támogatott tulajdonságok listáját tartalmazza.

### <a name="microsoft-access-as-source"></a>Microsoft-hozzáférés forrásként

Az adatok Microsoft Access-kompatibilis adattárból való másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **MicrosoftAccessSource** | Igen |
| query | Az egyéni lekérdezés használatával olvashatja el az adatolvasást. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Microsoft Access input dataset name>",
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
                "type": "MicrosoftAccessSource",
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
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

---
title: Adatok másolása Microsoft Access forrásokból
description: Megtudhatja, hogy miként másolhat adatokat a Microsoft Access forrásaiból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: d6110065e28b8f179cd1d113107fb3508e1c3e44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892550"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Adatok másolása a Microsoft Access adattárakból és a Microsoft Access adattárakba az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység et az Azure Data Factory-ban adatok másolására egy Microsoft Access adattárból. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Microsoft Access összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Microsoft Access forrásból adatokat bármely támogatott fogadó adattárba másolhatunk. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

## <a name="prerequisites"></a>Előfeltételek

A Microsoft Access összekötő használatához a következőket kell a következőkre használnia:

- Állítsa be a saját üzemeltetésű integrációs futásidejű. A [részleteket a saját üzemeltetésű integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben találja.
- Telepítse az adattár Microsoft Access ODBC illesztőprogramját az integrációs futásidejű gépen.

>[!NOTE]
>Az ODBC illesztőprogram Microsoft Access 2016-os verziója nem működik ezzel az összekötővel. Használja az illesztőprogram 2013-as vagy 2010-es verzióját.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok a Microsoft Access-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságokrészleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A Microsoft Access csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **MicrosoftAccess** | Igen |
| connectionString (kapcsolati karakterlánc) | Az ODBC kapcsolati karakterlánc a hitelesítő adatok kivételével. Megadhatja a kapcsolati karakterláncot, vagy használhatja az integrációs futásidejű gépen beállított rendszer adatforrásnevet (adatforrásnév) (ennek megfelelően meg kell adnia a csatolt szolgáltatás hitelesítő adatrészét).<br> Az Azure Key Vaultban is elhelyezhet `password` egy jelszót, és kihúzhatja a konfigurációt a kapcsolati karakterláncból.További részleteket az Azure Key  [Vault áruházi hitelesítő adataiban](store-credentials-in-key-vault.md)talál.| Igen |
| authenticationType | A Microsoft Access adattárhoz való csatlakozáshoz használt hitelesítés típusa.<br/>Az engedélyezett értékek a következők: **Alap és** **Névtelen**. | Igen |
| userName (Felhasználónév) | Adja meg a felhasználónevet, ha alapfokú hitelesítést használ. | Nem |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |
| hitelesítő adat | Az illesztőprogram-specifikus tulajdonságérték-formátumban megadott kapcsolati karakterlánc hozzáférési hitelesítő adatrésze. Jelölje meg ezt a mezőt SecureString ként. | Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Az előfeltételek ben említettek szerint saját üzemeltetésű [integrációs](#prerequisites)futásidejű re van szükség. |Igen |

**Példa:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;",
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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a Microsoft Access adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok Microsoft Access programból történő másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **MicrosoftAccessTable** | Igen |
| tableName | A Microsoft Access táblájának neve. | Nem a forráshoz (ha a tevékenységforrásban meg van adva a "lekérdezés" );<br/>Igen a mosogató |

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Microsoft Access forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="microsoft-access-as-source"></a>Microsoft Access forrásként

Ha adatokat szeretne másolni a Microsoft Access-kompatibilis adattárból, a másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás típustulajdonságának a következőre kell állítani: **MicrosoftAccessSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni lekérdezést. Például: `"SELECT * FROM MyTable"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

**Példa:**

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

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.

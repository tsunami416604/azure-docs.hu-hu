---
title: Adatok másolása IBM Informix-forrásokból az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az IBM Informix-forrásokból származó adatokat a támogatott fogadó adattárolókba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: cb4b81f7c5e219c520078ecf34eba3f5e98da684
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892572"
---
# <a name="copy-data-from-and-to-ibm-informix-data-stores-using-azure-data-factory"></a>Adatok másolása ibm Informix adattárakból és -ba az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenységet az Azure Data Factory-ban az IBM Informix-adattárból történő másoláshoz. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Informix-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az Informix-forrásból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

## <a name="prerequisites"></a>Előfeltételek

Az Informix-csatlakozó használatához a következőket kell használnia:

- Állítsa be a saját üzemeltetésű integrációs futásidejű. A [részleteket a saját üzemeltetésű integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben találja.
- Telepítse az Informix ODBC illesztőprogramot az adattárhoz az integrációs futásidejű gépen. Használhatja például az "IBM INFORMIX Informix DRIVER (64 bites)" illesztőprogramot.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Informix-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Informix csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **Informix** | Igen |
| connectionString (kapcsolati karakterlánc) | Az ODBC kapcsolati karakterlánc a hitelesítő adatok kivételével. Megadhatja a kapcsolati karakterláncot, vagy használhatja az integrációs futásidejű gépen beállított rendszer adatforrásnevet (adatforrásnév) (ennek megfelelően meg kell adnia a csatolt szolgáltatás hitelesítő adatrészét). <br> Az Azure Key Vaultban is elhelyezhet `password` egy jelszót, és kihúzhatja a konfigurációt a kapcsolati karakterláncból. További részleteket az Azure Key  [Vault áruházi hitelesítő adataiban](store-credentials-in-key-vault.md)talál.| Igen |
| authenticationType | Az Informix-adattárhoz való csatlakozáshoz használt hitelesítés típusa.<br/>Az engedélyezett értékek a következők: **Alap és** **Névtelen**. | Igen |
| userName (Felhasználónév) | Adja meg a felhasználónevet, ha alapfokú hitelesítést használ. | Nem |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |
| hitelesítő adat | Az illesztőprogram-specifikus tulajdonságérték-formátumban megadott kapcsolati karakterlánc hozzáférési hitelesítő adatrésze. Jelölje meg ezt a mezőt SecureString ként. | Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Az előfeltételek ben említettek szerint saját üzemeltetésű [integrációs](#prerequisites)futásidejű re van szükség. |Igen |

**Példa:**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz az Informix adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az Informix adatainak másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **InformixTable** | Igen |
| tableName | Az Informix táblájának neve. | Nem a forráshoz (ha a tevékenységforrásban meg van adva a "lekérdezés" );<br/>Igen a mosogató |

**Példa**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az Informix-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="informix-as-source"></a>Informix mint forrás

Az Informix adatainak másolásához a másolási tevékenység **forrásszakaszában** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **InformixSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni lekérdezést. Például: `"SELECT * FROM MyTable"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
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

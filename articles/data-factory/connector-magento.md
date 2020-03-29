---
title: Adatok másolása a Magento-ból az Azure Data Factory használatával (előzetes verzió)
description: Megtudhatja, hogyan másolhatja az adatokat a Magento-ból a támogatott fogadó adattárak egy Azure Data Factory-folyamat másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: a7317992eaeb06cb3909314be2bfd2057254b6a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927134"
---
# <a name="copy-data-from-magento-using-azure-data-factory-preview"></a>Adatok másolása a Magento-ból az Azure Data Factory használatával (előzetes verzió)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása a Magento.This article tagahow to use the Copy Activity in Azure Data Factory to copy data from Magento. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Kipróbálhatja, és visszajelzést adhat nekünk. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Magento összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Magento-ból adatokat bármely támogatott fogadó adattárba másolhat. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Az Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot is ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Magento-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A Magento csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **Magento** | Igen |
| gazda | A Magento-példány URL-címe. (azaz 192.168.222.110/magento3)  | Igen |
| accessToken | A magentoi hozzáférési jogkivonat. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| useEncryptedEndpoints | Itt adható meg, hogy az adatforrás végpontjai HTTPS protokoll használatával titkosítva legyenek-e. Az alapértelmezett érték az igaz.  | Nem |
| useHostVerification (useHostVerification) | Itt adható meg, hogy az SSL-kapcsolaton keresztüli csatlakozáskor a kiszolgáló tanúsítványában lévő állomásnév nek meg kell-e egyeznie a kiszolgáló állomásnevével. Az alapértelmezett érték az igaz.  | Nem |
| usePeerVerification | Itt adható meg, hogy az SSL-kapcsolaton keresztül imitomának ellenőrzése esetén ellenőrizze-e a kiszolgáló identitását. Az alapértelmezett érték az igaz.  | Nem |

**Példa:**

```json
{
    "name": "MagentoLinkedService",
    "properties": {
        "type": "Magento",
        "typeProperties": {
            "host" : "192.168.222.110/magento3",
            "accessToken": {
                "type": "SecureString",
                "value": "<accessToken>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a Magento adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Magento programból, állítsa az adatkészlet típustulajdonságát **MagentoObject objektumra.** A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságának a következőre kell állítani: **MagentoObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa**

```json
{
    "name": "MagentoDataset",
    "properties": {
        "type": "MagentoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Magento linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Magento-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="magento-as-source"></a>Magento mint forrás

Ha adatokat szeretne másolni a Magento-ból, állítsa be a forrástípust a másolási tevékenységben a **MagentoSource mezőre.** A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **MagentoSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM Customers"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromMagento",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Magento input dataset name>",
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
                "type": "MagentoSource",
                "query": "SELECT * FROM Customers where Id > XXX"
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

---
title: Adatok másolása az Oracle Eloqua programból (előzetes verzió)
description: Ismerje meg, hogyan másolhatja az adatokat az Oracle Eloqua-ból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: abb323bbdb2d49f46fea83e1c881f1ef6a4efff7
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990803"
---
# <a name="copy-data-from-oracle-eloqua-using-azure-data-factory-preview"></a>Adatok másolása az Oracle Eloqua szolgáltatásból az Azure Data Factory használatával (előzetes verzió)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység et az Azure Data Factory adatok másolásához az Oracle Eloqua-ból. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Kipróbálhatja, és visszajelzést adhat. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Oracle Eloqua csatlakozó a következő tevékenységekhez támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az Oracle Eloqua-ból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Az Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot is ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Oracle Eloqua-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Oracle Eloqua csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **Eloqua** | Igen |
| endpoint | Az Eloqua kiszolgáló végpontja. Az Eloqua több adatközpontot is támogat a végpont https://login.eloqua.com meghatározásához, a hitelesítő adatokkal való bejelentkezéshez, majd az `xxx.xxx.eloqua.com` **átirányított URL-cím alap URL-részének** másolásához a mintájával. | Igen |
| felhasználónév | Az Eloqua-fiók webhelyneve és felhasználóneve `SiteName\Username` a következő `Eloqua\Alice`formában: pl. .  | Igen |
| jelszó | A felhasználónévnek megfelelő jelszó. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| useEncryptedEndpoints | Itt adható meg, hogy az adatforrás végpontjai HTTPS protokoll használatával titkosítva legyenek-e. Az alapértelmezett érték az igaz.  | Nem |
| useHostVerification (useHostVerification) | Itt adható meg, hogy a kiszolgáló tanúsítványában lévő állomásnév nek meg kell-e egyeznie a kiszolgáló állomásnevével, amikor TLS-en keresztül csatlakozik. Az alapértelmezett érték az igaz.  | Nem |
| usePeerVerification | Itt adható meg, hogy a kiszolgáló identitását ellenőrizze-e a TLS-en keresztüli csatlakozáskor. Az alapértelmezett érték az igaz.  | Nem |

**Példa:**

```json
{
    "name": "EloquaLinkedService",
    "properties": {
        "type": "Eloqua",
        "typeProperties": {
            "endpoint" : "<base URL e.g. xxx.xxx.eloqua.com>",
            "username" : "<site name>\\<user name e.g. Eloqua\\Alice>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz az Oracle Eloqua adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni az Oracle Eloqua programból, állítsa az adatkészlet típustulajdonságát **EloquaObject**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságának a következőre kell állítania: **EloquaObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa**

```json
{
    "name": "EloquaDataset",
    "properties": {
        "type": "EloquaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Eloqua linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az Oracle Eloqua forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="eloqua-as-source"></a>Eloqua mint forrás

Ha adatokat szeretne másolni az Oracle Eloqua programból, állítsa a másolási tevékenység forrástípusát az **EloquaSource beállításra.** A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **EloquaSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM Accounts"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromEloqua",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Eloqua input dataset name>",
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
                "type": "EloquaSource",
                "query": "SELECT * FROM Accounts"
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
Az Azure Data Factory által tárolt támogatott adatok listáját a [támogatott adattárak című témakörben tetszetős.](copy-activity-overview.md#supported-data-stores-and-formats)

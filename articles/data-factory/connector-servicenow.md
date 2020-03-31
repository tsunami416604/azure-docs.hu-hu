---
title: Adatok másolása a ServiceNow-ból
description: Megtudhatja, hogyan másolhatja az adatokat a ServiceNow-ból a támogatott fogadó adattárakegy Azure Data Factory-folyamat másolási tevékenység használatával.
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
ms.openlocfilehash: dabcc5afe4a092e4919c854071a698c6e6ebf0b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926174"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Adatok másolása a ServiceNow szolgáltatásból az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása servicenow. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a ServiceNow-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Adatokat másolhat a ServiceNow bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Az Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot is ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a ServiceNow-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A ServiceNow csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **ServiceNow** | Igen |
| endpoint | A ServiceNow kiszolgáló (`http://<instance>.service-now.com`végpontja ( ).  | Igen |
| authenticationType | A használandó hitelesítési típus. <br/>Az engedélyezett értékek a következők: **Alap ,** **OAuth2** | Igen |
| felhasználónév | Az alapszintű és az OAuth2-hitelesítés ServiceNow kiszolgálójához való csatlakozáshoz használt felhasználónév.  | Igen |
| jelszó | Az alapszintű és az OAuth2-hitelesítés felhasználónevének megfelelő jelszó. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| ügyfél-azonosító | Az OAuth2-hitelesítés ügyfélazonosítója.  | Nem |
| ügyféltitkos | Az OAuth2-hitelesítés ügyféltitkos kulcsa. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |
| useEncryptedEndpoints | Itt adható meg, hogy az adatforrás végpontjai HTTPS protokoll használatával titkosítva legyenek-e. Az alapértelmezett érték az igaz.  | Nem |
| useHostVerification (useHostVerification) | Itt adható meg, hogy az SSL-kapcsolaton keresztüli csatlakozáskor a kiszolgáló tanúsítványában lévő állomásnév nek meg kell-e egyeznie a kiszolgáló állomásnevével. Az alapértelmezett érték az igaz.  | Nem |
| usePeerVerification | Itt adható meg, hogy az SSL-kapcsolaton keresztül imitomának ellenőrzése esetén ellenőrizze-e a kiszolgáló identitását. Az alapértelmezett érték az igaz.  | Nem |

**Példa:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a ServiceNow adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a ServiceNow szolgáltatásból, állítsa az adatkészlet típustulajdonságát **ServiceNowObject (SzolgáltatásObjektum)** tulajdonságra. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságának a következő re van állítva: **ServiceNowObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a ServiceNow forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="servicenow-as-source"></a>ServiceNow forrásként

Ha adatokat szeretne másolni a ServiceNow szolgáltatásból, állítsa be a forrástípust a copy **tevékenységserviceNowSource**mezőre. A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **ServiceNowSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM Actual.alm_asset"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

Vegye figyelembe az alábbiakat, amikor megadja a sémát és oszlopot a ServiceNow lekérdezésben, és **olvassa el a [teljesítményre vonatkozó teljesítménytippek](#performance-tips) című**részt.

- **Séma:** adja meg a `Actual` `Display` sémát a ServiceNow lekérdezésben, amelyet `sysparm_display_value` a [ServiceNow pihentető API-k](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET)hívásakor igazként vagy hamisként nézhet meg. 
- **Oszlop:** a `Actual` séma tényleges értékének `[column name]_value`oszlopneve , `Display` míg a `[column name]_display_value`séma alatti megjelenítési érték esetén a . Vegye figyelembe, hogy az oszlopnévnek le kell képeznie a lekérdezésben használt sémát.

**Mintalekérdezés:** 
 `SELECT col_value FROM Actual.alm_asset` VAGY 
`SELECT col_display_value FROM Display.alm_asset`

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>Teljesítménnyel kapcsolatos tippek

### <a name="schema-to-use"></a>Használandó séma

ServiceNow 2 különböző sémák, az egyik **a "Tényleges",** amely visszaadja a tényleges adatokat, a másik **a "Display",** amely visszaadja a megjelenített értékek et. 

Ha van szűrő a lekérdezésben, használja a "Tényleges" sémát, amely jobb másolási teljesítményt biztosít. A "Tényleges" séma lekérdezésekénál, ServiceNow natív anonim támogatás szűrő beolvasásakor az adatok csak a szűrt resultset, míg a lekérdezés "Display" séma, ADF letölteni az összes adatot, és alkalmazza szűrő belsőleg.

### <a name="index"></a>Index

A ServiceNow táblaindex segíthet a lekérdezés teljesítményének javításában, olvassa el a Táblaindex létrehozása című [témakört.](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html)

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.

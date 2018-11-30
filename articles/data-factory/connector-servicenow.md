---
title: Adatok másolása az Azure Data Factory használatával ServiceNow |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a ServiceNow támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
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
ms.date: 11/23/2018
ms.author: jingwang
ms.openlocfilehash: 1e0bbfafcda77ca48fb22ad919c5848a7670a102
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309674"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával ServiceNow

Ez a cikk az Azure Data Factory a másolási tevékenység használatával adatokat másol a ServiceNow módját ismerteti. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat a ServiceNow bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolat beépített illesztőprogramot tartalmaz, ezért nem kell manuálisan telepítenie az összes illesztőprogram ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások a ServiceNow-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

ServiceNow-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **ServiceNow** | Igen |
| endpoint | A végpont a ServiceNow-kiszolgáló (`http://<instance>.service-now.com`).  | Igen |
| authenticationType | A használandó hitelesítés típusa. <br/>Engedélyezett értékek a következők: **alapszintű**, **OAuth2** | Igen |
| felhasználónév | Az alapszintű és az OAuth2-hitelesítéshez a ServiceNow-kiszolgálóhoz való csatlakozáshoz használt felhasználónév.  | Igen |
| jelszó | A jelszó, a felhasználónevet Basic és OAuth2-hitelesítéshez megfelelő. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| clientId | Az ügyfél-azonosító OAuth2-hitelesítéshez.  | Nem |
| clientSecret | A titkos ügyfélkulcsot OAuth2-hitelesítéshez. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| useEncryptedEndpoints | Megadja, hogy a data source végpontok HTTPS segítségével titkosítja. Az alapértelmezett érték: igaz.  | Nem |
| useHostVerification | Megadja a kiszolgálói tanúsítvány a kiszolgáló állomásneve megfelelően, ha SSL-kapcsolaton keresztül kapcsolódik az állomás neve kötelező legyen-e. Az alapértelmezett érték: igaz.  | Nem |
| usePeerVerification | Megadja, hogy ellenőrizze a kiszolgáló identitását, ha SSL-kapcsolaton keresztül kapcsolódik. Az alapértelmezett érték: igaz.  | Nem |

**Példa**

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a ServiceNow-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatmásolás ServiceNow, állítsa be a type tulajdonság, az adatkészlet **ServiceNowObject**. Egy adatkészlet ilyen további típus-specifikus tulajdonság nincs.

**Példa**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a ServiceNow-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="servicenow-as-source"></a>ServiceNow forrásként

Adatok másolása a ServiceNow, állítsa be a forrás típusaként a másolási tevékenység **ServiceNowSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **ServiceNowSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM Actual.alm_asset"`. | Igen |

Vegye figyelembe a következőket megadásakor a séma és az oszlopot a ServiceNow lekérdezést, és **tekintse meg [teljesítménnyel kapcsolatos tippek](#performance-tips) a másolási teljesítmény utalás**.

- **Séma:** adja meg a séma szerint `Actual` vagy `Display` a ServiceNow-lekérdezés, tekintse meg, az paraméterként, amely `sysparm_display_value` , IGAZ vagy hamis értéket, ha a hívó [ServiceNow restful API-k](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Oszlop:** tényleges értéke alapján oszlopneve `Actual` sémája `[columne name]_value`, a megjelenítési érték alatt `Display` sémája `[columne name]_display_value`. Megjegyzés: az oszlop nevét kell a lekérdezésben használt séma térképet.

**Mintalekérdezés:** 
 `SELECT col_value FROM Actual.alm_asset` vagy 
`SELECT col_display_value FROM Display.alm_asset`

**Példa**

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

### <a name="schema-to-use"></a>Használandó sémát

ServiceNow 2 különböző sémákkal rendelkezik, az egyik **"Actual"** tényleges adatokat ad vissza, a másik **"Megjeleníteni"** az adatok a megjelenítendő értékeket ad vissza. 

Ha egy szűrőt a lekérdezés, használja a "Actual" séma, amely jobban rendelkezik másolja a teljesítmény. "Actual" séma lekérdezésekor a ServiceNow natív módon támogatja az szűrőt, csak adja vissza a szűrt eredménykészlettel rendelkező, mivel a "Megjelenített" séma lekérdezésekor ADF lekérni az összes adatot, és belsőleg szűrő alkalmazása az adatok beolvasása közben.

### <a name="index"></a>Index

A ServiceNow táblázatindexhez segítségével javíthatja a lekérdezések teljesítményét, tekintse meg [hozzon létre egy táblát indexet](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

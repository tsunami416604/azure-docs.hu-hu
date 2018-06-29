---
title: Adatok másolása az Azure Data Factory használatával ServiceNow |} Microsoft Docs
description: 'Útmutató: adatok másolása a ServiceNow támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával.'
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
ms.date: 05/28/2018
ms.author: jingwang
ms.openlocfilehash: c67f6c14dc396367e0179fe5bdb4663fcb7725da
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045966"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával ServiceNow

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory adatokat másolni a ServiceNow. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességei

A ServiceNow adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolódásának engedélyezése beépített illesztőprogramot tartalmaz, ezért nem szükséges manuálisan kell telepítenie minden olyan illesztőprogram ezt az összekötőt használja.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják való ServiceNow összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok ServiceNow kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **ServiceNow** | Igen |
| endpoint | A ServiceNow Server endpoint (`http://<instance>.service-now.com`).  | Igen |
| authenticationType | A használandó hitelesítési típus. <br/>Két érték engedélyezett: **alapvető**, **OAuth2** | Igen |
| felhasználónév | A ServiceNow kiszolgáló alapszintű és az OAuth2 hitelesítési való csatlakozáshoz használt felhasználónevet.  | Igen |
| jelszó | A jelszó, a felhasználónevet Basic és az OAuth2 hitelesítési megfelelő. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| clientId | Az OAuth2 hitelesítési ügyfél-azonosító.  | Nem |
| clientSecret | Az OAuth2 hitelesítési titkos ügyfélkódot. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Nem |
| useEncryptedEndpoints | Meghatározza, hogy a data source végpontok titkosítása HTTPS használatával. Az alapértelmezett érték: igaz.  | Nem |
| useHostVerification | Meghatározza, hogy az a számítógép neve a egyeznie kell a gazdagép nevével a kiszolgáló SSL-en keresztüli kapcsolódás esetén a kiszolgáló tanúsítványát. Az alapértelmezett érték: igaz.  | Nem |
| usePeerVerification | Megadja, hogy a kiszolgáló identitásának ellenőrzése SSL-en keresztüli kapcsolódás esetén. Az alapértelmezett érték: igaz.  | Nem |

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a ServiceNow dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása a ServiceNow, az adatkészlet típus tulajdonságának beállítása **ServiceNowObject**. Nincs ilyen típusú dataset további típusra vonatkozó tulajdonság.

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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a ServiceNow forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="servicenow-as-source"></a>A ServiceNow forrásaként

Adatok másolása a ServiceNow, állítsa be a forrás típusa a másolási tevékenység **ServiceNowSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **ServiceNowSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM Actual.alm_asset"`. | Igen |

Megadásakor a séma- és oszlop ServiceNow a lekérdezésben, vegye figyelembe a következőket:

- **Séma:** adja meg a séma szerint `Actual` vagy `Display` ServiceNow a lekérdezésben, vessen egy pillantást azt a paraméterét, amely `sysparm_display_value` igaz vagy hamis értéket, ha hívása [ServiceNow restful API-k](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Oszlop:** az oszlop nevét, a tényleges érték `Actual` sémája `[columne name]_value`, a megjelenítési érték alatti `Display` sémája `[columne name]_display_value`. Megjegyzés: az oszlop nevét kell a lekérdezésben használt séma leképezés.

**Mintalekérdezés:** 
 `SELECT col_value FROM Actual.alm_asset` vagy `SELECT col_display_value FROM Display.alm_asset`

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

## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).

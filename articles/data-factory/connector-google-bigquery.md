---
title: Adatok másolása a Google BigQuery a Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatokat a Google BigQuery a fogadó adattárakba egy másolási tevékenység használatával egy adatfeldolgozó-folyamatban.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: d57260db74a1373eb461c560e013be620910eee9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680923"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Adatok másolása a Google BigQuery a Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Google BigQuery való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Google BigQuery-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a Google BigQuery bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez. Ezért nem kell manuálisan telepítenie az illesztőprogramot az összekötő használatához.

>[!NOTE]
>Ez a Google BigQuery-összekötő a BigQuery API-kra épül. Ügyeljen arra, hogy a BigQuery korlátozza a bejövő kérések maximális számát, és a megfelelő kvótákat a projekten kívüli alapon kényszeríti ki, a [kvóták & korlátozások – API-kérelmek](https://cloud.google.com/bigquery/quotas#api_requests)esetében. Győződjön meg arról, hogy nem indít túl sok egyidejű kérelmet a fiókhoz.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a Google BigQuery-összekötőhöz kapcsolódó entitások definiálásához használhatók Data Factory.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Google BigQuery társított szolgáltatás a következő tulajdonságokat támogatja.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **GoogleBigQuery**értékre kell beállítani. | Igen |
| projekt | A lekérdezéshez használt alapértelmezett BigQuery-projekt azonosítója.  | Igen |
| additionalProjects | A nyilvános BigQuery-projektek eléréséhez használandó projekt-azonosítók vesszővel tagolt listája.  | Nem |
| requestGoogleDriveScope | Azt határozza meg, hogy hozzáférést kér-e a Google Drive-hoz. A Google Drive-hozzáférés engedélyezése lehetővé teszi az összevont táblázatok támogatását, amelyek a Google Drive-ból származó adatokkal ötvözik a BigQuery adatait. Az alapértelmezett érték **false (hamis**).  | Nem |
| authenticationType | A hitelesítéshez használt OAuth 2,0 hitelesítési mechanizmus. A ServiceAuthentication csak saját üzemeltetésű Integration Runtime használható. <br/>Az engedélyezett értékek a következők: **UserAuthentication** és **ServiceAuthentication**. A hitelesítési típusokra vonatkozó további tulajdonságokért és JSON-mintákért tekintse meg a táblázat alatti szakaszt. | Igen |

### <a name="using-user-authentication"></a>Felhasználói hitelesítés használata

Állítsa a "authenticationType" tulajdonságot **UserAuthentication**értékre, és adja meg a következő tulajdonságokat az előző szakaszban leírt általános tulajdonságokkal együtt:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| clientId | A frissítési jogkivonat létrehozásához használt alkalmazás azonosítója. | Nem |
| clientSecret | A frissítési jogkivonat létrehozásához használt alkalmazás titka. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Nem |
| refreshToken | A Google által a BigQuery való hozzáférés engedélyezéséhez használt frissítési jogkivonat. Ismerje meg, hogyan [szerezhet be egyet a OAuth 2,0 hozzáférési jogkivonatok](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) és [a közösségi blog](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59)beszerzéséhez. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Nem |

**Példa**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>A szolgáltatás hitelesítésének használata

Állítsa a "authenticationType" tulajdonságot **ServiceAuthentication**értékre, és adja meg a következő tulajdonságokat, valamint az előző szakaszban leírt általános tulajdonságokat. Ez a hitelesítési típus csak saját üzemeltetésű Integration Runtime használható.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| e-mail | A ServiceAuthentication használt szolgáltatásfiók e-mail azonosítója. Csak saját üzemeltetésű Integration Runtime használható.  | Nem |
| keyFilePath | A szolgáltatásfiók e-mail-címének hitelesítéséhez használt. P12-kulcsfájl teljes elérési útja. | Nem |
| trustedCertPath | A. PEM fájl teljes elérési útja, amely a kiszolgáló SSL-kapcsolaton keresztüli hitelesítéséhez használt megbízható HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat tartalmazza. Ez a tulajdonság csak akkor állítható be, ha az SSL-t használja a saját üzemeltetésű Integration Runtime. Az alapértelmezett érték a hitesítésszolgáltatói. PEM fájl, amelyet az Integration Runtime telepített.  | Nem |
| useSystemTrustStore | Megadja, hogy a rendszer a rendszermegbízhatósági tárolóból vagy egy megadott. PEM fájlból kíván-e HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használni. Az alapértelmezett érték **false (hamis**).  | Nem |

**Példa**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Google BigQuery adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Google BigQuery, állítsa az adatkészlet Type (típus) tulajdonságát **GoogleBigQueryObject**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **GoogleBigQueryObject** | Igen |
| Adatkészlet | A Google BigQuery-adatkészlet neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tableName | A tábla neve. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Az új számítási feladatokhoz használja a `dataset` és a `table` értéket. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Google BigQuery-Forrástípus által támogatott tulajdonságok listáját tartalmazza.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource

Az adatok Google BigQuery való másolásához állítsa a forrás típusát a másolás tevékenység **GoogleBigQuerySource**értékére. A másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát **GoogleBigQuerySource**értékre kell állítani. | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM MyTable"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>További lépések
A Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

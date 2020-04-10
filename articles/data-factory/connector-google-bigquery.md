---
title: Adatok másolása a Google BigQueryből az Azure Data Factory használatával
description: Megtudhatja, hogy miként másolhat adatokat a Google BigQueryből a támogatott fogadó adattárolókba egy adatfeldolgozó folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 9fe6b494398337dd49bd8f0fe53b24666412a1b0
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991586"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Adatok másolása a Google BigQueryből az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása a Google BigQuery. A [Másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Google BigQuery-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Google BigQuery adatai bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által forrásként vagy fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában láthatja.

A Data Factory beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez. Ezért nem kell manuálisan telepítenie egy illesztőprogramot az összekötő használatához.

>[!NOTE]
>Ez a Google BigQuery-összekötő a BigQuery API-k ra épül. Ne feledje, hogy a BigQuery korlátozza a bejövő kérelmek maximális arányát, és projektenként érvényesíti a megfelelő kvótákat, olvassa el [a Kvóták & korlátok – API-kérelmeket.](https://cloud.google.com/bigquery/quotas#api_requests) Győződjön meg arról, hogy nem indít túl sok egyidejű kérelmet a fiókhoz.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Data Factory-entitások a Google BigQuery-összekötőre jellemző definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A google BigQuery csatolt szolgáltatás a következő tulajdonságokat támogatja.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **GoogleBigQuery**beállításra kell állítani. | Igen |
| projekt | Az alapértelmezett BigQuery projekt projektazonosítója, amelyhez le kell kérdezni.  | Igen |
| további projektek | A nyilvános BigQuery-projektek hozzáféréssel rendelkező projektazonosítóinak vesszővel elválasztott listája.  | Nem |
| kérésGoogleDriveScope | A Google Drive-hoz való hozzáférés kérése. A Google Drive-hozzáférés engedélyezése lehetővé teszi az összevont táblázatok támogatását, amelyek a BigQuery-adatokat a Google Drive-ról származó adatokkal kombinálják. Az alapértelmezett érték: **hamis**.  | Nem |
| authenticationType | A hitelesítéshez használt OAuth 2.0 hitelesítési mechanizmus. A ServiceAuthentication csak saját üzemeltetésű integrációs futtatási futtatási rendszeren használható. <br/>Az engedélyezett értékek: **UserAuthentication** és **ServiceAuthentication**. Tekintse meg az alábbi szakaszokat a táblázat ban több tulajdonság és JSON-minták az adott hitelesítési típusok, illetve. | Igen |

### <a name="using-user-authentication"></a>Felhasználói hitelesítés használata

Állítsa a "authenticationType" tulajdonságot **UserAuthentication**tulajdonságra, és adja meg a következő tulajdonságokat az előző szakaszban ismertetett általános tulajdonságokkal együtt:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| ügyfél-azonosító | A frissítési jogkivonat létrehozásához használt alkalmazás azonosítója. | Nem |
| ügyféltitkos | A frissítési jogkivonat létrehozásához használt alkalmazás titkos kulcsa. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |
| refreshToken | A Google-tól kapott frissítési token a BigQuery-hez való hozzáférés engedélyezésére szolgál. Ismerje meg, hogyan szerezhet be egyet [az OAuth 2.0 hozzáférési tokenek és](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) [a közösségi blog](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59)beszerzéséből. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |

**Példa:**

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

### <a name="using-service-authentication"></a>Szolgáltatáshitelesítés használata

Állítsa a "authenticationType" tulajdonságot **ServiceAuthentication**tulajdonságra, és adja meg a következő tulajdonságokat az előző szakaszban ismertetett általános tulajdonságokkal együtt. Ez a hitelesítési típus csak saját üzemeltetésű integrációs futásidejű en használható.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| e-mail | A ServiceAuthentication szolgáltatásfiók e-mail azonosítója. Csak önkiszolgáló integrációs futásidejű en használható.  | Nem |
| keyFilePath | A szolgáltatási fiók e-mail címének hitelesítésére használt .p12 kulcsfájl teljes elérési útja. | Nem |
| trustedCertPath | A megbízható hitelesítésszolgáltatói tanúsítványokat tartalmazó .pem fájl teljes elérési útja, amely a kiszolgáló TLS-en keresztüli csatlakozáskor történő ellenőrzésére szolgál. Ez a tulajdonság csak akkor állítható be, ha a TLS-t önkiszolgáló integrációs futásidőben használja. Az alapértelmezett érték az integrációs futásidejűsel telepített cacerts.pem fájl.  | Nem |
| useSystemTrustStore | Itt adható meg, hogy a rendszermegbízhatósági tárolóból vagy egy megadott .pem fájlból származó hitelesítésszolgáltatói tanúsítványt használjon.Specifies whether to use a ca certificate from the system trust store or from a specified .pem file. Az alapértelmezett érték: **hamis**.  | Nem |

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. Ez a szakasz a Google BigQuery adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Google BigQuery programból, állítsa az adatkészlet típustulajdonságát **a GoogleBigQueryObject objektumra.** A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **GoogleBigQueryObject** | Igen |
| Adatkészlet | A Google BigQuery adatkészlet neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tableName | A tábla neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Az új számítási `dataset` `table`feladatokhoz használja és használja. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Google BigQuery forrástípusa által támogatott tulajdonságok listáját tartalmazza.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource forrástípusként

Ha adatokat szeretne másolni a Google BigQuery ből, állítsa be a forrástípusát a másolási tevékenységben a **GoogleBigQuerySource elemre.** A következő tulajdonságokat a másolási tevékenység **forrása** szakasz támogatja.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás típustulajdonságát **a GoogleBigQuerySource**beállításra kell állítani. | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

**Példa:**

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

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a Támogatott adattárak című témakörben [tetszését.](copy-activity-overview.md#supported-data-stores-and-formats)

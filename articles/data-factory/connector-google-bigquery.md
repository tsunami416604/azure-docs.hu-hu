---
title: Adatok másolása a Google BigQuery-ből az Azure Data Factory használatával |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Google BigQuery támogatott fogadó adattárakba egy data factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: c9320c8d0cf512bc9145accc07ab4c79630a7c84
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55301356"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Adatok másolása a Google BigQuery-ből az Azure Data Factory használatával

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolása a Google BigQuery-ből. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Google BigQuery-ből adatok másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

A Data Factory-kapcsolat beépített illesztőprogramot tartalmaz. Így nem kell manuálisan telepítenie az összekötő használatához illesztőprogramot.

>[!NOTE]
>A Google BigQuery-összekötő a BigQuery API-k épül. Vegye figyelembe, hogy BigQuery korlátok a maximális sebesség a bejövő kéréseket, és kikényszeríti a-project alapon, megfelelő kvóták hivatkozzon [kvóták és korlátok – API-kérelmek](https://cloud.google.com/bigquery/quotas#api_requests). Ellenőrizze, hogy Ön nem indítja el a fiókot a túl sok egyidejű kéréssel.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások a Google BigQuery-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak számára a Google BigQuery-beli társított szolgáltatást.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **GoogleBigQuery**. | Igen |
| Projekt | A projekt Azonosítóját, az alapértelmezett BigQuery projekt-lekérdezést.  | Igen |
| additionalProjects | Nyilvános projekt azonosítóinak vesszővel elválasztott listáját BigQuery projektek való hozzáférést.  | Nem |
| requestGoogleDriveScope | E kérelmezzenek hozzáférést a Google Drive-bA. Engedélyezi a Google Drive-hozzáférést lehetővé teszi, hogy összevont táblákat, amelyek BigQuery adatait a Google drive-on támogatása. Az alapértelmezett érték **hamis**.  | Nem |
| authenticationType | Az OAuth 2.0 hitelesítési mechanizmust a hitelesítéshez használt. ServiceAuthentication csak a helyi Integration Runtime használható. <br/>Engedélyezett értékek a következők **UserAuthentication** és **ServiceAuthentication**. Tekintse meg a további tulajdonságok és hitelesítési típusokhoz JSON-minták a táblázat alatti részek jelölik. | Igen |

### <a name="using-user-authentication"></a>Felhasználói hitelesítés használatával

"AuthenticationType" tulajdonságát állítsa **UserAuthentication**, és adja meg az előző szakaszban leírt általános tulajdonságai mellett a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| clientId | A frissítési token létrehozásához használt alkalmazás azonosítója. | Nem |
| clientSecret | Az alkalmazás a frissítési jogkivonat létrehozásához használt titkos kulcsot. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| refreshToken | A frissítési jogkivonat érkezett a Google BigQuery való hozzáférés engedélyezése. Ismerje meg, hogyan kérhet egyet [beszerzése az OAuth 2.0 hozzáférési jogkivonatok](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) és [közösségi blog](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |

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

### <a name="using-service-authentication"></a>Szolgáltatáshitelesítés a .NET használatával

"AuthenticationType" tulajdonságát állítsa **ServiceAuthentication**, és adja meg a következő tulajdonságokkal együtt az előző szakaszban leírt általános tulajdonságokat. A hitelesítési típus csak a helyi Integration Runtime használható.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| e-mail | A szolgáltatásfiók e-mail azonosítója, amely ServiceAuthentication szolgál. Csak a helyi Integration Runtime használható.  | Nem |
| keyFilePath | A szolgáltatásfiókjának e-mail címe hitelesítéséhez használt kulcs .p12-fájl teljes elérési útja. | Nem |
| trustedCertPath | A .pem-fájlt, amely tartalmazza a segítségével ellenőrizze a kiszolgálón, ha SSL-kapcsolaton keresztül csatlakozik a megbízható Hitelesítésszolgáltatói tanúsítvány teljes elérési útja. Ez a tulajdonság beállítható csak akkor, ha a helyi integrációs modul az SSL Protokollt használja. Az alapértelmezett érték a cacerts.pem fájlt az integration runtime telepítve.  | Nem |
| useSystemTrustStore | Megadja, hogy a Hitelesítésszolgáltatói tanúsítvány használatára, a rendszer megbízható áruházból vagy egy megadott .pem-fájlt. Az alapértelmezett érték **hamis**.  | Nem |

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Google BigQuery-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása a Google BigQuery, állítsa be a type tulajdonság, az adatkészlet **GoogleBigQueryObject**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **GoogleBigQueryObject** | Igen |
| tableName | A tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Google BigQuery-forrás típusa által támogatott tulajdonságok listáját tartalmazza.

### <a name="googlebigquerysource-as-a-source-type"></a>A forrás típusaként GoogleBigQuerySource

Adatok másolása a Google BigQuery, állítsa be a forrás típusaként a másolási tevékenység **GoogleBigQuerySource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **GoogleBigQuerySource**. | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

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

## <a name="next-steps"></a>További lépések
A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

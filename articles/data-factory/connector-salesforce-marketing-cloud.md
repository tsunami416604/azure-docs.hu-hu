---
title: Adatok másolása az Azure Data Factory (előzetes verzió) használatával Salesforce Marketing Cloud |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Salesforce Marketing Cloud támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: f20cccb87bf2da53086f2e7bb14edddaa57b991e
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010556"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory-preview"></a>Adatok másolása az Azure Data Factory (előzetes verzió) használatával Salesforce Marketing Cloud

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory használatával adatait átmásolhatja a Salesforce Marketing Cloud. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Próbálja ki, és küldjön visszajelzést. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

A Salesforce marketing Cloud Connector a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás-mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Adatok átmásolhatja a Salesforce Marketing Cloud, bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

A Salesforce marketing Cloud Connector támogatja a OAuth 2 hitelesítést. A [Salesforce marketing Cloud Rest APIra](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm)épül.

>[!NOTE]
>Ez az összekötő nem támogatja az egyéni objektumok vagy az egyéni adatbővítmények beolvasását.

## <a name="getting-started"></a>Első lépések

.NET SDK-t, Python SDK-t, az Azure PowerShell, REST API-t vagy az Azure Resource Manager-sablon használatával másolási tevékenységgel rendelkező folyamatot hozhat létre. Lásd: [másolási tevékenység oktatóanyagát](quickstart-create-data-factory-dot-net.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Salesforce Marketing Cloud connector-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Salesforce Marketing Cloud-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **SalesforceMarketingCloud** | Igen |
| clientId | A Salesforce Marketing Cloud alkalmazással társított ügyfél-azonosító.  | Igen |
| clientSecret | Az ügyfél titkos kulcsát, a Salesforce Marketing Cloud-alkalmazáshoz társított. Kiválaszthatja, hogy ezt a mezőt SecureString szeretné tárolni az ADF-ben való biztonságos tároláshoz, vagy a jelszó tárolásához Azure Key Vaultban, majd az ADF másolási tevékenységének lekérése az adatok másolásakor – további információ a [tárolt hitelesítő adatokról Key Vault](store-credentials-in-key-vault.md). | Igen |
| useEncryptedEndpoints | Megadja, hogy a data source végpontok HTTPS segítségével titkosítja. Az alapértelmezett érték: true.  | Nem |
| useHostVerification | Megadja a kiszolgálói tanúsítvány a kiszolgáló állomásneve megfelelően, ha SSL-kapcsolaton keresztül kapcsolódik az állomás neve kötelező legyen-e. Az alapértelmezett érték: true.  | Nem |
| usePeerVerification | Megadja, hogy ellenőrizze a kiszolgáló identitását, ha SSL-kapcsolaton keresztül kapcsolódik. Az alapértelmezett érték: true.  | Nem |

**Példa**

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}

```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Salesforce Marketing Cloud adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Használatával adatait átmásolhatja a Salesforce Marketing Cloud, állítsa be a type tulajdonság, az adatkészlet **SalesforceMarketingCloudObject**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **SalesforceMarketingCloudObject** | Igen |
| tableName | A tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Salesforce Marketing Cloud forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="salesforce-marketing-cloud-as-source"></a>A Salesforce Marketing Cloud forrásként

Használatával adatait átmásolhatja a Salesforce Marketing Cloud, állítsa be a forrás típusaként a másolási tevékenység **SalesforceMarketingCloudSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **SalesforceMarketingCloudSource** | Igen |
| query | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
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
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

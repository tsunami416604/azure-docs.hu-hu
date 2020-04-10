---
title: Adatok másolása a Salesforce Marketing Cloud szolgáltatásból
description: Megtudhatja, hogy miként másolhatja az adatokat a Salesforce Marketing Cloud szolgáltatásból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
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
ms.date: 10/25/2019
ms.openlocfilehash: a76150756d87b5f2c1b73b8755500e63cdfe05c9
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991739"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Adatok másolása a Salesforce Marketing Cloud szolgáltatásból az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenységet az Azure Data Factory-ban adatok másolásához a Salesforce Marketing Cloud-ból. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Salesforce Marketing Cloud összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Salesforce Marketing Cloud szolgáltatásból adatokat másolhat bármely támogatott fogadóadattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

A Salesforce Marketing Cloud összekötő támogatja az OAuth 2 hitelesítést. A [Salesforce Marketing Cloud REST API-ra](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm)épül.

>[!NOTE]
>Ez az összekötő nem támogatja az egyéni objektumok vagy egyéni adatbővítmények beolvasását.

## <a name="getting-started"></a>Első lépések

A .NET SDK, Python SDK, Azure PowerShell, REST API vagy Azure Resource Manager-sablon használatával másolási tevékenységet használó folyamatot hozhat létre. Lásd: [Tevékenység-oktatóanyag másolása](quickstart-create-data-factory-dot-net.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

A következő szakaszok a Salesforce Marketing Cloud-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A Salesforce Marketing Cloud kapcsolódó szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **SalesforceMarketingCloud** | Igen |
| ügyfél-azonosító | A Salesforce Marketing Cloud alkalmazáshoz társított ügyfélazonosító.  | Igen |
| ügyféltitkos | A Salesforce Marketing Cloud alkalmazáshoz társított ügyféltitok. Ezt a mezőt SecureStringként jelölheti meg, hogy biztonságosan tárolhassa az ADF-ben, vagy tárolja a jelszót az Azure Key Vaultban, és hagyja, hogy az ADF-másolási tevékenység lekérése az adatok másolásakor származzon – további információ a [Key Vault Áruházhitelesítő adataiból.](store-credentials-in-key-vault.md) | Igen |
| useEncryptedEndpoints | Itt adható meg, hogy az adatforrás végpontjai HTTPS protokoll használatával titkosítva legyenek-e. Az alapértelmezett érték az igaz.  | Nem |
| useHostVerification (useHostVerification) | Itt adható meg, hogy a kiszolgáló tanúsítványában lévő állomásnév nek meg kell-e egyeznie a kiszolgáló állomásnevével, amikor TLS-en keresztül csatlakozik. Az alapértelmezett érték az igaz.  | Nem |
| usePeerVerification | Itt adható meg, hogy a kiszolgáló identitását ellenőrizze-e a TLS-en keresztüli csatlakozáskor. Az alapértelmezett érték az igaz.  | Nem |

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a Salesforce Marketing Cloud adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Salesforce Marketing Cloud szolgáltatásból, állítsa az adatkészlet típustulajdonságát **A SalesforceMarketingCloudObject tulajdonságra.** A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **SalesforceMarketingCloudObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Salesforce Marketing Cloud forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce Marketing Cloud mint forrás

Ha adatokat szeretne másolni a Salesforce Marketing Cloud szolgáltatásból, állítsa be a forrástípust a másolási tevékenységben a **SalesforceMarketingCloudSource mezőre.** A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **SalesforceMarketingCloudSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

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

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.

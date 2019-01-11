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
ms.date: 01/09/2019
ms.author: jingwang
ms.openlocfilehash: 2a469864a7b4a693ecc908fda360da1b82e8c5f3
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200445"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory-preview"></a>Adatok másolása az Azure Data Factory (előzetes verzió) használatával Salesforce Marketing Cloud

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory használatával adatait átmásolhatja a Salesforce Marketing Cloud. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Próbálja ki, és küldjön visszajelzést. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok átmásolhatja a Salesforce Marketing Cloud, bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolat beépített illesztőprogramot tartalmaz, ezért nem kell manuálisan telepítenie az összes illesztőprogram ezzel az összekötővel.

>[!NOTE]
>Ez az összekötő nem támogatja az egyéni objektumok vagy egyéni kiterjesztések beolvasása.

## <a name="getting-started"></a>Első lépések

.NET SDK-t, Python SDK-t, az Azure PowerShell, REST API-t vagy az Azure Resource Manager-sablon használatával másolási tevékenységgel rendelkező folyamatot hozhat létre. Lásd: [másolási tevékenység oktatóanyagát](quickstart-create-data-factory-dot-net.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Salesforce Marketing Cloud connector-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Salesforce Marketing Cloud-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Következőhöz: SalesforceMarketingCloud** | Igen |
| clientId | A Salesforce Marketing Cloud alkalmazással társított ügyfél-azonosító.  | Igen |
| clientSecret | Az ügyfél titkos kulcsát, a Salesforce Marketing Cloud-alkalmazáshoz társított. Ha szeretné, ezt a mezőt megjelölése a SecureString tárolja biztonságos helyen az ADF-ben, vagy a jelszó tárolásához az Azure Key Vaultban, és lehetővé teszik az ADF acitivty lekéréses hajt végre az adatok másolása innen másolása – ismerje meg alaposabban a [Store hitelesítő adatokat a Key Vaultban](store-credentials-in-key-vault.md). | Igen |
| useEncryptedEndpoints | Megadja, hogy a data source végpontok HTTPS segítségével titkosítja. Az alapértelmezett érték: igaz.  | Nem |
| useHostVerification | Megadja a kiszolgálói tanúsítvány a kiszolgáló állomásneve megfelelően, ha SSL-kapcsolaton keresztül kapcsolódik az állomás neve kötelező legyen-e. Az alapértelmezett érték: igaz.  | Nem |
| usePeerVerification | Megadja, hogy ellenőrizze a kiszolgáló identitását, ha SSL-kapcsolaton keresztül kapcsolódik. Az alapértelmezett érték: igaz.  | Nem |

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
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **SalesforceMarketingCloudObject** | Igen |
| tableName | A tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Salesforce Marketing Cloud forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="salesforce-marketing-cloud-as-source"></a>A Salesforce Marketing Cloud forrásként

Használatával adatait átmásolhatja a Salesforce Marketing Cloud, állítsa be a forrás típusaként a másolási tevékenység **SalesforceMarketingCloudSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **SalesforceMarketingCloudSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa**

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

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

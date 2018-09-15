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
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: 62e925c3b1f48752b5062560a325953f8190c198
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634693"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Marketing Cloud Salesforce-ban

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory használatával adatait átmásolhatja a Salesforce Marketing Cloud. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Próbálja ki, és küldjön visszajelzést. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok átmásolhatja a Salesforce Marketing Cloud, bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolat beépített illesztőprogramot tartalmaz, ezért nem kell manuálisan telepítenie az összes illesztőprogram ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

.NET SDK-t, Python SDK-t, az Azure PowerShell, REST API-t vagy az Azure Resource Manager-sablon használatával másolási tevékenységgel rendelkező folyamatot hozhat létre. Lásd: [másolási tevékenység oktatóanyagát](quickstart-create-data-factory-dot-net.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Salesforce Marketing Cloud connector-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Salesforce Marketing Cloud-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **következőhöz: SalesforceMarketingCloud** | Igen |
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

Használatával adatait átmásolhatja a Salesforce Marketing Cloud, állítsa be a type tulajdonság, az adatkészlet **SalesforceMarketingCloudObject**. Egy adatkészlet ilyen további típus-specifikus tulajdonság nincs.

**Példa**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
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
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **SalesforceMarketingCloudSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Igen |

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

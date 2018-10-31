---
title: Adatok másolása a QuickBooks online-hoz az Azure Data Factory (előzetes verzió) használatával |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a QuickBooks online-hoz támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
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
ms.date: 10/29/2018
ms.author: jingwang
ms.openlocfilehash: a3d079483ecf4ea8cf9a4c6bda050bfe8befcfd0
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241684"
---
# <a name="copy-data-from-quickbooks-online-using-azure-data-factory-preview"></a>Adatok másolása a QuickBooks online-hoz az Azure Data Factory (előzetes verzió) használatával

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával adatokat másol a QuickBooks online-hoz. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Próbálja ki, és küldjön visszajelzést. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

A QuickBooks Online adatok másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolat beépített illesztőprogramot tartalmaz, ezért nem kell manuálisan telepítenie az összes illesztőprogram ezzel az összekötővel.

Ez az összekötő jelenleg csak 1.0a, ami azt jelenti, szüksége lesz egy fejlesztői fiók a 2017. július 17. előtt létrehozott alkalmazások támogatják.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott QuickBooks összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

QuickBooks társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **QuickBooks** | Igen |
| endpoint | A végpont a QuickBooks Online-kiszolgáló. (azaz quickbooks.api.intuit.com)  | Igen |
| companyId | A QuickBooks vállalati engedélyezésére vállalat azonosítója. Keresse meg a vállalati azonosító kapcsolatos információk: [Hogyan találhatom meg a vállalati azonosító?](https://quickbooks.intuit.com/community/Getting-Started/How-do-I-find-my-Company-ID/m-p/185551). | Igen |
| consumerKey | Az OAuth 1.0-s hitelesítési fogyasztói kulcs. | Igen |
| consumerSecret | A fogyasztói titkos OAuth 1.0-hitelesítéshez. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| hozzáférési tokent | Az OAuth 1.0-s hitelesítési jogkivonat. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| accessTokenSecret | A hozzáférési jogkivonat titkos kulcs OAuth 1.0-hitelesítéshez. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| useEncryptedEndpoints | Megadja, hogy a data source végpontok HTTPS segítségével titkosítja. Az alapértelmezett érték: igaz.  | Nem |

**Példa**

```json
{
    "name": "QuickBooksLinkedService",
    "properties": {
        "type": "QuickBooks",
        "typeProperties": {
            "endpoint" : "quickbooks.api.intuit.com",
            "companyId" : "<companyId>",
            "consumerKey": "<consumerKey>",
            "consumerSecret": {
                "type": "SecureString",
                "value": "<consumerSecret>"
            },
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            },
            "accessTokenSecret": {
                 "type": "SecureString",
                 "value": "<accessTokenSecret>"
            },
            "useEncryptedEndpoints" : true
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a QuickBooks adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása a QuickBooks online-hoz, állítsa be a type tulajdonság, az adatkészlet **QuickBooksObject**. Egy adatkészlet ilyen további típus-specifikus tulajdonság nincs.

**Példa**

```json
{
    "name": "QuickBooksDataset",
    "properties": {
        "type": "QuickBooksObject",
        "linkedServiceName": {
            "referenceName": "<QuickBooks linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a QuickBooks forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="quickbookssource-as-source"></a>QuickBooksSource forrásként

Adatok másolása a QuickBooks online-hoz, állítsa be a forrás típusaként a másolási tevékenység **QuickBooksSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **QuickBooksSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM "Bill" WHERE Id = '123'"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromQuickBooks",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<QuickBooks input dataset name>",
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
                "type": "QuickBooksSource",
                "query": "SELECT * FROM \"Bill\" WHERE Id = '123' "
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="copy-data-from-quickbooks-desktop"></a>Adatok másolása a Quickbooks Desktopból

A másolási tevékenység az Azure Data Factoryban közvetlenül a Quickbooks Desktopból nem lehet másolni az adatokat. Quickbooks asztali adatmásolás a Quickbooks adatok exportálása egy vesszővel tagolt (CSV) fájl, és majd feltöltjük a fájlt az Azure Blob Storage. Itt a Data Factory használatával másolja az adatokat tetszőleges a fogadó.

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

---
title: Adatok másolása az Azure Data Factory (előzetes verzió) használatával Xero |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Xero támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
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
ms.openlocfilehash: d9ee9a73f4e88786ca51fe9fac50ce51e25b4dde
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123368"
---
# <a name="copy-data-from-xero-using-azure-data-factory-preview"></a>Adatok másolása az Azure Data Factory (előzetes verzió) használatával Xero

Ez a cikk az Azure Data Factory a másolási tevékenység használatával adatokat másol a Xero módját ismerteti. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Próbálja ki, és visszajelzést. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat Xero bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

A Xero-összekötőt, a következőket támogatja:

- Xero [privát alkalmazás](https://developer.xero.com/documentation/getting-started/api-application-types) , de nem nyilvános alkalmazás.
- "Reports" kivételével az összes Xero táblák (API-végpontok). 

Az Azure Data Factory kapcsolat beépített illesztőprogramot tartalmaz, ezért nem kell manuálisan telepítenie az összes illesztőprogram ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Xero-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Xero-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **Xero** | Igen |
| gazdagép | A végpont a Xero-kiszolgáló (`api.xero.com`).  | Igen |
| consumerKey | A Xero-alkalmazáshoz társított fogyasztói kulcs. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| privateKey | A titkos kulcsot a .pem fájl jött létre a Xero magánhálózati alkalmazáshoz, tekintse meg [nyilvános/titkos kulcspár létrehozása](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Vegye figyelembe, hogy **készítése a privatekey.pem az 512 numbits** használatával `openssl genrsa -out privatekey.pem 512`; 1024 nem támogatott. A .pem-fájlt, többek között a Unix sor endings(\n) minden szöveget tartalmaznak, tekintse meg az alábbi mintát.<br/><br/>Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| useEncryptedEndpoints | Megadja, hogy a data source végpontok HTTPS segítségével titkosítja. Az alapértelmezett érték: igaz.  | Nem |
| useHostVerification | Itt adhatja meg, e a gazdagép nevét kötelező megadni a kiszolgálói tanúsítvány a kiszolgáló állomásneve megfelelően, ha SSL-kapcsolaton keresztül kapcsolódik. Az alapértelmezett érték: igaz.  | Nem |
| usePeerVerification | Megadja, hogy ellenőrizze a kiszolgáló identitását, ha SSL-kapcsolaton keresztül kapcsolódik. Az alapértelmezett érték: igaz.  | Nem |

**Példa**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

**Minta titkos kulcs értéke:**

A .pem-fájlt a Unix sor endings(\n) többek között az összes szöveget is.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Xero-adathalmaz által támogatott tulajdonságok listáját tartalmazza.

Xero adatmásolás, állítsa be a type tulajdonság, az adatkészlet **XeroObject**. Egy adatkészlet ilyen további típus-specifikus tulajdonság nincs.

**Példa**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Xero-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="xero-as-source"></a>Xero forrásként

Adatok másolása a Xero, állítsa be a forrás típusaként a másolási tevékenység **XeroSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **XeroSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM Contacts"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

A Xero-lekérdezés megadása közben, vegye figyelembe a következőket:

- Összetett elemek rendelkező táblák szét lesznek osztva a több táblájához. Például banki tranzakciók rendelkezik-e összetett adatstruktúra "Sorelemek", így a banki tranzakciók adatait le van képezve táblához `Bank_Transaction` és `Bank_Transaction_Line_Items`, a `Bank_Transaction_ID` kapcsolja össze a külső kulcsként.

- Xero-adatok két sémák keresztül érhető el: `Minimal` (alapértelmezett), és `Complete`. A teljes séma előfeltétel hívás táblák ehhez szükséges további adatokat (például az azonosító oszlop) tartalmaz a kívánt lekérdezés végrehajtása előtt.

Az alábbi táblázatok a minimális és teljes körű sémában ugyanazokat az információkat tartalmaznak. API-hívások számának csökkentése érdekében használja a minimális séma (alapértelmezett).

- Bank_Transactions
- Contact_Groups 
- Kapcsolatok 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Számlák 
- Invoices_Credit_Notes
- Invoices_ előre befizetett 
- Invoices_Overpayments 
- Manual_Journals 
- Túl 
- Overpayments_Allocations 
- Előre befizetett 
- Prepayments_Allocations 
- Befizetések 
- Receipt_Validation_Errors 
- Tracking_Categories

Az alábbi táblázatok a teljes séma csak kérdezhetők le:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ személyek 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ kifizetések 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="next-steps"></a>További lépések
A másolási tevékenység által támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

---
title: "Adatok másolása az Azure Data Factory (béta) használatával Xero |} Microsoft Docs"
description: "Útmutató: adatok másolása Xero támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: 458ad702b510c0fd01ab63541b2026b8a9a06e91
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="copy-data-from-xero-using-azure-data-factory-beta"></a>Adatok másolása az Azure Data Factory (béta) használatával Xero

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatokat másolni a Xero. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Ez az összekötő jelenleg bétaverziójú. Próbálja ki, és visszajelzést. Ne használja éles környezetben.

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok bármely támogatott fogadó adattárolóhoz Xero másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a Xero összekötő támogatja:

- Xero [titkos alkalmazás](https://developer.xero.com/documentation/getting-started/api-application-types) , de nem nyilvános alkalmazás.
- Minden Xero kívül más táblák (API-végpontok) "Jelentések". 

Az Azure Data Factory kapcsolódásának engedélyezése beépített illesztőprogramot tartalmaz, ezért nem szükséges manuálisan kell telepítenie minden olyan illesztőprogram ezt az összekötőt használja.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

A következő szakaszok részletesen bemutatják való Xero összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok Xero kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Xero** | Igen |
| gazdagép | A Xero Server endpoint (`api.xero.com`).  | Igen |
| consumerKey | A Xero alkalmazáshoz kapcsolódó kulcsa. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| privateKey | A titkos kulcsot a .pem fájl lett létrehozva, a Xero titkos alkalmazás, lásd: [hozzon létre egy nyilvános/titkos kulcspár](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). A .pem fájl, többek között a Unix sor endings(\n) az összes szöveg, tekintse meg az alábbi minta.<br/>Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| useEncryptedEndpoints | Meghatározza, hogy a data source végpontok titkosítása HTTPS használatával. Az alapértelmezett érték: igaz.  | Nem |
| useHostVerification | Meghatározza, hogy az állomásnév szükséges a kiszolgálói tanúsítvány felel meg a kiszolgáló állomásnevét, SSL-en keresztüli kapcsolódás esetén. Az alapértelmezett érték: igaz.  | Nem |
| usePeerVerification | Megadja, hogy a kiszolgáló identitásának ellenőrzése SSL-en keresztüli kapcsolódás esetén. Az alapértelmezett érték: igaz.  | Nem |

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

**A minta titkos kulcs értéke:**

A .pem fájl, többek között a Unix sor endings(\n) összes szöveget tartalmaznak.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Xero dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása a Xero, az adatkészlet típus tulajdonságának beállítása **XeroObject**. Nincs ilyen típusú dataset további típusra vonatkozó tulajdonság.

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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Xero forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="xero-as-source"></a>A Xero forrásaként

Adatok másolása a Xero, állítsa be a forrás típusa a másolási tevékenység **XeroSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **XeroSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM Contacts"`. | Igen |

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

Ha a Xero lekérdezést, vegye figyelembe a következőket:

- Összetett elemet tartalmazó táblák több táblájához lesznek osztva. Például banki tranzakciók rendelkezik-e olyan összetett adatszerkezet "LineItems", így adatok banki tranzakció le van képezve táblához `Bank_Transaction` és `Bank_Transaction_Line_Items`, a `Bank_Transaction_ID` kapcsolja össze a külső kulcsként.

- Xero adatok két sémák keresztül érhető el: `Minimal` (alapértelmezett) és `Complete`. A teljes séma előfeltétel hívás táblák ehhez szükséges további adatokat (pl. azonosító oszlop) tartalmaz a kívánt lekérdezés végrehajtása előtt.

Az alábbi táblázatok a minimális és a teljes sémában ugyanazokat az információkat tartalmaznak. Az API-hívások számának csökkentése érdekében használja a minimális séma (alapértelmezett).

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
- Invoices_ előlegek 
- Invoices_Overpayments 
- Manual_Journals 
- Túl 
- Overpayments_Allocations 
- Előlegek 
- Prepayments_Allocations 
- Visszaigazolások 
- Receipt_Validation_Errors 
- Tracking_Categories

Az alábbi táblázatok csak lekérdezhető teljes sémával:

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
A másolási tevékenység által támogatott adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).

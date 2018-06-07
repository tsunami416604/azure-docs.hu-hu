---
title: Adatok másolása az Azure Data Factory (béta) használatával Amazon piactér webszolgáltatás |} Microsoft Docs
description: 'Útmutató: adatok másolása az Amazon piactér webszolgáltatás támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával.'
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 0f15ab5a84a51f0a22b00b647046e5a15da469a9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34616362"
---
# <a name="copy-data-from-amazon-marketplace-web-service-using-azure-data-factory-beta"></a>Adatok másolása az Azure Data Factory (béta) használatával Amazon piactér webszolgáltatás

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatok másolása az Amazon piactér webszolgáltatás. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Ez az összekötő jelenleg bétaverziójú. Próbálja ki, és küldjön visszajelzést. Ne használja éles környezetben.

## <a name="supported-capabilities"></a>Támogatott képességei

Amazon piactér webszolgáltatás adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolódásának engedélyezése beépített illesztőprogramot tartalmaz, ezért nem szükséges manuálisan kell telepítenie minden olyan illesztőprogram ezt az összekötőt használja.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

A következő szakaszok részletesen bemutatják Amazon piactér Web Service Connector alkalmazáshoz való adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Amazon piactér webszolgáltatás kapcsolódó szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AmazonMWS** | Igen |
| endpoint | A végpont és az Amazon MWS server (Ez azt jelenti, hogy mws.amazonservices.com)  | Igen |
| marketplaceID | Az Amazon Piactér azonosítója szeretné beolvasni az adatokat. Adatok lekérése több piactér azonosítók, válassza el őket egy vesszővel (`,`). (Ez azt jelenti, hogy A2EUQ1WTGCTBG2)  | Igen |
| sellerID | Az Amazon értékesítő azonosítóját.  | Igen |
| mwsAuthToken | Az Amazon MWS hitelesítési jogkivonat. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| accessKeyId | A hozzáférési kulcs adatok eléréséhez használt azonosítója.  | Igen |
| secretKey | Az adatok eléréséhez használt titkos kulcs. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| useEncryptedEndpoints | Meghatározza, hogy a data source végpontok titkosítása HTTPS használatával. Az alapértelmezett érték: igaz.  | Nem |
| useHostVerification | Meghatározza, hogy az a számítógép neve a egyeznie kell a gazdagép nevével a kiszolgáló SSL-en keresztüli kapcsolódás esetén a kiszolgáló tanúsítványát. Az alapértelmezett érték: igaz.  | Nem |
| usePeerVerification | Megadja, hogy a kiszolgáló identitásának ellenőrzése SSL-en keresztüli kapcsolódás esetén. Az alapértelmezett érték: igaz.  | Nem |

**Példa**

```json
{
    "name": "AmazonMWSLinkedService",
    "properties": {
        "type": "AmazonMWS",
        "typeProperties": {
            "endpoint" : "mws.amazonservices.com",
            "marketplaceID" : "A2EUQ1WTGCTBG2",
            "sellerID" : "<sellerID>",
            "mwsAuthToken": {
                 "type": "SecureString",
                 "value": "<mwsAuthToken>"
            },
            "accessKeyId" : "<accessKeyId>",
            "secretKey": {
                 "type": "SecureString",
                 "value": "<secretKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a témakör az Amazon piactér webszolgáltatás dataset által támogatott tulajdonságokról.

Adatok másolása az Amazon piactér webes szolgáltatás, állítsa be a type tulajdonságot az adathalmaz **AmazonMWSObject**. Nincs ilyen típusú dataset további típusra vonatkozó tulajdonság.

**Példa**

```json
{
    "name": "AmazonMWSDataset",
    "properties": {
        "type": "AmazonMWSObject",
        "linkedServiceName": {
            "referenceName": "<AmazonMWS linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Amazon piactér webszolgáltatás forrás által támogatott tulajdonságokról.

### <a name="amazonmwssource-as-source"></a>AmazonMWSSource forrásaként

Adatok másolása Amazon piactér webes szolgáltatás, állítsa be a forrás típusa a másolási tevékenység **AmazonMWSSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **AmazonMWSSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM Orders where  Amazon_Order_Id = 'xx'"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromAmazonMWS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AmazonMWS input dataset name>",
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
                "type": "AmazonMWSSource",
                "query": "SELECT * FROM Orders where Amazon_Order_Id = 'xx'"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).

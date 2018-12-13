---
title: Adatok másolása az Azure Data Factory (előzetes verzió) használatával Google AdWords |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Google AdWords támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
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
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 2a17bca6f7b95850f84781b5838719617d45bdd2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077817"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory-preview"></a>Adatok másolása az Azure Data Factory (előzetes verzió) használatával Google AdWords

Ez a cikk az Azure Data Factory a másolási tevékenység használatával adatokat másol a Google AdWords módját ismerteti. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Próbálja ki, és visszajelzést. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat a Google AdWords bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolat beépített illesztőprogramot tartalmaz, ezért nem kell manuálisan telepítenie az összes illesztőprogram ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Google AdWords összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak a Google AdWords társított szolgáltatást:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **GoogleAdWords** | Igen |
| clientCustomerID | Az ügyfél ügyfél-azonosító, hogy a jelentés adatait beolvasni kívánt AdWords fiók.  | Igen |
| developerToken | A fejlesztői token társított a-kezelői fiók, amellyel a AdWords API a hozzáférési jogot.  Ha szeretné, ezt a mezőt megjelölése a SecureString tárolja biztonságos helyen az ADF-ben, vagy a jelszó tárolásához az Azure Key Vaultban, és lehetővé teszik az ADF acitivty lekéréses hajt végre az adatok másolása innen másolása – ismerje meg alaposabban a [Store hitelesítő adatokat a Key Vaultban](store-credentials-in-key-vault.md). | Igen |
| authenticationType | Az OAuth 2.0 hitelesítési mechanizmust a hitelesítéshez használt. ServiceAuthentication csak akkor használható a saját üzemeltetésű <br/>Engedélyezett értékek a következők: **ServiceAuthentication**, **UserAuthentication** | Igen |
| refreshToken | A Google szerzett AdWords való hozzáférést engedélyező UserAuthentication a frissítési jogkivonatot. Ha szeretné, ezt a mezőt megjelölése a SecureString tárolja biztonságos helyen az ADF-ben, vagy a jelszó tárolásához az Azure Key Vaultban, és lehetővé teszik az ADF acitivty lekéréses hajt végre az adatok másolása innen másolása – ismerje meg alaposabban a [Store hitelesítő adatokat a Key Vaultban](store-credentials-in-key-vault.md). | Nem |
| clientId | A frissítési jogkivonat beszerzéséhez használt google alkalmazás ügyfél-azonosító. Ha szeretné, ezt a mezőt megjelölése a SecureString tárolja biztonságos helyen az ADF-ben, vagy a jelszó tárolásához az Azure Key Vaultban, és lehetővé teszik az ADF acitivty lekéréses hajt végre az adatok másolása innen másolása – ismerje meg alaposabban a [Store hitelesítő adatokat a Key Vaultban](store-credentials-in-key-vault.md). | Nem |
| clientSecret | A titkos ügyfélkulcsot a frissítési jogkivonat beszerzéséhez használja a google-alkalmazás. Ha szeretné, ezt a mezőt megjelölése a SecureString tárolja biztonságos helyen az ADF-ben, vagy a jelszó tárolásához az Azure Key Vaultban, és lehetővé teszik az ADF acitivty lekéréses hajt végre az adatok másolása innen másolása – ismerje meg alaposabban a [Store hitelesítő adatokat a Key Vaultban](store-credentials-in-key-vault.md). | Nem |
| e-mail | A szolgáltatásfiók e-mail azonosítója, amely ServiceAuthentication szolgál, és csak akkor használható a saját üzemeltetésű  | Nem |
| keyFilePath | A .p12 kulcsfájl, amely használatos a hitelesítés a szolgáltatási fiók e-mail címét, és csak akkor használható a saját üzemeltetésű teljes elérési útja  | Nem |
| trustedCertPath | A .pem-fájlt tartalmazó ellenőrzésének folyamatát a kiszolgálón, ha SSL-kapcsolaton keresztül kapcsolódik a megbízható Hitelesítésszolgáltatói tanúsítvány teljes elérési útja. Ez a tulajdonság csak akkor állítható, ha SSL-lel a saját üzemeltetésű Az alapértelmezett érték a telepített bemutathatja cacerts.pem fájlt:  | Nem |
| useSystemTrustStore | Megadja, hogy a Hitelesítésszolgáltatói tanúsítvány használatára, a rendszer megbízható áruházból vagy egy adott PEM-fájl. Az alapértelmezett értéke FALSE (hamis).  | Nem |

**Példa**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                 "type": "SecureString",
                 "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refreshToken>"
            },
            "clientId": {
                 "type": "SecureString",
                 "value": "<clientId>"
            },
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Google AdWords adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása a Google AdWords, állítsa be a type tulajdonság, az adatkészlet **GoogleAdWordsObject**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **GoogleAdWordsObject** | Igen |
| tableName | A tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}

```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Google AdWords forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="google-adwords-as-source"></a>Google AdWords forrásként

Adatok másolása a Google AdWords, állítsa be a forrás típusaként a másolási tevékenység **GoogleAdWordsSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **GoogleAdWordsSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
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

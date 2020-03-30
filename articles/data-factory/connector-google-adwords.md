---
title: Adatok másolása a Google AdWords szolgáltatásból
description: Megtudhatja, hogy miként másolhat adatokat a Google AdWords szolgáltatásból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: b01dcad71747da6b7aa770e3993cb82892ae55fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929443"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Adatok másolása a Google AdWords szolgáltatásból az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenységet az Azure Data Factory-ban a Google AdWords-ből történő adatok másolásához. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Google AdWords-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)


A Google AdWords szolgáltatásból adatokat másolhat bármely támogatott fogadóadattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Az Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot is ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Google AdWords-összekötőre jellemző Data Factory-entitások definiálására használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A Google AdWords-hez csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **GoogleAdWords** | Igen |
| clientCustomerID | Annak az AdWords-fióknak az ügyfélügyfél-azonosítója, amelyhez jelentésadatokat szeretne letölteni.  | Igen |
| developerToken | Az AdWords API-hoz való hozzáférés engedélyezéséhez használt kezelői fiókhoz társított fejlesztői token.  Ezt a mezőt SecureStringként jelölheti meg, hogy biztonságosan tárolhassa az ADF-ben, vagy tárolja a jelszót az Azure Key Vaultban, és hagyja, hogy az ADF-másolási tevékenység lekérése az adatok másolásakor származzon – további információ a [Key Vault Áruházhitelesítő adataiból.](store-credentials-in-key-vault.md) | Igen |
| authenticationType | A hitelesítéshez használt OAuth 2.0 hitelesítési mechanizmus. A ServiceAuthentication csak saját üzemeltetésű infravörös kapcsolaton használható. <br/>Az engedélyezett értékek a következők: **ServiceAuthentication**, **UserAuthentication** | Igen |
| refreshToken | A Google-tól kapott frissítési jogkivonat, amely engedélyezi az AdWords userauthentication szolgáltatáshoz való hozzáférését. Ezt a mezőt SecureStringként jelölheti meg, hogy biztonságosan tárolhassa az ADF-ben, vagy tárolja a jelszót az Azure Key Vaultban, és hagyja, hogy az ADF-másolási tevékenység lekérése az adatok másolásakor származzon – további információ a [Key Vault Áruházhitelesítő adataiból.](store-credentials-in-key-vault.md) | Nem |
| ügyfél-azonosító | A frissítési jogkivonat megszerzéséhez használt Google-alkalmazás ügyfélazonosítója. Ezt a mezőt SecureStringként jelölheti meg, hogy biztonságosan tárolhassa az ADF-ben, vagy tárolja a jelszót az Azure Key Vaultban, és hagyja, hogy az ADF-másolási tevékenység lekérése az adatok másolásakor származzon – további információ a [Key Vault Áruházhitelesítő adataiból.](store-credentials-in-key-vault.md) | Nem |
| ügyféltitkos | A frissítési jogkivonat megszerzéséhez használt Google-alkalmazás ügyféltkája. Ezt a mezőt SecureStringként jelölheti meg, hogy biztonságosan tárolhassa az ADF-ben, vagy tárolja a jelszót az Azure Key Vaultban, és hagyja, hogy az ADF-másolási tevékenység lekérése az adatok másolásakor származzon – további információ a [Key Vault Áruházhitelesítő adataiból.](store-credentials-in-key-vault.md) | Nem |
| e-mail | A ServiceAuthentication szolgáltatásfiók e-mail azonosítója, amely csak saját üzemeltetésű infravörös kapcsolaton használható.  | Nem |
| keyFilePath | A .p12 kulcsfájl teljes elérési útja, amely a szolgáltatásfiók e-mail címének hitelesítésére szolgál, és csak saját üzemeltetésű infravörös rendszeren használható.  | Nem |
| trustedCertPath | A megbízható hitelesítésszolgáltatói tanúsítványokat tartalmazó .pem fájl teljes elérési útja a kiszolgáló SSL-kapcsolaton keresztüli ellenőrzéséhez. Ez a tulajdonság csak akkor állítható be, ha ssl-t használ saját üzemeltetésű infravörös rendszeren. Az alapértelmezett érték az infravörös kapcsolattal telepített cacerts.pem fájl.  | Nem |
| useSystemTrustStore | Itt adható meg, hogy a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból származó hitelesítésszolgáltatói tanúsítványt használjon.Specifies whether to use a ca certificate from the system trust store or from a specified PEM file. Az alapértelmezett érték a hamis.  | Nem |

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a Google AdWords-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Google AdWords szolgáltatásból, állítsa az adatkészlet típustulajdonságát a **GoogleAdWordsObject objektumra.** A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következő re kell állítani: **GoogleAdWordsObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a rész a Google AdWords-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="google-adwords-as-source"></a>A Google AdWords mint forrás

Ha adatokat szeretne másolni a Google AdWords szolgáltatásból, állítsa a másolási tevékenység forrástípusát a **GoogleAdWordsSource mezőre.** A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **GoogleAdWordsSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

**Példa:**

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

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.

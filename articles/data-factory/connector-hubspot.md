---
title: "Adatok másolása az Azure Data Factory (béta) használatával HubSpot |} Microsoft Docs"
description: "Útmutató: adatok másolása HubSpot támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: 1df2c5b7ff3fc1848fcc5fd6a2e4894688a08a98
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="copy-data-from-hubspot-using-azure-data-factory-beta"></a>Adatok másolása az Azure Data Factory (béta) használatával HubSpot

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory adatokat másolni HubSpot. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Ez az összekötő jelenleg bétaverziójú. Próbálja ki, és küldjön visszajelzést. Ne használja éles környezetben.

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok bármely támogatott fogadó adattárolóhoz HubSpot másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolódásának engedélyezése beépített illesztőprogramot tartalmaz, ezért nem szükséges manuálisan kell telepítenie minden olyan illesztőprogram ezt az összekötőt használja.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

A következő szakaszok részletesen bemutatják való HubSpot összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok HubSpot kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Hubspot** | Igen |
| clientId | A Hubspot az alkalmazáshoz tartozó ügyfél-azonosító.  | Igen |
| clientSecret | A Hubspot az alkalmazáshoz tartozó titkos ügyfélkulcsot. Ez a mező megjelölése a SecureString tárolja biztonságos helyen az ADF, vagy a jelszó tárolása az Azure Key Vault választhat, és lehetővé teszik a másolási tevékenység lekéréses ott adatmásolás végrehajtása során – további információhoz [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). | Igen |
| accessToken | Az OAuth integráció kezdetben hitelesítésekor kapott hozzáférési jogkivonat. Ez a mező megjelölése a SecureString tárolja biztonságos helyen az ADF, vagy a jelszó tárolása az Azure Key Vault választhat, és lehetővé teszik a másolási tevékenység lekéréses ott adatmásolás végrehajtása során – további információhoz [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). | Igen |
| refreshToken | A frissítési jogkivonatot kapott OAuth integráció kezdetben hitelesítésekor. Ez a mező megjelölése a SecureString tárolja biztonságos helyen az ADF, vagy a jelszó tárolása az Azure Key Vault választhat, és lehetővé teszik a másolási tevékenység lekéréses ott adatmásolás végrehajtása során – további információhoz [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). | Igen |
| useEncryptedEndpoints | Meghatározza, hogy a data source végpontok titkosítása HTTPS használatával. Az alapértelmezett érték: igaz.  | Nem |
| useHostVerification | Meghatározza, hogy az a számítógép neve a egyeznie kell a gazdagép nevével a kiszolgáló SSL-en keresztüli kapcsolódás esetén a kiszolgáló tanúsítványát. Az alapértelmezett érték: igaz.  | Nem |
| usePeerVerification | Megadja, hogy a kiszolgáló identitásának ellenőrzése SSL-en keresztüli kapcsolódás esetén. Az alapértelmezett érték: igaz.  | Nem |

**Példa**

```json
{
    "name": "HubspotLinkedService",
    "properties": {
        "type": "Hubspot",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            },
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refreshToken>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a témakör HubSpot dataset által támogatott tulajdonságokról.

Adatok másolása HubSpot, az adatkészlet típus tulajdonságának beállítása **HubspotObject**. Nincs ilyen típusú dataset további típusra vonatkozó tulajdonság.

**Példa**

```json
{
    "name": "HubspotDataset",
    "properties": {
        "type": "HubspotObject",
        "linkedServiceName": {
            "referenceName": "<Hubspot linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör HubSpot forrás által támogatott tulajdonságokról.

### <a name="hubspotsource-as-source"></a>HubspotSource forrásaként

Adatok másolása HubSpot, állítsa be a forrás típusa a másolási tevékenység **HubspotSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **HubspotSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM Companies where Company_Id = xxx"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromHubspot",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hubspot input dataset name>",
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
                "type": "HubspotSource",
                "query": "SELECT * FROM Companies where Company_Id = xxx"
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

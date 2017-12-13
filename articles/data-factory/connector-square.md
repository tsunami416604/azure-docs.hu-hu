---
title: "Adatok másolása az Azure Data Factory (béta) használatával szögletes |} Microsoft Docs"
description: "Útmutató: adatok másolása szögletes támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
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
ms.openlocfilehash: 08b931c7c172578191cbc2628d8fabca0984a6e0
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-square-using-azure-data-factory-beta"></a>Adatok másolása az Azure Data Factory (béta) használatával szögletes

Ez a cikk a másolási tevékenység használható az Azure Data Factory adatokat másolni szögletes módját ismerteti. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Ez az összekötő jelenleg bétaverziójú. Próbálja ki, és küldjön visszajelzést. Ne használja éles környezetben.

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok bármely támogatott fogadó adattárolóhoz szögletes másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolódásának engedélyezése beépített illesztőprogramot tartalmaz, ezért nem szükséges manuálisan kell telepítenie minden olyan illesztőprogram ezt az összekötőt használja.

## <a name="getting-started"></a>Bevezetés

A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják való négyzetes összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Négyzetes társított szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **szögletes** | Igen |
| állomás | A négyszögletű példány URL-CÍMÉT. (azaz mystore.mysquare.com)  | Igen |
| clientId | A négyszögletű az alkalmazáshoz tartozó ügyfél-azonosító.  | Igen |
| clientSecret | A négyszögletű az alkalmazáshoz tartozó titkos ügyfélkulcsot. Ez a mező megjelölése a SecureString tárolja biztonságos helyen az ADF, vagy a jelszó tárolása az Azure Key Vault választhat, és lehetővé teszik a másolási tevékenység lekéréses ott adatmásolás végrehajtása során – további információhoz [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). | Igen |
| redirectUri | Az átirányítási URL-cím hozzárendelése az négyzetes irányítópult. (azaz http://localhost:2500)  | Igen |
| useEncryptedEndpoints | Meghatározza, hogy a data source végpontok titkosítása HTTPS használatával. Az alapértelmezett érték: igaz.  | Nem |
| useHostVerification | Meghatározza, hogy az a számítógép neve a egyeznie kell a gazdagép nevével a kiszolgáló SSL-en keresztüli kapcsolódás esetén a kiszolgáló tanúsítványát. Az alapértelmezett érték: igaz.  | Nem |
| usePeerVerification | Megadja, hogy a kiszolgáló identitásának ellenőrzése SSL-en keresztüli kapcsolódás esetén. Az alapértelmezett érték: igaz.  | Nem |

**Példa**

```json
{
    "name": "SquareLinkedService",
    "properties": {
        "type": "Square",
        "typeProperties": {
            "host" : "mystore.mysquare.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "redirectUri" : "http://localhost:2500"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a négyzetes dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása szögletes, az adatkészlet típus tulajdonságának beállítása **SquareObject**. Nincs ilyen típusú dataset további típusra vonatkozó tulajdonság.

**Példa**

```json
{
    "name": "SquareDataset",
    "properties": {
        "type": "SquareObject",
        "linkedServiceName": {
            "referenceName": "<Square linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a négyzetes forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="squaresource-as-source"></a>SquareSource forrásaként

Adatok másolása szögletes, állítsa be a forrás típusa a másolási tevékenység **SquareSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **SquareSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM Business"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSquare",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Square input dataset name>",
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
                "type": "SquareSource",
                "query": "SELECT * FROM Business"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).

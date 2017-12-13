---
title: "Adatok másolása az Azure Data Factory (béta) használata a ServiceNow |} Microsoft Docs"
description: "Útmutató: adatok másolása a ServiceNow támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
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
ms.openlocfilehash: 28ac55dd21e9496a9e7c480d984a518dd03e4c94
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-servicenow-using-azure-data-factory-beta"></a>Adatok másolása az Azure Data Factory (béta) használata a ServiceNow

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory adatokat másolni a ServiceNow. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Ez az összekötő jelenleg bétaverziójú. Próbálja ki, és küldjön visszajelzést. Ne használja éles környezetben.

## <a name="supported-capabilities"></a>Támogatott képességei

A ServiceNow adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolódásának engedélyezése beépített illesztőprogramot tartalmaz, ezért nem szükséges manuálisan kell telepítenie minden olyan illesztőprogram ezt az összekötőt használja.

## <a name="getting-started"></a>Bevezetés

A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják való ServiceNow összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok ServiceNow kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **ServiceNow** | Igen |
| végpont | A végpont a ServiceNow kiszolgáló. (Ez azt jelenti, hogy http://ServiceNowData.com)  | Igen |
| AuthenticationType | A használandó hitelesítési típus. <br/>Két érték engedélyezett: **alapvető**, **OAuth2** | Igen |
| felhasználónév | A ServiceNow kiszolgáló alapszintű és az OAuth2 hitelesítési való csatlakozáshoz használt felhasználónevet.  | Nem |
| jelszó | A jelszó, a felhasználónevet Basic és az OAuth2 hitelesítési megfelelő. Ez a mező megjelölése a SecureString tárolja biztonságos helyen az ADF, vagy a jelszó tárolása az Azure Key Vault választhat, és lehetővé teszik a másolási tevékenység lekéréses ott adatmásolás végrehajtása során – további információhoz [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). | Nem |
| clientId | Az OAuth2 hitelesítési ügyfél-azonosító.  | Nem |
| clientSecret | Az OAuth2 hitelesítési titkos ügyfélkódot. Ez a mező megjelölése a SecureString tárolja biztonságos helyen az ADF, vagy a jelszó tárolása az Azure Key Vault választhat, és lehetővé teszik a másolási tevékenység lekéréses ott adatmásolás végrehajtása során – további információhoz [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). | Nem |
| useEncryptedEndpoints | Meghatározza, hogy a data source végpontok titkosítása HTTPS használatával. Az alapértelmezett érték: igaz.  | Nem |
| useHostVerification | Meghatározza, hogy az a számítógép neve a egyeznie kell a gazdagép nevével a kiszolgáló SSL-en keresztüli kapcsolódás esetén a kiszolgáló tanúsítványát. Az alapértelmezett érték: igaz.  | Nem |
| usePeerVerification | Megadja, hogy a kiszolgáló identitásának ellenőrzése SSL-en keresztüli kapcsolódás esetén. Az alapértelmezett érték: igaz.  | Nem |

**Példa**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://ServiceNowData.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a ServiceNow dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása a ServiceNow, az adatkészlet típus tulajdonságának beállítása **ServiceNowObject**. Nincs ilyen típusú dataset további típusra vonatkozó tulajdonság.

**Példa**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a ServiceNow forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="servicenowsource-as-source"></a>ServiceNowSource forrásaként

Adatok másolása a ServiceNow, állítsa be a forrás típusa a másolási tevékenység **ServiceNowSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **ServiceNowSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM alm.asset"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM alm.asset"
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

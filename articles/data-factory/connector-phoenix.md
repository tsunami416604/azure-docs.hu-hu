---
title: Adatok másolása az Azure Data Factory használatával Phoenix |} Microsoft Docs
description: 'Útmutató: adatok másolása Phoenix támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jingwang
ms.openlocfilehash: 56559adbc2ebd4e4379326607a28333d538504da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Phoenix 

Ez a cikk a másolási tevékenység használható az Azure Data Factory adatokat másolni Phoenix módját ismerteti. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Phoenix adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolódásának engedélyezése beépített illesztőprogramot tartalmaz, ezért nem szükséges manuálisan kell telepítenie minden olyan illesztőprogram ezt az összekötőt használja.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

A következő szakaszok részletesen bemutatják való Phoenix összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok Phoenix kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Phoenix** | Igen |
| gazdagép | A kiszolgáló IP-címét vagy állomásnevét kiszolgálónevét a Phoenix. (Ez azt jelenti, hogy 192.168.222.160)  | Igen |
| port | A TCP-portot, amelyen a Phoenix kiszolgáló ügyfélkapcsolatokat. Az alapértelmezett érték: 8765.  | Nem |
| httpPath | A részleges URL-címet a Phoenix kiszolgáló megfelelő. (Ez azt jelenti, hogy /gateway/sandbox/phoenix/version). Az alapértelmezett érték `hbasephoenix` WindowsAzureHDInsightService használata.  | Nem |
| authenticationType | A a Phoenix kiszolgálóhoz való csatlakozáshoz használt hitelesítési módszert. <br/>Két érték engedélyezett: **névtelen**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Igen |
| felhasználónév | A Phoenix kiszolgálóhoz való csatlakozáshoz használt felhasználónevet.  | Nem |
| jelszó | A jelszó, a felhasználónév megfelelő. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Nem |
| enableSsl | Meghatározza, hogy a kapcsolat titkosítása SSL használatával. Az alapértelmezett értéke hamis.  | Nem |
| trustedCertPath | Megbízható Hitelesítésszolgáltatói tanúsítványok ellenőrzése a kiszolgáló SSL-en keresztül kapcsolódáskor tartalmazó .pem fájl teljes elérési útja. Ez a tulajdonság csak akkor állítható, önálló üzemeltetett infravörös SSL használatakor Az alapértelmezett érték a cacerts.pem fájlt az infravörös telepített:  | Nem |
| useSystemTrustStore | Megadja, hogy a rendszer megbízható áruházból vagy a megadott PEM-fájl egy Hitelesítésszolgáltatói tanúsítványt használjon-e. Az alapértelmezett értéke hamis.  | Nem |
| allowHostNameCNMismatch | Megadja, hogy egy hitelesítésszolgáltató által kiállított SSL tanúsítvány nevének egyeznie kell a gazdagép nevével a kiszolgáló SSL-en keresztül kapcsolódáskor-e. Az alapértelmezett értéke hamis.  | Nem |
| allowSelfSignedServerCert | Megadja, hogy engedélyezi a kiszolgáló önaláírt tanúsítványokat. Az alapértelmezett értéke hamis.  | Nem |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "httpPath" : "hbasephoenix",
            "authenticationType" : "WindowsAzureHDInsightService",
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Phoenix dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása Phoenix, az adatkészlet típus tulajdonságának beállítása **PhoenixObject**. Nincs ilyen típusú dataset további típusra vonatkozó tulajdonság.

**Példa**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Phoenix forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="phoenixsource-as-source"></a>PhoenixSource forrásaként

Adatok másolása Phoenix, állítsa be a forrás típusa a másolási tevékenység **PhoenixSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **PhoenixSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM MyTable"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
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
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).

---
title: Adatok másolása az Azure Data Factory használatával HBase |} Microsoft Docs
description: 'Útmutató: adatok másolása HBase támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával.'
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
ms.date: 02/28/2018
ms.author: jingwang
ms.openlocfilehash: ee4fe897bc8b0b1c969ea2fbfc1289d6a5bba7c2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-hbase-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával HBase 

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory adatokat másolni a HBase. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Támogatott képességei

A HBase adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolódásának engedélyezése beépített illesztőprogramot tartalmaz, ezért nem szükséges manuálisan kell telepítenie minden olyan illesztőprogram ezt az összekötőt használja.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

A következő szakaszok részletesen bemutatják megadhatók a Data Factory tartozó entitások HBase-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

HBase kapcsolódó szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **HBase** | Igen |
| gazdagép | A kiszolgáló IP-címét vagy állomásnevét kiszolgálónevét a HBase. (i.e. 192.168.222.160, [clustername].azurehdinsight.net)  | Igen |
| port | A TCP-portot, amely a HBase-példány használja a kapcsolatok figyelésére. Az alapértelmezett érték: 9090.  | Nem |
| httpPath | A részleges URL-címet a HBase-kiszolgáló megfelelő. (azaz /gateway/sandbox/hbase/version)  | Nem |
| authenticationType | A HBase-kiszolgálóhoz való csatlakozáshoz használandó hitelesítési módszert. <br/>Két érték engedélyezett: **névtelen**, **alapvető** | Igen |
| felhasználónév | A HBase-példányhoz való csatlakozáshoz használt felhasználónevet.  | Nem |
| jelszó | A jelszó, a felhasználónév megfelelő. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Nem |
| enableSsl | Meghatározza, hogy a kapcsolat titkosítása SSL használatával. Az alapértelmezett értéke hamis.  | Nem |
| trustedCertPath | Megbízható Hitelesítésszolgáltatói tanúsítványok ellenőrzése a kiszolgáló SSL-en keresztül kapcsolódáskor tartalmazó .pem fájl teljes elérési útja. Ez a tulajdonság csak akkor állítható, önálló üzemeltetett infravörös SSL használatakor Az alapértelmezett érték a cacerts.pem fájlt az infravörös telepített:  | Nem |
| allowHostNameCNMismatch | Megadja, hogy egy hitelesítésszolgáltató által kiállított SSL tanúsítvány nevének egyeznie kell a gazdagép nevével a kiszolgáló SSL-en keresztül kapcsolódáskor-e. Az alapértelmezett értéke hamis.  | Nem |
| allowSelfSignedServerCert | Megadja, hogy engedélyezi a kiszolgáló önaláírt tanúsítványokat. Az alapértelmezett értéke hamis.  | Nem |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa a HDInsights HBase:**

```json
{
    "name": "HBaseLinkedService",
    "properties": {
        "type": "HBase",
        "typeProperties": {
            "host" : "<cluster name>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "<e.g. hbaserest>",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "enableSsl" : true
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Általános HBase példa:**

```json
{
    "name": "HBaseLinkedService",
    "properties": {
        "type": "HBase",
        "typeProperties": {
            "host" : "<host e.g. 192.168.222.160>",
            "port" : "<port>",
            "httpPath" : "<e.g. /gateway/sandbox/hbase/version>",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "enableSsl" : true,
            "trustedCertPath" : "<trustedCertPath>",
            "allowHostNameCNMismatch" : true,
            "allowSelfSignedServerCert" : true
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a HBase dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása HBase, az adatkészlet típus tulajdonságának beállítása **HBaseObject**. Nincs ilyen típusú dataset további típusra vonatkozó tulajdonság.

**Példa**

```json
{
    "name": "HBaseDataset",
    "properties": {
        "type": "HBaseObject",
        "linkedServiceName": {
            "referenceName": "<HBase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a HBase forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="hbasesource-as-source"></a>HBaseSource forrásaként

Adatok másolása HBase, állítsa be a forrás típusa a másolási tevékenység **HBaseSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **HBaseSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM MyTable"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromHBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HBase input dataset name>",
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
                "type": "HBaseSource",
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

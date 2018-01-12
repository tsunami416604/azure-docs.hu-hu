---
title: "Adatok másolása az Azure Data Factory használatával Hive |} Microsoft Docs"
description: "Útmutató: adatok másolása Hive támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
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
ms.openlocfilehash: 0a1d8ff4b9821b0d41b6225872472a9d5aedffc7
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával struktúra 

Ez a cikk a másolási tevékenység használható az Azure Data Factory adatokat másolni Hive módját ismerteti. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Hive adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolódásának engedélyezése beépített illesztőprogramot tartalmaz, ezért nem szükséges manuálisan kell telepítenie minden olyan illesztőprogram ezt az összekötőt használja.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory tartozó entitások és Hive-összekötő tulajdonságait.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok Hive kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **struktúra** | Igen |
| gazdagép | Kiszolgáló IP-címét vagy állomásnevét kiszolgálónevét a Hive, elválasztva (;) több gazdagépek (csak ha serviceDiscoveryMode engedélyezése).  | Igen |
| port | A TCP-portot, amelyen a Hive kiszolgáló ügyfélkapcsolatokat.  | Nem |
| serverType típusa | A Hive kiszolgáló típusa. <br/>Két érték engedélyezett: **HiveServer1**, **hiveserver2-n**, **HiveThriftServer** | Nem |
| thriftTransportProtocol | Az átviteli protokoll a Thrift-rétegben használatára. <br/>Két érték engedélyezett: **bináris**, **SASL**, ** HTTP ** | Nem |
| AuthenticationType | A hitelesítési módszer a Hive-kiszolgálóhoz való hozzáféréshez. <br/>Két érték engedélyezett: **névtelen**, **felhasználónév**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Igen |
| serviceDiscoveryMode | jelzi, hogy a szolgáltatással ZooKeeper, hamis nem igaz.  | Nem |
| zooKeeperNameSpace | ZooKeeper alapján mely Hive Server 2 csomópontokat ad hozzá a névteret.  | Nem |
| useNativeQuery | Megadja, hogy az illesztőprogram natív HiveQL-lekérdezést használja-e, vagy egy egyenértékű űrlap HiveQL alakítja azokat.  | Nem |
| felhasználónév | A Hive-kiszolgáló eléréséhez használt felhasználónév.  | Nem |
| jelszó | A jelszót, amit a felhasználónév mezőben megadott felhasználónév megfelelő esetén dönthet úgy, hogy a SecureString tárolja biztonságos helyen az ADF, vagy az Azure Key Vault jelszó tárolására, és lehetővé teszik a másolási tevékenység lekéréses ott adatmásolás - végrehajtása során, a jelölőnégyzet bejelölésével vezetővonalak a további RN [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). | Nem |
| httpPath | A részleges URL-címet a Hive-kiszolgáló megfelelő.  | Nem |
| enableSsl | Meghatározza, hogy a kapcsolat titkosítása SSL használatával. Az alapértelmezett értéke hamis.  | Nem |
| trustedCertPath | Megbízható Hitelesítésszolgáltatói tanúsítványok ellenőrzése a kiszolgáló SSL-en keresztül kapcsolódáskor tartalmazó .pem fájl teljes elérési útja. Ez a tulajdonság csak akkor állítható, önálló üzemeltetett infravörös SSL használatakor Az alapértelmezett érték a cacerts.pem fájlt az infravörös telepített:  | Nem |
| useSystemTrustStore | Megadja, hogy a rendszer megbízható áruházból vagy a megadott PEM-fájl egy Hitelesítésszolgáltatói tanúsítványt használjon-e. Az alapértelmezett értéke hamis.  | Nem |
| allowHostNameCNMismatch | Megadja, hogy egy hitelesítésszolgáltató által kiállított SSL tanúsítvány nevének egyeznie kell a gazdagép nevével a kiszolgáló SSL-en keresztül kapcsolódáskor-e. Az alapértelmezett értéke hamis.  | Nem |
| allowSelfSignedServerCert | Megadja, hogy engedélyezi a kiszolgáló önaláírt tanúsítványokat. Az alapértelmezett értéke hamis.  | Nem |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "httpPath" : "gateway/sandbox/spark"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Hive dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása struktúra, az adatkészlet típus tulajdonságának beállítása **HiveObject**. Nincs ilyen típusú dataset további típusra vonatkozó tulajdonság.

**Példa**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Hive forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="hivesource-as-source"></a>HiveSource forrásaként

Adatok másolása struktúra, állítsa be a forrás típusa a másolási tevékenység **HiveSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **HiveSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM MyTable"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
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

---
title: Adatok másolása az Azure Data Factory használatával Hive |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Hive támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 120aed4277abfb2ea977670c107a4ee759bd3524
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009141"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Hive 

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol a Hive használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a kaptár-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás-mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Másolhat adatokat Hive bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolat beépített illesztőprogramot tartalmaz, ezért nem kell manuálisan telepítenie az összes illesztőprogram ezzel az összekötővel.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások meghatározott Hive-összekötőre-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Hive-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **Hive** | Igen |
| host | A kaptár-kiszolgáló IP-címe vagy állomásneve, amely több gazdagép esetében ";" karakterrel elválasztva (csak akkor, ha a serviceDiscoveryMode engedélyezve van).  | Igen |
| port | A TCP-port a Hive-kiszolgáló által az ügyfélkapcsolatok figyeléséhez. Ha csatlakozik az Azure Hdinsight, meg a 443-as portot. | Igen |
| serverType | Hive server típusa. <br/>Engedélyezett értékek a következők: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Nem |
| thriftTransportProtocol | Az átviteli protokoll használatára a Thrift-rétegben. <br/>Engedélyezett értékek a következők: **Bináris**, **SASL**, **http** | Nem |
| authenticationType | A Hive-kiszolgáló eléréséhez használt hitelesítési módszert. <br/>Engedélyezett értékek a következők: **Névtelen**, **Felhasználónév**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Igen |
| serviceDiscoveryMode | Igaz értéket a szolgáltatással ZooKeeper, false nem jelzi.  | Nem |
| zooKeeperNameSpace | A névtér a ZooKeeper, mely Hive Server 2 szerint csomópontokat ad hozzá.  | Nem |
| useNativeQuery | Megadja, hogy az illesztőprogram natív HiveQL-lekérdezéseket használ-e, vagy a HiveQL-ben egy egyenértékű űrlapra konvertálja őket.  | Nem |
| username | A Hive-kiszolgáló hozzáféréséhez használt felhasználónév.  | Nem |
| password | A jelszó a felhasználónak megfelelő. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| httpPath | A részleges tartozó URL-cím a Hive-kiszolgáló.  | Nem |
| enableSsl | Itt adhatja meg, e-kiszolgálóhoz a rendszer SSL használatával titkosítja. Az alapértelmezett értéke FALSE (hamis).  | Nem |
| trustedCertPath | A .pem-fájlt tartalmazó ellenőrzésének folyamatát a kiszolgálón, ha SSL-kapcsolaton keresztül kapcsolódik a megbízható Hitelesítésszolgáltatói tanúsítvány teljes elérési útja. Ez a tulajdonság csak akkor állítható, ha SSL-lel a saját üzemeltetésű Az alapértelmezett érték a telepített bemutathatja cacerts.pem fájlt:  | Nem |
| useSystemTrustStore | Megadja, hogy a Hitelesítésszolgáltatói tanúsítvány használatára, a rendszer megbízható áruházból vagy egy adott PEM-fájl. Az alapértelmezett értéke FALSE (hamis).  | Nem |
| allowHostNameCNMismatch | Meghatározza a kiszolgáló állomásneve megfelelően, ha SSL-kapcsolaton keresztül kapcsolódik egy hitelesítésszolgáltató által kiállított SSL-tanúsítvány neve kötelező legyen-e. Az alapértelmezett értéke FALSE (hamis).  | Nem |
| allowSelfSignedServerCert | Megadja, hogy, hogy a kiszolgáló önaláírt tanúsítványokat. Az alapértelmezett értéke FALSE (hamis).  | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Hive-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatokat másol a Hive, az adatkészlet, a type tulajdonság beállítása **HiveObject**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **HiveObject** | Igen |
| schema | A séma neve. |Nem (Ha a tevékenység forrása az "query" van megadva)  |
| table | A tábla neve. |Nem (Ha a tevékenység forrása az "query" van megadva)  |
| tableName | A tábla neve, beleértve a séma részét. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Az új számítási feladatokhoz `table`használja `schema` a és a elemet. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Hive-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="hivesource-as-source"></a>HiveSource forrásként

Adatok másolása a Hive, állítsa be a forrás típusaként a másolási tevékenység **HiveSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **HiveSource** | Igen |
| query | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa:**

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

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

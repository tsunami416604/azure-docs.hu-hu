---
title: Adatok másolása a Hive-ból az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat a Hive-ból a támogatott fogadó adattárak egy Azure Data Factory-folyamat másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 1854465c463d78999674080048207cfa0916da3b
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992164"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Adatok másolása a Hive-ból az Azure Data Factory használatával 

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása a Hive.This article ismerteti, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása a Hive.This article ismerteti, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása a Hiv A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Hive-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Adatokat másolhat a Hive bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Az Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot is ezzel az összekötővel.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Hive-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A hive-kapcsolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságnak a következő re van állítva: **Hive** | Igen |
| gazda | A Hive-kiszolgáló IP-címe vagy állomásneve, több állomás esetén elválasztva a ";" értékkel (csak akkor, ha a serviceDiscoveryMode engedélyezve van).  | Igen |
| port | A Hive-kiszolgáló által az ügyfélkapcsolatok figyelésére használt TCP-port. Ha csatlakozik az Azure HDInsights, adja meg a port 443. | Igen |
| serverType típusú | A Hive-kiszolgáló típusa. <br/>Megengedett értékek: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Nem |
| thriftTransportProtocol | A takarékossági rétegben használandó átviteli protokoll. <br/>Az engedélyezett értékek a következők: **Bináris**, **SASL**, **HTTP** | Nem |
| authenticationType | A Hive-kiszolgáló eléréséhez használt hitelesítési módszer. <br/>Engedélyezett értékek: **Névtelen**, **Felhasználónév**, **Felhasználónév és jelszó**, **WindowsAzureHDInsightService** | Igen |
| szolgáltatásDiscoveryMode | igaz, hogy jelezze a ZooKeeper szolgáltatás használatát, hamis nem.  | Nem |
| zooKeeperNameSpace | A ZooKeeper névtere, amely alá a Hive Server 2 csomópontok hozzáadódnak.  | Nem |
| useNativeQuery | Itt adható meg, hogy az illesztőprogram natív HiveQL-lekérdezéseket használ-e, vagy azokat a HiveQL-ben egyenértékű formává alakítja.Specifies whether the driver uses native HiveQL queries, or converts them into a equivalent form in HiveQL.  | Nem |
| felhasználónév | A Hive-kiszolgáló eléréséhez használt felhasználónév.  | Nem |
| jelszó | A felhasználónak megfelelő jelszó. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |
| httpPath | A Hive-kiszolgálónak megfelelő részleges URL-cím.  | Nem |
| enableSsl | Itt adható meg, hogy a kiszolgálóval létesített kapcsolatok titkosítva legyenek-e a TLS használatával. Az alapértelmezett érték a hamis.  | Nem |
| trustedCertPath | A megbízható hitelesítésszolgáltatói tanúsítványokat tartalmazó .pem fájl teljes elérési útja a kiszolgáló TLS-en való csatlakozáskor történő ellenőrzéséhez. Ez a tulajdonság csak akkor állítható be, ha a TLS-t saját üzemeltetésű infravörös rendszeren használja. Az alapértelmezett érték az infravörös kapcsolattal telepített cacerts.pem fájl.  | Nem |
| useSystemTrustStore | Itt adható meg, hogy a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból származó hitelesítésszolgáltatói tanúsítványt használjon.Specifies whether to use a ca certificate from the system trust store or from a specified PEM file. Az alapértelmezett érték a hamis.  | Nem |
| allowHostNameCNMismatch | Itt adható meg, hogy a Hitelesítésszolgáltató által kibocsátott TLS/SSL-tanúsítvány név megegyezzen-e a kiszolgáló állomásnevével, amikor TLS-en keresztül csatlakozik. Az alapértelmezett érték a hamis.  | Nem |
| allowSelfSignedServerCert | Itt adható meg, hogy engedélyezze-e az önaláírt tanúsítványokat a kiszolgálóról. Az alapértelmezett érték a hamis.  | Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a Hive-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Hive-ból, állítsa az adatkészlet típustulajdonságát **HiveObject (HiveObject)** tulajdonságra. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságának a következő re van állítva: **HiveObject** | Igen |
| Séma | A séma neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tableName | A sémarészt is magában foglaló tábla neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Az új számítási `schema` `table`feladatokhoz használja és használja. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Hive-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="hivesource-as-source"></a>HiveSource forrásként

Ha adatokat szeretne másolni a Hive-ból, állítsa be a forrástípust a másolási tevékenységben a **HiveSource elemre.** A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **HiveSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

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

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.

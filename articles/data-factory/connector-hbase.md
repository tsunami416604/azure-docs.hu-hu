---
title: Adatok másolása a HBase szolgáltatásból az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat a HBase-ről a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: f2d10a6150a6e6957b303ca391c97e166342111c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417249"
---
# <a name="copy-data-from-hbase-using-azure-data-factory"></a>Adatok másolása a HBase szolgáltatásból az Azure Data Factory használatával 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása a HBase.This article tagahow to use the Copy Activity in Azure Data Factory to copy data from HBase. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a HBase-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

A HBase-ből adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Az Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot is ezzel az összekötővel.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a HBase-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A HBase csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságnak a következőre kell állítania: **HBase** | Igen |
| gazda | A HBase-kiszolgáló IP-címe vagy állomásneve. (azaz  `[clustername].azurehdinsight.net`,) `192.168.222.160`  | Igen |
| port | A HBase-példány által az ügyfélkapcsolatok figyelésére használt TCP-port. Az alapértelmezett érték 9090. Ha csatlakozik az Azure HDInsights, adja meg a port 443. | Nem |
| httpPath | A HBase-kiszolgálónak megfelelő részleges URL-cím, például a HDInsights-fürt használata `/hbaserest0` kor. | Nem |
| authenticationType | A HBase-kiszolgálóhoz való csatlakozáshoz használandó hitelesítési mechanizmus. <br/>Az engedélyezett értékek a következők: **Névtelen**, **Alapszintű** | Igen |
| felhasználónév | A HBase-példányhoz való csatlakozáshoz használt felhasználónév.  | Nem |
| jelszó | A felhasználónévnek megfelelő jelszó. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |
| enableSsl | Itt adható meg, hogy a kiszolgálóval létesített kapcsolatok titkosítva legyenek-e a TLS használatával. Az alapértelmezett érték a hamis.  | Nem |
| trustedCertPath | A megbízható hitelesítésszolgáltatói tanúsítványokat tartalmazó .pem fájl teljes elérési útja a kiszolgáló TLS-en való csatlakozáskor történő ellenőrzéséhez. Ez a tulajdonság csak akkor állítható be, ha a TLS-t saját üzemeltetésű infravörös rendszeren használja. Az alapértelmezett érték az infravörös kapcsolattal telepített cacerts.pem fájl.  | Nem |
| allowHostNameCNMismatch | Itt adható meg, hogy a Hitelesítésszolgáltató által kibocsátott TLS/SSL-tanúsítvány név megegyezzen-e a kiszolgáló állomásnevével, amikor TLS-en keresztül csatlakozik. Az alapértelmezett érték a hamis.  | Nem |
| allowSelfSignedServerCert | Itt adható meg, hogy engedélyezze-e az önaláírt tanúsítványokat a kiszolgálóról. Az alapértelmezett érték a hamis.  | Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

>[!NOTE]
>Ha a fürt nem támogatja a ragadós munkamenetet, például a HDInsightot, explicit módon adjon hozzá `/hbaserest0` csomópontindexet a http elérési út beállításának végén, például adja meg a helyett. `/hbaserest`

**Példa a HDInsights HBase-re:**

```json
{
    "name": "HBaseLinkedService",
    "properties": {
        "type": "HBase",
        "typeProperties": {
            "host" : "<cluster name>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbaserest0",
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

**Példa az általános HBase-re:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a HBase adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a HBase alapterületről, állítsa az adatkészlet típustulajdonságát **HBaseObject**objektumra. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságának a következő re van állítva: **HBaseObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa**

```json
{
    "name": "HBaseDataset",
    "properties": {
        "type": "HBaseObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<HBase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a HBase-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="hbasesource-as-source"></a>HBaseSource forrásként

Ha adatokat szeretne másolni a HBase-ből, állítsa a forrástípust a másolási tevékenységben a **HBaseSource**mezőre. A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás típustulajdonságának a következőre kell állítani: **HBaseSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

**Példa:**

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


## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.

---
title: Adatok másolása az Impala szolgáltatásból az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat az Impala-ból a támogatott fogadó adattárakba egy adatfeldolgozó folyamat másolási tevékenységének használatával.
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
ms.openlocfilehash: f465fe4bb69bc5ae81db6c78df51bf5133de1b60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929307"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Adatok másolása az Impala szolgáltatásból az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása impala. A [Másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Impala összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az Impala-ból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként vagy fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában láthatja.

A Data Factory beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez. Ezért nem kell manuálisan telepítenie egy illesztőprogramot az összekötő használatához.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Impala-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak az Impala csatolt szolgáltatás.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **Impala**-ra kell állítani. | Igen |
| gazda | Az Impala kiszolgáló IP-címe vagy állomásneve (azaz 192.168.222.160).  | Igen |
| port | Az impala-kiszolgáló által az ügyfélkapcsolatok figyelésére használt TCP-port. Az alapértelmezett érték 21050.  | Nem |
| authenticationType | A használandó hitelesítési típus. <br/>Az engedélyezett **értékek: Névtelen**, **SASLUsername**és **UsernameAndPassword**. | Igen |
| felhasználónév | Az Impala kiszolgáló eléréséhez használt felhasználónév. A SASLUsername használatakor az alapértelmezett érték névtelen.  | Nem |
| jelszó | A felhasználónév és jelszó használatakor a felhasználónévnek megfelelő jelszó. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |
| enableSsl | Itt adható meg, hogy a kiszolgálóval létesített kapcsolatok SSL-rel titkosítva legyenek-e. Az alapértelmezett érték: **hamis**.  | Nem |
| trustedCertPath | Az SSL-en keresztüli csatlakozáskor a kiszolgáló ellenőrzésére használt megbízható hitelesítésszolgáltatói tanúsítványokat tartalmazó .pem fájl teljes elérési útja. Ez a tulajdonság csak akkor állítható be, ha ssl-t használ a saját üzemeltetésű integrációs futásidőben. Az alapértelmezett érték az integrációs futásidejűsel telepített cacerts.pem fájl.  | Nem |
| useSystemTrustStore | Itt adható meg, hogy a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból származó hitelesítésszolgáltatói tanúsítványt használjon.Specifies whether to use a ca certificate from the system trust store or from a specified PEM file. Az alapértelmezett érték: **hamis**.  | Nem |
| allowHostNameCNMismatch | Itt adható meg, hogy az SSL-kapcsolaton keresztül időképes csatlakozáskor meg kell-e követelni, hogy a hitelesítésszolgáltató által kiállított SSL-tanúsítvány neve megegyezzen a kiszolgáló állomásnevével. Az alapértelmezett érték: **hamis**.  | Nem |
| allowSelfSignedServerCert | Itt adható meg, hogy engedélyezze-e az önaláírt tanúsítványokat a kiszolgálóról. Az alapértelmezett érték: **hamis**.  | Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

**Példa:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. Ez a szakasz az Impala adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni az Impala programból, állítsa az adatkészlet típustulajdonságát **ImpalaObject (ImpalaObject )** tulajdonságra. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságának a következőre kell állítania: **ImpalaObject** | Igen |
| Séma | A séma neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Használja `schema` `table` és új munkaterheléshez. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az Impala forrástípus által támogatott tulajdonságok listáját tartalmazza.

### <a name="impala-as-a-source-type"></a>Impala forrástípusként

Ha adatokat szeretne másolni az Impala programból, állítsa be a forrástípust a másolási tevékenységben az **ImpalaSource mezőre.** A következő tulajdonságokat a másolási tevékenység **forrása** szakasz támogatja.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát **ImpalaSource**beállításra kell állítani. | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
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
A Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a Támogatott adattárak című témakörben [tetszését.](copy-activity-overview.md#supported-data-stores-and-formats)

---
title: Adatok másolása az Impala-ből Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatokat a Impala-ből a fogadó adattárakba egy másolási tevékenységgel egy adatfeldolgozó-folyamatban.
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929307"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Adatok másolása az Impala-ből Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Impala-ból való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Impala-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az Impala-ből bármilyen támogatott fogadó adattárba másolhat adatok. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez. Ezért nem kell manuálisan telepítenie az illesztőprogramot az összekötő használatához.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Az első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az Impala-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Impala társított szolgáltatás a következő tulajdonságokat támogatja.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot az **Impala**értékre kell beállítani. | Igen |
| gazdagép | Az Impala-kiszolgáló IP-címe vagy állomásneve (azaz 192.168.222.160).  | Igen |
| port | Az a TCP-port, amelyet az Impala-kiszolgáló az ügyfélkapcsolatok figyelésére használ. Az alapértelmezett érték a 21050.  | Nem |
| authenticationType | A használni kívánt hitelesítési típus. <br/>Az engedélyezett értékek a **Névtelen**, a **SASLUsername**és a **UsernameAndPassword**. | Igen |
| felhasználónév | Az Impala-kiszolgáló eléréséhez használt Felhasználónév. A SASLUsername használatakor az alapértelmezett érték a névtelen.  | Nem |
| jelszó | A UsernameAndPassword használatakor a felhasználónévnek megfelelő jelszó. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Nem |
| enableSsl | Megadja, hogy a kiszolgálóval létesített kapcsolatok SSL használatával legyenek titkosítva. Az alapértelmezett érték **false (hamis**).  | Nem |
| trustedCertPath | A. PEM fájl teljes elérési útja, amely a kiszolgáló SSL-kapcsolaton keresztüli hitelesítéséhez használt megbízható HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat tartalmazza. Ez a tulajdonság csak akkor állítható be, ha az SSL-t használja a saját üzemeltetésű Integration Runtime. Az alapértelmezett érték a hitesítésszolgáltatói. PEM fájl, amelyet az Integration Runtime telepített.  | Nem |
| useSystemTrustStore | Megadja, hogy a rendszer a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból kíván-e HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használni. Az alapértelmezett érték **false (hamis**).  | Nem |
| allowHostNameCNMismatch | Megadja, hogy szükséges-e a HITELESÍTÉSSZOLGÁLTATÓ által kiadott SSL-tanúsítvány neve ahhoz, hogy a kiszolgáló állomásneve egyezzen az SSL-kapcsolaton keresztül. Az alapértelmezett érték **false (hamis**).  | Nem |
| allowSelfSignedServerCert | Megadja, hogy engedélyezi-e az önaláírt tanúsítványokat a kiszolgálóról. Az alapértelmezett érték **false (hamis**).  | Nem |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

**Példa**

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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az Impala-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Impala-ból való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **ImpalaObject**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **ImpalaObject** | Igen |
| séma | A séma neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| table | A tábla neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Új számítási feladatokhoz használjon `schema` és `table`. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az Impala-forrás típusa által támogatott tulajdonságok listáját tartalmazza.

### <a name="impala-as-a-source-type"></a>Impala forrás típusaként

Az adatok Impala-ből való másolásához állítsa a forrás típusát a másolás tevékenység **ImpalaSource**. A másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát **ImpalaSource**értékre kell állítani. | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM MyTable"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

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

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések
A Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

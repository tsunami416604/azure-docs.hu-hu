---
title: Adatok másolása SAP HANAból Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatok SAP HANAból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 8174b2e8bc63db8954e596d831eb2f9cad2ba440
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211618"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Adatok másolása SAP HANA használatával Azure Data Factory
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-sap-hana-connector.md)
> * [Aktuális verzió](connector-sap-hana.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok SAP HANA-adatbázisból történő másolásához. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

>[!TIP]
>Az ADF SAP-adatintegrációs forgatókönyvre vonatkozó általános támogatásának megismeréséhez tekintse meg az [SAP-Adatintegráció Azure Data Factory tanulmány használatával](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) részletes bevezetést, comparsion és útmutatást.

## <a name="supported-capabilities"></a>Támogatott képességek

Az adatok másolása SAP HANA adatbázisból bármely támogatott fogadó adattárba. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Pontosabban, ez az SAP HANA-összekötő a következőket támogatja:

- Adatok másolása SAP HANA adatbázis bármely verziójáról.
- Adatok másolása a **HANA-információs modellből** (például analitikai és számítási nézetből) és **sor/oszlop táblákból**.
- Adatok másolása az alapszintű vagy a **Windows** -hitelesítéssel.

> [!TIP]
> Az Adatmásolás SAP HANA adattárba való másolásához használja az általános ODBC-összekötőt. További részletek: [SAP HANA](connector-odbc.md#sap-hana-sink) fogadó. Figyelje meg, hogy SAP HANA összekötőhöz társított szolgáltatások és az ODBC-összekötő eltérő típusú, ezért nem használható fel újra.

## <a name="prerequisites"></a>Előfeltételek

A SAP HANA-összekötő használatához a következőket kell tennie:

- Saját üzemeltetésű Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.
- Telepítse a SAP HANA ODBC-illesztőt a Integration Runtime gépen. Az SAP HANA ODBC-illesztőprogramot az [SAP Software Download Center](https://support.sap.com/swdc)webhelyről töltheti le. Keressen rá a **Windows-ügyfél kulcsszava SAP HANA**.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek Data Factory SAP HANA-összekötőhöz tartozó entitások definiálásához használatosak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

SAP HANA társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **SapHana** | Igen |
| connectionString | Adjon meg olyan információt, amely az alapszintű **hitelesítés** vagy a **Windows-hitelesítés**használatával a SAP HANAhoz való kapcsolódáshoz szükséges. Tekintse át a következő mintákat.<br>A kapcsolódási karakterláncban a kiszolgáló/port kötelező (az alapértelmezett port 30015), és a Felhasználónév és a jelszó megadása kötelező, ha alapszintű hitelesítést használ. További speciális beállításokért lásd: [SAP HANA ODBC-kapcsolatok tulajdonságai](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>A jelszót a Azure Key Vaultban is elhelyezheti, és lekérheti a jelszó konfigurációját a kapcsolatok karakterláncán kívül. További részletekért tekintse meg a [hitelesítő adatok tárolása Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Igen |
| userName | Windows-hitelesítés használata esetén adja meg a felhasználónevet. Például: `user@domain.com` | Nem |
| password | A felhasználói fiók jelszavának megadása. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. A saját üzemeltetésű Integration Runtime az [Előfeltételek](#prerequisites)szakaszban említettek szerint kell megadni. |Igen |

**Példa: egyszerű hitelesítés használata**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: Windows-hitelesítés használata**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Ha SAP HANA társított szolgáltatást használta a következő hasznos adattartalommal, akkor továbbra is támogatott, miközben a rendszer azt javasolja, hogy az új továbbítást használja.

**Példa:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a SAP HANA adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok SAP HANAból történő másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **SapHanaTable** | Igen |
| schema | A séma neve a SAP HANA adatbázisban. | Nem (Ha a tevékenység forrása az "query" van megadva) |
| table | A SAP HANA adatbázisban található tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Ha a beírt adatkészletet használta `RelationalTable` , a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a SAP HANA forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-hana-as-source"></a>SAP HANA forrásként

Az adatok SAP HANAból történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **SapHanaSource** | Igen |
| query | Azt az SQL-lekérdezést adja meg, amely az SAP HANA-példányból olvassa be az adatok beolvasását. | Igen |
| packetSize | Meghatározza a hálózati csomagok méretét (kilobájtban), hogy több blokkra ossza fel az adatmennyiséget. Ha nagy mennyiségű adattal rendelkezik a másoláshoz, a csomagok méretének növelése a legtöbb esetben növelheti SAP HANA olvasási sebességét. A csomagok méretének beállításakor a teljesítmény tesztelése javasolt. | Nem.<br>Az alapértelmezett érték 2048 (2MB). |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ha a beírt másolási forrást használta `RelationalSource` , a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA adattípusának leképezése

Az adatok SAP HANAból való másolása során a rendszer a következő leképezéseket használja SAP HANA adattípusokból Azure Data Factory köztes adattípusokra. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| SAP HANA adattípus | Data factory közbenső adattípus |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| DATE               | DateTime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| REAL               | Single                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

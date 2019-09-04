---
title: Adatok másolása ODBC-forrásokból Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat OData-forrásokból származó adatokból támogatott fogadó adattárakat egy Azure Data Factory folyamat másolási tevékenységének használatával.
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
ms.openlocfilehash: a20a901d5fde251fdc1a044795615acdc1d61c5b
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277630"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Adatok másolása és átmásolása az ODBC-adattárakba Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-odbc-connector.md)
> * [Aktuális verzió](connector-odbc.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok és az ODBC-adattárba való másolásához. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Az ODBC-forrásból származó adatok bármely támogatott fogadó adattárba másolhatók, vagy bármely támogatott forrásból származó adattárból másolhatók az ODBC-tárolóba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Ez az ODBC-összekötő az **alapszintű** vagy a **Névtelen** hitelesítés használatával támogatja az adatok másolását **bármely ODBC-kompatibilis adattárba** .

## <a name="prerequisites"></a>Előfeltételek

Az ODBC-összekötő használatához a következőket kell tennie:

- Saját üzemeltetésű Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.
- Telepítse az adattárakhoz tartozó ODBC-illesztőt a Integration Runtime gépen.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az ODBC-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az ODBC társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **Odbc** | Igen |
| connectionString | A hitelesítő adatok kivételével a kapcsolatok karakterlánca. Megadhatja a kapcsolati karakterláncot, `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`mint például a `"DSN=<name of the DSN on IR machine>;"` (z), vagy használhatja a Integration Runtime gépen beállított rendszeradatforrás-nevet (az adatforrás nevét) a következővel: (a hitelesítő adatoknak a társított szolgáltatásban való megadása ennek megfelelően történik)<br>Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md).| Igen |
| authenticationType | Az ODBC-adattárhoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Engedélyezett értékek a következők: **Alapszintű** és **Névtelen**. | Igen |
| userName | Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. | Nem |
| password | Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| credential | Az illesztőprogram-specifikus tulajdonság-érték formátumban megadott kapcsolati karakterlánc hozzáférési hitelesítő része. Példa: `"RefreshToken=<secret refresh token>;"`. A mező megjelölése SecureString. | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. A saját üzemeltetésű Integration Runtime az [Előfeltételek](#prerequisites)szakaszban említettek szerint kell megadni. |Igen |

**1. példa: egyszerű hitelesítés használata**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
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

**2. példa: Névtelen hitelesítés használata**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az ODBC-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok ODBC-kompatibilis adattárba való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **OdbcTable** | Igen |
| tableName | Az ODBC-adattárban található tábla neve. | Nem forrásként (ha a "Query" a tevékenység forrásában van megadva);<br/>Igen a fogadó számára |

**Példa**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Ha a beírt adatkészletet használta `RelationalTable` , a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az ODBC-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="odbc-as-source"></a>ODBC forrásként

Az adatok ODBC-kompatibilis adattárból történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **OdbcSource** | Igen |
| query | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ha a beírt forrást használta `RelationalSource` , a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

### <a name="odbc-as-sink"></a>ODBC mint fogadó

Az Adatmásolás ODBC-kompatibilis adattárba való másolásához a másolási tevékenységben állítsa be a fogadó típusát a **OdbcSink**értékre. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát a következőre kell beállítani: **OdbcSink** | Igen |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt.<br/>Az engedélyezett értékek a következők: TimeSpan. Példa: "00:30:00" (30 perc). |Nem |
| writeBatchSize |Beilleszti az adatmennyiséget az SQL-táblába, ha a puffer mérete eléri a writeBatchSize.<br/>Az engedélyezett értékek: Integer (sorok száma). |Nem (alapértelmezett érték: 0 – automatikusan észlelve) |
| preCopyScript |Adja meg az SQL-lekérdezést a másolási tevékenység végrehajtásához, mielőtt az egyes futtatásokban az adattárba írja az adattárban. Ezt a tulajdonságot használhatja az előre betöltött adatértékek törléséhez. |Nem |

> [!NOTE]
> Ha nincs beállítva a "writeBatchSize" (automatikusan észlelt) beállítás, a másolási tevékenység először észleli, hogy az illesztőprogram támogatja-e a Batch-műveleteket, majd állítsa 10000-re, ha nem. Ha a 0 érték helyett explicit módon állítja be a értéket, a másolási tevékenység kiértékeli az értéket, és futás közben meghiúsul, ha az illesztőprogram nem támogatja a Batch-műveleteket.

**Példa:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="sap-hana-sink"></a>SAP HANA fogadó

>[!NOTE]
>Az adatok SAP HANA adattárból történő másolásához tekintse meg a natív [SAP HANA-összekötőt](connector-sap-hana.md). Az adatSAP HANAba való másolásához kövesse ezt az utasítást az ODBC-összekötő használatához. Figyelje meg, hogy SAP HANA összekötőhöz társított szolgáltatások és az ODBC-összekötő eltérő típusú, ezért nem használható fel újra.
>

Az általános ODBC-összekötő használatával másolhatja az adatSAP HANA-adatbázisba.

Saját üzemeltetésű Integration Runtime beállítása egy olyan gépen, amely hozzáféréssel rendelkezik az adattárhoz. A Integration Runtime a SAP HANA ODBC-illesztőjét használja az adattárhoz való kapcsolódáshoz. Ezért telepítse az illesztőprogramot, ha még nincs telepítve ugyanarra a gépre. Részletekért lásd az [Előfeltételek](#prerequisites) című szakaszt.

A SAP HANA fogadó Data Factory megoldásban való használata előtt ellenőrizze, hogy a Integration Runtime tud-e csatlakozni az adattárhoz a [kapcsolódási problémák elhárítása](#troubleshoot-connectivity-issues) szakasz útmutatásai alapján.

Hozzon létre egy ODBC-társított szolgáltatást egy SAP HANA adattár egy Azure-beli adattárolóhoz való összekapcsolásához az alábbi példában látható módon:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
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

A másolási művelet során olvassa el a következő cikket: az ODBC-adattárak forrás/fogadó adattárként való használatának részletes áttekintése.

## <a name="troubleshoot-connectivity-issues"></a>Kapcsolódási problémák elhárítása

A kapcsolódási problémák elhárításához használja **Integration Runtime Configuration Manager** **diagnosztika** lapját.

1. **Integration Runtime Configuration Manager**elindítása.
2. Váltson a **diagnosztika** lapra.
3. A "kapcsolat tesztelése" szakaszban válassza ki az adattár **típusát** (társított szolgáltatás).
4. Adja meg az adattárhoz való kapcsolódáshoz használt **kapcsolati karakterláncot** , válassza ki a **hitelesítést** , és írja be a **felhasználónevet**, a **jelszót**és/vagy a **hitelesítő adatokat**.
5. Kattintson a **Kapcsolódás tesztelése** elemre az adattárhoz való kapcsolódás teszteléséhez.

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

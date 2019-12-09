---
title: Adatok másolása ODBC-forrásokból Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat OData-forrásokból származó adatokból támogatott fogadó adattárakat egy Azure Data Factory folyamat másolási tevékenységének használatával.
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
ms.openlocfilehash: b94dbb81b2ab5b7e4421357ee81d6c3ea8e8d3c0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912490"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Adatok másolása és átmásolása az ODBC-adattárakba Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-odbc-connector.md)
> * [Aktuális verzió](connector-odbc.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok és az ODBC-adattárba való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az ODBC-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az ODBC-forrásból származó adatok bármely támogatott fogadó adattárba másolhatók, vagy bármely támogatott forrásból származó adattárból másolhatók az ODBC-tárolóba. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez az ODBC-összekötő az **alapszintű** vagy a **Névtelen** hitelesítés használatával támogatja az adatok másolását **bármely ODBC-kompatibilis adattárba** .

## <a name="prerequisites"></a>Előfeltételek

Az ODBC-összekötő használatához a következőket kell tennie:

- Saját üzemeltetésű Integration Runtime beállítása. További részletekért tekintse meg a saját üzemeltetésű [Integration Runtime](create-self-hosted-integration-runtime.md) szóló cikket.
- Telepítse az adattárakhoz tartozó ODBC-illesztőt a Integration Runtime gépen.

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az ODBC-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az ODBC társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **ODBC** | Igen |
| connectionString | A hitelesítő adatok kivételével a kapcsolatok karakterlánca. Megadhatja a kapcsolati karakterláncot, mint például a `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, vagy használhatja a Integration Runtime gépen beállított rendszeradatforrás-nevet (adatforrás neve) a `"DSN=<name of the DSN on IR machine>;"` használatával (a társított szolgáltatásban még mindig meg kell adnia a hitelesítő adatok részleteit).<br>Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md).| Igen |
| authenticationType | Az ODBC-adattárhoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Az engedélyezett értékek a következők: **Alapszintű** és **Névtelen**. | Igen |
| userName (Felhasználónév) | Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. | Nem |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Nem |
| hitelesítő adat | Az illesztőprogram-specifikus tulajdonság-érték formátumban megadott kapcsolati karakterlánc hozzáférési hitelesítő része. Példa: `"RefreshToken=<secret refresh token>;"`. A mező megjelölése SecureString. | Nem |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . A saját üzemeltetésű Integration Runtime az [Előfeltételek](#prerequisites)szakaszban említettek szerint kell megadni. |Igen |

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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az ODBC-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

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

Ha `RelationalTable` gépelt adatkészletet használ, a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az ODBC-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="odbc-as-source"></a>ODBC forrásként

Az adatok ODBC-kompatibilis adattárból történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **OdbcSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM MyTable"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

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

Ha `RelationalSource` gépelt forrást használ, a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

### <a name="odbc-as-sink"></a>ODBC mint fogadó

Az Adatmásolás ODBC-kompatibilis adattárba való másolásához a másolási tevékenységben állítsa be a fogadó típusát a **OdbcSink**értékre. A másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát a következőre kell beállítani: **OdbcSink** | Igen |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt.<br/>Az engedélyezett értékek a következők: TimeSpan. Például: "00:30:00" (30 perc). |Nem |
| writeBatchSize |Beilleszti az adatmennyiséget az SQL-táblába, ha a puffer mérete eléri a writeBatchSize.<br/>Az engedélyezett értékek: Integer (sorok száma). |Nem (alapértelmezett érték: 0 – automatikusan észlelve) |
| preCopyScript |Adja meg az SQL-lekérdezést a másolási tevékenység végrehajtásához, mielőtt az egyes futtatásokban az adattárba írja az adattárban. Ezt a tulajdonságot használhatja az előre betöltött adatértékek törléséhez. |Nem |

> [!NOTE]
> Ha nincs beállítva a "writeBatchSize" (automatikusan észlelt) beállítás, a másolási tevékenység először észleli, hogy az illesztőprogram támogatja-e a Batch-műveleteket, majd állítsa 10000-re, ha nem. Ha a 0 érték helyett explicit módon állítja be a értéket, a másolási tevékenység kiértékeli az értéket, és futás közben meghiúsul, ha az illesztőprogram nem támogatja a Batch-műveleteket.

**Példa**

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

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="troubleshoot-connectivity-issues"></a>Kapcsolódási problémák elhárítása

A kapcsolódási problémák elhárításához használja **Integration Runtime Configuration Manager** **diagnosztika** lapját.

1. **Integration Runtime Configuration Manager**elindítása.
2. Váltson a **diagnosztika** lapra.
3. A "kapcsolat tesztelése" szakaszban válassza ki az adattár **típusát** (társított szolgáltatás).
4. Adja meg az adattárhoz való kapcsolódáshoz használt **kapcsolati karakterláncot** , válassza ki a **hitelesítést** , és írja be a **felhasználónevet**, a **jelszót**és/vagy a **hitelesítő adatokat**.
5. Kattintson a **Kapcsolódás tesztelése** elemre az adattárhoz való kapcsolódás teszteléséhez.

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

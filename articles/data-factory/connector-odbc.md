---
title: Adatok másolása ODBC-forrásokból az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat az OData-forrásokból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: da5c53f8953960c382070be658add2877fff3f8c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416900"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Adatok másolása az ODBC-adattárakból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-odbc-connector.md)
> * [Jelenlegi verzió](connector-odbc.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása, és egy ODBC adattárba. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az ODBC-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

Az ODBC-forrásból adatokat másolhat bármely támogatott fogadó adattárba, vagy bármely támogatott forrásadattárból az ODBC-fogadóba másolhat. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Pontosabban ez az ODBC-összekötő támogatja az adatok **másolását/át bármely ODBC-kompatibilis adattárak** **alapszintű** vagy **névtelen** hitelesítéssel. **64 bites ODBC illesztőprogram** szükséges.

## <a name="prerequisites"></a>Előfeltételek

Az ODBC-csatlakozó használatához a következőket kell használnia:

- Állítsa be a saját üzemeltetésű integrációs futásidejű. A [részleteket a saját üzemeltetésű integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben találja.
- Telepítse a 64 bites ODBC illesztőprogramot az adattárhoz az integrációs futásidejű gépen.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az ODBC-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az ODBC-hez csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságnak a következőre kell állítania: **Odbc** | Igen |
| connectionString (kapcsolati karakterlánc) | A hitelesítő adatok at nem tartalmazó kapcsolati karakterlánc. Megadhatja a kapcsolati karakterláncot a mintához hasonló `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`mintával, vagy használhatja az integrációs `"DSN=<name of the DSN on IR machine>;"` futásidejű gépen beállított rendszeradatforrás-nevet (adatforrásnév) (ennek megfelelően továbbra is meg kell adnia a hitelesítő adatok egy részét a csatolt szolgáltatásban).<br>Az Azure Key Vaultban is elhelyezhet `password` egy jelszót, és kihúzhatja a konfigurációt a kapcsolati karakterláncból.További részleteket az Azure Key  [Vault áruházi hitelesítő adataiban](store-credentials-in-key-vault.md)talál.| Igen |
| authenticationType | Az ODBC-adattárhoz való csatlakozáshoz használt hitelesítés típusa.<br/>Az engedélyezett értékek a következők: **Alap és** **Névtelen**. | Igen |
| userName (Felhasználónév) | Adja meg a felhasználónevet, ha alapfokú hitelesítést használ. | Nem |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |
| hitelesítő adat | Az illesztőprogram-specifikus tulajdonságérték-formátumban megadott kapcsolati karakterlánc hozzáférési hitelesítő adatrésze. Példa: `"RefreshToken=<secret refresh token>;"`. Jelölje meg ezt a mezőt SecureString ként. | Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Az előfeltételek ben említettek szerint saját üzemeltetésű [integrációs](#prerequisites)futásidejű re van szükség. |Igen |

**1. példa: alapfokú hitelesítés használata**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
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

**2. példa: névtelen hitelesítés használata**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz az ODBC adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az ODBC-kompatibilis adattárból/ODBC-kompatibilis adattárba történő másoláshoz a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **OdbcTable** | Igen |
| tableName | A tábla neve az ODBC adattárban. | Nem a forráshoz (ha a tevékenységforrásban meg van adva a "lekérdezés" );<br/>Igen a mosogató |

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

Ha gépelt adatkészletet használt, `RelationalTable` továbbra is támogatott, amíg a rendszer az újat a jövőben használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az ODBC-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="odbc-as-source"></a>ODBC mint forrás

Az ODBC-kompatibilis adattárból történő adatok másolásához a másolási tevékenység **forrásszakaszában** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **OdbcSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

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

Ha gépelt forrást használt, `RelationalSource` akkor továbbra is támogatott, amíg az újat a jövőben is használhatja.

### <a name="odbc-as-sink"></a>ODBC mint mosogató

Ha adatokat szeretne másolni az ODBC-kompatibilis adattárba, állítsa a fogadó típusát a másolási tevékenységben **OdbcSink**.to copy the copy A másolási tevékenység **fogadója** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának típustulajdonságát a következőre kell állítani: **OdbcSink** | Igen |
| writeBatchTimeout |Várjon időt a kötegbehelyezési művelet befejezésére, mielőtt az időtúljárna.<br/>Az engedélyezett értékek a következők: timespan. Példa: "00:30:00" (30 perc). |Nem |
| writeBatchSize |Adatokat szúr be az SQL-táblába, amikor a puffer mérete eléri a WriteBatchSize-ot.<br/>Az engedélyezett értékek a következők: egész szám (sorok száma). |Nem (az alapértelmezett érték 0 - automatikus észlelés) |
| preCopyScript |Adjon meg egy SQL-lekérdezést a Másolási tevékenység végrehajtásához, mielőtt minden futtatásban adatokat írna az adattárba. Ezzel a tulajdonsággal megtisztíthatja az előre betöltött adatokat. |Nem |

> [!NOTE]
> A "writeBatchSize", ha nincs beállítva (automatikusan észlelt), másolási tevékenység először észleli, hogy az illesztőprogram támogatja a kötegelt műveleteket, és állítsa be 10000, ha igen, vagy állítsa be 1, ha nem. Ha explicit módon 0-tól eltérő értéket állít be, a másolási tevékenység tiszteletben tartja az értéket, és futásidőben sikertelen lesz, ha az illesztőprogram nem támogatja a kötegelt műveleteket.

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

## <a name="sap-hana-sink"></a>SAP HANA-fogadó

>[!NOTE]
>Adatok másolása az SAP HANA-adattárból, olvassa el a natív [SAP HANA-összekötő.](connector-sap-hana.md) Adatok másolásához az SAP HANA, kövesse ezt az utasítást az ODBC-összekötő használatához. Vegye figyelembe, hogy az SAP HANA-összekötő és az ODBC-összekötő csatolt szolgáltatásai eltérő típusúak, így nem használható fel újra.
>

Adatokmásolása az SAP HANA-adatbázisba az általános ODBC-összekötő használatával.

Állítsa be a saját üzemeltetésű integrációs futásidejű egy gépen az adattárhoz való hozzáféréssel. Az integrációs futásidejű az SAP HANA ODBC-illesztőprogramját használja az adattárhoz való csatlakozáshoz. Ezért telepítse az illesztőprogramot, ha még nincs telepítve ugyanarra a gépre. A részleteket lásd [az Előfeltételek](#prerequisites) című szakaszban.

Mielőtt az SAP HANA-fogadó t használna egy Data Factory-megoldásban, ellenőrizze, hogy az integrációs futásidő csatlakozhat-e az adattárhoz a [Kapcsolódási problémák elhárítása](#troubleshoot-connectivity-issues) című témakör utasításainak használatával.

Hozzon létre egy ODBC-hez csatolt szolgáltatást, amely összeköti az SAP HANA adattalapot egy Azure-adat-előállítóval, ahogy az a következő példában látható:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
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

Olvassa el a cikket az elejétől a részletes áttekintést az ODBC-adattárak forrás/fogadó adattárakként egy másolási műveletben.

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="troubleshoot-connectivity-issues"></a>Kapcsolódási problémák elhárítása

A csatlakozási problémák elhárításához használja az **Integrációs futásidejű konfigurációkezelő** **Diagnosztika lapját.**

1. Indítsa el **az Integrációs futásidejű konfigurációkezelőt**.
2. Váltson a **Diagnosztika** lapra.
3. A "Kapcsolat tesztelése" szakaszban válassza ki az adattár **típusát** (csatolt szolgáltatás).
4. Adja meg az adattárhoz való csatlakozáshoz használt **kapcsolati karakterláncot,** válassza ki a **hitelesítést,** és adja meg a **felhasználónevet,** **a jelszót**és/vagy **a hitelesítő adatokat.**
5. Kattintson a **Kapcsolat tesztelése** elemre az adattárhoz való csatlakozás teszteléséhez.

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.

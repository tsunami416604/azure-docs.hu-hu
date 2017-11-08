---
title: "Adatok másolása az Azure Data Factory használatával ODBC adatforrások |} Microsoft Docs"
description: "Ismerje meg az adatok másolása az OData forrásból származó támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
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
ms.date: 10/19/2017
ms.author: jingwang
ms.openlocfilehash: d8fa78585842a7e4414c8decf422c971938b683f
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Adatok másolása az és ODBC adattárolókhoz Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-odbc-connector.md)
> * [2. verzió – Előzetes verzió](connector-odbc.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory másolja az adatokat, a kezdő és egy ODBC-adattároló a. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [ODBC-összekötőt a V1](v1/data-factory-odata-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

ODBC-adatforrás adatainak másolása bármely támogatott fogadó adattár, vagy másolja a támogatott forráshierarchiából adattárolóból ODBC fogadó. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, az ODBC-összekötő támogatja az adatok másolását vagy **bármely ODBC-kompatibilis adattárolókhoz** használatával **alapvető** vagy **névtelen** hitelesítés.

## <a name="prerequisites"></a>Előfeltételek

Az ODBC-összekötő használatához meg kell:

- Állítson be egy Self-hosted integrációs futásidejű. Lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben alább.
- Az ODBC-illesztőprogram az adattároló telepítse az integrációs futásidejű számítógépen.

## <a name="getting-started"></a>Bevezetés
A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják megadhatók a Data Factory tartozó entitások ODBC-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

ODBC kapcsolódó szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Odbc** | Igen |
| connectionString | A kapcsolati karakterlánc credential rész. A kapcsolati karakterlánc megadhatja például a mintával `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, vagy használja a rendszer DSN (adatforrás neve), a integrációs futásidejű gépen beállította `"DSN=<name of the DSN on IR machine>;"` (kell továbbra is megadhatja a hitelesítő adatok részében hivatkozott szolgáltatásban található ennek megfelelően).| Igen |
| AuthenticationType | Az ODBC-adattár eléréséhez használt hitelesítés típusa.<br/>Két érték engedélyezett: **alapvető** és **névtelen**. | Igen |
| Felhasználónév | Ha egyszerű hitelesítést használ, adja meg a felhasználónevet. | Nem |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése a SecureString. | Nem |
| hitelesítő adatok | A hozzáférési hitelesítő adatok része illesztőprogram-specifikus tulajdonság-érték formátumban megadott kapcsolódási karakterlánc. Példa: `"RefreshToken=<secret refresh token>;"`. Ez a mező megjelölése a SecureString. | Nem |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Egy Self-hosted integrációs futásidejű szükség, ahogyan az [Előfeltételek](#prerequisites). |Igen |

**1. példa: egyszerű hitelesítést használ.**

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

**2. példa: a névtelen hitelesítés használatával**

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör ODBC dataset által támogatott tulajdonságokról.

Adatok másolása vagy ODBC-kompatibilis adattár, az adatkészlet típus tulajdonságának beállítása **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **RelationalTable** | Igen |
| tableName | Az ODBC-tárolóban a tábla neve. | Nincs forrás (Ha a tevékenység forrása "lekérdezést" meg van adva);<br/>A fogadó Igen |

**Példa**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "RelationalTable",
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

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az ODBC-adatforrás által támogatott tulajdonságokról.

### <a name="odbc-as-source"></a>ODBC forrásaként

Adatok másolása az ODBC-kompatibilis adattár, állítsa be a forrás típusa a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="odbc-as-sink"></a>Az ODBC fogadó

Adatok másolása az ODBC-kompatibilis adattár, állítsa be a fogadó típusa a másolási tevékenység **OdbcSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **OdbcSink** | Igen |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot.<br/>Két érték engedélyezett: timespan. Példa: "00: 30:00" (30 perc). |Nem |
| WriteBatchSize |Szúr be az SQL-tábla adatokat, amikor a puffer mérete eléri writeBatchSize.<br/>Két érték engedélyezett: egész szám (sorok száma). |Nem (alapértelmezett érték a 0 – automatikus észlelt) |
| preCopyScript |Adja meg a másolási tevékenység végrehajtása előtt minden egyes futtatásához adattárba adatok írása az SQL-lekérdezést. Ez a tulajdonság segítségével törölje az előre betöltött adatokat. |Nem |

> [!NOTE]
> "WriteBatchSize", a nincs beállítva (automatikusan észleli), ha másolási tevékenység során először észleli, hogy az illesztőprogram támogatja a kötegelt műveleteket, és állítsa az értékét 10000, ellenkező esetben vagy állítsa 1 értékűre, ha nem. Ha explicit módon az érték 0-tól eltérő, a másolási tevékenység eleget tegyen az érték, és futásidőben sikertelen lesz, ha az illesztőprogram nem támogatja a kötegelt műveleteket.

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

## <a name="ibm-informix-source"></a>IBM Informix-forrás

Az általános ODBC-összekötővel IBM Informix-adatbázishoz adatainak másolhatja.

Állítson be egy Self-hosted integrációs futásidejű az adattárolóhoz hozzáféréssel rendelkező gépen. Az integrációs futásidejű az ODBC-illesztő Informix az adattároló való kapcsolódáshoz használ. Ezért telepítenie az illesztőprogramot, ha még nincs telepítve ugyanazon a számítógépen. Például az illesztőprogram "IBM INFORMIX ODBC-illesztőprogram (64 bites) is használhatja. Lásd: [Előfeltételek](#prerequisites) című szakaszban talál információt.

A Data Factory-megoldásban Informix forrás használatához ellenőrizze, hogy az integrációs futásidejű az adattár szereplő utasításokat követve csatlakozhat [csatlakozási problémák](#troubleshoot-connectivity-issues) szakaszban.

IBM Informix adattároló összekapcsolása egy Azure data factory, a következő példában látható módon ODBC társított szolgáltatás létrehozása:

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
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

A cikk részletes ismertetése az elejétől ODBC adatok használatával tárolja a másolási műveletek a forrás/fogadó adattárolókhoz olvasható.

## <a name="microsoft-access-source"></a>Microsoft Access-forrás

Az általános ODBC-összekötő segítségével a Microsoft Access-adatbázis adatainak másolhatja.

Állítson be egy Self-hosted integrációs futásidejű az adattárolóhoz hozzáféréssel rendelkező gépen. Az integrációs futásidejű az ODBC-illesztő a Microsoft Access számára az adattár való kapcsolódáshoz használ. Ezért telepítenie az illesztőprogramot, ha még nincs telepítve ugyanazon a számítógépen. Lásd: [Előfeltételek](#prerequisites) című szakaszban talál információt.

A Data Factory megoldás a Microsoft Access forrás használatához ellenőrizze, hogy az integrációs futásidejű az adattár szereplő utasításokat követve csatlakozhat [csatlakozási problémák](#troubleshoot-connectivity-issues) szakaszban.

Egy Microsoft Access-adatbázissal összekapcsolása egy Azure data factory, a következő példában látható módon ODBC társított szolgáltatás létrehozása:

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
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

A cikk részletes ismertetése az elejétől ODBC adatok használatával tárolja a másolási műveletek a forrás/fogadó adattárolókhoz olvasható.

## <a name="ge-historian-source"></a>GE történész forrás

Az általános ODBC-összekötővel GE történész adatainak másolhatja.

Állítson be egy Self-hosted integrációs futásidejű az adattárolóhoz hozzáféréssel rendelkező gépen. Az integrációs futásidejű GE történész az ODBC-illesztőprogram az adattár való kapcsolódáshoz használ. Ezért telepítenie az illesztőprogramot, ha még nincs telepítve ugyanazon a számítógépen. Lásd: [Előfeltételek](#prerequisites) című szakaszban talál információt.

A Data Factory-megoldásban GE történész forrás használatához ellenőrizze, hogy az integrációs futásidejű az adattár szereplő utasításokat követve csatlakozhat [csatlakozási problémák](#troubleshoot-connectivity-issues) szakaszban.

Egy Microsoft Access-adatbázissal összekapcsolása egy Azure data factory, a következő példában látható módon ODBC társított szolgáltatás létrehozása:

```json
{
    "name": "HistorianLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<GE Historian store connection string or DSN>"
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

A cikk részletes ismertetése az elejétől ODBC adatok használatával tárolja a másolási műveletek a forrás/fogadó adattárolókhoz olvasható.

## <a name="sap-hana-sink"></a>SAP HANA fogadó

>[!NOTE]
>Adatok másolása SAP HANA-adattár, tekintse meg natív [SAP HANA-összekötő](connector-sap-hana.md). Adatok másolása SAP HANA, kérjük, kövesse ezeket az utasításokat, ODBC-összekötő használatára. Megjegyzés: a különböző típusú van az összekapcsolt szolgáltatások SAP HANA-összekötő és az ODBC-összekötő így nem használható fel újra.
>

Az általános ODBC-összekötővel SAP HANA-adatbázisból adatainak másolhatja.

Állítson be egy Self-hosted integrációs futásidejű az adattárolóhoz hozzáféréssel rendelkező gépen. Az integrációs futásidejű SAP Hana ODBC-illesztőprogram az adattár való kapcsolódáshoz használ. Ezért telepítenie az illesztőprogramot, ha még nincs telepítve ugyanazon a számítógépen. Lásd: [Előfeltételek](#prerequisites) című szakaszban talál információt.

A SAP HANA-gyűjtő a Data Factory megoldás használata előtt ellenőrizze, hogy az integrációs futásidejű az adattár szereplő utasításokat követve csatlakozhat [csatlakozási problémák](#troubleshoot-connectivity-issues) szakaszban.

Egy SAP HANA-adattár összekapcsolása egy Azure data factory, a következő példában látható módon ODBC társított szolgáltatás létrehozása:

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

A cikk részletes ismertetése az elejétől ODBC adatok használatával tárolja a másolási műveletek a forrás/fogadó adattárolókhoz olvasható.

## <a name="troubleshoot-connectivity-issues"></a>Csatlakozási problémák

Kapcsolódási problémák elhárításához, használja a **diagnosztika** lapján **integrációs futásidejű Configuration Manager**.

1. Indítsa el **integrációs futásidejű a Configuration Manager**.
2. Váltás a **diagnosztika** fülre.
3. A "Kapcsolat tesztelése" szakaszban válassza ki a **típus** adatok tárolására (társított szolgáltatás).
4. Adja meg a **kapcsolati karakterlánc** , amelynek használatával az adattár csatlakozni, válassza ki a **hitelesítési** , és írja be **felhasználónév**, **jelszó**, és/vagy **hitelesítő adatok**.
5. Kattintson a **tesztkapcsolat** az adattár a kapcsolat ellenőrzéséhez.

## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).
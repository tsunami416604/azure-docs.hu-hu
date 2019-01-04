---
title: Adatok másolása az Azure Data Factory használatával az ODBC-források |} A Microsoft Docs
description: 'Útmutató: adatok másolása az OData-források a támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
ms.openlocfilehash: c51804748e4313d79cc3a369b659974d2d32e2e2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014267"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>A kezdő és a ODBC-adattárak Azure Data Factory használatával az adatok másolása
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-odbc-connector.md)
> * [Aktuális verzió](connector-odbc.md)

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolásához a kezdő és a egy ODBC data store. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok másolása az ODBC-forráshoz a bármely támogatott fogadó adattárba, vagy bármely támogatott forrásadattárból ODBC fogadó másolja. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az ODBC-összekötő támogatja az Azure blobból vagy az adatok másolását **bármely ODBC-kompatibilis adattárak** használatával **alapszintű** vagy **névtelen** hitelesítést.

## <a name="prerequisites"></a>Előfeltételek

Az ODBC-összekötő használatához meg kell:

- Egy helyi Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.
- Telepítse az ODBC-illesztő az adattár a saját üzemeltetésű integrációs gépen.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások ODBC-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

ODBC-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **ODBC** | Igen |
| kapcsolati Sztringje | A kapcsolati karakterlánc, hitelesítő adat rész. Például a minta segítségével adható meg a kapcsolati karakterlánc `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, vagy használja a rendszer DSN (adatforrás neve), állítsa be, az Integration Runtime gép `"DSN=<name of the DSN on IR machine>;"` (meg kell továbbra is adja meg a hitelesítő adatok rész társított szolgáltatásban ennek megfelelően).<br>Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md).| Igen |
| authenticationType | Az ODBC-adattárban való kapcsolódáshoz használt hitelesítés típusa.<br/>Engedélyezett értékek a következők: **Alapszintű** és **névtelen**. | Igen |
| Felhasználónév | Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. | Nem |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| hitelesítő adat | A hozzáférési hitelesítő adatok része, a kapcsolati karakterláncot a megadott illesztőprogram-specifikus tulajdonság-érték formátuma. Példa: `"RefreshToken=<secret refresh token>;"`. Ez a mező jelölhetnek egy SecureString. | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Egy helyi Integration Runtime szükség, az említett [Előfeltételek](#prerequisites). |Igen |

**1. példa: alapszintű hitelesítést használ.**

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

**2. példa: névtelen hitelesítés használatával**

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz az ODBC-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása Azure blobból vagy ODBC-kompatibilis adattárba, állítsa be a type tulajdonság, az adatkészlet **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **RelationalTable** | Igen |
| tableName | A tábla az ODBC-adattár neve. | Nincs forrás (Ha a tevékenység forrása "le" van megadva);<br/>Igen, a fogadó |

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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az ODBC-adatforrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="odbc-as-source"></a>ODBC forrásként

Az adatok másolása az ODBC-kompatibilis adatokat az adattárból, állítsa be a forrás típusaként a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **RelationalSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

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

### <a name="odbc-as-sink"></a>ODBC pedig a fogadó

ODBC-kompatibilis a másolhatja az adatokat, állítsa a fogadó típusa a másolási tevékenység **OdbcSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **OdbcSink** | Igen |
| writeBatchTimeout |Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot.<br/>Engedélyezett értékek a következők: időtartam. Példa: "00: 30:00" (30 perc). |Nem |
| WriteBatchSize |Amikor a puffer mérete eléri a writeBatchSize adatok beszúrása SQL-táblát.<br/>Engedélyezett értékek a következők: egész szám (sorok száma). |Nem (alapértelmezés szerint a 0 – automatikus észlelt) |
| preCopyScript |Adjon meg egy másolási tevékenység végrehajtása előtt minden egyes Futtatás data store-bA írja az adatokat SQL-lekérdezést. Ez a tulajdonság segítségével törölje az előre betöltött adatokat. |Nem |

> [!NOTE]
> "WriteBatchSize", a nincs beállítva (automatikusan észlelt), ha a másolási tevékenység először észleli, hogy az illesztőprogram támogatja a kötegelt műveletek, és beállíthatja azt a 10000, ha létezik, vagy állítsa 1-re, ha nem. Ha explicit módon az érték a 0 kivételével, a másolási tevékenység figyelembe veszi az értéket, és a futásidőben sikertelen lesz, ha az illesztőprogram nem támogatja a kötegelt műveletek.

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

IBM Informix-adatbázisból az általános ODBC-összekötő segítségével adatokat másolja.

A data store-hozzáféréssel rendelkező gépen helyi Integration Runtime beállítása. Az integrációs modul használ az ODBC-illesztő az Informix rendszerhez kapcsolódni az adattárhoz. Ha ugyanazon a számítógépen már nincs telepítve, ezért telepítse az illesztőprogram. Ha például illesztőprogram "IBM INFORMIX ODBC-illesztőprogram (64 bites) is használhatja. Lásd: [Előfeltételek](#prerequisites) című szakasz részletezi.

Az Informix forrás egy Data Factory-megoldás használatához ellenőrizze, hogy az integrációs modul az adattárban található utasításokat követve csatlakozhat [kapcsolati hibák elhárításához](#troubleshoot-connectivity-issues) szakaszban.

Hozzon létre egy ODBC-beli társított szolgáltatás egy IBM Informix-adattár összekapcsolása egy Azure data factory, az alábbi példában látható módon:

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

Olvassa el a cikk részletes áttekintést az elejétől ODBC-adatokat a forrás/fogadó adattárak a másolási műveletek tárolja.

## <a name="microsoft-access-source"></a>Microsoft Access-forrás

Microsoft Access-adatbázisból az általános ODBC-összekötő segítségével adatokat másolja.

A data store-hozzáféréssel rendelkező gépen helyi Integration Runtime beállítása. Az integrációs modul az ODBC-illesztő a Microsoft Access számára az adattárban való kapcsolódáshoz használ. Ha ugyanazon a számítógépen már nincs telepítve, ezért telepítse az illesztőprogram. Lásd: [Előfeltételek](#prerequisites) című szakasz részletezi.

Előtt használja a Microsoft Access-forrás egy Data Factory-megoldás az, ellenőrizze, hogy az integrációs modul az adattárban található utasításokat követve csatlakozhat [kapcsolati hibák elhárításához](#troubleshoot-connectivity-issues) szakaszban.

Hozzon létre egy ODBC-beli társított szolgáltatást, egy Microsoft Access-adatbázis egy Azure data factory a következő példában látható módon:

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

Olvassa el a cikk részletes áttekintést az elejétől ODBC-adatokat a forrás/fogadó adattárak a másolási műveletek tárolja.

## <a name="sap-hana-sink"></a>SAP HANA fogadó

>[!NOTE]
>Adatok másolása az SAP HANA-adattár, tekintse meg natív [SAP HANA-összekötő](connector-sap-hana.md). Adatok másolása az SAP HANA, kövesse ezeket az utasításokat az ODBC-összekötő használatára. Megjegyzés: a társított szolgáltatások, a SAP HANA-összekötő és az ODBC-összekötő rendelkező más típusú, ezért nem használható fel újra.
>

Az SAP HANA-adatbázis az általános ODBC-összekötő segítségével adatokat másolja.

A data store-hozzáféréssel rendelkező gépen helyi Integration Runtime beállítása. Az integrációs modul az SAP Hana ODBC-illesztő az adattárban való kapcsolódáshoz használ. Ha ugyanazon a számítógépen már nincs telepítve, ezért telepítse az illesztőprogram. Lásd: [Előfeltételek](#prerequisites) című szakasz részletezi.

Előtt használja a Data Factory-megoldás az SAP HANA fogadó, ellenőrizze, hogy az integrációs modul az adattárban található utasításokat követve csatlakozhat [kapcsolati hibák elhárításához](#troubleshoot-connectivity-issues) szakaszban.

Hozzon létre egy ODBC-beli társított szolgáltatást, egy SAP HANA-adattár egy Azure data factory a következő példában látható módon:

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

Olvassa el a cikk részletes áttekintést az elejétől ODBC-adatokat a forrás/fogadó adattárak a másolási műveletek tárolja.

## <a name="troubleshoot-connectivity-issues"></a>Kapcsolódási problémák elhárításához

A kapcsolati hibák elhárításához, használja a **diagnosztikai** lapján **Integration Runtime konfigurációkezelőjének**.

1. Indítsa el a **Integration Runtime konfigurációkezelőjének**.
2. Váltson a **diagnosztikai** fülre.
3. A "Kapcsolat tesztelése" szakasz alatt válassza ki a **típus** adatok tárolására (társított szolgáltatás).
4. Adja meg a **kapcsolati karakterlánc** , amelynek használatával az adattár csatlakozni, válassza ki a **hitelesítési** , és adja meg **felhasználónév**, **jelszó**, és/vagy **hitelesítő adatok**.
5. Kattintson a **kapcsolat tesztelése** az adattár a kapcsolat ellenőrzéséhez.

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

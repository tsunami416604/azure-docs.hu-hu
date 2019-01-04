---
title: Adatok áthelyezése a MySQL-hez az Azure Data Factory használatával |} A Microsoft Docs
description: Tudnivalók az adatok áthelyezése az Azure Data Factory segítségével MySQL-adatbázisból.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 452f4fce-9eb5-40a0-92f8-1e98691bea4c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: de1263d68e96a23bd6b5eca4297e74b56ba22e40
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021638"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Adatok áthelyezése a MySQL Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-onprem-mysql-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-mysql.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [MySQL-összekötő a v2-ben](../connector-mysql.md).


Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával helyezheti át egy helyszíni MySQL-adatbázishoz. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

A MySQL a helyszíni adattárolókból adatokat másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. A Data factory jelenleg csak helyez át adatokat egy MySQL-adattár pedig más adattárakban, de nem szükséges más adattárakból származó adatok áthelyezése egy MySQL-adattárba. 

## <a name="prerequisites"></a>Előfeltételek
A Data Factory szolgáltatás támogatja a helyszíni MySQL források az adatkezelési átjáró segítségével csatlakozik. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk további információt talál az adatkezelési átjáró-lépésenként az átjáró beállítása.

Átjáróra szükség, akkor is, ha a MySQL-adatbázishoz az Azure IaaS virtuális gépen (VM) üzemel. Telepítheti az átjáró adattárként ugyanazon a virtuális Gépen vagy egy másik virtuális gép mindaddig, amíg az átjáró képes kapcsolódni az adatbázishoz.

> [!NOTE]
> Lásd: [gateway hibáinak elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolódási/átjáró hibaelhárítási tippek a kapcsolatos problémákat.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Az adatkezelési átjárót a MySQL-adatbázishoz csatlakozni, telepítenie kell a [MySQL Connector/Net a Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (6.6.5 és 6.10.7 közötti verzió) az adatkezelési átjárót ugyanazon a rendszeren. A 32 bites illesztőprogramot az adatkezelési átjárót 64 bites kompatibilis. MySQL 5.1-es és újabb támogatott.

> [!TIP]
> Ha eléri a "Hitelesítés nem sikerült, mert a távoli fél bezárta az átviteli adatfolyamot." hibaüzenet, fontolja meg a MySQL Connector/Net frissítése újabb verzióra.

## <a name="getting-started"></a>Első lépések
Egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez a helyszíni Cassandra adattárolókból más eszközök/API-k használatával is létrehozhat. 

- A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával. 
- A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját. 

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. 
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A Data Factory-entitások, amelyek a MySQL a helyszíni adattárolókból az adatok másolása JSON-definíciói egy minta: [JSON-példa: Adatok másolása az MySQL az Azure Blob](#json-example-copy-data-from-mysql-to-azure-blob) című szakaszát. 

A következő szakaszok a MySQL adattárba adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az alábbi táblázatban a JSON-elemeket társított MySQL szolgáltatás leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **A OnPremisesMySql** |Igen |
| kiszolgáló |A MySQL-kiszolgáló neve. |Igen |
| adatbázis |A MySQL-adatbázis neve. |Igen |
| séma |A séma az adatbázis neve. |Nem |
| authenticationType |A MySQL-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek a következők: `Basic`. |Igen |
| felhasználónév |Adja meg a felhasználónevet, a MySQL-adatbázishoz való csatlakozáshoz. |Igen |
| jelszó |Adja meg a megadott felhasználói fiókhoz tartozó jelszót. |Igen |
| átjáró neve |Az átjáró által a Data Factory szolgáltatás a helyi MySQL-adatbázishoz való kapcsolódáshoz használandó neve. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. A typeProperties szakasz típusú adatkészlet **RelationalTable** (amely tartalmazza a MySQL-adatkészlet) a következő tulajdonságokkal rendelkezik

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A MySQL-adatbázispéldányban, amelyek a társított szolgáltatás hivatkozik a tábla neve. |Nem (Ha **lekérdezés** , **RelationalSource** van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Név, leírás, bemeneti és kimeneti táblák, például a tulajdonságok akkor, házirend érhető el az összes típusú tevékenységet.

Mivel a tulajdonságok érhetők el a **typeProperties** a tevékenység szakaszban tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

Ha a másolási tevékenység forrása típusa **RelationalSource** (amely tartalmazza a MySQL), a következő tulajdonságok typeProperties szakasz érhető el:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: válassza ki * from tábla. |Nem (Ha **tableName** , **adatkészlet** van megadva) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>JSON-példa: Adatok másolása az MySQL az Azure Blob
Ebben a példában biztosít, amellyel létrehoz egy folyamatot használatával példa JSON-definíciók [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Azt mutatja, hogyan másolhat adatokat egy helyi MySQL-adatbázisból egy Azure Blob Storage. Azonban adatok átmásolhatók a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.

> [!IMPORTANT]
> Ez a példa JSON-kódrészletek biztosít. Nem tartalmaz részletes útmutató az adat-előállító létrehozásához. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) részletesen ismertető cikket.

A minta az alábbi data factory-entitások rendelkezik:

1. A társított szolgáltatás típusa [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [RelationalTable](data-factory-onprem-mysql-connector.md#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [RelationalSource](data-factory-onprem-mysql-connector.md#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol egy lekérdezés eredménye a MySQL-adatbázis egy blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

Első lépésként a telepítő az adatkezelési átjárót. A rendszer az utasításokat a [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**MySQL-beli társított szolgáltatást:**

```JSON
    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
        }
      }
    }
```

**Az Azure Storage társított szolgáltatást:**

```JSON
    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }
```

**MySQL bemeneti adatkészlet:**

A minta azt feltételezi, létrehozott egy táblát "MyTable" MySQL-ben és a egy idősorozat-adatok a "timestampcolumn" nevű oszlopot tartalmaz.

Beállítás az "external": "true" tájékoztatja a Data Factory szolgáltatásban, hogy a tábla a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

```JSON
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }
```

**Azure blobkimeneti adatkészlet:**

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1.). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

```JSON
    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
```

**Másolási tevékenységgel rendelkező folyamat:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **RelationalSource** és **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

```JSON
    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }
```


### <a name="type-mapping-for-mysql"></a>MySQL-leképezés típusa
Ahogy korábban már említettük, az a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) a cikkben a másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi kétlépéses módszer a forrás-típusok közül:

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

Ha helyez át adatokat a MySQL-hez, a következő hozzárendeléseket a rendszer MySQL-típusok közül .NET Tulajdonságtípusokat használja.

| MySQL-adatbázis típusa | .NET-keretrendszer típusa |
| --- | --- |
| aláíratlan bigint |Tizedes tört |
| bigint |Int64 |
| bit |Tizedes tört |
| blob |Byte] |
| Logikai |Logikai |
| CHAR |Karakterlánc |
| dátum |Dátum és idő |
| dátum/idő |Dátum és idő |
| tizedes tört |Tizedes tört |
| a kétszeres pontosság |Dupla |
| double |Dupla |
| Enum |Karakterlánc |
| lebegőpontos |Önálló |
| aláíratlan int |Int64 |
| int |Int32 |
| egész szám aláíratlan |Int64 |
| egész szám |Int32 |
| hosszú varbinary |Byte] |
| hosszú varchar |Karakterlánc |
| longblob |Byte] |
| LONGTEXT |Karakterlánc |
| mediumblob |Byte] |
| aláíratlan mediumint |Int64 |
| mediumint |Int32 |
| mediumtext |Karakterlánc |
| numerikus |Tizedes tört |
| valódi |Dupla |
| halmaz |Karakterlánc |
| aláíratlan smallint |Int32 |
| smallint |Int16 |
| szöveg |Karakterlánc |
| time |Időtartam |
| időbélyeg |Dátum és idő |
| tinyblob |Byte] |
| aláíratlan tinyint |Int16 |
| tinyint |Int16 |
| tinytext |Karakterlánc |
| varchar |Karakterlánc |
| év |Int |

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
Fogadó-adatkészlet oszlopaihoz forrásadatkészlet leképezés oszlopai kapcsolatos további információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források megismételhető olvasása
Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell. Lásd: [olvasni a relációs források Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.

---
title: Adatok áthelyezése a MySQL-ből az Azure Data Factory használatával
description: Ismerje meg, hogyan helyezhetát át az adatokat a MySQL-adatbázisból az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 452f4fce-9eb5-40a0-92f8-1e98691bea4c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 90fccba016a3db9ff85f8ec7c8fd426ef3c896a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281287"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Adatok áthelyezése a MySQL-ből az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-mysql-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-mysql.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [MySQL-összekötő t a V2-ben.](../connector-mysql.md)


Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése egy helyszíni MySQL-adatbázisból. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

A helyszíni MySQL-adattárból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját a [Támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblában láthatja. A Data Factory jelenleg csak a MySQL-adattárakból más adattárolókba való átmozgatását támogatja, de nem támogatja az adatok más adattárakból a MySQL-adattárba való áthelyezését. 

## <a name="prerequisites"></a>Előfeltételek
A Data Factory szolgáltatás támogatja a helyszíni MySQL-forrásokhoz való csatlakozást az adatkezelési átjáró használatával. Tekintse meg [az adatok áthelyezését a helyszíni helyek és a felhőalapú](data-factory-move-data-between-onprem-and-cloud.md) cikk között, és ismerje meg az Adatkezelési átjárót és az átjáró beállításának lépésenkénti útmutatóját.

Átjáró akkor is szükség van, ha a MySQL-adatbázis üzemelteti az Azure IaaS virtuális gép (VM). Az átjáró t az adattárban lévő ugyanazon virtuális gépre vagy egy másik virtuális gépre telepítheti, amíg az átjáró csatlakozhat az adatbázishoz.

> [!NOTE]
> A kapcsolatokkal/átjáróval kapcsolatos problémák elhárításával kapcsolatos tippek az [átjáróval kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) című témakörben olvashat.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Ahhoz, hogy az Adatkezelési átjáró a MySQL-adatbázishoz kapcsolódhasson, telepítenie kell a [MySQL Connector/NET for Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (6.6.5 és 6.10.7 közötti verzió) rendszert, mint az adatkezelési átjáró. Ez a 32 bites illesztőprogram kompatibilis a 64 bites adatkezelési átjáróval. MySQL 5.1-es és újabb verzió támogatott.

> [!TIP]
> Ha a "Hitelesítés nem sikerült, mert a távoli fél bezárta az átviteli adatfolyamot" hibaüzenetet kap, fontolja meg a MySQL Connector/NET magasabb verzióra való frissítését.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával áthelyezi az adatokat egy helyszíni Cassandra-adattárból. 

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához. 
- A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához. 

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. 
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel. 

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja.  A helyszíni MySQL-adattólimából adatok másolására használt Data Factory-entitások JSON-definícióival rendelkező minta a [JSON-példa: Adatok másolása a MySQL-ből](#json-example-copy-data-from-mysql-to-azure-blob) az Azure Blob szakaszban ebben a cikkben. 

A következő szakaszok a MySQL-adattára jellemző Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat a MySQL-hez csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságnak a következő re van állítva: **OnPremisesMySql** |Igen |
| kiszolgáló |A MySQL-kiszolgáló neve. |Igen |
| adatbázis |A MySQL adatbázis neve. |Igen |
| Séma |A séma neve az adatbázisban. |Nem |
| authenticationType |A MySQL-adatbázishoz való csatlakozáshoz használt hitelesítés típusa. Lehetséges értékek: `Basic`. . |Igen |
| userName (Felhasználónév) |Adja meg a MySQL-adatbázishoz való csatlakozáshoz kívánt felhasználónevet. |Igen |
| jelszó |Adja meg a megadott felhasználói fiók jelszavát. |Igen |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni MySQL-adatbázishoz való csatlakozáshoz kell használnia. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. A **RelationalTable** típusú adatkészlet typeProperties szakasza (amely a MySQL adatkészletet is tartalmazza) a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve a MySQL adatbázis-példányban, amelyre a csatolt szolgáltatás hivatkozik. |Nem (ha a **RelationalSource** **lekérdezése** meg van adva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, mint a név, leírás, bemeneti és kimeneti táblák, a házirendek érhetők el minden típusú tevékenységek.

Mivel a tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

Ha a másolási tevékenység forrása **RelationalSource** típusú (amely magában foglalja a MySQL-t is), a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: válassza a * lehetőséget a MyTable táblából. |Nem (ha **a tableName** of **dataset** meg van adva) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>JSON-példa: Adatok másolása a MySQL-ből az Azure Blobba
Ebben a példában minta JSON-definíciók, amelyek segítségével hozzon létre egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Bemutatja, hogyan másolhat adatokat egy helyszíni MySQL-adatbázisból egy Azure Blob Storage-ba. Azonban az adatok átmásolhatók az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott fogadók bármelyikébe az Azure Data Factory másolási tevékenység használatával.

> [!IMPORTANT]
> Ez a minta JSON-kódrészleteket biztosít. Nem tartalmazza az adat-előállító létrehozásának lépésenkénti útmutatóit. Tekintse meg [az adatok áthelyezését a helyszíni helyek és](data-factory-move-data-between-onprem-and-cloud.md) a felhőalapú cikk között, és részletes útmutatást talál.

A minta a következő adatfeldolgozó entitásokkal rendelkezik:

1. [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties)típusú összekapcsolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [RelationalTable](data-factory-onprem-mysql-connector.md#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [Relációsforrást](data-factory-onprem-mysql-connector.md#copy-activity-properties) és [BlobSinket](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta adatokat másol egy lekérdezéseredmény a MySQL-adatbázisban egy blob óránként. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni helyek és a felhőalapú cikk [közötti átmozgatási](data-factory-move-data-between-onprem-and-cloud.md) adatokban találhatók.

**MySQL kapcsolt szolgáltatás:**

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

**Azure Storage-hoz kapcsolódó szolgáltatás:**

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

A minta feltételezi, hogy létrehozott egy "MyTable" táblát a MySQL-ben, és tartalmaz egy "timestampcolumn" nevű oszlopot az idősorozat-adatokhoz.

A "külső": "true" beállítás tájékoztatja a Data Factory szolgáltatást, hogy a tábla az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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

**Azure Blob kimeneti adatkészlet:**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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

**Folyamat másolási tevékenységgel:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **RelationalSource** lesz állítva, **a fogadó** típusa pedig **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés kiválasztja a másolni kívánt adatokat az elmúlt órában.

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


### <a name="type-mapping-for-mysql"></a>A MySQL típusleképezése
Az [adatmozgatási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben említettek szerint a Másolás tevékenység automatikus típuskonverziót hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel:

1. Konvertálás natív forrástípusokból .NET-típussá
2. Konvertálás .NET típusból natív fogadótípussá

Amikor adatokat helyez át a MySQL-be, a következő leképezéseket használjuk a MySQL típusokból a .NET típusokba.

| MySQL-adatbázis típusa | .NET keretrendszer típusa |
| --- | --- |
| bigint aláíratlan |Decimal |
| bigint |Int64 |
| bit |Decimal |
| blob |Bájt[] |
| Bool |Logikai |
| Char |Sztring |
| dátum |Datetime |
| dátum/idő |Datetime |
| tizedes tört |Decimal |
| dupla pontosság |Double |
| double |Double |
| Enum |Sztring |
| lebegőpontos |Egyirányú |
| int aláíratlan |Int64 |
| int |Int32 |
| nem aláírt egész szám |Int64 |
| egész szám |Int32 |
| hosszú varbinary |Bájt[] |
| hosszú varchar |Sztring |
| longblob |Bájt[] |
| hosszú szöveg |Sztring |
| közepes festék |Bájt[] |
| közepesen aláíratlan |Int64 |
| közepes |Int32 |
| közepes szöveg |Sztring |
| numerikus |Decimal |
| real |Double |
| halmaz |Sztring |
| smallint aláíratlan |Int32 |
| smallint |Int16 |
| szöveg |Sztring |
| time |időtartam |
| időbélyeg |Datetime |
| apróblob |Bájt[] |
| tinyint aláíratlan |Int16 |
| tinyint |Int16 |
| apró szöveg |Sztring |
| varchar |Sztring |
| év |Int |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz
Ha többet szeretne tudni arról, hogy a forrásadatkészlet oszlopait a fogadó adatkészlet oszlopaihoz szeretné-e leképezni, olvassa [el az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-read-from-relational-sources"></a>Relációs forrásokból ismételhető olvasmony
Ha relációs adattárakból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet újrapróbálkozási házirendje is konfigurálható, így a szelet újrafut, ha hiba történik. Ha egy szeletet mindkét irányban újrafuttat, meg kell győződnie arról, hogy ugyanazokat az adatokat olvassa el, függetlenül attól, hogy hányszor fut egy szelet. Lásd: [Ismételhető olvasás relációs forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.

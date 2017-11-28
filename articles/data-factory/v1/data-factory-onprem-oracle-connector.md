---
title: "Másolja az adatokat, az Oracle Data Factory használatával |} Microsoft Docs"
description: "Ismerje meg, és a Oracle adatbázis, amely a helyszíni Azure Data Factory használatával adatok másolása."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8ff071ed1ce5a3e9927e4c24d23efae3ae0cd6c6
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="copy-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával a helyszíni Oracle és a
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-onprem-oracle-connector.md)
> * [2. verzió – Előzetes verzió](../connector-oracle.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [Oracle-összekötőt, a V2](../connector-oracle.md).


Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factory áthelyezni az adatokat és a helyszíni Oracle-adatbázishoz. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolhat **az Oracle-adatbázishoz** tárolja a következő adatokat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Adatok másolása a következő adatokat tárolja **Oracle-adatbázishoz**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Előfeltételek
Adat-előállító helyszíni Oracle-adatforrások az adatkezelési átjáró használatával történő csatlakozást támogatja. Lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md) cikkben tájékozódhat az adatkezelési átjáró és [tárolt adatok mozgatása felhőbe helyszíni](data-factory-move-data-between-onprem-and-cloud.md) cikk lépésenkénti állít be, az átjáró adatok folyamat adatok áthelyezése.

Átjáróra szükség, akkor is, ha az Oracle egy Azure IaaS virtuális gép található. Telepítheti az átjáró adattárként ugyanazon infrastruktúra-szolgáltatási virtuális gép vagy egy másik virtuális gép mindaddig, amíg az átjáró képes kapcsolódni az adatbázishoz.

> [!NOTE]
> Lásd: [átjáró elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolati/átjáró hibaelhárítási tippek a kapcsolódó problémákat.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Az Oracle-összekötő illesztőprogramok két verziója támogatja:

- **Microsoft-illesztőprogram (ajánlott) Oracle**: az adatkezelési átjáró verziója 2.7, a Microsoft illesztőprogram az Oracle automatikusan telepítve van az átjárón, így nem kell továbbá kezelni annak érdekében, hogy az illesztőprogram-től kezdődő Oracle kapcsolatot létrehozni, és akkor is jelentkezhet jobb másolási teljesítményt az illesztőprogramot. Oracle verziói alatti adatbázisok támogatottak:
    - Oracle 12c R1 (12.1)
    - Oracle 11g R1 vagy R2 (11.1, 11.2)
    - Oracle 10g R1 vagy R2 (10.1, 10,2)
    - Oracle 9i R1 vagy R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7-es)

> [!IMPORTANT]
> Jelenleg Microsoft Oracle-illesztőprogram csak az adatok másolását a Oracle, de nincs írás Oracle támogatja. És jegyezze meg a teszt kapcsolat funkció adatok felügyeleti átjáró Diagnosztika lap nem támogatja az illesztőprogramot. A varázsló segítségével azt is megteheti, ellenőrizze a kapcsolatot.
>

- **.NET-keretrendszerhez készült Oracle-adatszolgáltatóban:** másolja az adatokat, Oracle vagy Oracle-adatszolgáltatóban segítségével is beállíthatja. Ez az összetevő megtalálható [Oracle Data Access Windows összetevők](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Telepítse a megfelelő verzióját (32 vagy 64 bites) a számítógépen, amelyen az átjáró telepítve van. [Oracle-adatszolgáltatóban .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) férhetnek hozzá Oracle Database 10 g, 2 vagy újabb kiadás.

    Ha úgy dönt, hogy a "XCopy telepítés", kövesse a readme.htm. Azt javasoljuk, hogy úgy dönt, hogy a telepítő felhasználói felületen (nem-XCopy egy).

    A szolgáltató telepítése után **indítsa újra a** az adatkezelési átjáró gazdaszolgáltatás a gépen a szolgáltatások kisalkalmazásával (vagy) az adatkezelési átjáró konfigurációkezelőjének használatával.  

Ahhoz, hogy a másolási folyamat másolása varázslót használja, ha az illesztőprogram-típus lesz automatikusan határozza meg. Microsoft illesztőprogram által használható alapértelmezett, kivéve, ha az átjáró verziója alacsonyabb, mint 2.7, vagy ha úgy dönt, Oracle, a fogadó.

## <a name="getting-started"></a>Bevezetés
A másolási tevékenység, amely helyezi át az adatokat a helyszíni Oracle-adatbázishoz és a különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**, és **REST API**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Hozzon létre egy **adat-előállító**. Egy adat-előállító tartalmazhat egy vagy több folyamatok. 
2. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory. Adatok Oralce adatbázisból az Azure blob Storage másolása, akkor hozzon létre például az Oracle-adatbázishoz és az Azure storage-fiók összekapcsolása a data factory két társított szolgáltatások. Oracle jellemző csatolt szolgáltatás tulajdonságait, lásd: [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz.
3. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. A példa az előző lépésben említett Ha meg szeretné adni a tábla az Oracle-adatbázishoz a bemeneti adatokat tartalmazó adatkészlet hoz létre. Továbbá adja meg a blob-tároló és a mappa, amely tárolja az adatokat másolni az Oracle-adatbázisból egy másik dataset létrehozhat. Oracle adott adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
4. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. A korábban említett példában OracleSource forrás-és BlobSink akár használhatja a fogadó a másolási tevékenységhez. Ehhez hasonlóan az Azure Blob Storage Oracle-adatbázishoz való másolása, használható BlobSource és OracleSink a másolási tevékenység. Oracle-adatbázishoz adott tevékenység Tulajdonságok másolása, lásd: [tevékenység Tulajdonságok másolása](#copy-activity-properties) szakasz. További részletek a tárolóban használatáról a forrás vagy a fogadó a hivatkozásra a adattároló az előző szakaszban. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  JSON-definíciók, amely segítségével másolja az adatokat a helyszíni Oracle-adatbázishoz az adat-előállító entitások minták, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-oracle-database) című szakaszát.

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory entitások JSON-tulajdonságok:

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat a JSON-elemek szerepelnek Oracle kapcsolódó szolgáltatásra vonatkozó leírást.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **OnPremisesOracle** |Igen |
| driverType | Adja meg, melyik illesztőprogram használatával másolja az adatokat, vagy Oracle-adatbázishoz. Két érték engedélyezett **Microsoft** vagy **ODP** (alapértelmezett). Lásd: [verziójától és a telepítés támogatott](#supported-versions-and-installation) illesztőprogram adatai szakaszban. | Nem |
| connectionString | Adja meg az Oracle adatbázispéldányt a connectionString tulajdonság való kapcsolódáshoz szükséges adatokat. | Igen |
| gatewayName | Azon átjáró neve, amely a helyszíni Oracle-kiszolgálóhoz való csatlakozáshoz használt |Igen |

**Példa: Microsoft-illesztőprogramot használ:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Példa: ODP illesztőprogramot használja.**

Tekintse meg [ezen a helyen](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/) az engedélyezett formátumokat.

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Oracle, az Azure blob, Azure-tábla, stb.).

A typeProperties szakasz más adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A typeProperties szakasz az adatkészlet OracleTable típusú tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla az Oracle-adatbázishoz, amely hivatkozik a társított szolgáltatás neve. |Nem (Ha **oracleReaderQuery** a **OracleSource** van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Például a nevét, leírását, valamint bemeneti és kimeneti táblák és házirend tulajdonságai minden típusú tevékenységek érhetők el.

> [!NOTE]
> A másolási tevékenység során csak egy bemenettel rendelkezik, és csak egy kimenetet.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

### <a name="oraclesource"></a>OracleSource
A másolási tevékenység, ha az adatforrás típusú **OracleSource** a következő tulajdonságok érhetők el **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| oracleReaderQuery |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: Válasszon * from tábla <br/><br/>Ha nincs megadva, az SQL-utasítás végrehajtott: Válasszon * from tábla |Nem (Ha **tableName** a **dataset** van megadva) |

### <a name="oraclesink"></a>OracleSink
**OracleSink** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot. |A TimeSpan<br/><br/> . Példa: 00:30:00 (30 perc). |Nem |
| WriteBatchSize |Szúr be az SQL-tábla adatokat, amikor a puffer mérete eléri writeBatchSize. |Egész szám (sorok száma) |Nem (alapértelmezett: 100) |
| sqlWriterCleanupScript |Adja meg egy lekérdezést a másolási tevékenység végrehajtása úgy, hogy egy adott szelet adatait. |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Adja meg a másolási tevékenység során automatikusan létrejön szelet azonosító, amely segítségével távolítja el az adatokat egy adott szelet, amikor futtassa újra a töltse ki az oszlopnevet. |Egy oszlop binary(32) adattípusú oszlop neve. |Nem |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>Adatok másolása, és az Oracle-adatbázisból JSON példák
Az alábbi példa minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot biztosít [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ezek szemléltetik adatok másolása az / / az Azure Blob Storage Oracle-adatbázishoz. Azonban adatok átmásolhatók a megadott mosdók bármelyikét [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Példa: Adatok másolása az Oracle az Azure-Blobba

A minta a következő data factory entitások rendelkezik:

1. A társított szolgáltatás típusa [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) forrásként és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) mint fogadó.

A minta másol adatokat egy helyszíni Oracle adatbázis egyik táblája blob óránként. További információ a különböző, a mintában használt tulajdonságok a mintákat a következő szakaszok dokumentációjában olvasható.

**Oracle kapcsolódó szolgáltatás:**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Az Azure Blob storage társított szolgáltatásnak:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Oracle bemeneti adatkészlet:**

A példa azt feltételezi, hogy létrehozott egy tábla "MyTable" Oracle és egy "timestampcolumn" nevű adatsorozat időadatok oszlopot tartalmaz.

"External" beállítása: "true" arról tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet külső data factoryval való és adat-előállító tevékenység nem hozzák.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
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

**Az Azure Blob kimeneti adatkészlet:**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan értékeli ki a kezdési időt a szelet által feldolgozott alapján. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**A másolási tevékenység során a következő feldolgozási sorban:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. Az adatcsatorna JSON-definícióból a **forrás** típusúra **OracleSource** és **fogadó** típusúra **BlobSink**.  A megadott SQL-lekérdezés **oracleReaderQuery** tulajdonság kiválasztása az adatok másolása az elmúlt órában.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Példa: Adatok másolása az Azure Blob az Oracle
Ez a példa bemutatja az adatok másolása egy Azure Blob Storage-ból a helyszíni Oracle-adatbázishoz. Azonban az adatok átmásolhatók **közvetlenül** bármelyik megadott forrás [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.  

A minta a következő data factory entitások rendelkezik:

1. A társított szolgáltatás típusa [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) forrásaként [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) mint fogadó.

A minta másol adatokat egy blobot egy helyszíni Oracle adatbázis egyik táblája óránként. További információ a különböző, a mintában használt tulajdonságok a mintákat a következő szakaszok dokumentációjában olvasható.

**Oracle kapcsolódó szolgáltatás:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Az Azure Blob storage társított szolgáltatásnak:**
```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Az Azure Blob bemeneti adatkészlet**

Adatok van felvett egy új blobból minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan értékeli ki a kezdési időt a szelet által feldolgozott alapján. A mappa elérési útját használja év, hónap és nap részét kezdési idejét, valamint fájl nevét a kezdő időpontja óra részét. "external": "true" beállítás arról értesíti az, hogy ezt a táblázatot az adat-előállítóban külső, és egy tevékenység adat-előállító nem hozzák a Data Factory szolgáltatásnak.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
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

**Oracle kimeneti adatkészlet:**

A példa feltételezi, hogy létrehozott egy "MyTable" táblát az Oracle. A tábla létrehozása az azonos számú oszlopot az Oracle a Blob CSV-fájl tartalmazza a várt módon. Új sorok hozzáadásakor a tábla minden órában.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**A másolási tevékenység során a következő feldolgozási sorban:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **BlobSource** és a **fogadó** típusúra **OracleSink**.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```


## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
### <a name="problem-1-net-framework-data-provider"></a>1. hiba: A .NET-keretrendszer adatszolgáltatója

Lásd a következő **hibaüzenet**:

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Lehetséges okok:**

1. A .NET Framework Data Provider – Oracle nem lett telepítve.
2. A .NET Framework Data Provider – Oracle lett telepítve a .NET-keretrendszer 2.0, és nem található a .NET Framework 4.0 mappákban.

**Megoldás vagy megoldás:**

1. Ha még nem telepítette a .NET-szolgáltató az Oracle rendszerhez, [telepítse](http://www.oracle.com/technetwork/topics/dotnet/downloads/) , majd próbálja megismételni a forgatókönyvet.
2. Ha a hiba jelenik meg a szolgáltató telepítése után is, tegye a következőket:
   1. Nyissa meg a .NET 2.0 gép config a mappából: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Keresse meg **.NET-keretrendszerhez készült Oracle-adatszolgáltatóban**, és meg kell található bejegyzés a következő mintában látható módon **system.data** -> **DbProviderFactories**: "<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description=".NET oracle-adatszolgáltatója" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Ez a bejegyzés másolja a machine.config fájlban a következő v4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config, és módosítsa a 4.xxx.x.x verzióra.
4. A globális szerelvény-gyorsítótárban (GAC) "< ODP.NET telepített elérési útja > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll" futtatásával telepítse `gacutil /i [provider path]`. ## hibaelhárítási tippek

### <a name="problem-2-datetime-formatting"></a>2. hiba: dátum és idő formázása

Lásd a következő **hibaüzenet**:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Megoldás vagy megoldás:**

Szükség lehet úgy, hogy a lekérdezési karakterláncok a másolási tevékenység alapján dátumok hogyan vannak konfigurálva az Oracle-adatbázis (a to_date függvény használatával) a következő mintában látható módon:

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Oracle típusú leképezése
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk másolási tevékenység az eseményforrás-típusnak gyűjtése módszert használja a következő 2. lépés típusok automatikus típuskonverziók hajtja végre:

1. A natív eseményforrás-típusnak átalakítása .NET-típusa
2. .NET-típus konvertálása natív a fogadó típusa

Ha az adatok áthelyezése az Oracle, a következő megfeleltetéseket használtak Oracle-adattípusra .NET-típus, és ez fordítva is igaz.

| Oracle-adattípusra | .NET-keretrendszer adattípus |
| --- | --- |
| BFÁJL |Byte] |
| A BLOB |Byte]<br/>(csak Oracle 10g és magasabb when támogatott használatával a Microsoft-illesztő) |
| KARAKTER |Karakterlánc |
| CLOB |Karakterlánc |
| DÁTUM |Dátum és idő |
| LEBEGŐPONTOS |Decimális, karakterlánc (Ha pontosság > 28) |
| EGÉSZ SZÁM |Decimális, karakterlánc (Ha pontosság > 28) |
| IDŐKÖZ HÓNAP ÉV |Int32 |
| MÁSODIK INTERVALLUM NAPONTA |A TimeSpan |
| HOSSZÚ |Karakterlánc |
| HOSSZÚ NYERS |Byte] |
| NCHAR |Karakterlánc |
| NCLOB |Karakterlánc |
| SZÁM |Decimális, karakterlánc (Ha pontosság > 28) |
| NVARCHAR2 |Karakterlánc |
| NYERS |Byte] |
| ROWID |Karakterlánc |
| IDŐBÉLYEG |Dátum és idő |
| A HELYI IDŐZÓNÁRA IDŐBÉLYEG |Dátum és idő |
| AZ IDŐZÓNA IDŐBÉLYEG |Dátum és idő |
| ELŐJEL NÉLKÜLI EGÉSZKÉNT. |Szám |
| VARCHAR2 |Karakterlánc |
| XML |Karakterlánc |

> [!NOTE]
> Adattípus **IDŐKÖZ év TO hónap** és **IDŐKÖZ nap TO második** Microsoft illesztőprogram használata esetén nem támogatottak.

## <a name="map-source-to-sink-columns"></a>Térkép forrás oszlopok gyűjtése
A forrás oszlop szerepel a fogadó dataset adatkészlet leképezési oszlopok, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források ismételhető Olvasás
Ha az adatok másolását a relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja úgy is egy adatkészlet újrapróbálkozási házirendje, hogy a szelet akkor fut újra, ha hiba történik. A szelet akkor fut újra, vagy módon, ha győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy a szelet futtatása hány alkalommal kell. Lásd: [relációs források olvasni Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.

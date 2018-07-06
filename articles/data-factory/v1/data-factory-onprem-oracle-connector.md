---
title: Másolja az adatokat, vagy az Oracle Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat és-tárolókról Oracle adatbázis, amely a helyszíni Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 10535e75a32a9f95e759340cf14d693f43639473
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856841"
---
# <a name="copy-data-to-or-from-on-premises-oracle-using-azure-data-factory"></a>Másolja az adatokat, vagy az Azure Data Factory használatával a helyszíni Oracle
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-onprem-oracle-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-oracle.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [Oracle-összekötő a v2-ben](../connector-oracle.md).


Ez a cikk bemutatja, hogyan használható a másolási tevékenység az Azure Data Factoryban adatok és-tárolókról a helyszíni Oracle-adatbázis áthelyezése. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolja **Oracle-adatbázisból** tárolja, a következő adatokat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból származó adatokat másolja **Oracle-adatbázishoz**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Előfeltételek
A Data Factory támogatja a helyszíni Oracle-adatforrások az adatkezelési átjáró segítségével csatlakozik. Lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md) adatkezelési átjáró kapcsolatos cikk és [adatok áthelyezése a helyszínről a felhőbe](data-factory-move-data-between-onprem-and-cloud.md) kapcsolatos lépésenkénti útmutatót az átjáró beállítása egy adatfolyamat cikk adatok áthelyezése.

Átjáróra szükség, akkor is, ha az Oracle az Azure IaaS virtuális gép üzemel. Telepítheti az átjáró adattárként azonos IaaS virtuális Gépen vagy egy másik virtuális gép mindaddig, amíg az átjáró képes kapcsolódni az adatbázishoz.

> [!NOTE]
> Lásd: [gateway hibáinak elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolódási/átjáró hibaelhárítási tippek a kapcsolatos problémákat.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Az Oracle-összekötő illesztőprogramok két verziója támogatja:

- **Oracle (ajánlott) Microsoft-illesztőprogram**: az adatkezelési átjáró verziója 2.7-es, a Microsoft-illesztőprogram az Oracle automatikusan települ az átjáró együtt, így nem szükséges emellett kezelni annak érdekében, hogy az illesztőprogram-től Oracle kapcsolatot, és akkor is jelentkezhet a jobb másolási teljesítmény az illesztőprogramot. Oracle-verziók alatt az adatbázisok támogatottak:
    - Oracle 12c R1 (12.1)
    - Oracle 11g R1 vagy R2 (11.1, 11.2)
    - Oracle 10g R1 vagy R2 (10.1, 10,2)
    - Oracle 9i R1 vagy R2 (9.0.1, 9.2)
    - Oracle-8i R3 (8.1.7-es)

> [!NOTE]
> Oracle-proxy kiszolgáló nem támogatott.

> [!IMPORTANT]
> Microsoft Oracle-illesztőprogram jelenleg csak az Oracle, de nem írása és Oracle-ig adatmásolásra támogatja. És vegye figyelembe a tesztelés kapcsolat funkció Data Management Gateway-Diagnosztika lap nem támogatja az illesztőprogramot. A másolás varázsló segítségével azt is megteheti, ellenőrizze a kapcsolatot.
>

- **Oracle-adatszolgáltató a .NET-hez:** azt is beállíthatja az adatok másolása Azure blobból vagy az Oracle, Oracle-adatszolgáltató használata. Ez az összetevő megtalálható [Oracle Data Access összetevői a Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Telepítse a megfelelő verziót (32 vagy 64 bites) a gépen, amelyen az átjáró telepítve van. [Oracle-adatszolgáltató a .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) el tud érni az Oracle Database 10 g, 2 vagy újabb kiadás.

    Ha "XCopy-telepítés" lehetőséget választja, kövesse a readme.htm lépéseit. Azt javasoljuk, hogy úgy dönt, hogy a telepítő felhasználói felületének (nem-XCopy egyet).

    A szolgáltató telepítése után **indítsa újra a** a Data Management Gateway szolgáltatás a gépen szolgáltatások kisalkalmazásával (vagy) Data Management Gateway Configuration Manager használatával.  

Ha a másolás varázsló használatával hozhat létre a másolási folyamat, az illesztőprogram-típus nem automatikus határozza meg. Microsoft-illesztőprogram alapértelmezés szerint használandó, kivéve, ha az átjáró verziószáma 2.7-es kisebb, vagy pedig a fogadó Oracle választja.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok és-tárolókról a helyszíni Oracle-adatbázis áthelyezéséhez a különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: folyamat létrehozása a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

-Folyamatok létrehozására is használhatja az alábbi eszközöket: **az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre egy **adat-előállító**. Adat-előállító egy vagy több folyamattal is tartalmazhat. 
2. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához. Például ha az adatok egy Oralce-adatbázisból egy Azure blob Storage-tárolóba másol, létrehozhat két társított szolgáltatást, az Oracle database és az Azure storage-fiók összekapcsolása a data factory. Konkrétan Oracle a társított szolgáltatás tulajdonságait, lásd: [társított szolgáltatások tulajdonságai](#linked-service-properties) szakaszban.
3. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. A példában az előző lépésben említett az Oracle-adatbázis, amely tartalmazza a bemeneti adatokat adja meg a táblát egy adatkészletet hoz létre. És megadja a blobtárolót és a mappát, amely tárolja az adatokat másolja az Oracle-adatbázisból egy másik adatkészletet hoz létre. Oracle adott adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakaszban.
4. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. A példában azt korábban említettük OracleSource forrás-és BlobSink fogadóként esetében használja a másolási tevékenység. Hasonlóképpen, ha Oracle-adatbázis másolása az Azure Blob Storage-ból, BlobSource és a használata Oraclesinkben a másolási tevékenység. Másolási tevékenység tulajdonságai, amelyek Oracle-adatbázishoz meghatározott, lásd: [másolási tevékenység tulajdonságai](#copy-activity-properties) szakaszban. A forrás vagy a fogadó adattár használatát részletekért kattintson a hivatkozásra az adattár az előző szakaszban. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  Az adatok másolása és- tárolókról a helyszíni Oracle-adatbázishoz használt Data Factory-entitások JSON-definíciói minták, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-oracle-database) című szakaszát.

A következő szakaszok a Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletek:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az alábbi táblázatban a JSON-elemeket Oracle társított szolgáltatás leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonság értékre kell állítani: **OnPremisesOracle** |Igen |
| driverType | Adja meg, mely illesztőprogram-adatok másolása Azure blobból vagy az Oracle-adatbázis használatával. Engedélyezett értékek a következők **Microsoft** vagy **ODP** (alapértelmezett). Lásd: [verziójától és a telepítés támogatott](#supported-versions-and-installation) illesztőprogram adatai szakaszán. | Nem |
| kapcsolati Sztringje | Adja meg a connectionString tulajdonság az Oracle Database-példányhoz való kapcsolódáshoz szükséges adatokat. | Igen |
| átjáró neve | Az átjáró a helyszíni Oracle-kiszolgálóhoz való csatlakozáshoz használt név |Igen |

**Példa: Microsoft-illesztőprogram használatával:**

>[!TIP]
>Ha eléri hiba üzenettel "ORA-01025: UPI paraméter engedélyezett tartományon kívül esik" és az Oracle-verzió 8i, adjon hozzá `WireProtocolMode=1` a kapcsolati karakterláncot, és próbálkozzon újra.

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

**Példa: ODP illesztőprogram segítségével**

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
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (Oracle, az Azure blob-, az Azure table-, stb.).

A typeProperties szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyére vonatkozó információkat. A typeProperties szakasz az adatkészlet típusa OracleTable a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |Az Oracle-adatbázis, amelyre a társított szolgáltatás hivatkozik a tábla neve. |Nem (Ha **oracleReaderQuery** , **OracleSource** van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és a házirend az összes típusú tevékenységek érhetők el.

> [!NOTE]
> A másolási tevékenység csak egy bemenettel rendelkezik, és csak egy kimenetet.

Mivel a tevékenység a typeProperties szakasz tulajdonságai tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

### <a name="oraclesource"></a>OracleSource
A másolási tevékenység, ha a forrás típusa **OracleSource** érhetők el a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| oracleReaderQuery |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: válassza ki * from tábla <br/><br/>Ha nincs megadva, az SQL-utasítás végrehajtott: válassza ki * from tábla |Nem (Ha **tableName** , **adatkészlet** van megadva) |

### <a name="oraclesink"></a>Oraclesinkben
**Oraclesinkben** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| writeBatchTimeout |Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot. |időtartam<br/><br/> . Példa: 00:30:00 (30 perc). |Nem |
| writeBatchSize |Amikor a puffer mérete eléri a writeBatchSize adatok beszúrása SQL-táblát. |Egész szám (sorok száma) |Nem (alapértelmezett: 100) |
| sqlWriterCleanupScript |Adjon meg egy lekérdezést a másolási tevékenység végrehajtásához úgy, hogy az adott szeletre vonatkozó adatok törlődnek. |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Adja meg az oszlop nevét adja meg az automatikusan generált szelet azonosítóval, amelyet egy adott szeletre mikor futtassa újra a adatainak a másolási tevékenység. |Egy oszlop binary(32) adattípusú oszlop neve. |Nem |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>Az Oracle-adatbázisába, illetve az adatok másolása JSON példák
Az alábbi példa mintául szolgáló JSON-definíciók, amelyek segítségével létrehoz egy folyamatot használatával tartalmaz [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Ezek bemutatják, hogyan adatokat másolni a/Oracle-adatbázishoz, és-tárolókról az Azure Blob Storage. Azonban adatok átmásolhatók a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Példa: Adatok másolása az Oracle az Azure Blob

A minta az alábbi data factory-entitások rendelkezik:

1. A társított szolgáltatás típusa [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) forrásként és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) pedig a fogadó.

A minta adatokat másol egy helyszíni Oracle-adatbázis egy táblájából blob óránként. További információ a különböző tulajdonságok a mintában használt a minták a következő szakaszokban dokumentációjában talál.

**Oracle-társított szolgáltatást:**

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

**Az Azure Blob storage-beli társított szolgáltatást:**

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

A minta azt feltételezi, létrehozott egy táblát "MyTable" az Oracle-ben és a egy idősorozat-adatok a "timestampcolumn" nevű oszlopot tartalmaz.

Beállítás az "external": "true" tájékoztatja a Data Factory szolgáltatásban, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

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

**Azure blobkimeneti adatkészlet:**

Adatok írása egy új blob minden órában (frequency: óra, az interval: 1). A mappa elérési útját és nevét a BLOB dinamikusan a feldolgozás alatt álló szelet kezdő időpontja alapján értékeli ki. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

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

**Másolási tevékenységgel rendelkező folyamat:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és a tervek szerint óránkénti tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **OracleSource** és **fogadó** típusa **BlobSink**.  Az SQL-lekérdezést a megadott **oracleReaderQuery** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

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

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Példa: Adatok másolása Azure blobból és Oracle-ig
Ez a példa bemutatja, hogyan másolhat adatokat egy Azure Blob Storage-ból a helyszíni Oracle-adatbázishoz. Azonban az adatok átmásolhatók **közvetlenül** bármelyik megadott forrás [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.  

A minta az alábbi data factory-entitások rendelkezik:

1. A társított szolgáltatás típusa [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) forrásként [Oraclesinkben](data-factory-onprem-oracle-connector.md#copy-activity-properties) pedig a fogadó.

A minta adatokat másol egy blobot egy helyszíni Oracle database egyik táblájába óránként. További információ a különböző tulajdonságok a mintában használt a minták a következő szakaszokban dokumentációjában talál.

**Oracle-társított szolgáltatást:**
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

**Az Azure Blob storage-beli társított szolgáltatást:**
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

**Azure blobbemeneti adatkészlet**

Adatok felülettől új blob minden órában (frequency: óra, interval: 1). A mappa elérési útját és nevét a BLOB dinamikusan a feldolgozás alatt álló szelet kezdő időpontja alapján értékeli ki. A mappa elérési útjának év, hónap és nap részét a kezdési időpont és fájlnevet a kezdő időpontja óra részét használja. "external": "true" beállítással, hogy ez a táblázat a data factory a külső, és nem egy adat-előállító tevékenység által előállított arról tájékoztatja a Data Factory szolgáltatásban.

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

A minta azt feltételezi, hogy létrehozott egy "MyTable" tábla az Oracle-ben. A tábla létrehozásához azonos számú oszlopot az Oracle, a Blob CSV-fájl tartalmazza a várt módon. Új sorok hozzáadódnak a tábla minden órában.

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

**Másolási tevékenységgel rendelkező folyamat:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **BlobSource** és a **fogadó** típusa **Oraclesinkben**.  

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
### <a name="problem-1-net-framework-data-provider"></a>1. hiba: .NET-keretrendszer adatszolgáltatója

Megjelenik a következő **hibaüzenet**:

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Lehetséges okok:**

1. Oracle .NET Framework Data Provider nem lett telepítve.
2. A .NET keretrendszer adatszolgáltatója az Oracle lett telepítve a .NET-keretrendszer 2.0, és nem található a .NET keretrendszer 4.0-s mappákat.

**Megoldás/megkerülő megoldás:**

1. Ha még nem telepítette, Oracle, a .NET-szolgáltatója [telepítheti](http://www.oracle.com/technetwork/topics/dotnet/downloads/) , és ismételje meg a forgatókönyvet.
2. Ha a következő hibaüzenet jelenik meg a szolgáltató telepítése után, kövesse az alábbi lépéseket:
   1. Nyissa meg a gép konfigurációs .NET 2.0 a mappából: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Keresse meg **Oracle-adatszolgáltató a .NET-hez**, és a egy bejegyzés található, ahogyan az alábbi minta alapján elvileg **system.data** -> **DbProviderFactories**: "<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle-adatszolgáltató a .NET-hez" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Ez a bejegyzés átmásolása a machine.config fájlban a következő 4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config, és módosítsa a 4.xxx.x.x verzióra.
4. "< ODP.NET telepített elérési útja > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll" telepítse azokat a globális szerelvény-gyorsítótárban (GAC) futtatásával `gacutil /i [provider path]`. ## hibaelhárítási tippek

### <a name="problem-2-datetime-formatting"></a>2. hiba: dátum és idő formázása

Megjelenik a következő **hibaüzenet**:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Megoldás/megkerülő megoldás:**

Szükség lehet módosítani a lekérdezési karakterláncot a a másolási tevékenység alapján dátumok konfigurációjától az Oracle-adatbázis (a to_date függvény használatával) a következő mintában látható módon:

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Oracle-leképezés típusa
Említetteknek megfelelően az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikk másolási tevékenység hajt végre automatikus típuskonverziók a fogadó-típusokat az alábbi 2. lépés – a módszert használja a forrás típusa:

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

Adatok áthelyezése az Oracle, amikor a következő hozzárendeléseket használják Oracle adattípust .NET típusát, és ez fordítva is igaz.

| Oracle-adattípus | .NET-keretrendszer adattípus |
| --- | --- |
| BFILE |Byte] |
| BLOB |Byte]<br/>(csak támogatott Oracle 10g és magasabb akkor, ha a Microsoft-illesztőprogram használatával) |
| CHAR |Sztring |
| CLOB |Sztring |
| DATE |DateTime |
| LEBEGŐPONTOS |Tizedes tört, karakterlánc (Ha a pontosság > 28) |
| EGÉSZ SZÁM |Tizedes tört, karakterlánc (Ha a pontosság > 28) |
| INTERVALLUM ÉV, HÓNAP |Int32 |
| INTERVALLUM NAP – MÁSODPERC |Időtartam |
| HOSSZÚ |Sztring |
| MENNYI IDEIG NYERS |Byte] |
| NCHAR |Sztring |
| NCLOB |Sztring |
| SZÁM |Tizedes tört, karakterlánc (Ha a pontosság > 28) |
| NVARCHAR2 |Sztring |
| RAW |Byte] |
| }, ROWID |Sztring |
| IDŐBÉLYEG |DateTime |
| A HELYI IDŐZÓNA IDŐBÉLYEG |DateTime |
| AZ IDŐZÓNA IDŐBÉLYEG |DateTime |
| ELŐJEL NÉLKÜLI EGÉSZ SZÁM |Szám |
| VARCHAR2 |Sztring |
| XML |Sztring |

> [!NOTE]
> Adattípus **IDŐKÖZ YEAR TO hónap** és **IDŐKÖZ nap TO második** nem támogatottak, ha a Microsoft-illesztőprogram használatával.

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
Fogadó-adatkészlet oszlopaihoz forrásadatkészlet leképezés oszlopai kapcsolatos további információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források megismételhető olvasása
Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell. Lásd: [olvasni a relációs források Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.

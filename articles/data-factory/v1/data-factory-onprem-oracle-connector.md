---
title: Adatok másolása az Oracle-nek vagy onnan a Data Factory használatával
description: Ismerje meg, hogyan másolhat adatokat egy helyszíni Oracle-adatbázisba az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 066e32d5ab21f88b170498173606043c54fec586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265856"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Adatok másolása az Oracle helyszíni vagy onnan az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-oracle-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-oracle.md)

> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha az Azure Data Factory szolgáltatás aktuális verzióját használja, tekintse meg az [Oracle-összekötő t a V2-ben.](../connector-oracle.md)


Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése, vagy egy helyszíni Oracle-adatbázis. A cikk [az adatmozgatási tevékenységekre](data-factory-data-movement-activities.md)épül, amely általános áttekintést nyújt az adatok mozgásáról a Másolási tevékenység használatával.

## <a name="supported-scenarios"></a>Támogatott esetek

Az Oracle *adatbázisból* adatokat másolhat a következő adattárakba:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból adatokat másolhat *oracle adatbázisba:*

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Előfeltételek

A Data Factory támogatja a helyszíni Oracle-forrásokhoz való csatlakozást a Data Management Gateway használatával. Az [Adatkezelési átjáróról](data-factory-data-management-gateway.md) további információért tekintse meg az Adatkezelési átjárót. Az adatok áthelyezése a [helyszíni adatokról](data-factory-move-data-between-onprem-and-cloud.md)a felhőbe című témakörben részletesútmutatást talál arról, hogyan állíthatja be az átjárót az adatfolyamatban az adatok áthelyezéséhez.

Az átjáró akkor is szükséges, ha az Oracle üzemelteti az Azure-infrastruktúra szolgáltatásként (IaaS) virtuális gép. Az átjáró t az adattárban lévő IaaS virtuális gépre vagy egy másik virtuális gépre telepítheti, feltéve, hogy az átjáró csatlakozhat az adatbázishoz.

> [!NOTE]
> A kapcsolattal és az átjáróval kapcsolatos problémák elhárításával kapcsolatos tippek az [átjáróval kapcsolatos problémák elhárítása című](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)témakörben találhatók.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés

Ez az Oracle csatlakozó az illesztőprogramok két verzióját támogatja:

- **Microsoft driver for Oracle (ajánlott)**: A Data Management Gateway 2.7-es verziójától kezdve az Oracle microsoftos illesztőprogramja automatikusan települ az átjáróval. Az Oracle-hez való csatlakozás létrehozásához nem kell telepítenie vagy frissítenie az illesztőprogramot. Az illesztőprogram használatával jobb másolási teljesítményt is megtapasztalhat. Az Oracle adatbázisok ezen verziói támogatottak:
  - Oracle 12c R1 (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Az Oracle proxykiszolgáló nem támogatott.

    > [!IMPORTANT]
    > Jelenleg az Oracle Microsoft illesztőprogramja csak az Oracle-től származó adatok másolását támogatja. Az illesztőprogram nem támogatja az Oracle írását. Az Adatkezelési átjáró **diagnosztika** lapján található tesztkapcsolati funkció nem támogatja ezt az illesztőprogramot. Másik lehetőségként a Másolás varázslóval ellenőrizheti a kapcsolatot.
    >

- **Oracle Data Provider for .NET**: Az Oracle Data Provider segítségével adatokat másolhat az Oracle-től vagy az Oracle-nek. Ez az összetevő a [Windows Oracle Data Access Components](https://www.oracle.com/technetwork/topics/dotnet/downloads/)részét képezi. Telepítse a megfelelő verziót (32 vagy 64 bites) arra a gépre, amelyen az átjáró telepítve van. [Az Oracle Data Provider .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) hozzáférhet az Oracle Database 10g Release 2 és újabb verziókhoz.

    Ha az XCopy telepítése lehetőséget **választja,** hajtsa végre a readme.htm fájlban ismertetett lépéseket. Azt javasoljuk, hogy válassza ki a telepítőt, amely rendelkezik a felhasználói felület (nem az XCopy telepítő).

    A szolgáltató telepítése után indítsa újra a Data Management Gateway host szolgáltatást a számítógépen a Szolgáltatások kisalkalmazás vagy az Adatkezelési átjáró konfigurációkezelője segítségével.

Ha a Másolás varázslóval készíti a másolási folyamatot, az illesztőprogram típusa automatikusan meg lesz határozva. A Microsoft-illesztőprogram alapértelmezés szerint használatos, kivéve, ha az átjáró verziója korábbi a 2.7-es verziónál, vagy ha az Oracle-t választja fogadóként.

## <a name="get-started"></a>Bevezetés

Létrehozhat egy másolási tevékenységet tartalmazó folyamatot. A folyamat az adatokat egy helyszíni Oracle-adatbázisba vagy onnan különböző eszközök vagy API-k használatával helyezi át.

A folyamat létrehozásának legegyszerűbb módja a Másolás varázsló használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) egy gyors forgatókönyvet a folyamat létrehozásához az Adatok másolása varázslóval.

A következő eszközök egyikével is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager-sablon,** **.NET API**vagy REST **API.** Tekintse meg a [Másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) lépésről-lépésre, hogyan hozhat létre egy folyamat, amely egy másolási tevékenységet.

Akár az eszközöket, akár api-kat használja, az alábbi lépéseket úgy hozza létre, hogy olyan folyamatot hozzon létre, amely adatokat helyezi át a forrásadattárból a fogadó adattárba:

1. Hozzon létre egy **adat-előállító**. Az adat-előállító konklúzió egy vagy több folyamatot is tartalmazhat.
2. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására. Ha például adatokat másol egy Oracle-adatbázisból az Azure Blob storage-ba, hozzon létre két összekapcsolt szolgáltatást az Oracle-adatbázis és az Azure storage-fiók és az adat-előállító összekapcsolására. Az Oracle-re jellemző kapcsolt szolgáltatástulajdonságokról a Csatolt szolgáltatás tulajdonságai ( Csatolt szolgáltatás tulajdonságai ) [(Csatolt szolgáltatás tulajdonságai) (Csatolt szolgáltatás tulajdonságai) (Csatolt szolgáltatás tulajdonságai) (Csatolt szolgáltatás tulajdonságai) (Csatolt szolgáltatás tulajdonságai) (Csatolt szolgáltatás tulajdonságai](#linked-service-properties)
3. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. Az előző lépésben található példában hozzon létre egy adatkészletet, amely megadja a bemeneti adatokat tartalmazó táblát az Oracle-adatbázisban. Hozzon létre egy másik adatkészletet a blobtároló és az Oracle-adatbázisból másolt adatokat tároló mappa megadásához. Az Oracle-re jellemző adatkészlet-tulajdonságokról az [Adatkészlet tulajdonságai (Dataset properties) (Adatkészlet tulajdonságai) (Adatkészlet tulajdonságai) (Dataset properties) (Adatkészlettulajdonságai) (Dataset properties) (Adatkészlet-tulajdonságok) (Dataset](#dataset-properties)
4. Hozzon létre egy **folyamatot,** amely egy másolási tevékenység, amely egy adatkészletet bemenetként, és egy adatkészletkimenetként. Az előző példában az **OracleSource-t** használja forrásként, **a BlobSink-et** pedig a másolási tevékenység fogadójaként. Hasonlóképpen, ha az Azure Blob storage-ból egy Oracle-adatbázisba másol, a **BlobSource** és az **OracleSink** a másolási tevékenységben használja. Az Oracle-adatbázisra jellemző tevékenységek másolási tulajdonságairól a [Tevékenység tulajdonságainak másolása (Másolási tulajdonságok) (Tevékenység tulajdonságainak másolása) témakörben](#copy-activity-properties)találhatók. Az adattár forrásként vagy fogadóként való használatáról az előző szakaszban található hivatkozást.

A varázsló használatakor a Data Factory entitásokhoz tartozó JSON-definíciók automatikusan létrejönnek: csatolt szolgáltatások, adatkészletek és a folyamat. Eszközök vagy API-k használataesetén (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. Azokat a mintákat, amelyek JSON-definíciók data factory entitások, amelyek segítségével adatok másolása egy helyszíni Oracle-adatbázis, lásd: JSON példák.

A következő szakaszok a Data Factory entitások definiálásához használt JSON-tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az alábbi táblázat az Oracle kapcsolódó szolgáltatására jellemző JSON-elemeket ismerteti:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A **típustulajdonságot** **OnPremisesOracle (OnPremisesOracle**) tulajdonságra kell állítani. |Igen |
| driverType (illesztőprogram típusa) | Adja meg, hogy melyik illesztőprogramot használja az Oracle-adatbázisból vagy -ba történő másoláshoz. Az engedélyezett értékek a **Microsoft** és **az ODP** (alapértelmezett). Az illesztőprogram részleteiről a [Támogatott verzió és telepítés](#supported-versions-and-installation) című témakörben talál. | Nem |
| connectionString (kapcsolati karakterlánc) | Adja meg a **connectionString** tulajdonság Oracle adatbázispéldányához való csatlakozáshoz szükséges adatokat. | Igen |
| átjárónév | A helyszíni Oracle-kiszolgálóhoz való csatlakozáshoz használt átjáró neve. |Igen |

**Példa: A Microsoft-illesztőprogram használata**

> [!TIP]
> Ha az "ORA-01025: UPI paraméter hatótávolságon kívül" hibaüzenet jelenik meg, `WireProtocolMode=1` és az Oracle 8i-as verziójú, adja hozzá a kapcsolati karakterlánchoz, és próbálkozzon újra:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Példa: Az ODP-illesztőprogram használata**

Az engedélyezett formátumokról a [.NET ODP oracle adatszolgáltatója](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek létrehozása című](data-factory-create-datasets.md)témakörben található.

A JSON-fájl adatkészletszakaszai, például a struktúra, az elérhetőség és a szabályzat, minden adatkészlettípushoz hasonlóak (például az Oracle, az Azure Blob storage és az Azure Table storage esetében).

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. Az **OracleTable** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A csatolt szolgáltatás által hivatkozott Oracle-adatbázisban lévő tábla neve. |Nem (ha **az oracleReaderQuery** vagy az **OracleSource** meg van adva) |

## <a name="copy-activity-properties"></a>Tevékenység tulajdonságainak másolása

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok létrehozása című témakörben található.](data-factory-create-pipelines.md)

Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák és házirend állnak rendelkezésre minden típusú tevékenységek.

> [!NOTE]
> A Másolási tevékenység csak egy bemenetet vesz igénybe, és csak egy kimenetet hoz létre.

A tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. A másolási tevékenység tulajdonságai a forrás és a fogadó típusától függően változnak.

### <a name="oraclesource"></a>OracleForrás

A Másolási tevékenység ben, ha a forrás az **OracleSource** típus, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| oracleReaderQuery |Az adatok olvasásához használja az egyéni lekérdezést. |SQL-lekérdezési karakterlánc. Például a \* "select from **MyTable".** <br/><br/>Ha nincs megadva, ez az SQL \* utasítás végrehajtása: "válassza ki a **MyTable"** |Nem<br />(ha **a tableName** of **dataset** meg van adva) |

### <a name="oraclesink"></a>OracleMosogató

**Az OracleSink** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| writeBatchTimeout |A kötegbehelyezési művelet befejezéséhez szükséges várakozási idő, mielőtt az időtúljárna. |**időtartomány**<br/><br/> Példa: 00:30:00 (30 perc) |Nem |
| writeBatchSize |Adatokat szúr be az SQL táblába, amikor a puffermérete eléri a **writeBatchSize**értékét. |Egész szám (sorok száma) |Nem (alapértelmezett: 100) |
| sqlWriterCleanupScript |Megadja a végrehajtandó másolási tevékenység lekérdezését, hogy egy adott szelet adatai törlődjenek. |Lekérdezési utasítás. |Nem |
| sliceIdentifierColumnName |Megadja az automatikusan létrehozott szeletazonosítóval kitöltendő másolási tevékenység oszlopnevét. A **sliceIdentifierColumnName** értéke egy adott szelet adatainak tisztítására szolgál az újrafuttatáskor. |A **bináris(32)** adattípusú oszlop oszlopneve. |Nem |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>JSON-példák az Oracle adatbázisba és az Oracle adatbázisból történő másoláshoz

Az alábbi példák minta JSON-definíciókat tartalmaznak, amelyek segítségével folyamatot hozhat létre a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. A példák bemutatják, hogyan másolhat adatokat egy Oracle-adatbázisból vagy -ba, illetve az Azure Blob storage-ból. Az adatok azonban a [Támogatott adattárakban és formátumokban](data-factory-data-movement-activities.md#supported-data-stores-and-formats) felsorolt fogadók bármelyikébe átmásolhatók az Azure Data Factory másolási tevékenység használatával.

**Példa: Adatok másolása az Oracle-től az Azure Blob storage-ba**

A minta a következő Data Factory entitásokkal rendelkezik:

* [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties)típusú összekapcsolt szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
* [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
* Olyan [folyamat,](data-factory-create-pipelines.md) amelynek másolási tevékenysége [az OracleSource-t](data-factory-onprem-oracle-connector.md#copy-activity-properties) használja forrásként és [BlobSink-t](data-factory-azure-blob-connector.md#copy-activity-properties) fogadóként.

A minta adatokat másol egy táblából egy helyszíni Oracle-adatbázisban egy blob óránként. A mintában használt különböző tulajdonságokról a mintákat követő szakaszokban talál további információt.

**Oracle kapcsolt szolgáltatás**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure Blob storage-hoz csatolt szolgáltatás**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Oracle bemeneti adatkészlet**

A minta feltételezi, hogy létrehozott egy **MyTable** nevű táblát az Oracle-ben. Az **idősorozat-adatok időbélyegoszlopnevű** oszlopát tartalmazza.

**Külső**beállítása: **true** tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet külső adat-előállító, és hogy az adatkészlet nem az adat-előállító tevékenység által előállított.

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

**Azure blobkimeneti adatkészlet**

Az adatok óránként új blobba vannak írva (**gyakoriság:** **óra**, **időköz**: **1**). A blob mappaelérési útja és fájlneve dinamikusan kiértékelésre kerül a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészét használja.

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

**Másolási tevékenységgel rendelkező folyamat**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futtatásra ütemezve. A folyamat JSON-definíciójában a **forrástípus** **OracleSource** lesz állítva, a **fogadó** típusa pedig **BlobSink**. Az **oracleReaderQuery** tulajdonsággal megadott SQL-lekérdezés kiválasztja a másolni kívánt adatokat az elmúlt órában.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
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

**Példa: Adatok másolása az Azure Blob storage-ból az Oracle-nek**

Ez a minta bemutatja, hogyan másolhat adatokat egy Azure Blob-tárfiókból egy helyszíni Oracle-adatbázisba. Azonban *közvetlenül* másolhatja az adatokat a [Támogatott adattárakban és formátumokban](data-factory-data-movement-activities.md#supported-data-stores-and-formats) felsorolt források bármelyikéből az Azure Data Factory másolási tevékenység használatával.

A minta a következő Data Factory entitásokkal rendelkezik:

* [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties)típusú összekapcsolt szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
* [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
* Olyan [folyamat,](data-factory-create-pipelines.md) amely nek van egy másolási tevékenysége, amely [a BlobSource-ot](data-factory-azure-blob-connector.md#copy-activity-properties) használja forrás [OracleSink-ként](data-factory-onprem-oracle-connector.md#copy-activity-properties) fogadóként.

A minta óránként átmásolja az adatokat egy blobból egy helyszíni Oracle-adatbázis táblájára. A mintában használt különböző tulajdonságokról a mintákat követő szakaszokban talál további információt.

**Oracle kapcsolt szolgáltatás**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure Blob storage-hoz csatolt szolgáltatás**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Azure blobbemeneti adatkészlet**

Az adatokat óránként veszik fel egy új blobból (**gyakoriság:** **óra**, **időköz**: **1**). A blob mappaelérési útja és fájlneve dinamikusan kiértékelésre kerül a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év-, hónap- és naprészét használja. A fájlnév a kezdési időpont órarészét használja. A **beállítás külső:** **true** tájékoztatja a Data Factory szolgáltatást, hogy ez a tábla az adat-előállító, és nem az adat-előállító tevékenység által előállított.

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

**Oracle kimeneti adatkészlet**

A minta feltételezi, hogy létrehozott egy **MyTable** nevű táblát az Oracle-ben. Hozza létre a táblát az Oracle-ben ugyanannyi oszloppal, mint ablob CSV-fájl. A rendszer óránként új sorokat ad a táblához.

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

**Másolási tevékenységgel rendelkező folyamat**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A json-definícióban a **forrástípus** **BlobSource,** a **fogadó** típusa pedig **OracleSink**lesz.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
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

### <a name="problem-1-net-framework-data-provider"></a>1. probléma: .NET keretrendszer-adatszolgáltató

**Hibaüzenet**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Lehetséges okok**

* A .NET Framework Data Provider for Oracle nincs telepítve.
* A .NET Framework Data Provider for Oracle a .NET Framework 2.0 rendszerre lett telepítve, és nem található meg a .NET Framework 4.0 mappáiban.

**Resolution** (Osztás)

* Ha még nem telepítette a .NET Provider for Oracle programot, [telepítse azt,](https://www.oracle.com/technetwork/topics/dotnet/downloads/)majd próbálkozzon újra a forgatókönyvvel.
* Ha a hibaüzenet a szolgáltató telepítése után is megjelenik, hajtsa végre az alábbi lépéseket:
    1. Nyissa meg a .NET 2.0 számítógép konfigurációs\>fájlját a <rendszerlemez mappájából :\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Keresse meg **az Oracle Data Provider for .NET**. Meg kell tudnia találni egy bejegyzést, amint az a **rendszer.data** > **DbProviderFactories**alábbi mintában látható:`<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Másolja a bejegyzést a machine.config fájlba a következő .NET\>4.0 mappába: <rendszerlemez :\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Ezután változtassa meg a verziót 4.xxx.x.x.
* Telepítse <ODP.NET\>Telepített elérési utat \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll a globális összeállítási gyorsítótárban (GAC) a **gacutil /i [szolgáltató elérési útja]** futtatásával.

### <a name="problem-2-datetime-formatting"></a>2. probléma: Dátum/idő formázása

**Hibaüzenet**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Resolution** (Osztás)

Előfordulhat, hogy módosítania kell a lekérdezési karakterláncot a másolási tevékenységben a dátumok Oracle-adatbázisban való konfigurálásának módját alapulva. Íme egy példa (a **to_date** függvény használatával):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Típusleképezés az Oracle-hez

Az [Adatmozgatási tevékenységekben](data-factory-data-movement-activities.md)említettek szerint a Másolási tevékenység automatikus típuskonverziót hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítés használatával:

1. Konvertálás natív forrástípusokról .NET típusra.
2. Konvertálás a .NET típusról a natív fogadótípusra.

Amikor adatokat helyez át az Oracle-ből, a rendszer a következő leképezéseket használja az Oracle adattípusból a .NET típusba, és fordítva:

| Oracle adattípus | .NET Framework adattípus |
| --- | --- |
| BFILE |Bájt[] |
| Blob |Bájt[]<br/>(csak Microsoft-illesztőprogram használata esetén támogatott Oracle 10g és újabb verziókban) |
| Char |Sztring |
| CLOB |Sztring |
| DATE |DateTime |
| Úszó |Decimális, Karakterlánc (ha a pontosság 28 >) |
| EGÉSZ SZÁM |Decimális, Karakterlánc (ha a pontosság 28 >) |
| INTERVALLUM ÉVRŐL HÓNAPRA |Int32 |
| IDŐKÖZ NAPTÓL A MÁSODIKIG |időtartam |
| Hosszú |Sztring |
| HOSSZÚ NYERS |Bájt[] |
| Nchar |Sztring |
| NCLOB között |Sztring |
| Szám |Decimális, Karakterlánc (ha a pontosság 28 >) |
| NVARCHAR2 között |Sztring |
| Nyers |Bájt[] |
| SORAZONOSÍTÓ |Sztring |
| Időbélyeg |DateTime |
| IDŐBÉLYEG HELYI IDŐZÓNÁVAL |DateTime |
| IDŐBÉLYEG IDŐZÓNÁVAL |DateTime |
| Aláíratlan egész szám |Szám |
| VARCHAR2 között |Sztring |
| XML |Sztring |

> [!NOTE]
> Microsoft-illesztőprogram használata esetén az **adattípusok évről hónapra,** a **napról a másodpercre** pedig intervallumra vonatkozó akta nem támogatottak.

## <a name="map-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz

Ha többet szeretne tudni arról, hogy a forrásadatkészlet oszlopait a fogadó adatkészlet oszlopaihoz szeretné-e hozzásorolni, olvassa el [az Adatkészletoszlopok leképezése a Data Factory ban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-read-from-relational-sources"></a>Relációs forrásokból ismételhető olvasmony

Amikor a relációs adattárakból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factory ban manuálisan futtathat egy szeletet. Az adatkészlet újrapróbálkozási házirendje is konfigurálható, így a szelet újrafut, ha hiba történik. Amikor egy szeletet manuálisan vagy újrapróbálkozási házirenddel újrafuttat, győződjön meg arról, hogy ugyanazokat az adatokat olvassa be, függetlenül attól, hogy hányszor fut egy szelet. További információ: [Ismételhető olvasva relációs forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás

Tekintse meg a [Másolási tevékenység teljesítmény-és hangolási útmutatót,](data-factory-copy-activity-performance.md) amely ből megtudhatja, hogy milyen kulcsfontosságú tényezők befolyásolják az adatok mozgatását (másolási tevékenység) az Azure Data Factoryban. Az optimalizálás különböző módjairól is megismerheti.

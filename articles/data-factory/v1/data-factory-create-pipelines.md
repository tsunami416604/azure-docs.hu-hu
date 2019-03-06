---
title: Folyamatok létrehozása, ütemezése, a lánc tevékenységek a Data Factoryban |} A Microsoft Docs
description: Ismerkedjen meg az adatfolyamatok létrehozásához az Azure Data Factoryben az adatok áthelyezéséhez és átalakításához. Hozzon létre egy adatvezérelt munkafolyamat készen szükséges információkat használja a.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 65763eb57904210b202fb30b4394deea3a1f7baf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449563"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Az Azure Data Factory folyamatai és tevékenységei
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-create-pipelines.md)
> * [2-es verzió (aktuális verzió)](../concepts-pipelines-activities.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a V2 folyamatai](../concepts-pipelines-activities.md).

Ennek a cikknek a segítségével megismerheti az Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját adatáthelyezési és -feldolgozási forgatókönyveihez.

> [!NOTE]
> Ez a cikk feltételezi, hogy elvégezte [az Azure Data Factory bemutatását](data-factory-introduction.md). Részletezik-a-élmény-adat-előállítók létrehozásakor nem rendelkeznek, ha áthaladna [data transformation oktatóanyag](data-factory-build-your-first-pipeline.md) és/vagy [adatok mozgását útmutató](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) jobban megérthessék, ez a cikk segíthet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. A másolási tevékenység használatával például egy helyszíni SQL Serverből egy Azure Blob Storage-tárolóba másolhatja az adatokat. Ezt követően egy Hive-tevékenység segítségével futtasson egy Hive-szkriptet egy Azure HDInsight fürtön, amely a Blob Storage-ből származó adatokat feldolgozza vagy átalakítja a kimeneti adatok előállításához. Végül egy második másolási tevékenység használatával másolja át a kimeneti adatokat egy olyan Azure SQL Data Warehouse-ba, amelyre üzletiintelligencia-alapú (BI-) jelentéskészítési megoldások épülnek.

Minden tevékenység nulla vagy több bemeneti [adatkészletet](data-factory-create-datasets.md) képes fogadni, és egy vagy több kimeneti [adatkészletet](data-factory-create-datasets.md) képes előállítani. Az alábbi ábrán a folyamat, a tevékenység és az adat-előállító adatkészlete közötti kapcsolat látható:

![Folyamat, tevékenységek és adatkészletek közötti kapcsolat](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

A folyamat lehetővé teszi a tevékenységek külön-külön mindegyik egy készletben kezelhetők. Például telepítheti, ütemezhet, felfüggesztése, és egy folyamatot, helyett a folyamat tevékenységeinek kezelése egymástól függetlenül folytatódik.

A Data Factory két típusú tevékenységet támogat: az adattovábbítási tevékenységeket és az adatátalakítási tevékenységeket. Minden tevékenység nulla vagy több bemeneti rendelkezhet [adatkészletek](data-factory-create-datasets.md) , és egy vagy több kimeneti adatkészletet.

A bemeneti adatkészletek a folyamattevékenységek bemenetét jelölik, a kimeneti adatkészletek pedig a tevékenységek kimeneteit. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Az adatkészlet létrehozását követően használhatja azt egy folyamat tevékenységei esetében. Az adatkészletek lehetnek például egy másolási tevékenység vagy egy HDInsightHive-tevékenység be- vagy kimeneti adatkészletei. Az adatkészletekről további információkat [Az Azure Data Factory adatkészletei](data-factory-create-datasets.md) cikkben talál.

### <a name="data-movement-activities"></a>Adattovábbítási tevékenységek
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> A * jellel ellátott adattárak lehetnek helyszíniek vagy az Azure IaaS részei, és használatukhoz telepíteni kell az [Adatkezelési átjárót](data-factory-data-management-gateway.md) a helyszíni/Azure IaaS gépre.

További információkért tekintse meg az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikket.

### <a name="data-transformation-activities"></a>Adatátalakítási tevékenységek
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

További információkért tekintse meg az [adatátalakítási tevékenységekről](data-factory-data-transformation-activities.md) szóló cikket.

### <a name="custom-net-activities"></a>Egyéni .NET-tevékenységek
Ha át kell helyeznie egy adatok tárolására, hogy a másolási tevékenység nem támogatja, vagy saját logika adatok átalakítása, hozzon létre egy **egyéni .NET-tevékenységet**. További információ az egyéni tevékenységek létrehozásával és használatával kapcsolatban: [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>A folyamatok ütemezése
Folyamat aktív csak között a **start** idő és **záró** idő. Nem hajtotta végre, a kezdési időpont előtt vagy után a Befejezés időpontja. A folyamat szüneteltetve van, ha azt nem végrehajtásra kerülhetnek attól függetlenül, a kezdési és befejezési idő. A folyamat futtatásához akkor kell szüneteltethető. Lásd: [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) , ütemezési és végrehajtási működésének megismerése az Azure Data Factoryban.

## <a name="pipeline-json"></a>A folyamat JSON-fájlja
Nézzük meg közelebbről, hogyan történik egy folyamat JSON-formátumban való meghatározása. A folyamatok általános szerkezete a következő:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets":
        [
        ]
    }
}
```

| Címke | Leírás | Szükséges |
| --- | --- | --- |
| név |A folyamat neve. Adjon meg egy, a folyamat által végrehajtandó műveletet jelölő nevet. <br/><ul><li>Karakterek maximális száma: 260</li><li>Betűvel, számmal vagy aláhúzásjellel kell kezdődnie (\_)</li><li>A következő karakterek nem engedélyezettek: ".", "+","?", "/", "<",">", "\*", "%", "&", ":","\\"</li></ul> |Igen |
| leírás | Adjon meg egy, az adott folyamat alkalmazását leíró szöveget. |Igen |
| tevékenységek | A **tevékenységek** szakaszon belül egy vagy több tevékenység is meghatározható. A tevékenységek JSON-elemeiről részleteit a következő szakaszban talál. | Igen |
| start | Kezdő dátum-idő a folyamat. Kell [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601). Például: `2016-10-14T16:32:41Z`. <br/><br/>Adja meg a helyi időt, például egy becsült idő, lehetőség. Íme egy példa: `2016-02-27T06:00:00-05:00`", azaz 6 Órakor becsült<br/><br/>A kezdő és záró tulajdonságok együtt a a folyamat aktív időszakát határozzák meg. Kimeneti szeleteket csak előállítása az aktív ebben az időszakban. |Nem<br/><br/>Ha megad egy záró tulajdonság értéke, kezdő tulajdonság értékének kell megadnia.<br/><br/>A kezdési és befejezési idejének is lehet üres folyamat létrehozása. A folyamat futtatásához egy aktív időszak beállítása mindkét értéket meg kell adnia. Ha nem adja meg a kezdő és befejező időpontok egy folyamat létrehozásakor beállíthatja azokat később a Set-AzDataFactoryPipelineActivePeriod parancsmaggal. |
| vége | Záró dátum és időpont a folyamat. Ha meg van adva, ISO formátumban kell megadni. Például:`2016-10-14T17:32:41Z` <br/><br/>Adja meg a helyi időt, például egy becsült idő, lehetőség. Íme egy példa: `2016-02-27T06:00:00-05:00`, azaz 6 Órakor becsült<br/><br/>A folyamat határozatlan ideig történő futtatásához, adja meg a 9999-09-09 end tulajdonság értékeként. <br/><br/> A folyamat akkor aktív, csak a kezdő és befejező időpontja között. Nem hajtotta végre, a kezdési időpont előtt vagy után a Befejezés időpontja. A folyamat szüneteltetve van, ha azt nem végrehajtásra kerülhetnek attól függetlenül, a kezdési és befejezési idő. A folyamat futtatásához akkor kell szüneteltethető. Lásd: [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) , ütemezési és végrehajtási működésének megismerése az Azure Data Factoryban. |Nem <br/><br/>Ha megadja a (Kezdés) tulajdonság értékét, a végfelhasználók tulajdonság értéke kell megadnia.<br/><br/>Tekintse meg a megjegyzéseit a **start** tulajdonság. |
| isPaused | Ha igaz értékű, a folyamat nem futtatható. A felfüggesztett állapotban van. Alapértelmezett érték = false. Ez a tulajdonság segítségével engedélyezheti vagy tilthatja le a folyamatot. |Nem |
| pipelineMode | Az ütemezés a folyamat futtatásának módja. Engedélyezett értékek a következők: ütemezett (alapértelmezett), Item parancsot.<br/><br/>"Ütemezett", az azt jelzi, hogy a folyamat megadott időközönként az aktív időtartam (kezdő és záró idő) megfelelően fut-e. "Egyszeri" azt jelzi, hogy a folyamat csak egyszer fut-e. Ezután onetime folyamatok nem lehet módosítani vagy frissíteni jelenleg. Lásd: [Onetime folyamat](#onetime-pipeline) onetime beállítás részleteit. |Nem |
| expirationTime | Időtartam, amely a létrehozás után a [egyszeri folyamat](#onetime-pipeline) érvényes, és a kiépített kell maradniuk. Ha nem rendelkezik minden aktív, sikertelen, vagy függőben lévő futtatásának, a folyamat automatikusan törlése után éri el a lejárati időt. Az alapértelmezett érték: `"expirationTime": "3.00:00:00"`|Nem |
| adatkészletek |A folyamat meghatározott tevékenységek által használható adatkészletek listája. Ez a tulajdonság segítségével, amely az adatcsatorna adott és az adat-előállító nem meghatározott adatkészletet határoz meg. Ebben az adatcsatornában meghatározott adatkészletek csak használható ez a folyamat, és nem oszthatók meg. Lásd: [hatókörrel rendelkező adatkészletek](data-factory-create-datasets.md#scoped-datasets) részleteiről. |Nem |

## <a name="activity-json"></a>Tevékenység JSON-fájlja
A **tevékenységek** szakaszon belül egy vagy több tevékenység is meghatározható. Minden tevékenység rendelkezik az alábbi felső szintű struktúrával:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

Az alábbi táblában a tevékenység JSON-definíciójában lévő tulajdonságok szerepelnek:

| Címke | Leírás | Szükséges |
| --- | --- | --- |
| név | A tevékenység neve. Adjon meg egy, a tevékenység által végrehajtandó műveletet jelölő nevet. <br/><ul><li>Karakterek maximális száma: 260</li><li>Betűvel, számmal vagy aláhúzásjellel kell kezdődnie (\_)</li><li>A következő karakterek nem engedélyezettek: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Igen |
| leírás | Az adott tevékenységet vagy annak alkalmazását leíró szöveg |Igen |
| type | A tevékenység típusa. Tekintse meg a [adattovábbítási tevékenységek](#data-movement-activities) és [adat-átalakítási tevékenységeket](#data-transformation-activities) tevékenységek a különböző tevékenységtípusokkal kapcsolatban. |Igen |
| bemenetek |A tevékenység által használt bemeneti táblák<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Igen |
| kimenetek |A tevékenység által használt kimeneti táblák.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Igen |
| linkedServiceName |A tevékenység által használt társított szolgáltatás neve. <br/><br/>Egy adott tevékenység megkövetelheti annak a társított szolgáltatásnak a megadását, amely a szükséges számítási környezethez kapcsolódik. |Igen, a HDInsight-tevékenység és az Azure Machine Learning kötegelt pontozási tevékenység <br/><br/>Minden egyéb esetében: nem |
| typeProperties |A tulajdonságok a **typeProperties** szakasz függenek a tevékenység típusa. Az adott tevékenység típustulajdonságainak megtekintéséhez kattintson az előző szakaszban szereplő tevékenységhivatkozásokra. | Nem |
| szabályzat |Olyan szabályzatok, amelyek az adott tevékenység futásidejű viselkedését befolyásolják. Ha nincs megadva, az alapértelmezett házirendek használhatók. |Nem |
| a Scheduler | "a scheduler" tulajdonság a tevékenység kívánt ütemezés meghatározására szolgál. A altulajdonságok ugyanazok, mint az a [rendelkezésre állási tulajdonságot egy adatkészlet](data-factory-create-datasets.md#dataset-availability). |Nem |

### <a name="policies"></a>Házirendek
Házirendek egy tevékenység futásidejű viselkedését befolyásolják, kifejezetten egy tábla a szelet feldolgozása során. Az alábbi táblázatban a részleteket.

| Tulajdonság | Megengedett értékek | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| Egyidejűség |Egész szám <br/><br/>A maximális érték: 10 |1 |A tevékenység párhuzamos végrehajtások száma.<br/><br/>Ez határozza meg, amely akkor fordulhat elő, a másik szeletek párhuzamos tevékenység-végrehajtások száma. Például ha egy tevékenység kell áthaladnia rengeteg rendelkezésre álló adatok, a nagyobb párhuzamosság értéke felgyorsítja az adatfeldolgozás. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Meghatározza, hogy a feldolgozott adatszelet rendezése.<br/><br/>Például ha 2 szeletekre (du. 4: az egyik oka és a egy másik, 17: 00), és mindkettő végrehajtás függőben. A executionPriorityOrder NewestFirst kell állít be, ha a szelet délután 5-kor lesz elsőként feldolgozva. Hasonlóképpen ha beállította a executionPriorityORder OldestFIrst kell, majd du. 4: a szeletet dolgoz fel. |
| retry |Egész szám<br/><br/>A maximális érték 10 lehet. |0 |Mielőtt az adatfeldolgozás a szelet hiba van megjelölve. az újrapróbálkozások száma. Tevékenység-végrehajtási adatszelet rendszer legfeljebb a megadott újrapróbálkozások számát. Az újrapróbálkozás történik, a hiba után minél hamarabb. |
| timeout |TimeSpan |00:00:00 |A tevékenység időkorlátja. Példa: 00:10:00 (magában foglalja a időkorlátja 10 perc)<br/><br/>Ha egy érték nincs megadva vagy 0, az időtúllépési érték a végtelen.<br/><br/>Ha a szelet adatok feldolgozási idő meghaladja az időtúllépés értéke, meg lett szakítva, és a rendszer megpróbálja próbálkozzon újra a feldolgozást. Az újrapróbálkozások száma attól függ, hogy az újrapróbálkozási tulajdonság. Időtúllépés történik, ha az állapot értéke időtúllépés miatt megszakadt. |
| késleltetés |TimeSpan |00:00:00 |Adja meg a késleltetés, elindul a szelet feldolgozásának előtt.<br/><br/>Adatszelet tevékenység végrehajtása után a késleltetési idő legyen a várt végrehajtási időn túli elindult.<br/><br/>Példa: 00:10:00 (magában foglalja a késés 10 perc) |
| longRetry |Egész szám<br/><br/>A maximális érték: 10 |1 |A szelet végrehajtása előtt hosszú újrapróbálkozási kísérletek száma.<br/><br/>longRetry kísérletek által longRetryInterval elosztásban. Ezért ha egy újrapróbálkozási kísérletek közötti időre van szüksége, a longRetry. Ha az újrapróbálkozás és longRetry is meg van adva, egyes longRetry kísérletek magában foglalja az újrapróbálkozási kísérletek és kísérletek maximális számát. a rendszer újrapróbálkozik * longRetry.<br/><br/>Például ha a tevékenységszabályzat is van a következő beállításokat:<br/>Ismételje meg: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Tegyük fel, hogy csak egyetlen szeletet végrehajtására van (Várakozás állapot) és a tevékenység-végrehajtási minden alkalommal sikertelen lesz. Kezdetben lenne 3 egymást követő végrehajtási kísérlet. Minden kísérlet után a szelet állapota lenne, próbálkozzon újra. Első 3 kísérletek esnek, miután a szelet állapota LongRetry lehet.<br/><br/>Egy óra (azaz longRetryInteval a érték) egy másik hárompéldányos készletet 3 egymást követő végrehajtási kísérlet lenne. Ezt követően a szelet állapota szeretné végrehajtani, és nincs további újrapróbálkozások volna lehetséges. Ezért a teljes 6 kísérlet történt.<br/><br/>Minden olyan végrehajtása sikeres, ha a szelet állapota kész lenne, és nincs további próbálkozások nem megkísérlése.<br/><br/>a függő adatok érkeznek nem determinisztikus időpontokban vagy a teljes környezet nem flaky akkor fordul elő, hogy mely adatok feldolgozása a longRetry használni. Ezekben az esetekben újrapróbálkozások egymás után nem segíthet ezzel és a egy időszak után így időben a kívánt kimenetet eredményez.<br/><br/>Legyen körültekintő, Word: nincs beállítva a longRetry, longRetryInterval vagy nagy értékeket. Általában a magasabb értékek hasonló más rendszerből adódó problémákat. |
| longRetryInterval |TimeSpan |00:00:00 |Hosszú újrapróbálkozás kísérletek közötti késleltetés |

## <a name="sample-copy-pipeline"></a>Minta másolási folyamat
Az alábbi mintafolyamat **tevékenységek** szakaszában egyetlen **Másolás** típusú tevékenység található. Ebben a mintában a [másolási tevékenység](data-factory-data-movement-activities.md) adatokat másol egy Azure blobtárolóból egy Azure SQL-adatbázisba.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

Vegye figyelembe a következő szempontokat:

* A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa** **Copy** értékre van beállítva.
* A tevékenység bemenetének beállítása **InputDataset**, a kimeneté pedig **OutputDataset**. Az adatkészletek JSON-fáljban történő meghatározását lásd az [Adatkészletek](data-factory-create-datasets.md) cikket.
* A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva. Az a [adattovábbítási tevékenységek](#data-movement-activities) szakaszt, kattintson az adatok tárolására, hogy szeretné-e további információkhoz jusson/adattár egy forrás és a egy fogadó használhatók.

Ez a folyamat létrehozásának részletes útmutatást lásd: [oktatóanyag: Adatok másolása Blob Storage-ból az SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="sample-transformation-pipeline"></a>Minta átalakítási folyamat
Az alábbi mintafolyamat **tevékenységek** szakaszában egyetlen **HDInsightHive** típusú tevékenység található. Ebben a mintában a [HDInsight Hive-tevékenység](data-factory-hive-activity.md) egy Azure blobtárolóból származó adatokat alakít át egy Hive-szkriptfájl Azure HDInsight Hadoop-fürtön történő futtatásával.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Vegye figyelembe a következő szempontokat:

* A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa** **HDInsightHive** értékre van beállítva.
* A **partitionweblogs.hql** Hive-parancsfájl tárolása az Azure Storage-fiókban (az **AzureStorageLinkedService** nevű scriptLinkedService szolgáltatás által megadva), és az **adfgetstarted** tároló **script** mappájában történik.
* A `defines` szakasz segítségével meghatározza a futásidő beállításait, a hive-parancsfájlnak átadott Hive konfigurációs értékekként (például: `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

A **typeProperties** szakasz eltérő az egyes átalakítási tevékenységek esetében. Támogatott típustulajdonságokkal egy Adatátalakítási tevékenységgel kapcsolatos további információkért kattintson az adott átalakítási tevékenységre az [adat-átalakítási tevékenységeket](#data-transformation-activities) tábla.

Ez a folyamat létrehozásának részletes útmutatást lásd: [oktatóanyag: Hadoop-fürt használatával dolgozza fel az adatokat az első folyamat létrehozását](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Több tevékenység egy adott folyamatban
Az előző két mintában a folyamatok csak egyetlen tevékenységet tartalmaztak. Egy folyamathoz azonban több tevékenység is tartozhat.

Ha több tevékenység egy folyamatban van, és egy tevékenység kimenetét, nem egy másik tevékenység bemeneti, a tevékenységek párhuzamosan is futtathatók a tevékenységek bemeneti adatszeletek készen állnak.

A másik tevékenység bemeneti adatkészleteként egyik tevékenység kimeneti adatkészlete sablonkonfigurációkat összefűzhet két tevékenységet. A második tevékenység végrehajtása, csak ha az elsőre sikeresen befejeződik.

![Láncolási tevékenységek a azonos folyamat](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Ebben a példában a folyamat két tevékenységet tartalmaz: Hogy az Activity1 és az Activity2. Az Activity1 bemenetként Dataset1 vesz igénybe, és a egy kimenetet Dataset2. A tevékenység bemeneteként Dataset2 vesz igénybe, és a egy kimenetet Dataset3. A kimenet az Activity1 óta (Dataset2) a runbook az Activity2, a runbook az Activity2 fut csak akkor, ha a tevékenység sikeresen befejeződik, és a Dataset2 szeletet előállító bemenetével. Ha nem állít elő a Dataset2 szelet az Activity1 valamilyen okból meghiúsul, a 2. tevékenység nem fut, hogy a szelet (például: 9 AM 10 kor).

Emellett láncolhatja össze a különböző folyamatok a tevékenységek.

![Láncolási tevékenységek a két folyamatot](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Ebben a példában Pipeline1 csak egyetlen tevékenységgel rendelkezik, amely bemenetként Dataset1 tart, és állít elő kimenetként Dataset2. A Pipeline2 is csak egy tevékenységgel rendelkezik, amely Dataset2 veszi a bemenetnek és Dataset3 kimenetként.

További információkért lásd: [ütemezési és végrehajtási](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Hozzon létre és folyamatok figyelése
A következő eszközök és SDK-k használatával folyamatokat hozhat létre.

- Másolás varázsló.
- Azure Portal
- Visual Studio
- Azure PowerShell
- Azure Resource Manager-sablon
- REST API
- .NET API

Tekintse meg a következő oktatóanyagok részletes útmutatás a folyamatok létrehozásához a következő eszközök és SDK-k használatával.

- [Adatátalakítási tevékenységgel rendelkező folyamat létrehozása](data-factory-build-your-first-pipeline.md)
- [Adatok áthelyezése tevékenységgel rendelkező folyamat létrehozása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Ha egy folyamat létrehozott és telepített, felügyelheti, és -folyamatok figyelése az Azure portal paneljeinek vagy Monitor and Manage Appot használatával. Az alábbi témakörökben részletes útmutatásait.

- [Folyamatok figyelése és felügyelete az Azure portal paneljeinek használatával](data-factory-monitor-manage-pipelines.md).
- [Folyamatok figyelése és felügyelete a Monitor and Manage Appot](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Egyszeri folyamat
Létrehozhat és ütemezhet egy folyamatot futtatása rendszeres időközönként (például: óránként vagy naponta) az a folyamat definíciójában megadott kezdési és befejezési idejének belül. Tekintse meg az ütemezés tevékenységek részleteiről. Létrehozhat egy folyamatot, amely csak egyszer fut le. Ehhez állítsa be a **pipelineMode** tulajdonság, a folyamat definíciójában **onetime** a következő JSON-mintában látható módon. Az alapértelmezett érték a tulajdonság **ütemezett**.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

Vegye figyelembe a következőket:

* **Indítsa el** és **záró** alkalommal a folyamathoz nincs megadva.
* **Rendelkezésre állási** bemeneti és kimeneti adatkészleteket van megadva (**gyakorisága** és **időköz**), annak ellenére, hogy a Data Factory nem használja az értékeket.
* Diagram nézet egyszeri folyamatok nem jeleníti meg. Ez a működésmód szándékos.
* Egyszeri folyamatok nem lehet frissíteni. Egy egyszeri folyamat klónozza, nevezze át, tulajdonságainak frissítése és üzembe helyezése, hogy hozzon létre egy újat.

## <a name="next-steps"></a>További lépések
- Az adatkészletek kapcsolatos további információkért lásd: [adatkészleteket hoz létre](data-factory-create-datasets.md) cikk.
- A folyamatok ütemezése és végrehajtott további információkért lásd: [ütemezés és végrehajtás az Azure Data Factoryban](data-factory-scheduling-and-execution.md) cikk.

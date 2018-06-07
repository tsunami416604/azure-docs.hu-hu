---
title: Folyamatok létrehozása/ütemezés, adat-előállítóban tevékenységek láncolt |} Microsoft Docs
description: Ismerkedjen meg az adatok folyamatokat létrehozni az Azure Data Factory és adatok. Adatalapú készítenie létrehozásához információkat használja a munkafolyamat létrehozása.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f80a22c39608a9d9c67977f2d0493af7300f475b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622706"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Adatcsatornák és az Azure Data Factory tevékenységek
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-create-pipelines.md)
> * [2. verzió – Előzetes verzió](../concepts-pipelines-activities.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [V2 adatcsatornák](../concepts-pipelines-activities.md).

Ennek a cikknek a segítségével megismerheti az Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját adatáthelyezési és -feldolgozási forgatókönyveihez.  

> [!NOTE]
> Ez a cikk feltételezi, hogy elvégezték- [Bevezetés az Azure Data Factory](data-factory-introduction.md). Nincs hands-a-élmény az adat-előállítók létrehozása, ha áthaladás [data transformation oktatóanyag](data-factory-build-your-first-pipeline.md) és/vagy [adatok mozgása oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) jobban megtudhatja, hogy ez a cikk segít.  

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. A másolási tevékenység használatával például egy helyszíni SQL Serverből egy Azure Blob Storage-tárolóba másolhatja az adatokat. Ezt követően egy Hive-tevékenység segítségével futtasson egy Hive-szkriptet egy Azure HDInsight fürtön, amely a Blob Storage-ből származó adatokat feldolgozza vagy átalakítja a kimeneti adatok előállításához. Végül egy második másolási tevékenység használatával másolja át a kimeneti adatokat egy olyan Azure SQL Data Warehouse-ba, amelyre üzletiintelligencia-alapú (BI-) jelentéskészítési megoldások épülnek. 

Minden tevékenység nulla vagy több bemeneti [adatkészletet](data-factory-create-datasets.md) képes fogadni, és egy vagy több kimeneti [adatkészletet](data-factory-create-datasets.md) képes előállítani. Az alábbi ábrán a folyamat, a tevékenység és az adat-előállító adatkészlete közötti kapcsolat látható: 

![Feldolgozási sor, a tevékenység és a dataset közötti kapcsolat](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Egy folyamat tevékenységek kezelése helyett minden egyes készletként külön-külön teszi lehetővé. Például telepítheti, ütemezhet, felfüggeszteni, és egy sorban, egymástól függetlenül az adatcsatorna tevékenységeinek foglalkozó helyett folytatni.

A Data Factory két típusú tevékenységet támogat: az adattovábbítási tevékenységeket és az adatátalakítási tevékenységeket. Minden tevékenység állhat nulla vagy több bemeneti [adatkészletek](data-factory-create-datasets.md) , majd előállítanak egy vagy több kimeneti adatkészletek.

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
Ha át kell helyeznie egy adatok az adattárolási, hogy a másolási tevékenység nem támogatja, vagy a saját logikát használó adatok átalakítása, hozzon létre egy **egyéni .NET tevékenység**. További információ az egyéni tevékenységek létrehozásával és használatával kapcsolatban: [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Ütemezés folyamatok
Egy folyamat aktív csak között a **start** idő és **end** idő. Nem hajtotta végre, a kezdő időpont elé vagy a befejező időpont után. Ha a feldolgozási sor fel van függesztve, akkor nem hajtódnak függetlenül a kezdő és záró idő. A futtatásához egy folyamatot azt kell nem lehet szüneteltetni. Lásd: [ütemezés és a végrehajtás](data-factory-scheduling-and-execution.md) az Azure Data Factory ütemezés és a végrehajtási működésének megismerése.

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
| név |A folyamat neve. Adjon meg egy, a folyamat által végrehajtandó műveletet jelölő nevet. <br/><ul><li>A karakterek maximális száma: 260</li><li>Betűvel, számmal vagy aláhúzásjellel (_) kell kezdődnie</li><li>Következő karakterek nem engedélyezettek: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Igen |
| leírás | Adjon meg egy, az adott folyamat alkalmazását leíró szöveget. |Igen |
| tevékenységek | A **tevékenységek** szakaszon belül egy vagy több tevékenység is meghatározható. A tevékenység JSON-elem részleteit a következő szakaszban talál. | Igen |  
| start | Kezdő dátum-idő az adatcsatornához. Meg kell [ISO formátum](http://en.wikipedia.org/wiki/ISO_8601). Például: `2016-10-14T16:32:41Z`. <br/><br/>Akkor adja meg a helyi időt, például egy keleti TÉLI idő lehet. Példa: `2016-02-27T06:00:00-05:00`", amely 6 AM becsült<br/><br/>A kezdő és záró tulajdonságok együtt adja meg az adatcsatorna aktív időszakát. Kimeneti szeletek csak előállítása és az aktív időszakban. |Nem<br/><br/>Ha end tulajdonság értékét adja meg, meg kell adnia a kezdő tulajdonság értéke.<br/><br/>A kezdő és befejező időpontja is lehet folyamatokat létrehozni üres. Állítsa az aktív időszakot futtatásához a tölcsér mindkét értéket meg kell adni. Ha nem adja meg a kezdési és befejezési időpontjai folyamat létrehozásakor beállíthatja azokat később a Set-AzureRmDataFactoryPipelineActivePeriod parancsmaggal. |
| vége | Befejező dátum-idő az adatcsatorna. Ha a megadott ISO-formátumban kell lennie. Például:`2016-10-14T17:32:41Z` <br/><br/>Akkor adja meg a helyi időt, például egy keleti TÉLI idő lehet. Példa: `2016-02-27T06:00:00-05:00`, vagyis 6 AM becsült<br/><br/>A folyamat határozatlan idejű futásra, adja meg a 9999-09-09 end tulajdonság értékét. <br/><br/> Egy folyamat aktív csak a kezdési és befejezési időpontja között. Nem hajtotta végre, a kezdő időpont elé vagy a befejező időpont után. Ha a feldolgozási sor fel van függesztve, akkor nem hajtódnak függetlenül a kezdő és záró idő. A futtatásához egy folyamatot azt kell nem lehet szüneteltetni. Lásd: [ütemezés és a végrehajtás](data-factory-scheduling-and-execution.md) az Azure Data Factory ütemezés és a végrehajtási működésének megismerése. |Nem <br/><br/>Ha a start tulajdonság értékét adja meg, meg kell adnia end tulajdonság értéke.<br/><br/>Tekintse meg a megjegyzéseket az **start** tulajdonság. |
| isPaused | Ha true értéke esetén a folyamat nem futtatható. A felfüggesztett állapotban van. Alapértelmezett érték = false. Ez a tulajdonság segítségével engedélyezheti vagy tilthatja le a folyamat. |Nem |
| pipelineMode | Az ütemezési módszer a következő feldolgozási sor fut. Két érték engedélyezett: (alapértelmezett), ütemezett alkalommal.<br/><br/>"Ütemezett" azt jelzi, hogy a folyamat az aktív időszak (kezdő és záró idő) alapján meghatározott időközönként. "Alkalommal" jelzi, hogy a folyamat csak egyszer fut-e. Létrehozását követően alkalommal adatcsatornák nem lehet módosítani/frissített jelenleg. Lásd: [Onetime csővezeték](#onetime-pipeline) alkalommal beállítás vonatkozó további információért. |Nem |
| ExpirationTime | Időtartam, amelynek létrehozása után a [egyszeri folyamat](#onetime-pipeline) érvényes és kiépített kell maradnia. Ha nem rendelkezik minden aktív sikertelen volt, vagy függőben lévő fut, a folyamat a rendszer automatikusan törlése után lehet spórolni a lejárati idő. Az alapértelmezett érték: `"expirationTime": "3.00:00:00"`|Nem |
| Adatkészletek |Folyamatban meghatározott tevékenységek használják az adatkészletek listáját. Ez a tulajdonság segítségével határozza meg, hogy ez az adatcsatorna és az adat-előállítóban nem meghatározott adatkészletek. Ez az adatcsatorna meghatározott adatkészleteket csak ez az adatcsatorna használható, és nem osztható meg. Lásd: [adathalmaz hatóköre](data-factory-create-datasets.md#scoped-datasets) részleteiről. |Nem |

## <a name="activity-json"></a>Tevékenység JSON-fájlja
A **tevékenységek** szakaszon belül egy vagy több tevékenység is meghatározható. Minden tevékenység legfelső szintű struktúrája a következő:

```json
{
    "name": "ActivityName",
    "description": "description", 
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
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
| név | A tevékenység neve. Adjon meg egy, a tevékenység által végrehajtandó műveletet jelölő nevet. <br/><ul><li>A karakterek maximális száma: 260</li><li>Betűvel, számmal vagy aláhúzásjellel (_) kell kezdődnie</li><li>Következő karakterek nem engedélyezettek: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Igen |
| leírás | Az adott tevékenységet vagy annak alkalmazását leíró szöveg |Igen |
| type | A tevékenység típusa. Tekintse meg a [adatok mozgása tevékenységek](#data-movement-activities) és [adatok átalakítása tevékenységek](#data-transformation-activities) szakaszok a tevékenységek különböző típusú. |Igen |
| bemenetek |A tevékenység által felhasznált bemeneti táblák<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Igen |
| kimenetek |A tevékenység által használt kimeneti táblák.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Igen |
| linkedServiceName |A tevékenység által használt társított szolgáltatás neve. <br/><br/>Egy adott tevékenység megkövetelheti annak a társított szolgáltatásnak a megadását, amely a szükséges számítási környezethez kapcsolódik. |Igen, a HDInsight tevékenységet és az Azure Machine Learning kötegelt pontozási tevékenység <br/><br/>Minden egyéb esetében: nem |
| typeProperties |A tulajdonságok a **typeProperties** szakasz a tevékenység-típustól függnek. Az adott tevékenység típustulajdonságainak megtekintéséhez kattintson az előző szakaszban szereplő tevékenységhivatkozásokra. | Nem |
| szabályzat |Olyan szabályzatok, amelyek az adott tevékenység futásidejű viselkedését befolyásolják. Ha nincs megadva, az alapértelmezett házirendek használhatók. |Nem |
| A Feladatütemező | "Feladatütemező" tulajdonság a tevékenység kívánt ütemezés meghatározására szolgál. A altulajdonságok ugyanazok, mint az a [availability tulajdonság DataSet adatkészletben](data-factory-create-datasets.md#dataset-availability). |Nem |


### <a name="policies"></a>Házirendek
A házirendek milyen hatással a futtatási viselkedés tevékenység, kifejezetten a szelet egy tábla feldolgozásakor. A következő táblázat a részletesen.

| Tulajdonság | Megengedett értékek | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| Egyidejűségi |Egész szám <br/><br/>A maximális érték: 10 |1 |A tevékenység egyidejű végrehajtások száma.<br/><br/>Meghatározza, hogy a párhuzamos tevékenység végrehajtások, amely akkor fordulhat elő, a másik szeletek számát. Például ha egy tevékenység kell végighaladnia rendelkezésre álló adatok, nagyobb feldolgozási értéke számos felgyorsítja az adatok feldolgozása. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Meghatározza, hogy feldolgozott adatszeletek sorrendje.<br/><br/>Például ha 2 szeletek (du. 4: egy azonban és délután 5 óra egy másik tulajdonságnak), és mindkét végrehajtási függőben van. Ha a executionPriorityOrder NewestFirst kell, a szelet, délután 5 óra lesz elsőként feldolgozva. Hasonlóképpen ha OldestFIrst kell executionPriorityORder, majd a szelet du. 4: dolgoz fel. |
| retry |Egész szám<br/><br/>A maximális érték 10 is lehet. |0 |Az adatok feldolgozása a szeletre vonatkozó hiba van megjelölve, mielőtt újrapróbálkozások száma. Egy adatszelet tevékenység végrehajtása a rendszer ismét megkísérli legfeljebb a megadott újrapróbálkozások maximális számát. Az újra gombra a lehető leghamarabb a meghibásodás után történik. |
| timeout |A TimeSpan |00:00:00 |A tevékenység időkorlátja. . Példa: 00:10:00 (azt jelenti, időtúllépés 10 perc)<br/><br/>Ha az érték nincs megadva vagy 0, az időtúllépési érték végtelen.<br/><br/>A szelet adatok feldolgozási ideje meghaladja a időtúllépési értéket, ha azt megszakadt, és a rendszer megkísérli újra feldolgozását. Az újrapróbálkozások száma attól függ, hogy az újra gombra tulajdonság. Időtúllépés történik, ha a beállítás időtúllépésbe került. |
| Késleltetés |A TimeSpan |00:00:00 |Adja meg a késleltetés, elindul a szelet feldolgozásának előtt.<br/><br/>Egy adatszelet tevékenység végrehajtása után a késleltetési idő legyen a várt végrehajtási ideje elmúlt elindult.<br/><br/>. Példa: 00:10:00 (magában foglalja a késleltetést a 10 perc) |
| hosszú újrapróbálkozás |Egész szám<br/><br/>A maximális érték: 10 |1 |Sikertelen volt a szelet végrehajtása előtt hosszú újrapróbálkozási kísérletek száma.<br/><br/>hosszú újrapróbálkozás kísérletek által longRetryInterval távolságban helyezkednek el. Ha meg kell adnia egy újrapróbálkozási kísérletek között eltelt idő, így hosszú újrapróbálkozás használja. Ha mind az újra gombra, és a hosszú újrapróbálkozás meg van adva, minden hosszú újrapróbálkozás kísérlet tartalmazza újrapróbálkozások és kísérletek maximális számát. Próbálkozzon újra * hosszú újrapróbálkozás.<br/><br/>Ha például a tevékenység-házirend van a következő beállításokat:<br/>Próbálkozzon újra: 3<br/>hosszú újrapróbálkozás: 2. régiója<br/>longRetryInterval: 01:00:00<br/><br/>Tegyük fel, nincs végrehajtandó csak egy szelet (állapot vár) és a tevékenység végrehajtása meghiúsul minden alkalommal. Eredetileg nem lenne 3 egymást követő végrehajtási kísérletek. A szelet állapota minden kísérlet után újra lehet. Miután először 3 kísérletet keresztül történik, a szelet állapota hosszú újrapróbálkozás lehet.<br/><br/>Egy óra (Ez azt jelenti, hogy longRetryInteval tartozó érték) nem lenne a 3 egymást követő végrehajtási kísérletek egy másik készlet. Ezt követően a szelet állapota akkor sikertelen, és nincs további újrapróbálkozások volna kísérli meg a. Ezért a teljes 6 történt kísérlet.<br/><br/>Ha bármely végrehajtása sikeres, a szelet állapota Kész és nincs további újrapróbálkozások próbált vannak.<br/><br/>hosszú újrapróbálkozás függő adatok nem determinisztikus időpontokban érkeznek vagy flaky akkor következik be, mely az adatfeldolgozás alatt a környezetben használható. Ilyen esetekben újrapróbálkozások egymás után nem segíthet ezzel, és ezzel egy időszak után időt a kívánt kimeneti eredményez.<br/><br/>Járjon el a Word: nincs beállítva hosszú újrapróbálkozás vagy longRetryInterval magas értékeit. Általában a magasabb értékkel rendszeres problémákkal utalnak. |
| longRetryInterval |A TimeSpan |00:00:00 |Hosszú újrapróbálkozások közötti késleltetés |

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
* A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva. Az a [adatok mozgása tevékenységek](#data-movement-activities) szakaszban, kattintson az adatok tárolásához, amelyet a forrás és a fogadó tudhat meg többet az adatok áthelyezése az adott adattár használhatók. 

Ez az adatcsatorna létrehozásának részletes útmutatást lásd: [oktatóanyag: adatok másolása az Blob-tároló az SQL-adatbázis](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

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
* A `defines` szakasz használatával adja meg a Hive értékként a hive-parancsfájl átadott futásidejű beállítások (például `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

A **typeProperties** szakasz eltérő az egyes átalakítási tevékenységek esetében. Átalakítási tevékenységhez támogatott típustulajdonságokat kapcsolatos információkért kattintson a átalakítása tevékenység a [adatok átalakítása tevékenységek](#data-transformation-activities) tábla. 

Ez az adatcsatorna létrehozásának részletes útmutatást lásd: [oktatóanyag: felépítheti első folyamatát Hadoop-fürt használatával adatfeldolgozásra történő](data-factory-build-your-first-pipeline.md). 

## <a name="multiple-activities-in-a-pipeline"></a>Több tevékenység egy adott folyamatban
Az előző két mintában a folyamatok csak egyetlen tevékenységet tartalmaztak. Egy folyamathoz azonban több tevékenység is tartozhat.  

Ha több tevékenység rendelkezik egy folyamatot, és egy tevékenység kimenete nem bemenet egy másik tevékenység, a tevékenységek párhuzamos futtathatja, ha készen áll a tevékenységekhez tartozó bemeneti adatszeletek. 

Két tevékenység is láncolt azzal, hogy a kimeneti adatkészlet egy tevékenység, mint a többi tevékenység bemeneti adatkészletet. A második tevékenység hajtja végre, csak ha az elsőre sikeresen befejeződik.

![Láncolás az azonos adatcsatorna tevékenységeinek](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Ez a példa a feldolgozási sor két tevékenység rendelkezik: Activity1 és az Activity2. Az Activity1 bemenetként Dataset1 vesz igénybe, és egy kimenetet Dataset2. A tevékenység bemeneti adatokként Dataset2 vesz igénybe, és egy kimenetet Dataset3. A kimenet az Activity1 óta (Dataset2) a bemeneti az Activity2, az Activity2 fut, csak a tevékenység sikeresen befejeződik, és a Dataset2 szeletet előállító után. Ha az Activity1 valamilyen okból sikertelen, és nem hozhatók létre a Dataset2 szelet, a 2. tevékenység nem fut az adott szeletek (például: a 10 de Reggel 9). 

Tevékenységek más folyamatok láncában is találhatók.

![Két kimenetátirányítási titkosításblokkoló tevékenységek](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Ez a példa Pipeline1 Dataset1 bemenetként veszi, és hozza létre a kimenetként Dataset2 csak egy tevékenység rendelkezik. A Pipeline2 is rendelkezik, amely Dataset2 bemeneti és kimenetként Dataset3 csak egy tevékenységgel. 

További információkért lásd: [ütemezés és a végrehajtási](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

## <a name="create-and-monitor-pipelines"></a>Hozzon létre és folyamatok figyelése
Folyamatok valamelyikének ezen eszközök vagy az SDK-k használatával hozhat létre. 

- Másolása varázsló. 
- Azure Portal
- Visual Studio
- Azure PowerShell
- Azure Resource Manager-sablon
- REST API
- .NET API

Tekintse meg a folyamatok egyikével a következő eszközök, illetve az SDK-k létrehozására vonatkozó részletes utasításokat az alábbi oktatóanyagok.
 
- [Adatátalakítási tevékenységgel rendelkező folyamat létrehozása](data-factory-build-your-first-pipeline.md)
- [Adatok mozgása tevékenységgel folyamat létrehozása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Ha egy folyamatot, akkor létre/telepített, kezeléséhez, és a folyamatok figyelése az Azure portál paneleken vagy a figyelő és App kezelése. A következő témakörök részletes útmutatásait. 

- [Figyelheti és folyamatok kezelése az Azure portál panel segítségével](data-factory-monitor-manage-pipelines.md).
- [Figyelheti és kezelheti a folyamatok figyelése és kezelése App használatával](data-factory-monitor-manage-app.md)


## <a name="onetime-pipeline"></a>Alkalommal folyamat
Létrehozhat és ütemezhet egy folyamat rendszeres időközönként futtatásához (például: óránként vagy naponta) belül a kezdő és befejező időpontja, akkor a csővezeték-definíció adható meg. Lásd: [tevékenységek ütemezése](#scheduling-and-execution) részleteiről. Létrehozhat egy folyamatot, amely csak egyszer fut le. Ehhez az szükséges, állítsa be a **pipelineMode** tulajdonságot az adatcsatorna-definíciót az **alkalommal** a következő JSON-mintában látható módon. Az alapértelmezett érték a tulajdonság **ütemezett**.

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
                ]
                "name": "CopyActivity-0"
            }
        ]
        "pipelineMode": "OneTime"
    }
}
```

Vegye figyelembe a következőket:

* **Start** és **end** a tölcsér alkalommal nincs megadva.
* **Rendelkezésre állási** bemeneti és kimeneti adatkészletek van megadva (**gyakoriság** és **időköz**), annak ellenére, hogy a Data Factory nem az értékeket használja.  
* Diagram nézet nem szerepelnek a egyszeri folyamatok. Ez a működésmód szándékos.
* Egyszeri adatcsatornák nem lehet frissíteni. Klónozni egy egyszeri folyamat, nevezze át, tulajdonságainak frissítéséhez és központi telepítése úgy, hogy hozzon létre egy újat.


## <a name="next-steps"></a>További lépések
- Adatkészletek kapcsolatos további információkért lásd: [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. 
- Hogyan adatcsatornák ütemezett és végrehajtott kapcsolatos további információkért lásd: [ütemezés és a végrehajtása az Azure Data Factory](data-factory-scheduling-and-execution.md) cikk. 
  


---
title: Az Azure Data Factory - JSON-Parancsprogramokról |} Microsoft Docs
description: JSON-sémákat biztosít a Data Factory-entitásokhoz.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d5d085e24efb7008e8275ec51afe00ba18ca9ba0
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753557"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Az Azure Data Factory - JSON-Parancsprogramokról
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik.


A cikkben JSON-sémákat és példák meghatározásához az Azure Data Factory entitások (adatcsatorna, tevékenység, adatkészlet és társított szolgáltatás).  

## <a name="pipeline"></a>Folyamat 
A magas szintű struktúra folyamat meghatározása a következőképpen történik: 

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
} 
```

Következő táblázat az adatcsatorna JSON-definícióból a tulajdonságokat:

| Tulajdonság | Leírás | Szükséges
-------- | ----------- | --------
| név | A folyamat neve. Adjon meg egy nevet a műveletet jelenti, hogy a tevékenység vagy csővezeték van konfigurálva<br/><ul><li>A karakterek maximális száma: 260</li><li>Betűvel, számmal vagy aláhúzásjellel (_) kell kezdődnie</li><li>Következő karakterek nem engedélyezettek: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Igen |
| leírás |A tevékenység vagy csővezeték alkalmazott leíró szöveg | Nem |
| tevékenységek | A tevékenységek listáját tartalmazza. | Igen |
| start |Kezdő dátum-idő az adatcsatornához. Meg kell [ISO formátum](http://en.wikipedia.org/wiki/ISO_8601). Például: 2014-10-14T16:32:41. <br/><br/>Akkor adja meg a helyi időt, például egy keleti TÉLI idő lehet. Példa: `2016-02-27T06:00:00**-05:00`, vagyis 6 AM becsült<br/><br/>A kezdő és záró tulajdonságok együtt adja meg az adatcsatorna aktív időszakát. Kimeneti szeletek csak előállítása és az aktív időszakban. |Nem<br/><br/>Ha end tulajdonság értékét adja meg, meg kell adnia a kezdő tulajdonság értéke.<br/><br/>A kezdő és befejező időpontja is lehet folyamatokat létrehozni üres. Állítsa az aktív időszakot futtatásához a tölcsér mindkét értéket meg kell adni. Ha nem adja meg a kezdési és befejezési időpontjai folyamat létrehozásakor beállíthatja azokat később a Set-AzureRmDataFactoryPipelineActivePeriod parancsmaggal. |
| vége |Befejező dátum-idő az adatcsatorna. Ha a megadott ISO-formátumban kell lennie. Például: 2014-10-14T17:32:41 <br/><br/>Akkor adja meg a helyi időt, például egy keleti TÉLI idő lehet. Példa: `2016-02-27T06:00:00**-05:00`, vagyis 6 AM becsült<br/><br/>A folyamat határozatlan idejű futásra, adja meg a 9999-09-09 end tulajdonság értékét. |Nem <br/><br/>Ha a start tulajdonság értékét adja meg, meg kell adnia end tulajdonság értéke.<br/><br/>Tekintse meg a megjegyzéseket az **start** tulajdonság. |
| isPaused |Ha a feldolgozási sor igaz értékre való beállítása nem működik. Alapértelmezett érték = false. Ez a tulajdonság segítségével engedélyezheti vagy tilthatja le. |Nem |
| pipelineMode |Az ütemezési módszer a következő feldolgozási sor fut. Két érték engedélyezett: (alapértelmezett), ütemezett alkalommal.<br/><br/>"Ütemezett" azt jelzi, hogy a folyamat az aktív időszak (kezdő és záró idő) alapján meghatározott időközönként. "Alkalommal" jelzi, hogy a folyamat csak egyszer fut-e. Létrehozását követően alkalommal adatcsatornák nem lehet módosítani/frissített jelenleg. Lásd: [Onetime csővezeték](data-factory-create-pipelines.md#onetime-pipeline) alkalommal beállítás vonatkozó további információért. |Nem |
| expirationTime |Időtartam, amelyek a feldolgozási sor érvényes, és kiépített maradjon létrehozása után. Ha nem rendelkezik minden aktív sikertelen volt, vagy függőben lévő fut, a folyamat a rendszer automatikusan törli lejárati időpont után. |Nem |


## <a name="activity"></a>Tevékenység 
A magas szintű struktúra egy tevékenységhez, a folyamat definition (tevékenységek elem) belül a következőképpen történik:

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
    }
    "scheduler":
    {
    }
}
```

A következő táblázat a tulajdonságokat a tevékenység JSON-definícióból ismertetik:

| Címke | Leírás | Szükséges |
| --- | --- | --- |
| név |A tevékenység neve. Adjon meg egy nevet a műveletet jelenti, hogy a tevékenység van konfigurálva<br/><ul><li>A karakterek maximális száma: 260</li><li>Betűvel, számmal vagy aláhúzásjellel (_) kell kezdődnie</li><li>Következő karakterek nem engedélyezettek: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Igen |
| leírás |Mire használható a tevékenységet leíró szöveg. |Nem |
| type |Adja meg a tevékenység típusa. Tekintse meg a [ADATTÁROLÓKHOZ](#data-stores) és [adatok ÁTALAKÍTÁSA tevékenységek](#data-transformation-activities) szakaszok a tevékenységek különböző típusú. |Igen |
| bemenetek |A tevékenység által felhasznált bemeneti táblák<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Nem HDInsightStreaming és SqlServerStoredProcedure tevékenységek <br/> <br/> Az összes többi Igen |
| kimenetek |A tevékenység által használt kimeneti táblák.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Igen |
| linkedServiceName |A tevékenység által használt társított szolgáltatás neve. <br/><br/>Egy adott tevékenység megkövetelheti annak a társított szolgáltatásnak a megadását, amely a szükséges számítási környezethez kapcsolódik. |HDInsight tevékenységek, az Azure Machine Learning tevékenységek és a tárolt eljárási tevékenység igen. <br/><br/>Minden egyéb esetében: nem |
| typeProperties |A typeProperties szakaszban tulajdonságok attól függnek, hogy a tevékenység típusa. |Nem |
| szabályzat |Olyan szabályzatok, amelyek az adott tevékenység futásidejű viselkedését befolyásolják. Ha nincs megadva, az alapértelmezett házirendek használhatók. |Nem |
| A Feladatütemező |"Feladatütemező" tulajdonság a tevékenység kívánt ütemezés meghatározására szolgál. A altulajdonságok ugyanazok, mint az a [availability tulajdonság DataSet adatkészletben](data-factory-create-datasets.md#dataset-availability). |Nem |

### <a name="policies"></a>Házirendek
A házirendek milyen hatással a futtatási viselkedés tevékenység, kifejezetten a szelet egy tábla feldolgozásakor. A következő táblázat a részletesen.

| Tulajdonság | Megengedett értékek | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| egyidejűségi |Egész szám <br/><br/>A maximális érték: 10 |1 |A tevékenység egyidejű végrehajtások száma.<br/><br/>Meghatározza, hogy a párhuzamos tevékenység végrehajtások, amely akkor fordulhat elő, a másik szeletek számát. Például ha egy tevékenység kell végighaladnia rendelkezésre álló adatok, nagyobb feldolgozási értéke számos felgyorsítja az adatok feldolgozása. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Meghatározza, hogy feldolgozott adatszeletek sorrendje.<br/><br/>Például ha 2 szeletek (du. 4: egy azonban és délután 5 óra egy másik tulajdonságnak), és mindkét végrehajtási függőben van. Ha a executionPriorityOrder NewestFirst kell, a szelet, délután 5 óra lesz elsőként feldolgozva. Hasonlóképpen ha OldestFIrst kell executionPriorityORder, majd a szelet du. 4: dolgoz fel. |
| retry |Egész szám<br/><br/>A maximális érték 10 is lehet. |0 |Az adatok feldolgozása a szeletre vonatkozó hiba van megjelölve, mielőtt újrapróbálkozások száma. Egy adatszelet tevékenység végrehajtása a rendszer ismét megkísérli legfeljebb a megadott újrapróbálkozások maximális számát. Az újra gombra a lehető leghamarabb a meghibásodás után történik. |
| timeout |A TimeSpan |00:00:00 |A tevékenység időkorlátja. . Példa: 00:10:00 (azt jelenti, időtúllépés 10 perc)<br/><br/>Ha az érték nincs megadva vagy 0, az időtúllépési érték végtelen.<br/><br/>A szelet adatok feldolgozási ideje meghaladja a időtúllépési értéket, ha azt megszakadt, és a rendszer megkísérli újra feldolgozását. Az újrapróbálkozások száma attól függ, hogy az újra gombra tulajdonság. Időtúllépés történik, ha a beállítás időtúllépésbe került. |
| késleltetés |A TimeSpan |00:00:00 |Adja meg a késleltetés, elindul a szelet feldolgozásának előtt.<br/><br/>Egy adatszelet tevékenység végrehajtása után a késleltetési idő legyen a várt végrehajtási ideje elmúlt elindult.<br/><br/>. Példa: 00:10:00 (magában foglalja a késleltetést a 10 perc) |
| hosszú újrapróbálkozás |Egész szám<br/><br/>A maximális érték: 10 |1 |Sikertelen volt a szelet végrehajtása előtt hosszú újrapróbálkozási kísérletek száma.<br/><br/>hosszú újrapróbálkozás kísérletek által longRetryInterval távolságban helyezkednek el. Ha meg kell adnia egy újrapróbálkozási kísérletek között eltelt idő, így hosszú újrapróbálkozás használja. Ha mind az újra gombra, és a hosszú újrapróbálkozás meg van adva, minden hosszú újrapróbálkozás kísérlet tartalmazza újrapróbálkozások és kísérletek maximális számát. Próbálkozzon újra * hosszú újrapróbálkozás.<br/><br/>Ha például a tevékenység-házirend van a következő beállításokat:<br/>Próbálkozzon újra: 3<br/>hosszú újrapróbálkozás: 2. régiója<br/>longRetryInterval: 01:00:00<br/><br/>Tegyük fel, nincs végrehajtandó csak egy szelet (állapot vár) és a tevékenység végrehajtása meghiúsul minden alkalommal. Eredetileg nem lenne 3 egymást követő végrehajtási kísérletek. A szelet állapota minden kísérlet után újra lehet. Miután először 3 kísérletet keresztül történik, a szelet állapota hosszú újrapróbálkozás lehet.<br/><br/>Egy óra (Ez azt jelenti, hogy longRetryInteval tartozó érték) nem lenne a 3 egymást követő végrehajtási kísérletek egy másik készlet. Ezt követően a szelet állapota akkor sikertelen, és nincs további újrapróbálkozások volna kísérli meg a. Ezért a teljes 6 történt kísérlet.<br/><br/>Ha bármely végrehajtása sikeres, a szelet állapota Kész és nincs további újrapróbálkozások próbált vannak.<br/><br/>hosszú újrapróbálkozás függő adatok nem determinisztikus időpontokban érkeznek vagy flaky akkor következik be, mely az adatfeldolgozás alatt a környezetben használható. Ilyen esetekben újrapróbálkozások egymás után nem segíthet ezzel, és ezzel egy időszak után időt a kívánt kimeneti eredményez.<br/><br/>Járjon el a Word: nincs beállítva hosszú újrapróbálkozás vagy longRetryInterval magas értékeit. Általában a magasabb értékkel rendszeres problémákkal utalnak. |
| longRetryInterval |A TimeSpan |00:00:00 |Hosszú újrapróbálkozások közötti késleltetés |

### <a name="typeproperties-section"></a>typeProperties szakasz
A typeProperties szakaszban nem egyezik, minden egyes tevékenységhez. Átalakítás tevékenységek rendelkezik csak a típus tulajdonságokat. Lásd: [adatok ÁTALAKÍTÁSA tevékenységek](#data-transformation-activities) szakasz ebben a cikkben egy folyamat átalakítása tevékenységek meghatározó JSON-minták. 

**Másolási tevékenység** rendelkezik-e a typeProperties szakasz két alszakaszokat: **forrás** és **fogadó**. Lásd: [ADATTÁROLÓKHOZ](#data-stores) szakasz ebben a cikkben, amelyek bemutatják, hogyan használható az adatok JSON-minták tárolót, mint a forrás és fogadó, vagy a. 

### <a name="sample-copy-pipeline"></a>Minta másolási folyamat
Az alábbi mintafolyamat **tevékenységek** szakaszában egyetlen **Másolás** típusú tevékenység található. Ez a példa a [másolási tevékenység](data-factory-data-movement-activities.md) másol adatokat az Azure Blob storage Azure SQL-adatbázishoz. 

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
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
} 
```

Vegye figyelembe a következő szempontokat:

* A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa** **Copy** értékre van beállítva.
* A tevékenység bemenetének beállítása **InputDataset**, a kimeneté pedig **OutputDataset**.
* A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva.

Lásd: [ADATTÁROLÓKHOZ](#data-stores) szakasz ebben a cikkben, amelyek bemutatják, hogyan használható az adatok JSON-minták tárolót, mint a forrás és fogadó, vagy a.    

Ez az adatcsatorna létrehozásának részletes útmutatást lásd: [oktatóanyag: adatok másolása az Blob-tároló az SQL-adatbázis](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

### <a name="sample-transformation-pipeline"></a>Minta átalakítási folyamat
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
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Vegye figyelembe a következő szempontokat: 

* A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa** **HDInsightHive** értékre van beállítva.
* A **partitionweblogs.hql** Hive-parancsfájl tárolása az Azure Storage-fiókban (az **AzureStorageLinkedService** nevű scriptLinkedService szolgáltatás által megadva), és az **adfgetstarted** tároló **script** mappájában történik.
* A **meghatározása** szakasz használatával adja meg a Hive értékként a hive-parancsfájl átadott futásidejű beállítások (például `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Lásd: [adatok ÁTALAKÍTÁSA tevékenységek](#data-transformation-activities) szakasz ebben a cikkben egy folyamat átalakítása tevékenységek meghatározó JSON-minták.

Ez az adatcsatorna létrehozásának részletes útmutatást lásd: [oktatóanyag: felépítheti első folyamatát Hadoop-fürt használatával adatfeldolgozásra történő](data-factory-build-your-first-pipeline.md). 

## <a name="linked-service"></a>Társított szolgáltatások
A magas szintű struktúra, a társított szolgáltatás definíciójának a következőképpen történik:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

A következő táblázat a tulajdonságokat a tevékenység JSON-definícióból ismertetik:

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- | 
| név | A társított szolgáltatás neve. | Igen | 
| Tulajdonságok - típus | A társított szolgáltatás típusa. Például: az Azure Storage, Azure SQL Database. |
| typeProperties | A typeProperties szakasz különböző minden adattároló vagy számítási környezet elemeket tartalmaz. Lásd: [adattárolókhoz](#datastores) szakasz az összes adat tárolására társított szolgáltatások és [számítási környezetek](#compute-environments) a számítás kapcsolódó szolgáltatások |   

## <a name="dataset"></a>Adathalmaz 
Az Azure Data Factory dataset a következők:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
       "policy":
        {      
        }
    }
}
```

A következő táblázat ismerteti a fenti JSON-tulajdonságokat:   

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| név | A DataSet adatkészlet neve. Lásd: [Azure Data Factory - elnevezési szabályait](data-factory-naming-rules.md) elnevezési szabályait. |Igen |NA |
| type | A dataset típusa. Meg kell adni az Azure Data Factory által támogatott típusok egyikét (például: AzureBlob, AzureSqlTable). Lásd: [ADATTÁROLÓKHOZ](#data-stores) szakasz az adatok áruházak és a Data Factory által támogatott adatkészlet-típusok. | 
| struktúra | Az adatkészlet sémája. Tartalmaz oszlopok, azok típusok, stb. | Nem |NA |
| typeProperties | A kiválasztott típus megfelelő tulajdonságok. Lásd: [ADATTÁROLÓKHOZ](#data-stores) szakaszban a támogatott típusok és azok tulajdonságait. |Igen |NA |
| external | Logikai jelző, amely adja meg, hogy a data factory-folyamathoz explicit módon létrehozott adatkészlet vagy nem. |Nem |false |
| rendelkezésre állás | A feldolgozási időszakában vagy az adatkészlet üzemi slicing modell határoz meg. Az adatkészlet modell felosztás a részletekért lásd: [ütemezés és a végrehajtás](data-factory-scheduling-and-execution.md) cikk. |Igen |NA |
| szabályzat |Meghatározza a feltételek vagy a feltétellel, hogy a dataset szeletek teljesítenie kell. <br/><br/>További információkért lásd: [Dataset házirend](#Policy) szakasz. |Nem |NA |

Minden egyes oszlopának a **struktúra** a szakasz a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| név |Az oszlop neve. |Igen |
| type |Az oszlop adattípusát.  |Nem |
| kulturális környezet |.NET-alapú kulturális környezet lehet használni, ha a típus meg van adva, de a .NET-típus `Datetime` vagy `Datetimeoffset`. Az alapértelmezett szint a `en-us`. |Nem |
| Formátumban |Formázó karakterlánc, használandó típus van megadva, és a .NET-típus `Datetime` vagy `Datetimeoffset`. |Nem |

A következő példában a dataset adatkészletben három oszlopot `slicetimestamp`, `projectname`, és `pageviews` és típus: karakterlánc, karakterlánc és tizedes kulcsattribútumokkal.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

A következő táblázat ismerteti a használható tulajdonságok a **rendelkezésre állási** szakasz:

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| frequency |Megadja a dataset szelet üzemi időegységét.<br/><br/><b>Támogatott gyakoriság</b>: perc, óra, nap, hét, hónap |Igen |NA |
| interval |Megadja egy szorzóval gyakoriság<br/><br/>"X időköz" határozza meg, milyen gyakran a szelet jön létre.<br/><br/>Ha módosítania kell a adatkészlet kell szeletelhetők óránként, beállíthatja <b>gyakorisága</b> való <b>óra</b>, és <b>időköz</b> való <b>1</b>.<br/><br/><b>Megjegyzés:</b>: perces gyakoriságot ad meg, ha azt javasoljuk, hogy beállította az intervallum nem lehet kisebb, mint 15 |Igen |NA |
| stílus |Meghatározza, hogy a szelet akkor a rendszer az időköz kezdő/végén.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Ha gyakoriságának beállítása hónapot és stílus EndOfInterval beállítása, a szelet hónap utolsó napján elő. Ha a stílus StartOfInterval van megadva, a szelet hónap első napján elő.<br/><br/>Ha nap gyakoriságának beállítása és stílus EndOfInterval beállítása, a szelet elő az elmúlt órában a nap.<br/><br/>Ha gyakoriságának beállítása óra és stílus EndOfInterval beállítása, a szelet elő az órát végén. Például egy adatszelethez du. 1 – 2 óra időszakban, a szelet hozzák 2 du. |Nem |EndOfInterval |
| anchorDateTime |Határozza meg az idő az ütemező által használt adatkészlet szelet határok számítási abszolút pozíciója. <br/><br/><b>Megjegyzés:</b>: Ha a AnchorDateTime részekből dátum, amelyek részletesebben, mint a gyakorisága, akkor a részletesebb részek figyelmen kívül lesznek hagyva. <br/><br/>Például ha a <b>időköz</b> van <b>óránkénti</b> (gyakoriság: óra és időköz: 1) és a <b>AnchorDateTime</b> tartalmaz <b>percet és másodpercet</b>a <b>percet és másodpercet</b> a AnchorDateTime részei a rendszer figyelmen kívül hagyja. |Nem |01/01/0001 |
| offset |TimeSpan érték, amely a kezdő és a záró összes adatkészlet szeletek vette. <br/><br/><b>Megjegyzés:</b>: Ha anchorDateTime és az offset is meg van adva, az eredmény a kombinált shift-e. |Nem |NA |

A következő rendelkezésre állási szakasz Megadja, hogy a kimeneti adatkészlet előállított óránként (vagy) bemeneti adatkészlet óránkénti áll rendelkezésre:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

A **házirend** az adatkészlet-definícióban szakaszban határozza meg, a feltételek vagy a feltétellel, hogy a dataset szeletek teljesítenie kell.

| Házirend neve | Leírás | Vonatkozik. | Szükséges | Alapértelmezett |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Azt ellenőrzi, hogy az adatokat egy **Azure blob** megfelel a minimális méretét (megabájtban). |Azure-blob |Nem |NA |
| minimumRows |Azt ellenőrzi, hogy az adatokat egy **Azure SQL adatbázis** vagy egy **Azure-tábla** a sorok legkisebb számát tartalmazza. |<ul><li>Azure SQL Database</li><li>Azure-tábla</li></ul> |Nem |NA |

**Példa**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

Azure Data Factory hozzák alatt álló adatkészlet, kivéve azt állapotúként kell megjelölni **külső**. Ez a beállítás általában vonatkozik a bemenet az adatcsatorna első tevékenység, kivéve, ha a tevékenység vagy csővezeték-láncolás használatban van.

| Name (Név) | Leírás | Szükséges | Alapértelmezett érték |
| --- | --- | --- | --- |
| dataDelay |A külső adatokat az adott szelet rendelkezésre állásának az ellenőrzését késleltetési idő. Például ha óránkénti érhető el az adatokat, ellenőrizze, hogy a külső adatok elérhetők legyenek és a megfelelő szelet készen áll az dataDelay használatával késleltethető.<br/><br/>Csak a jelenlegi időpont vonatkozik.  Például ha 1:00 PM azonnal, és az értéke 10 perc, az érvényesítési kezdődik, 1:10 óra.<br/><br/>Ez a beállítás nincs hatással a szeletek a múltban (szelet befejezési időpontja + dataDelay szeletek < most) dolgoznak fel késedelem nélkül.<br/><br/>Idő nagyobb, mint 23:59 óra kell megadni, használja a `day.hours:minutes:seconds` formátumban. Például adja meg a 24 órát, ne használja 24:00:00. Ehelyett használjon 1.00:00:00. Ha 24:00:00 használja, akkor a rendszer 24 napos (24.00:00:00). 1 nap és 4 óra adja meg 1:04:00:00. |Nem |0 |
| retryInterval |A várakozási idő közötti hiba, a következő kísérlet próbálkozzon újra. Ha egy try sikertelen, a következő kísérlet retryInterval utánra esik. <br/><br/>Ha 1:00 PM most, az első lépések az első próbálkozás. Ha az első ellenőrzési ellenőrzés időtartam 1 perc és a művelet sikertelen volt, a következő újrapróbálkozási jelenleg 1:00 + 1 perc (időtartam) + 1 perces (újrapróbálkozási időköz) = 1:02 PM. <br/><br/>A múltban szeletek nincs késleltetés. Az újrapróbálkozási azonnal történik. |Nem |00:01:00 (1 perc) |
| retryTimeout |Az egyes újrapróbálkozások időkorlátját.<br/><br/>Ha ez a tulajdonság 10 percre van beállítva, az érvényesítési kell elvégezni, 10 percen belül. Ha az érvényesítés végrehajtásához 10 percnél hosszabb ideig tart, az ismételt próbálkozás túllépi az időkorlátot.<br/><br/>Ha az érvényesítéshez bármilyen kísérlet időkorlátja lejár, a szelet időtúllépésbe került van megjelölve. |Nem |00:10:00 (10 perc) |
| maximumRetry |Ellenőrizze, hogy elérhető-e a külső adatok futtatásainak száma. A megengedett maximális értéke 10. |Nem |3 |


## <a name="data-stores"></a>ADATTÁROLÓ
A [társított szolgáltatás](#linked-service) szakaszban megadott JSON-elemek szerepelnek, amelyek közösek az összes eszköztípuson összekapcsolt szolgáltatások leírása. Ez a szakasz ismerteti a JSON-elemek szerepelnek, mindegyik adattár jellemző.

A [Dataset](#dataset) JSON-elemek szerepelnek, amelyek közösek az összes eszköztípuson adatkészletek a megadott szakasz leírását. Ez a szakasz ismerteti a JSON-elemek szerepelnek, mindegyik adattár jellemző.

A [tevékenység](#activity) JSON-elemek szerepelnek, amelyek közösek az összes eszköztípuson tevékenységek leírásainak szakaszát. Ez a szakasz ismerteti a JSON-elemek szerepelnek, amelyek minden adattár kötődnek, mint a másolási tevékenység során a forrás/fogadó használata esetén.  

A tároló érdekli a JSON-sémák társított szolgáltatás, a DataSet adatkészlet és a másolási tevékenység során a forrás/fogadó megjelenítéséhez a hivatkozásra kattintva.

| Kategória | Adattár 
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |[Azure Data Lake Store](#azure-datalake-store) |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure SQL Data Warehouse](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Search](#azure-search) |
| &nbsp; |[Azure Table storage](#azure-table-storage) |
| **Adatbázisok** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **Fájl** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Fájlrendszer](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Egyéb** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |[Webes tábla](#web-table) |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatások két típusa van: Azure Storage társított szolgáltatásnak, és a társított szolgáltatásnak Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
A data factory használatával az Azure storage-fiók összekapcsolása a **fiókkulcs**, hozzon létre egy Azure Storage társított szolgáltatást. Meghatározásához az Azure Storage társított szolgáltatásnak, és állítsa a **típus** a társított szolgáltatás **AzureStorage**. Ezután megadhatja tulajdonságait a következő a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| connectionString |Adja meg a connectionString tulajdonság az Azure storage való kapcsolódáshoz szükséges adatokat. |Igen |

##### <a name="example"></a>Példa  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Az Azure Storage SAS társított szolgáltatás
A társított Azure Storage SAS-szolgáltatás lehetővé teszi egy Azure Storage-fiók összekapcsolása egy Azure data factory egy közös hozzáférésű Jogosultságkód (SAS) használatával. Az adat-előállítóban minden/specifikus erőforrások (blobtárolóban /) a tárolóban lévő korlátozott/időhöz kötött hozzáférést biztosít. Az Azure storage-fiók összekapcsolása egy adat-előállító közös hozzáférésű Jogosultságkód használatával, a társított Azure Storage SAS-szolgáltatás létrehozása. Egy Azure Storage SAS meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AzureStorageSas**. Ezután megadhatja tulajdonságait a következő a **typeProperties** szakasz:   

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| sasUri |Adja meg a megosztott hozzáférési aláírást URI az Azure Storage-erőforrások, például a blob, -tároló vagy tábla. |Igen |

##### <a name="example"></a>Példa

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

További információ a következő összekapcsolt szolgáltatások: [Azure Blob Storage összekötő](data-factory-azure-blob-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg az Azure-Blob adatkészletet, állítsa be a **típus** a DataSet **AzureBlob**. Ezt követően adja meg a következő Azure Blob tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |A tároló és a blob-tároló mappa elérési útja. Példa: myblobcontainer\myblobfolder\ |Igen |
| fileName |A blob neve. Fájlnév nem, kötelező, és a kis-és nagybetűket.<br/><br/>Ha megad egy fájlnevet, a tevékenység (például a Másolás) működik a megadott Blob.<br/><br/>Ha nincs megadva fájlnév, másolása összes BLOB bemeneti adatkészlet folderPath tartalmazza.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet, a létrehozott fájl nevét a következő lenne ebben a formátumban: adatok. <Guid>.txt (például:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| partitionedBy |partitionedBy egy nem kötelező tulajdonság. Használhatja a dinamikus folderPath és fájlnevét idő adatsorozat adatok megadása. Például folderPath is paraméteres adatok óránkénti. |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


További információkért lásd: [Azure Blob összekötő](data-factory-azure-blob-connector.md#dataset-properties) cikk.

### <a name="blobsource-in-copy-activity"></a>A másolási tevékenység BlobSource
Adatok másolása az Azure Blob-tárolóból, állítsa be a **adatforrástípust** a másolási tevékenység **BlobSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát. |TRUE hamis (alapértelmezés) |Nem |

#### <a name="example-blobsource"></a>Példa: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>A másolási tevékenység BlobSink
Adatok másolása az Azure Blob Storage, állítsa be a **típus gyűjtése** a másolási tevékenység **BlobSink**, és adja meg a következő tulajdonságokat a **fogadó** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| copyBehavior |Másolás viselkedését határozza meg, ha az adatforrás BlobSource vagy a fájlrendszer. |<b>PreserveHierarchy</b>: őrzi meg a fájl hierarchia a célmappában. A következő forrásfájl forrásmappához relatív elérési a relatív elérési út a cél-fájlját és a célmappa megegyezik.<br/><br/><b>FlattenHierarchy</b>: a forrásmappából a fájlok a célmappában első szintjét is. A fájlok céljaként automatikusan létrehozott nevet adni. <br/><br/><b>Mergefiles típusú (alapértelmezett):</b> egyesíti a forrásmappából egy fájl összes fájlt. Ha a fájl/Blob neve meg van adva, az egyesített neve legyen a megadott név; Ellenkező esetben lenne automatikusan létrehozott fájl nevét. |Nem |

#### <a name="example-blobsink"></a>Példa: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

További információkért lásd: [Azure Blob összekötő](data-factory-azure-blob-connector.md#copy-activity-properties) cikk. 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Társított szolgáltatások
Egy Azure Data Lake Store meghatározásához társított szolgáltatás, a társított szolgáltatás típusának beállítása **AzureDataLakeStore**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureDataLakeStore** | Igen |
| dataLakeStoreUri | Adja meg az Azure Data Lake Store-fiók adatait. A következő formátumban: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/`. | Igen |
| subscriptionId | Azure-előfizetése azonosítóját, amelyhez a Data Lake Store tartozik. | A fogadó szükséges |
| resourceGroupName | Azure erőforráscsoport-név, amely a Data Lake Store tartozik. | A fogadó szükséges |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen (a szolgáltatás egyszerű hitelesítés) |
| servicePrincipalKey | Adja meg az alkalmazás kulcsot. | Igen (a szolgáltatás egyszerű hitelesítés) |
| bérlő | Adja meg a bérlői adatokat (tartomány nevét vagy a bérlő azonosító) alatt az alkalmazás található. Azt az Azure-portál jobb felső sarkában az egér rámutató által kérheti le. | Igen (a szolgáltatás egyszerű hitelesítés) |
| Engedélyezési | Kattintson a **engedélyezés** gombra a **Data Factory Editor** , és írja be a hitelesítő adatok, amelyek az automatikusan létrehozott engedélyezési URL-címet rendel hozzá ehhez a tulajdonsághoz. | Igen (a felhasználói hitelesítő)|
| sessionId | OAuth munkamenet-azonosító az OAuth hitelesítési munkamenetből. Minden munkamenet-azonosító egyedi, és előfordulhat, hogy csak egyszer használható. Ez a beállítás automatikusan létrejön a Data Factory Editor használatakor. | Igen (a felhasználói hitelesítő) |

#### <a name="example-using-service-principal-authentication"></a>Példa: a szolgáltatás egyszerű hitelesítést használó
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Példa: a felhasználói hitelesítő adatok hitelesítést használnak
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

További információkért lásd: [Azure Data Lake Store összekötő](data-factory-azure-datalake-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg az Azure Data Lake Store adatkészlethez, állítsa be a **típus** a DataSet **AzureDataLakeStore**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| folderPath |A tároló és az Azure Data Lake mappa elérési útja tárolja. |Igen |
| fileName |A fájl az Azure Data Lake store nevét. Fájlnév nem, kötelező, és a kis-és nagybetűket. <br/><br/>Ha megad egy fájlnevet, a tevékenység (például a Másolás) működik az adott fájlt.<br/><br/>Ha nincs megadva fájlnév, példány bemeneti adatkészlet folderPath minden fájl tartalmazza.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet, a létrehozott fájl nevét a következő lenne ebben a formátumban: adatok. <Guid>.txt (például:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| partitionedBy |partitionedBy egy nem kötelező tulajdonság. Használhatja a dinamikus folderPath és fájlnevét idő adatsorozat adatok megadása. Például folderPath is paraméteres adatok óránkénti. |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

#### <a name="example"></a>Példa
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
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

További információkért lásd: [Azure Data Lake Store összekötő](data-factory-azure-datalake-connector.md#dataset-properties) cikk. 

### <a name="azure-data-lake-store-source-in-copy-activity"></a>A másolási tevékenység az Azure Data Lake Store-forrás
Adatok másolása az Azure Data Lake Store a, állítsa be a **adatforrástípust** a másolási tevékenység **AzureDataLakeStoreSource**, és adja meg a következő tulajdonságokat a **forrás**szakasz:

**AzureDataLakeStoreSource** támogatja a következő tulajdonságok **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát. |TRUE hamis (alapértelmezés) |Nem |

#### <a name="example-azuredatalakestoresource"></a>Példa: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

További információkért lásd: [Azure Data Lake Store összekötő](data-factory-azure-datalake-connector.md#copy-activity-properties) cikk.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>A másolási tevékenység az Azure Data Lake Store fogadó
Adatok másolása az Azure Data Lake Store, állítsa be a **típus gyűjtése** a másolási tevékenység **AzureDataLakeStoreSink**, és adja meg a következő tulajdonságokat a **fogadó** a szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| copyBehavior |Meghatározza a másolási viselkedését. |<b>PreserveHierarchy</b>: őrzi meg a fájl hierarchia a célmappában. A következő forrásfájl forrásmappához relatív elérési a relatív elérési út a cél-fájlját és a célmappa megegyezik.<br/><br/><b>FlattenHierarchy</b>: minden fájl a forrásmappából az első szintű célmappában jönnek létre. A cél fájlok jönnek létre automatikusan létrehozott névvel.<br/><br/><b>Mergefiles típusú</b>: egy fájl összes fájlt a forrásmappából egyesíti. Ha a fájl/Blob neve meg van adva, az egyesített neve legyen a megadott név; Ellenkező esetben lenne automatikusan létrehozott fájl nevét. |Nem |

#### <a name="example-azuredatalakestoresink"></a>Példa: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
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
        }]
    }
}
```

További információkért lásd: [Azure Data Lake Store összekötő](data-factory-azure-datalake-connector.md#copy-activity-properties) cikk. 

## <a name="azure-cosmos-db"></a>Azure Cosmos DB  

### <a name="linked-service"></a>Társított szolgáltatások
Egy Azure Cosmos DB meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **DocumentDb**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| connectionString |Adja meg Azure Cosmos DB adatbázishoz való kapcsolódáshoz szükséges adatokat. |Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
További információkért lásd: [Azure Cosmos DB összekötő](data-factory-azure-documentdb-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Adja meg az Azure Cosmos DB adatkészlethez, állítsa be a **típus** a DataSet **DocumentDbCollection**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| collectionName |Az Azure Cosmos DB gyűjtemény nevét. |Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
További információkért lásd: [Azure Cosmos DB összekötő](data-factory-azure-documentdb-connector.md#dataset-properties) cikk.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>A másolási tevékenység Azure Cosmos DB gyűjtemény forrás
Adatok másolása az Azure Cosmos adatbázisából, állítsa be a **adatforrástípust** a másolási tevékenység **DocumentDbCollectionSource**, és adja meg a következő tulajdonságokat a **forrás**szakasz:


| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Szükséges** |
| --- | --- | --- | --- |
| lekérdezés |Adja meg a lekérdezés adatainak olvasására. |Lekérdezés-karakterlánc hossza Azure Cosmos DB által támogatott. <br/><br/>Példa: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, az SQL-utasítást, amely végrehajtja a rendszer: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Jelzi, hogy a dokumentum van beágyazva speciális karakter |Bármely karakter. <br/><br/>Azure Cosmos-adatbázis egy NoSQL-tároló JSON-dokumentumok, amelyben beágyazott struktúrákat engedélyezett. Az Azure Data Factory lehetővé teszi, hogy a felhasználó nestingSeparator, amely használatával a hierarchia jelöléséhez "." a fenti példákban. A elválasztóval, a másolási tevékenység hoz létre a "Name" objektum három gyermekek elemekkel először középső és az utolsó, "Name.First", "Name.Middle" és "Name.Last" tábla definíciójában. |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Az Azure Cosmos DB gyűjtemény fogadó a másolási tevékenység
Adatok másolása az Azure Cosmos Adatbázishoz, állítsa be a **típus gyűjtése** a másolási tevékenység **DocumentDbCollectionSink**, és adja meg a következő tulajdonságokat a **fogadó** szakasz :

| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Szükséges** |
| --- | --- | --- | --- |
| nestingSeparator |A forrás oszlop nevét jelzi, hogy a beágyazott dokumentum egy különleges karakterek van szükség. <br/><br/>Például fent: `Name.First` a kimeneti táblát hoz létre a következő JSON struktúrában a Cosmos DB dokumentumban:<br/><br/>"Name": {<br/>    "Első": "John"<br/>}, |A beágyazási szinteket elválasztó karakter.<br/><br/>Alapértelmezett érték `.` (pont). |A beágyazási szinteket elválasztó karakter. <br/><br/>Alapértelmezett érték `.` (pont). |
| writeBatchSize |Dokumentumok létrehozásához Azure Cosmos DB szolgáltatás párhuzamos kérelmek száma.<br/><br/>A teljesítmény úgy finomhangolhatja, e tulajdonság használatával vagy az Azure Cosmos Adatbázisból adatok másolásakor. A jobb teljesítmény számíthat, mivel több párhuzamos kérelem Azure Cosmos DB writeBatchSize növelésével. Azonban kell elkerülheti a sávszélesség-szabályozás, amely képes throw a hibaüzenet a következő: "Ez nagy lekérő".<br/><br/>Sávszélesség-szabályozás tényező, beleértve a dokumentumok, a dokumentumok számát méretét, indexelő házirend célgyűjteményt stb határoz meg. A másolási műveletek segítségével jobban gyűjtemény (például S3) rendelkezik a legtöbb átviteli sebesség érhető el (2500 kérés egység/másodperc). |Egész szám |Nem (alapértelmezett: 5) |
| writeBatchTimeout |Várakozási idő a művelet befejezését, mielőtt azt az időkorlátot. |A TimeSpan<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

További információkért lásd: [Azure Cosmos DB összekötő](data-factory-azure-documentdb-connector.md#copy-activity-properties) cikk.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Társított szolgáltatások
Egy Azure SQL Database meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AzureSqlDatabase**, és adja meg a következő tulajdonságokat a **typeProperties** a szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| connectionString |Adja meg az Azure SQL Database-példányt a connectionString tulajdonság való kapcsolódáshoz szükséges adatokat. |Igen |

#### <a name="example"></a>Példa
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

További információkért lásd: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg az Azure SQL Database adatkészlethez, állítsa be a **típus** a DataSet **AzureSqlTable**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla vagy nézet társított szolgáltatásnak Azure SQL Database-példány neve hivatkozik. |Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
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
További információkért lásd: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#dataset-properties) cikk. 

### <a name="sql-source-in-copy-activity"></a>A másolási tevékenység SQL-forrás
Adatok másolása az Azure SQL-adatbázisból, állítsa be a **adatforrástípust** a másolási tevékenység **SqlSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Példa: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárás, amely adatokat olvas a forrástábla neve. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit. |A név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```
További információkért lásd: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#copy-activity-properties) cikk. 

### <a name="sql-sink-in-copy-activity"></a>A másolási tevékenység SQL fogadó
Adatok másolása az Azure SQL Database, állítsa be a **típus gyűjtése** a másolási tevékenység **SqlSink**, és adja meg a következő tulajdonságokat a **fogadó** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot. |A TimeSpan<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |
| writeBatchSize |Szúr be az SQL-tábla adatokat, amikor a puffer mérete eléri writeBatchSize. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |Adja meg egy lekérdezést a másolási tevékenység végrehajtása úgy, hogy egy adott szelet adatait. |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Adja meg a másolási tevékenység során automatikusan létrejön szelet azonosítóval, amely távolítja el az adatokat egy adott szelet, amikor futtassa újra a kitöltésének oszlop nevét. |Egy oszlop binary(32) adattípusú oszlop neve. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárás neve a cél táblázatba upserts (frissítés/Beszúrás) adatok. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit. |A név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |
| sqlWriterTableType |Adjon meg egy tábla típus a következő tárolt eljárás használható. Másolási tevékenység elérhetővé teszi az adatok áthelyezése egy ideiglenes táblát, amely a táblatípus. Tárolt eljárás kódot is majd egyesítheti az adatokat, a meglévő adatok másolásának. |Egy tábla környezettípus nevét. |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
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
        }]
    }
}
```

További információkért lásd: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#copy-activity-properties) cikk. 

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Társított szolgáltatások
Azure SQL Data Warehouse meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AzureSqlDW**, és adja meg a következő tulajdonságokat a **typeProperties** a szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| connectionString |Adja meg a connectionString tulajdonság az Azure SQL Data Warehouse-példány való kapcsolódáshoz szükséges adatokat. |Igen |



#### <a name="example"></a>Példa

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

További információkért lásd: [Azure SQL Data Warehouse-összekötő](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg az Azure SQL Data Warehouse adatkészlethez, állítsa be a **típus** a DataSet **AzureSqlDWTable**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla vagy nézet, az Azure SQL Data Warehouse-adatbázis hivatkozik a társított szolgáltatás neve. |Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
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

További információkért lásd: [Azure SQL Data Warehouse-összekötő](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) cikk. 

### <a name="sql-dw-source-in-copy-activity"></a>A másolási tevékenység SQL DW-forrás
Adatok másolása az Azure SQL Data Warehouse, állítsa be a **adatforrástípust** a másolási tevékenység **SqlDWSource**, és adja meg a következő tulajdonságokat a **forrás** a szakasz:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárás, amely adatokat olvas a forrástábla neve. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit. |A név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```

További információkért lásd: [Azure SQL Data Warehouse-összekötő](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) cikk. 

### <a name="sql-dw-sink-in-copy-activity"></a>A másolási tevékenység SQL DW fogadó
Adatok másolása az Azure SQL Data Warehouse, állítsa be a **típus gyűjtése** a másolási tevékenység **SqlDWSink**, és adja meg a következő tulajdonságokat a **fogadó** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Adja meg egy lekérdezést a másolási tevékenység végrehajtása úgy, hogy egy adott szelet adatait. |A lekérdezési utasítást. |Nem |
| allowPolyBase |Azt jelzi, hogy (ha alkalmazható), a PolyBase használata helyett BULKINSERT mechanizmus. <br/><br/> **Az ajánlott módszer az adatok betöltése az SQL Data Warehouse PolyBase a használata.** |True (Igaz) <br/>Hamis (alapértelmezés) |Nem |
| polyBaseSettings |Egy csoport, amely tulajdonságok megadott, amikor a **allowPolybase** tulajdonsága **igaz**. |&nbsp; |Nem |
| rejectValue |Megadja a szám vagy is el kell utasítani, mielőtt a lekérdezés nem sikerült sorokat százalékát. <br/><br/>További információ a PolyBase utasítsa el a beállítások elemre a **argumentumok** szakasza [külső tábla létrehozása (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) témakör. |0 (alapértelmezés), 1, 2... |Nem |
| rejectType |Határozza meg, hogy a rejectValue beállítás konstans értéket vagy százalékában van megadva. |Érték (alapértelmezett), százalékos aránya |Nem |
| rejectSampleValue |Mielőtt a PolyBase újraszámítja a visszautasított sorok százalékát beolvasandó sorok számát határozza meg. |1, 2, … |Igen, ha **rejectType** van **százalékos aránya** |
| useTypeDefault |Megadja, hogyan legyen kezelve tagolt szövegfájlok a hiányzó értékeket, amikor a PolyBase kér le adatokat a szövegfájlból.<br/><br/>Ezt a tulajdonságot, az argumentumok ismertető részben olvashat [létrehozása külső FÁJLFORMÁTUM (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |IGAZ, hamis (alapértelmezés) |Nem |
| writeBatchSize |Adatok beszúrása a SQL táblázatba, amikor a puffer mérete eléri writeBatchSize |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot. |A TimeSpan<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
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
        }]
    }
}
```

További információkért lásd: [Azure SQL Data Warehouse-összekötő](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) cikk. 

## <a name="azure-search"></a>Azure Search

### <a name="linked-service"></a>Társított szolgáltatások
Meghatározásához az Azure Search társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AzureSearch**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| url | Az Azure Search szolgáltatás URL-címe. | Igen |
| kulcs | Az Azure Search szolgáltatás adminisztrációs kulcsot. | Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

További információkért lásd: [Azure Search összekötő](data-factory-azure-search-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Adja meg az Azure Search adatkészlethez, állítsa be a **típus** a DataSet **AzureSearchIndex**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| type | A type tulajdonságot meg kell **AzureSearchIndex**.| Igen |
| indexName | Az Azure Search-index neve. Adat-előállító nem hoz létre az indexet. Az index az Azure Search léteznie kell. | Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

További információkért lásd: [Azure Search összekötő](data-factory-azure-search-connector.md#dataset-properties) cikk.

### <a name="azure-search-index-sink-in-copy-activity"></a>A másolási tevékenység az Azure Search-Index fogadó
Adatok másolása az Azure Search-index, állítsa be a **típus gyűjtése** a másolási tevékenység **AzureSearchIndexSink**, és adja meg a következő tulajdonságokat a **fogadó** a szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| -------- | ----------- | -------------- | -------- |
| writeBehavior | Megadja, hogy egyesíteni vagy cserélje le, ha az index már létezik egy dokumentumot. | Egyesítés (alapértelmezett)<br/>Feltöltés| Nem |
| WriteBatchSize | Amikor a puffer mérete eléri writeBatchSize feltölti az adatok be az Azure Search-index. | 1-1 000. Alapértelmezett érték 1000. | Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
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
        }]
    }
}
```

További információkért lásd: [Azure Search összekötő](data-factory-azure-search-connector.md#copy-activity-properties) cikk.

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatások két típusa van: Azure Storage társított szolgáltatásnak, és a társított szolgáltatásnak Azure Storage SAS.

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
A data factory használatával az Azure storage-fiók összekapcsolása a **fiókkulcs**, hozzon létre egy Azure Storage társított szolgáltatást. Meghatározásához az Azure Storage társított szolgáltatásnak, és állítsa a **típus** a társított szolgáltatás **AzureStorage**. Ezután megadhatja tulajdonságait a következő a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **AzureStorage** |Igen |
| connectionString |Adja meg a connectionString tulajdonság az Azure storage való kapcsolódáshoz szükséges adatokat. |Igen |

**Példa**  

```json
{  
    "name": "StorageLinkedService",  
    "properties": {  
        "type": "AzureStorage",  
        "typeProperties": {  
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
        }  
    }  
}  
```

#### <a name="azure-storage-sas-linked-service"></a>Az Azure Storage SAS társított szolgáltatás
A társított Azure Storage SAS-szolgáltatás lehetővé teszi egy Azure Storage-fiók összekapcsolása egy Azure data factory egy közös hozzáférésű Jogosultságkód (SAS) használatával. Az adat-előállítóban minden/specifikus erőforrások (blobtárolóban /) a tárolóban lévő korlátozott/időhöz kötött hozzáférést biztosít. Az Azure storage-fiók összekapcsolása egy adat-előállító közös hozzáférésű Jogosultságkód használatával, a társított Azure Storage SAS-szolgáltatás létrehozása. Egy Azure Storage SAS meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AzureStorageSas**. Ezután megadhatja tulajdonságait a következő a **typeProperties** szakasz:   

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **AzureStorageSas** |Igen |
| sasUri |Adja meg a megosztott hozzáférési aláírást URI az Azure Storage-erőforrások, például a blob, -tároló vagy tábla. |Igen |

**Példa**

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

További információ a következő összekapcsolt szolgáltatások: [Azure Table Storage összekötő](data-factory-azure-table-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg az Azure Table-adatkészlet, állítsa be a **típus** a DataSet **AzureTable**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |Az az Azure tábla adatbázispéldány táblájának, amelyre a társított szolgáltatás neve hivatkozik. |Igen. Amikor egy Táblanév egy azureTableSourceQuery nélkül van megadva, a tábla összes rekordot a cél lesz másolva. Ha egy azureTableSourceQuery is meg van adva, a cél a táblázatból, amely eleget tesz a lekérdezés rekordok lesz másolva. |

#### <a name="example"></a>Példa

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
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

További információ a következő összekapcsolt szolgáltatások: [Azure Table Storage összekötő](data-factory-azure-table-connector.md#dataset-properties) cikk. 

### <a name="azure-table-source-in-copy-activity"></a>A másolási tevékenység az Azure tábla forrása
Adatok másolása az Azure Table Storage, állítsa be a **adatforrástípust** a másolási tevékenység **AzureTableSource**, és adja meg a következő tulajdonságokat a **forrás** a szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| azureTableSourceQuery |Az egyéni lekérdezés segítségével adatokat olvasni. |Azure-tábla lekérdezési karakterlánc. Példák a következő szakaszban. |Nem. Amikor egy Táblanév egy azureTableSourceQuery nélkül van megadva, a tábla összes rekordot a cél lesz másolva. Ha egy azureTableSourceQuery is meg van adva, a cél a táblázatból, amely eleget tesz a lekérdezés rekordok lesz másolva. |
| azureTableSourceIgnoreTableNotFound |Azt jelzi, hogy a tábla kivétel swallow nem létezik. |IGAZ<br/>HAMIS |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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
        }]
    }
}
```

További információ a következő összekapcsolt szolgáltatások: [Azure Table Storage összekötő](data-factory-azure-table-connector.md#copy-activity-properties) cikk. 

### <a name="azure-table-sink-in-copy-activity"></a>A másolási tevékenység az Azure tábla fogadó
Adatok másolása az Azure Table Storage, állítsa be a **típus gyűjtése** a másolási tevékenység **AzureTableSink**, és adja meg a következő tulajdonságokat a **fogadó** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Alapértelmezett partíció kulcs értékét, amely a fogadó által használható. |Egy karakterlánc-érték. |Nem |
| azureTablePartitionKeyName |Adja meg az oszlop, amelynek értékeket fogja használni, mint partíciókulcsok nevét. Ha nincs megadva, a partíciós kulcs AzureTableDefaultPartitionKeyValue lesz. |Egy oszlop neve. |Nem |
| azureTableRowKeyName |Adja meg az oszlop, amelynek oszlop értékeit sor kulcsaként vannak használatban. Ha nincs megadva, minden egyes sorára használjon a GUID Azonosítót. |Egy oszlop neve. |Nem |
| azureTableInsertType |A mód lehet adatokat beszúrni az Azure-tábla.<br/><br/>Ez a tulajdonság szabja meg, hogy rendelkeznek-e a meglévő sorokat a táblában az egyező partíció-és sorkulcsok cseréje vagy egyesített értékükre. <br/><br/>Ezeket a beállításokat (lemezegyesítési és -csere) működése, lásd: [Insert vagy az egyesítéses entitás](https://msdn.microsoft.com/library/azure/hh452241.aspx) és [Insert vagy az entitás cseréje](https://msdn.microsoft.com/library/azure/hh452242.aspx) témaköröket. <br/><br> Ez a beállítás a sor szintjén, a táblázatok szintjén nem vonatkozik, és sem a lehetőség törli a kimeneti táblához, amely nem szerepel a bemeneti sorokat. |Egyesítés (alapértelmezett)<br/>cserélje le |Nem |
| writeBatchSize |Amikor writeBatchSize vagy writeBatchTimeout találati adatok beillesztése az Azure-tábla. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Adatok szúr be az Azure-táblázatra, ha a writeBatchSize vagy writeBatchTimeout találati |A TimeSpan<br/><br/>Példa: "00: 20:00" (20 perc) |Nem (alapértelmezett tároló ügyfél alapértelmezett időtúllépési érték 90 másodperc) |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
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
        }]
    }
}
```
További információ a következő összekapcsolt szolgáltatások: [Azure Table Storage összekötő](data-factory-azure-table-connector.md#copy-activity-properties) cikk. 

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Társított szolgáltatások
Az Amazon Redshift meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AmazonRedshift**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz :  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |Kiszolgáló IP-címét vagy állomásnevét kiszolgálónevét az Amazon Redshift. |Igen |
| port |A TCP-portot, amelyen az Amazon Redshift kiszolgáló ügyfélkapcsolatokat száma. |Nem, alapértelmezett érték: 5439 |
| adatbázis |Az Amazon Redshift adatbázis nevét. |Igen |
| felhasználónév |Felhasználó, aki hozzáfér az adatbázis neve. |Igen |
| jelszó |A felhasználói fiók jelszavát. |Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

További információkért lásd: [Amazon Redshift összekötő](#data-factory-amazon-redshift-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg az Amazon Redshift adatkészlethez, állítsa be a **típus** a DataSet **RelationalTable**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla az Amazon Redshift adatbázisban, amelyre a társított szolgáltatás neve hivatkozik. |Nem (Ha **lekérdezés** a **RelationalSource** van megadva) |


#### <a name="example"></a>Példa

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
További információkért lásd: [Amazon Redshift összekötő](#data-factory-amazon-redshift-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs adatforrás 
Adatok másolása az Amazon Redshift, állítsa be a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: `select * from MyTable`. |Nem (Ha **tableName** a **dataset** van megadva) |

#### <a name="example"></a>Példa

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
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
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
További információkért lásd: [Amazon Redshift összekötő](#data-factory-amazon-redshift-connector.md#copy-activity-properties) cikk.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Társított szolgáltatások
Az IBM DB2 meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **OnPremisesDB2**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |A DB2-kiszolgáló neve. |Igen |
| adatbázis |Neve a DB2-adatbázishoz. |Igen |
| Séma |Az adatbázisban séma neve. A séma neve a kis-és nagybetűket. |Nem |
| authenticationType |A DB2-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek a következők: névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet Basic vagy Windows-hitelesítés használata. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| gatewayName |Az átjáró, amely használatával a Data Factory szolgáltatásnak csatlakoznia a helyszíni DB2-adatbázishoz való kapcsolódáshoz neve. |Igen |

#### <a name="example"></a>Példa
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
További információkért lásd: [IBM DB2-összekötő](#data-factory-onprem-db2-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Adja meg a DB2 adatkészletet, állítsa be a **típus** a DataSet **RelationalTable**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla a DB2-adatbázishoz példány, amelyre a társított szolgáltatás neve hivatkozik. A táblanév a kis-és nagybetűket. |Nem (Ha **lekérdezés** a **RelationalSource** van megadva) 

#### <a name="example"></a>Példa
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
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

További információkért lásd: [IBM DB2-összekötő](#data-factory-onprem-db2-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs adatforrás
Adatok másolása az IBM DB2, állítsa be a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: `"query": "select * from "MySchema"."MyTable""`. |Nem (Ha **tableName** a **dataset** van megadva) |

#### <a name="example"></a>Példa
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
További információkért lásd: [IBM DB2-összekötő](#data-factory-onprem-db2-connector.md#copy-activity-properties) cikk.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Társított szolgáltatások
Egy MySQL meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **OnPremisesMySql**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |A MySQL-kiszolgáló neve. |Igen |
| adatbázis |A MySQL-adatbázis neve. |Igen |
| Séma |Az adatbázisban séma neve. |Nem |
| authenticationType |A MySQL-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek a következők: `Basic`. |Igen |
| felhasználónév |Adja meg a felhasználónevet a MySQL-adatbázishoz való kapcsolódáshoz. |Igen |
| jelszó |Adja meg a megadott felhasználói fiók jelszavát. |Igen |
| gatewayName |Az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia való kapcsolódáshoz a helyszíni MySQL-adatbázis neve. |Igen |

#### <a name="example"></a>Példa

```json
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

További információkért lásd: [MySQL összekötő](data-factory-onprem-mysql-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg a MySQL adatkészletet, állítsa be a **típus** a DataSet **RelationalTable**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla a MySQL-adatbázis-példány, amelyre a társított szolgáltatás neve hivatkozik. |Nem (Ha **lekérdezés** a **RelationalSource** van megadva) |

#### <a name="example"></a>Példa

```json
{
    "name": "MySqlDataSet",
    "properties": {
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
További információkért lásd: [MySQL összekötő](data-factory-onprem-mysql-connector.md#dataset-properties) cikk. 

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs adatforrás
Adatok másolása a MySQL-adatbázis, állítsa be a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: `select * from MyTable`. |Nem (Ha **tableName** a **dataset** van megadva) |


#### <a name="example"></a>Példa
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
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
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

További információkért lásd: [MySQL összekötő](data-factory-onprem-mysql-connector.md#copy-activity-properties) cikk. 

## <a name="oracle"></a>Oracle 

### <a name="linked-service"></a>Társított szolgáltatások
Az Oracle meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **OnPremisesOracle**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| driverType | Adja meg, melyik illesztőprogram használatával másolja az adatokat, vagy Oracle-adatbázishoz. Két érték engedélyezett **Microsoft** vagy **ODP** (alapértelmezett). Lásd: [verziójától és a telepítés támogatott](#supported-versions-and-installation) illesztőprogram adatai szakaszban. | Nem |
| connectionString | Adja meg az Oracle adatbázispéldányt a connectionString tulajdonság való kapcsolódáshoz szükséges adatokat. | Igen |
| gatewayName | Azon átjáró neve, amely a helyszíni Oracle-kiszolgálóhoz való csatlakozáshoz használt |Igen |

#### <a name="example"></a>Példa
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

További információkért lásd: [Oracle összekötő](data-factory-onprem-oracle-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Adja meg az Oracle-adatkészlet, állítsa be a **típus** a DataSet **OracleTable**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla az Oracle-adatbázishoz, amely hivatkozik a társított szolgáltatás neve. |Nem (Ha **oracleReaderQuery** a **OracleSource** van megadva) |

#### <a name="example"></a>Példa

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
            "anchorDateTime": "2016-02-27T12:00:00",
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
További információkért lásd: [Oracle összekötő](data-factory-onprem-oracle-connector.md#dataset-properties) cikk.

### <a name="oracle-source-in-copy-activity"></a>A másolási tevékenység Oracle-forrás
Adatok másolása az Oracle-adatbázishoz, állítsa be a **adatforrástípust** a másolási tevékenység **OracleSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| oracleReaderQuery |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például:`select * from MyTable` <br/><br/>Ha nincs megadva, az SQL-utasítást, amely végrehajtja a rendszer: `select * from MyTable` |Nem (Ha **tableName** a **dataset** van megadva) |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
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
        }]
    }
}
```

További információkért lásd: [Oracle összekötő](data-factory-onprem-oracle-connector.md#copy-activity-properties) cikk.

### <a name="oracle-sink-in-copy-activity"></a>A másolási tevékenység Oracle fogadó
Adatok másolása az Oracle-adatbázishoz am, állítsa be a **típus gyűjtése** a másolási tevékenység **OracleSink**, és adja meg a következő tulajdonságokat a **fogadó** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot. |A TimeSpan<br/><br/> . Példa: 00:30:00 (30 perc). |Nem |
| writeBatchSize |Szúr be az SQL-tábla adatokat, amikor a puffer mérete eléri writeBatchSize. |Egész szám (sorok száma) |Nem (alapértelmezett: 100) |
| sqlWriterCleanupScript |Adja meg egy lekérdezést a másolási tevékenység végrehajtása úgy, hogy egy adott szelet adatait. |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Adja meg a másolási tevékenység során automatikusan létrejön szelet azonosító, amely segítségével távolítja el az adatokat egy adott szelet, amikor futtassa újra a töltse ki az oszlopnevet. |Egy oszlop binary(32) adattípusú oszlop neve. |Nem |

#### <a name="example"></a>Példa
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
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
        }]
    }
}
```
További információkért lásd: [Oracle összekötő](data-factory-onprem-oracle-connector.md#copy-activity-properties) cikk.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Társított szolgáltatások
Egy PostgreSQL meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **OnPremisesPostgreSql**, és adja meg a következő tulajdonságokat a **typeProperties** a szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |A PostgreSQL-kiszolgáló neve. |Igen |
| adatbázis |A PostgreSQL-adatbázis neve. |Igen |
| Séma |Az adatbázisban séma neve. A séma neve a kis-és nagybetűket. |Nem |
| authenticationType |A PostgreSQL-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek a következők: névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet Basic vagy Windows-hitelesítés használata. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| gatewayName |Az átjáró, amely használatával a Data Factory szolgáltatásnak csatlakoznia a helyszíni PostgreSQL-adatbázishoz való kapcsolódáshoz neve. |Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
További információkért lásd: [PostgreSQL összekötő](data-factory-onprem-postgresql-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Adja meg egy PostgreSQL-adatkészlet, állítsa be a **típus** a DataSet **RelationalTable**, adja meg a következő tulajdonságokat és a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla PostgreSQL-adatbázispéldány, amelyre a társított szolgáltatás neve hivatkozik. A táblanév a kis-és nagybetűket. |Nem (Ha **lekérdezés** a **RelationalSource** van megadva) |

#### <a name="example"></a>Példa
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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
További információkért lásd: [PostgreSQL összekötő](data-factory-onprem-postgresql-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs adatforrás
Adatok másolása egy PostgreSQL-adatbázisból, állítsa be a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** a szakasz:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: "query": "Válasszon * a \"MySchema\".\" MyTable\"". |Nem (Ha **tableName** a **dataset** van megadva) |

#### <a name="example"></a>Példa

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

További információkért lásd: [PostgreSQL összekötő](data-factory-onprem-postgresql-connector.md#copy-activity-properties) cikk.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Társított szolgáltatások
Egy SAP Business Warehouse (BW) meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **SapBw**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz :  

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
kiszolgáló | A kiszolgálóra az SAP BW-példány neve. | sztring | Igen
systemNumber | Az SAP BW rendszer rendszer száma. | Kétjegyű tizedes tört karakterláncból. | Igen
clientId | Az ügyfél számára a SAP W rendszer ügyfél-azonosítója. | Három számjegyből tizedes tört karakterláncból. | Igen
felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Igen
jelszó | A felhasználó jelszavát. | sztring | Igen
gatewayName | Az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia a helyszíni SAP BW-példányra neve. | sztring | Igen
encryptedCredential | A titkosított hitelesítő adatokban karakterlánc. | sztring | Nem

#### <a name="example"></a>Példa

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

További információkért lásd: [SAP Business Warehouse-összekötő](data-factory-sap-business-warehouse-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg egy SAP BW dataset, állítsa be a **típus** a DataSet **RelationalTable**. Nincsenek az SAP BW adatkészlet típusú támogatott típusra vonatkozó tulajdonságok **RelationalTable**.  

#### <a name="example"></a>Példa

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
További információkért lásd: [SAP Business Warehouse-összekötő](data-factory-sap-business-warehouse-connector.md#dataset-properties) cikk. 

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs adatforrás
Adatok másolása az SAP Business Warehouse, állítsa be a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** a szakasz:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés | Meghatározza az MDX-lekérdezés adatokat olvasni az SAP BW-példány. | MDX-lekérdezés. | Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

További információkért lásd: [SAP Business Warehouse-összekötő](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) cikk. 

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Társított szolgáltatások
Egy SAP HANA meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **SapHana**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
kiszolgáló | A kiszolgálóra az SAP HANA-példány neve. Ha a kiszolgáló egy testreszabott portot használ, adja meg a `server:port`. | sztring | Igen
authenticationType | Hitelesítés típusa. | karakterlánc. "Basic" vagy "Windows" | Igen 
felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Igen
jelszó | A felhasználó jelszavát. | sztring | Igen
gatewayName | Az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia való kapcsolódáshoz a helyszíni SAP HANA-példány neve. | sztring | Igen
encryptedCredential | A titkosított hitelesítő adatokban karakterlánc. | sztring | Nem

#### <a name="example"></a>Példa

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
További információkért lásd: [SAP HANA-összekötő](data-factory-sap-hana-connector.md#linked-service-properties) cikk.
 
### <a name="dataset"></a>Adathalmaz
Adja meg egy SAP HANA-adatkészlet, állítsa be a **típus** a DataSet **RelationalTable**. Nincsenek az SAP HANA-adatkészlet típusú támogatott típusra vonatkozó tulajdonságok **RelationalTable**. 

#### <a name="example"></a>Példa

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
További információkért lásd: [SAP HANA-összekötő](data-factory-sap-hana-connector.md#dataset-properties) cikk. 

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs adatforrás
Adatok másolása egy SAP HANA adattárból, állítsa be a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** a szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés | Adja meg az adatokat olvasni az SAP HANA-példány az SQL-lekérdezést. | SQL-lekérdezésben. | Igen |


#### <a name="example"></a>Példa


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

További információkért lásd: [SAP HANA-összekötő](data-factory-sap-hana-connector.md#copy-activity-properties) cikk.


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Társított szolgáltatások
Típusú társított szolgáltatás létrehozása **OnPremisesSqlServer** a helyszíni SQL Server adatbázis összekapcsolása egy adat-előállítóban. A következő táblázat a JSON-elemek szerepelnek a helyszíni SQL Server kapcsolódó szolgáltatásra vonatkozó leírást.

A következő táblázat a JSON-elemek szerepelnek kapcsolódó SQL Server-szolgáltatásra vonatkozó leírást.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell megadni: **OnPremisesSqlServer**. |Igen |
| connectionString |Az SQL-hitelesítéssel vagy a Windows-hitelesítés a helyszíni SQL Server adatbázishoz való kapcsolódáshoz szükséges connectionString információkat adják meg. |Igen |
| gatewayName |Neve az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia kell a helyszíni SQL Server adatbázishoz. |Igen |
| felhasználónév |Adja meg a felhasználónevet, ha a Windows-hitelesítést használ. Példa: **tartománynév\\felhasználónév**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |

Hitelesítő adatok használatával titkosíthatja az **New-AzureRmDataFactoryEncryptValue** parancsmag és a következő példában látható módon használhatja őket a kapcsolódási karakterláncban (**EncryptedCredential** tulajdonság):  

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Példa: JSON az SQL-hitelesítéssel

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Példa: JSON a Windows-hitelesítés használatával

Ha a felhasználónév és jelszó van adva, átjáró őket a megszemélyesítéshez használ a megadott felhasználói fióknak a helyi SQL Server-adatbázishoz való kapcsolódáshoz. Ellenkező esetben átjáró csatlakozik az SQL Server közvetlenül az átjáró (az indítási fiókjához) biztonsági környezetében.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

További információkért lásd: [SQL Server-összekötő](data-factory-sqlserver-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg az SQL Server dataset, állítsa be a **típus** a DataSet **SqlServerTable**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla vagy nézet, amelyre a társított szolgáltatás SQL Server adatbázis-példány neve hivatkozik. |Igen |

#### <a name="example"></a>Példa
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
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

További információkért lásd: [SQL Server-összekötő](data-factory-sqlserver-connector.md#dataset-properties) cikk. 

### <a name="sql-source-in-copy-activity"></a>A másolási tevékenység SQL-forrás
Adatok másolása egy SQL Server-adatbázisból, állítsa be a **adatforrástípust** a másolási tevékenység **SqlSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: `select * from MyTable`. Előfordulhat, hogy a bemeneti adatkészlet által hivatkozott adatbázishoz több táblát is hivatkozik. Ha nincs megadva, az SQL-utasítás végrehajtott: táblanév kiválaszthatja. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárás, amely adatokat olvas a forrástábla neve. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit. |A név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |

Ha a **sqlReaderQuery** van megadva a SqlSource, a másolási tevékenység során ez a lekérdezés fut az SQL Server-adatbázis forrás az adatok eléréséhez.

Másik lehetőségként megadhat tárolt eljárás megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméterek fogadja el).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, struktúra szakaszában meghatározott oszlopokat válassza futtatni az SQL Server adatbázis-lekérdezés összeállításához használt. Az adatkészlet-definícióban nem rendelkezik a struktúra, ha minden kiválasztott oszlop. a táblából.

> [!NOTE]
> Amikor **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy értéket a **tableName** az adatkészlet JSON tulajdonság. Nincs érvényesítést hajt végre ezt a táblázatot, ha van.


#### <a name="example"></a>Példa
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```

Ebben a példában **sqlReaderQuery** a SqlSource van megadva. A másolási tevékenység során ez a lekérdezés fut az SQL Server-adatbázis forrás az adatok eléréséhez. Másik lehetőségként megadhat tárolt eljárás megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméterek fogadja el). A sqlReaderQuery hivatkozhat több táblák az adatbázisban a következő bemeneti adatkészlet hivatkozik. Nincs korlátozva csak a tábla a dataset tableName typeProperty állítja be.

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, struktúra szakaszában meghatározott oszlopokat válassza futtatni az SQL Server adatbázis-lekérdezés összeállításához használt. Az adatkészlet-definícióban nem rendelkezik a struktúra, ha minden kiválasztott oszlop. a táblából.

További információkért lásd: [SQL Server-összekötő](data-factory-sqlserver-connector.md#copy-activity-properties) cikk. 

### <a name="sql-sink-in-copy-activity"></a>A másolási tevékenység SQL fogadó
Adatok másolása az SQL Server-adatbázishoz, állítsa be a **típus gyűjtése** a másolási tevékenység **SqlSink**, és adja meg a következő tulajdonságokat a **fogadó** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot. |A TimeSpan<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |
| writeBatchSize |Szúr be az SQL-tábla adatokat, amikor a puffer mérete eléri writeBatchSize. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |Adja meg a lekérdezést úgy, hogy egy adott szelet adatait végrehajtásához másolási tevékenységhez. További információkért lásd: [ismételhetőség](#repeatability-during-copy) szakasz. |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Adja meg a másolási tevékenység során automatikusan létrejön szelet azonosító, amely segítségével távolítja el az adatokat egy adott szelet, amikor futtassa újra a töltse ki az oszlopnevet. További információkért lásd: [ismételhetőség](#repeatability-during-copy) szakasz. |Egy oszlop binary(32) adattípusú oszlop neve. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárás neve a cél táblázatba upserts (frissítés/Beszúrás) adatok. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit. |A név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |
| sqlWriterTableType |Adja meg a tárolt eljárásban használandó tábla neve. Másolási tevékenység elérhetővé teszi az adatok áthelyezése egy ideiglenes táblát, amely a táblatípus. Tárolt eljárás kódot is majd egyesítheti az adatokat, a meglévő adatok másolásának. |Egy tábla környezettípus nevét. |Nem |

#### <a name="example"></a>Példa
A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **BlobSource** és **fogadó** típusúra **SqlSink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
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
        }]
    }
}
```

További információkért lásd: [SQL Server-összekötő](data-factory-sqlserver-connector.md#copy-activity-properties) cikk. 

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Társított szolgáltatások
Adja meg a Sybase társított a szolgáltatás, állítsa be a **típus** a társított szolgáltatás **OnPremisesSybase**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |A Sybase-kiszolgáló neve. |Igen |
| adatbázis |Neve a Sybase-adatbázishoz. |Igen |
| Séma |Az adatbázisban séma neve. |Nem |
| authenticationType |A Sybase-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek a következők: névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet Basic vagy Windows-hitelesítés használata. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| gatewayName |Az átjáró, amely használatával a Data Factory szolgáltatásnak csatlakoznia a helyszíni Sybase-adatbázishoz való kapcsolódáshoz neve. |Igen |

#### <a name="example"></a>Példa
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

További információkért lásd: [Sybase összekötő](data-factory-onprem-sybase-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg a Sybase adatkészletet, állítsa be a **típus** a DataSet **RelationalTable**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla a Sybase-adatbázishoz példány, amelyre a társított szolgáltatás neve hivatkozik. |Nem (Ha **lekérdezés** a **RelationalSource** van megadva) |

#### <a name="example"></a>Példa

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
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

További információkért lásd: [Sybase összekötő](data-factory-onprem-sybase-connector.md#dataset-properties) cikk. 

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs adatforrás
Adatok másolása egy Sybase-adatbázisból, állítsa be a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz :


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: `select * from MyTable`. |Nem (Ha **tableName** a **dataset** van megadva) |

#### <a name="example"></a>Példa

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

További információkért lásd: [Sybase összekötő](data-factory-onprem-sybase-connector.md#copy-activity-properties) cikk.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Társított szolgáltatások
Adja meg a teradata rendszerhez társított a szolgáltatás, állítsa be a **típus** a társított szolgáltatás **OnPremisesTeradata**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |A Teradata-kiszolgáló neve. |Igen |
| authenticationType |A Teradata-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek a következők: névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet Basic vagy Windows-hitelesítés használata. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| gatewayName |Az átjáró, amely használatával a Data Factory szolgáltatásnak csatlakoznia a helyszíni Teradata-adatbázishoz való kapcsolódáshoz neve. |Igen |

#### <a name="example"></a>Példa
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

További információkért lásd: [Teradata összekötő](data-factory-onprem-teradata-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Adja meg a Teradata-Blob adatkészletet, állítsa be a **típus** a DataSet **RelationalTable**. Jelenleg nem támogatott a Teradata-adatkészlet tulajdonságokat. 

#### <a name="example"></a>Példa
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
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

További információkért lásd: [Teradata összekötő](data-factory-onprem-teradata-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs adatforrás
Adatok másolása egy Teradata-adatbázisból, állítsa be a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** a szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: `select * from MyTable`. |Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

További információkért lásd: [Teradata összekötő](data-factory-onprem-teradata-connector.md#copy-activity-properties) cikk.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Társított szolgáltatások
Kapcsolódó Cassandra szolgáltatás definiálásához, állítsa be a **típus** a társított szolgáltatás **OnPremisesCassandra**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| gazdagép |Egy vagy több IP-címek vagy Cassandra kiszolgálók állomás nevét.<br/><br/>IP-címek vagy állomásnevek kiszolgálókhoz való kapcsolódáshoz összes egyidejűleg vesszővel tagolt listáját adja meg. |Igen |
| port |A TCP-portot, amelyen a Cassandra kiszolgáló ügyfélkapcsolatokat. |Nem, alapértelmezett érték: 9042 |
| authenticationType |Basic vagy Anonymous |Igen |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha authenticationType beállítása alapszintű. |
| jelszó |Adja meg a felhasználói fiók jelszavát. |Igen, ha authenticationType beállítása alapszintű. |
| gatewayName |A helyszíni Cassandra adatbázishoz való csatlakozáshoz használt átjáró neve. |Igen |
| encryptedCredential |Az átjáró által titkosított hitelesítő. |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

További információkért lásd: [Cassandra összekötő](data-factory-onprem-cassandra-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Cassandra dataset határozza meg, állítsa be a **típus** a DataSet **CassandraTable**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kulcstérértesítések használatával |Kulcstérértesítések használatával vagy séma Cassandra adatbázis nevét. |Igen (Ha **lekérdezés** a **CassandraSource** nincs definiálva). |
| tableName |A tábla Cassandra adatbázis neve. |Igen (Ha **lekérdezés** a **CassandraSource** nincs definiálva). |

#### <a name="example"></a>Példa

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
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

További információkért lásd: [Cassandra összekötő](data-factory-onprem-cassandra-connector.md#dataset-properties) cikk. 

### <a name="cassandra-source-in-copy-activity"></a>A másolási tevékenység Cassandra forrás
Adatok másolása az Cassandra, állítsa be a **adatforrástípust** a másolási tevékenység **CassandraSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-92 vagy CQL lekérdezés. Lásd: [CQL hivatkozás](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL-lekérdezés használata esetén adja meg a **kulcstérértesítések használatával name.table neve** a lekérdezni kívánt táblázat képviseli. |Nem (ha van megadva a tableName és a dataset kulcstérértesítések használatával). |
| consistencyLevel |A konzisztencia szint határozza meg, hány replikák adatok visszatér az ügyfélalkalmazás egy olvasási kérést kell válaszolnia. Cassandra ellenőrzi a megadott számú replikákat az adatok a olvasási kérelem teljesítéséhez. |EGY, KETTŐ, HÁROM, KVÓRUM, AZ ÖSSZES, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Lásd: [konfigurálása az adatok konzisztenciájának](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) részleteiről. |Nem. Alapértelmezett érték: egyet. |

#### <a name="example"></a>Példa
  
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
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
        }]
    }
}
```

További információkért lásd: [Cassandra összekötő](data-factory-onprem-cassandra-connector.md#copy-activity-properties) cikk.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Társított szolgáltatások
A MongoDB meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **OnPremisesMongoDB**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |Kiszolgáló IP-címét vagy állomásnevét kiszolgálónevét a mongodb-Protokolltámogatással. |Igen |
| port |A MongoDB-kiszolgálóhoz a kapcsolatok figyelésére használt TCP portot. |Nem kötelező, alapértelmezett érték: 27017 |
| authenticationType |Alapszintű, vagy névtelen. |Igen |
| felhasználónév |Felhasználói fiók MongoDB eléréséhez. |Igen (Ha alapszintű hitelesítést használ). |
| jelszó |A felhasználó jelszavát. |Igen (Ha alapszintű hitelesítést használ). |
| authSource |A MongoDB-adatbázist, amely a hitelesítő adatok kereséséhez használni kívánt nevét. |Választható (Ha alapszintű hitelesítést használ). alapértelmezett: a rendszergazdai fiókot és a databaseName tulajdonsággal megadott adatbázis használ. |
| databaseName |A MongoDB-adatbázist, amely az elérni kívánt nevét. |Igen |
| gatewayName |Az átjáró, aki hozzáfér az adattár neve. |Igen |
| encryptedCredential |A hitelesítőadat-átjáró által titkosított. |Optional |

#### <a name="example"></a>Példa

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

További információkért lásd: [MongoDB összekötő cikk](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Adathalmaz
Adja meg a MongoDB adatkészletet, állítsa be a **típus** a DataSet **MongoDbCollection**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| collectionName |A MongoDB-adatbázist a gyűjtemény nevét. |Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

További információkért lásd: [MongoDB összekötő cikk](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>A másolási tevékenység MongoDB-forrás
Adatok másolása a MongoDB, állítsa be a **adatforrástípust** a másolási tevékenység **MongoDbSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-92 lekérdezési karakterlánc. Például: `select * from MyTable`. |Nem (Ha **collectionName** a **dataset** van megadva) |

#### <a name="example"></a>Példa

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

További információkért lásd: [MongoDB összekötő cikk](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Társított szolgáltatások
Az Amazon S3 meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AwsAccessKey**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| accessKeyID |A titkos hívóbetű azonosítója. |sztring |Igen |
| secretAccessKey |A titkos hívóbetű magát. |Titkosított titkos karakterlánc |Igen |

#### <a name="example"></a>Példa
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

További információkért lásd: [Amazon S3 összekötő cikk](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Adathalmaz
Adja meg az Amazon S3 adatkészlethez, állítsa be a **típus** a DataSet **AmazonS3**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| bucketName |S3 gyűjtő neve. |Sztring |Igen |
| kulcs |S3 objektum kulcsa. |Sztring |Nem |
| előtag |S3 objektum kulcshoz előtag. Kiválasztott objektumok, amelynek kulcsait a előtaggal kezdődik. Érvényes, csak ha kulcsa üres. |Sztring |Nem |
| verzió: |Ha engedélyezve van a S3 versioning S3 objektum verziója. |Sztring |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem | |
| tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. A támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem | |


> [!NOTE]
> bucketName + kulcs elérési útja a S3 objektum, ahol a gyűjtő S3 objektumok a legfelső szintű tárolója, és a kulcs S3 objektum teljes elérési útja.

#### <a name="example-sample-dataset-with-prefix"></a>Példa: Minta-adatkészleteken előtaggal

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Példa: Mintát adatkészletre (verziójával)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Példa: A dinamikus útvonalak S3
A minta rögzített értékeket használjuk az Amazon S3 adatkészlet kulcs és bucketName tulajdonságok.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

A kulcs és a futási időben dinamikusan bucketName kiszámításához rendszerváltozók SliceStart például a Data Factory lehet.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Az Amazon S3 adatkészlethez előtag tulajdonságának azonos teheti meg. Lásd: [adat-előállító funkciók és rendszerváltozók](data-factory-functions-variables.md) támogatott funkciók és változók listáját.

További információkért lásd: [Amazon S3 összekötő cikk](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>A másolási tevékenység rendszer forrás
Adatok másolása az Amazon S3, állítsa be a **adatforrástípust** a másolási tevékenység **FileSystemSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| rekurzív |Határozza meg, hogy a rekurzív módon listában S3 objektumok a könyvtárban. |Igaz/hamis |Nem |


#### <a name="example"></a>Példa


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

További információkért lásd: [Amazon S3 összekötő cikk](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Fájlrendszer


### <a name="linked-service"></a>Társított szolgáltatások
Egy helyszíni fájlrendszer hozzákapcsolhatja egy az Azure data factory, a **a helyi fájlkiszolgáló** társított szolgáltatás. A következő táblázat ismerteti, amelyek a helyszíni fájl kiszolgálóhoz kapcsolódó szolgáltatásra vonatkozó JSON-elemek szerepelnek.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |Győződjön meg arról, hogy a type tulajdonság értéke **OnPremisesFileServer**. |Igen |
| gazdagép |Adja meg a legfelső szintű mappa elérési útját, amelyet szeretne másolni. Az escape-karakter használata "\" a speciális karakterek a karakterláncban. Lásd: [minta kapcsolódó szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat. |Igen |
| felhasználói azonosítóját |Adja meg a felhasználó, aki hozzáfér a kiszolgáló Azonosítóját. |Nem (Ha úgy dönt, hogy encryptedCredential) |
| jelszó |Adja meg a felhasználó (userid) jelszavát. |Nem (Ha úgy dönt, hogy encryptedCredential |
| encryptedCredential |Adja meg a titkosított hitelesítő adatokat kaphat a New-AzureRmDataFactoryEncryptValue parancsmag futtatásával. |Nem (Ha úgy dönt, hogy adja meg a felhasználói azonosítót és jelszót a szövegként) |
| gatewayName |Megadja a Data Factory kell csatlakozni a helyi fájlkiszolgálón használó átjáró nevét. |Igen |

#### <a name="sample-folder-path-definitions"></a>A minta mappa elérési útja definíciók 
| Forgatókönyv | A társított szolgáltatás definíciójának üzemeltetéséhez | Az adatkészlet-definícióban folderPath |
| --- | --- | --- |
| Az adatkezelési átjáró gépen helyi mappában: <br/><br/>Példák: D:\\ \* vagy D:\folder\subfolder\\* |D:\\ \\ (az adatok felügyeleti átjáró 2.0-s és újabb verziók) <br/><br/> a localhost (korábbi verzióihoz mint adatok felügyeleti átjáró 2.0-s) |. \\ \\ vagy mappa\\\\almappa (az adatok felügyeleti átjáró 2.0-s és újabb verziók) <br/><br/>D:\\ \\ vagy D:\\\\mappa\\\\almappa (az átjáró verziója alatt 2.0-s) |
| Távoli megosztott mappa: <br/><br/>Példák: \\ \\myserver\\megosztása\\ \* vagy \\ \\myserver\\megosztása\\mappa\\almappa\\* |\\\\\\\\myserver\\\\megosztása |. \\ \\ vagy mappa\\\\almappa |


#### <a name="example-using-username-and-password-in-plain-text"></a>Példa: Felhasználónév és jelszó használatával egyszerű szöveges

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Példa: Encryptedcredential használatával

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

További információkért lásd: [fájlrendszer összekötő cikk](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Adathalmaz
Adja meg a fájlrendszer adatkészletet, állítsa be a **típus** a DataSet **fájlmegosztási**, adja meg a következő tulajdonságokat és a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Adja meg a részleges elérési útja a mappához. Az escape-karakter használata "\" a speciális karakterek a karakterláncban. Lásd: [minta kapcsolódó szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat.<br/><br/>Ez a tulajdonság a kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg ehhez a tulajdonsághoz értéket, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Nincs megadva fájlnév egy kimeneti adatkészletet, a létrehozott fájl neve esetén a következő formátumban: <br/><br/>`Data.<Guid>.txt` (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nem |
| fileFilter |Adjon meg egy szűrőt, amely minden fájl helyett a fájlok Tárolónév részhalmazának kiválasztására szolgál. <br/><br/>Két érték engedélyezett: `*` (több karaktert) és `?` (egyetlen karakter).<br/><br/>1. példa: "fileFilter": "* .log"<br/>2. példa: "fileFilter": 2016 - 1-?. txt"<br/><br/>Vegye figyelembe, hogy fileFilter egy bemeneti fájlmegosztási adatkészlet esetében alkalmazható. |Nem |
| partitionedBy |PartitionedBy segítségével adjon meg egy dinamikus folderPath/fájlnevet idősorozat adatok. Példa: az adatok óránkénti paraméteres folderPath. |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**; és a támogatott szintek a következők: **Optimal** és **leggyorsabb**. Lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

> [!NOTE]
> Nem használható egyszerre fájlnév és fileFilter.

#### <a name="example"></a>Példa

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
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

További információkért lásd: [fájlrendszer összekötő cikk](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>A másolási tevékenység rendszer forrás
Adatok másolása a fájlrendszerből, állítsa be a **adatforrástípust** a másolási tevékenység **FileSystemSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákat, illetve csak a megadott mappát. |IGAZ, hamis (alapértelmezés) |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
        }]
    }
}
```
További információkért lásd: [fájlrendszer összekötő cikk](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>A másolási tevékenység gyűjtése fájlrendszer
Adatok másolása fájlrendszerre, állítsa be a **típus gyűjtése** a másolási tevékenység **FileSystemSink**, és adja meg a következő tulajdonságokat a **fogadó** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| copyBehavior |Másolás viselkedését határozza meg, ha az adatforrás BlobSource vagy a fájlrendszer. |**PreserveHierarchy:** őrzi meg a fájl hierarchia a célmappában. Ez azt jelenti, hogy a forrásfájl, a forrásmappához relatív elérési út ugyanaz, mint a relatív a cél elérési útja a célként megadott mappába.<br/><br/>**FlattenHierarchy:** minden fájl a forrásmappából az első szintű célmappában jönnek létre. A cél fájlok jönnek létre automatikusan létrehozott névvel.<br/><br/>**Mergefiles típusú:** egyesíti a forrásmappából egy fájl összes fájlt. Ha a fájl neve/blob neve meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben egy automatikusan létrehozott nevét. |Nem |
automatikus-

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

További információkért lásd: [fájlrendszer összekötő cikk](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Társított szolgáltatások
Egy FTP meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **FTP-kiszolgáló**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| gazdagép |Az FTP-kiszolgáló neve vagy IP-cím |Igen |&nbsp; |
| authenticationType |A hitelesítés típusának megadása |Igen |Alapszintű, a névtelen |
| felhasználónév |Az FTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Nem |&nbsp; |
| jelszó |A felhasználó (felhasználónév) jelszavát |Nem |&nbsp; |
| encryptedCredential |Az FTP-kiszolgáló eléréséhez titkosított hitelesítő adatokat |Nem |&nbsp; |
| gatewayName |Az adatkezelési átjáró helyszíni FTP-kiszolgálóhoz való kapcsolódáshoz neve |Nem |&nbsp; |
| port |Port, amelyet az FTP-kiszolgáló figyel |Nem |21 |
| enableSsl |Adja meg, hogy a TLS/SSL csatornán keresztül FTP használata |Nem |true |
| enableServerCertificateValidation |Adja meg, hogy engedélyezi az FTP-használ, a TLS/SSL csatornán keresztül kiszolgálói SSL-tanúsítvány hitelesítése |Nem |true |

#### <a name="example-using-anonymous-authentication"></a>Példa: A névtelen hitelesítést használó

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Példa: Használatával felhasználónevet és jelszót egyszerű szövegként az egyszerű hitelesítés

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Példa: Port, enableSsl, enableServerCertificateValidation használatával

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Példa: EncryptedCredential használata a hitelesítéshez és az átjáró

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

További információkért lásd: [FTP-összekötő](data-factory-ftp-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Adja meg az FTP-adatkészlet, állítsa be a **típus** a DataSet **fájlmegosztási**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Sub mappa elérési útját. Használja az escape-karakter "\" a speciális karakterek a karakterláncban. Lásd: [minta kapcsolódó szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat.<br/><br/>Ez a tulajdonság a kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen 
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg ehhez a tulajdonsághoz értéket, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet, a létrehozott fájl nevét a következő lenne ebben a formátumban: <br/><br/>Adatok. <Guid>.txt (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| fileFilter |Adjon meg egy szűrőt, amely minden fájl helyett a fájlok Tárolónév részhalmazának kiválasztására szolgál.<br/><br/>Két érték engedélyezett: `*` (több karaktert) és `?` (egyetlen karakter).<br/><br/>1. példa: `"fileFilter": "*.log"`<br/>2. példa: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter is alkalmazható egy bemeneti fájlmegosztási az adatkészlethez. Ez a tulajdonság a HDFS nem támogatott. |Nem |
| partitionedBy |Adjon meg egy dinamikus folderPath idő adatsor fájlnevét partitionedBy használható. Például folderPath adatok óránkénti paraméteres. |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**; és a támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli mód használata. A bináris mód és a hamis értéket ASCII igaz. Alapértelmezett érték: igaz. A tulajdonság csak akkor használható, típusú társított kapcsolódószolgáltatás-típus esetén: FTP-kiszolgáló. |Nem |

> [!NOTE]
> fájlnév és fileFilter nem használható egyszerre.

#### <a name="example"></a>Példa

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

További információkért lásd: [FTP-összekötő](data-factory-ftp-connector.md#dataset-properties) cikk.

### <a name="file-system-source-in-copy-activity"></a>A másolási tevékenység rendszer forrás
Adatok másolása az FTP-kiszolgálóról, állítsa be a **adatforrástípust** a másolási tevékenység **FileSystemSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát. |IGAZ, hamis (alapértelmezés) |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

További információkért lásd: [FTP-összekötő](data-factory-ftp-connector.md#copy-activity-properties) cikk.


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Társított szolgáltatások
A HDFS meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **Hdfs**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **Hdfs** |Igen |
| URL-cím |A HDFS URL-címe |Igen |
| authenticationType |Névtelen, vagy a Windows. <br><br> Használandó **Kerberos-hitelesítés** HDFS-összekötőhöz, tekintse meg [ebben a szakaszban](#use-kerberos-authentication-for-hdfs-connector) ennek megfelelően a helyszíni környezet beállítása. |Igen |
| Felhasználónév |Felhasználónév a Windows-hitelesítést. |Igen (a Windows-hitelesítés) |
| jelszó |A Windows-hitelesítés jelszót. |Igen (a Windows-hitelesítés) |
| gatewayName |Az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia a HDFS a neve. |Igen |
| encryptedCredential |[Új AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) kimenetét a hozzáférési hitelesítő adatok. |Nem |

#### <a name="example-using-anonymous-authentication"></a>Példa: A névtelen hitelesítést használó

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Példa: A Windows-hitelesítés használatával

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

További információkért lásd: [HDFS összekötő](#data-factory-hdfs-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg a HDFS adatkészletet, állítsa be a **típus** a DataSet **fájlmegosztási**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |A mappa elérési útját. Példa: `myfolder`<br/><br/>Használja az escape-karakter "\" a speciális karakterek a karakterláncban. Például: folder\subfolder, adja meg a mappa\\\\almappa és d:\samplefolder, adja meg a d:\\\\mappába.<br/><br/>Ez a tulajdonság a kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg ehhez a tulajdonsághoz értéket, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet, a létrehozott fájl nevét a következő lenne ebben a formátumban: <br/><br/>Adatok. <Guid>.txt (például:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| partitionedBy |Adjon meg egy dinamikus folderPath idő adatsor fájlnevét partitionedBy használható. Példa: folderPath adatok óránkénti paraméteres. |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

> [!NOTE]
> fájlnév és fileFilter nem használható egyszerre.

#### <a name="example"></a>Példa

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

További információkért lásd: [HDFS összekötő](#data-factory-hdfs-connector.md#dataset-properties) cikk. 

### <a name="file-system-source-in-copy-activity"></a>A másolási tevékenység rendszer forrás
Adatok másolása a HDFS, állítsa be a **adatforrástípust** a másolási tevékenység **FileSystemSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:

**FileSystemSource** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát. |IGAZ, hamis (alapértelmezés) |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

További információkért lásd: [HDFS összekötő](#data-factory-hdfs-connector.md#copy-activity-properties) cikk.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Társított szolgáltatások
Az SFTP meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **Sftp**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- | --- |
| gazdagép | Az SFTP-kiszolgáló neve vagy IP-címét. |Igen |
| port |Port, amelyen az SFTP kiszolgáló figyel. Az alapértelmezett érték: 21. |Nem |
| authenticationType |Adja meg a hitelesítés típusát. Megengedett értékek: **alapvető**, **SshPublicKey**. <br><br> Tekintse meg [használja az egyszerű hitelesítés](#using-basic-authentication) és [használatával SSH nyilvános kulcsos hitelesítés](#using-ssh-public-key-authentication) további tulajdonságokat és JSON-minták szakasz. |Igen |
| skipHostKeyValidation | Adja meg, hogy a gazdagép kulcs ellenőrzésének kihagyására. | Nem. Az alapértelmezett érték: hamis |
| hostKeyFingerprint | Adja meg a gazdagép kulcs az ujjlenyomat. | Igen, ha a `skipHostKeyValidation` hamis értékre van állítva.  |
| gatewayName |Az adatkezelési átjáró egy helyszíni SFTP-kiszolgálóhoz való csatlakozáshoz neve. | Igen, ha az adatok másolása egy helyszíni SFTP-kiszolgálón. |
| encryptedCredential | Titkosított hitelesítő adatokat a SFTP-kiszolgálóhoz való hozzáféréshez. Automatikusan létrehozott Ha megadja az egyszerű hitelesítés (felhasználónév + jelszó) vagy az SshPublicKey hitelesítési (felhasználónév + titkos kulcs elérési útja vagy tartalom) másolása varázsló vagy a ClickOnce előugró párbeszédpanelen. | Nem. Csak akkor, ha az adatok másolása egy helyszíni SFTP kiszolgáló alkalmazni. |

#### <a name="example-using-basic-authentication"></a>Például: Alapszintű hitelesítést használ

Egyszerű hitelesítést használ, állítsa be `authenticationType` , `Basic`, és adja meg az SFTP összekötő általános néhányat a meglévők közül az utolsó szakaszban bemutatott mellett az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- | --- |
| felhasználónév | Felhasználó, aki hozzáfér az SFTP-kiszolgálóhoz. |Igen |
| jelszó | A felhasználó (felhasználónév) jelszavát. | Igen |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Példa: **titkosított hitelesítő adatokat az egyszerű hitelesítés**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**SSH nyilvános kulcsos hitelesítés használatával:**

Egyszerű hitelesítést használ, állítsa be `authenticationType` , `SshPublicKey`, és adja meg az SFTP összekötő általános néhányat a meglévők közül az utolsó szakaszban bemutatott mellett az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- | --- |
| felhasználónév |SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Igen |
| privateKeyPath | Adjon meg abszolút elérési útját a titkos kulcs fájlját, hogy az átjáró férhetnek hozzá. | Adja meg a `privateKeyPath` vagy `privateKeyContent`. <br><br> Csak akkor, ha az adatok másolása egy helyszíni SFTP kiszolgáló alkalmazni. |
| privateKeyContent | A titkos kulcs tartalmát, mivel a szerializált karakterlánc. A varázsló a titkos kulcsfájl olvashatja, és automatikusan bontsa ki a titkos kulcs tartalmát. Ha minden egyéb eszköz/SDK használja, használja a privateKeyPath tulajdonságot. | Adja meg a `privateKeyPath` vagy `privateKeyContent`. |
| hozzáférési kód | Adja meg a pass kifejezést/jelszót a titkos kulcs visszafejtésére, ha a kulcs fájlját egy hozzáférési kódot védi. | Igen, ha a titkos kulcsfájl védik a hozzáférési kód. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Példa: **SshPublicKey hitelesítés használata a titkos kulcs tartalmát**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

További információkért lásd: [SFTP összekötő](data-factory-sftp-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg az SFTP adatkészlethez, állítsa be a **típus** a DataSet **fájlmegosztási**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Sub mappa elérési útját. Használja az escape-karakter "\" a speciális karakterek a karakterláncban. Lásd: [minta kapcsolódó szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat.<br/><br/>Ez a tulajdonság a kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg ehhez a tulajdonsághoz értéket, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet, a létrehozott fájl nevét a következő lenne ebben a formátumban: <br/><br/>Adatok. <Guid>.txt (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| fileFilter |Adjon meg egy szűrőt, amely minden fájl helyett a fájlok Tárolónév részhalmazának kiválasztására szolgál.<br/><br/>Két érték engedélyezett: `*` (több karaktert) és `?` (egyetlen karakter).<br/><br/>1. példa: `"fileFilter": "*.log"`<br/>2. példa: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter is alkalmazható egy bemeneti fájlmegosztási az adatkészlethez. Ez a tulajdonság a HDFS nem támogatott. |Nem |
| partitionedBy |Adjon meg egy dinamikus folderPath idő adatsor fájlnevét partitionedBy használható. Például folderPath adatok óránkénti paraméteres. |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli mód használata. A bináris mód és a hamis értéket ASCII igaz. Alapértelmezett érték: igaz. A tulajdonság csak akkor használható, típusú társított kapcsolódószolgáltatás-típus esetén: FTP-kiszolgáló. |Nem |

> [!NOTE]
> fájlnév és fileFilter nem használható egyszerre.

#### <a name="example"></a>Példa

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

További információkért lásd: [SFTP összekötő](data-factory-sftp-connector.md#dataset-properties) cikk. 

### <a name="file-system-source-in-copy-activity"></a>A másolási tevékenység rendszer forrás
SFTP forrásból származó adatok másolása, állítsa be a **adatforrástípust** a másolási tevékenység **FileSystemSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát. |IGAZ, hamis (alapértelmezés) |Nem |



#### <a name="example"></a>Példa

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

További információkért lásd: [SFTP összekötő](data-factory-sftp-connector.md#copy-activity-properties) cikk.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Társított szolgáltatások
Egy HTTP meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **Http**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| url | A webkiszolgáló alap URL-címe | Igen |
| authenticationType | Megadja a hitelesítési típus. Két érték engedélyezett: **névtelen**, **alapvető**, **kivonatoló**, **Windows**, **ClientCertificate**. <br><br> Tekintse meg a további tulajdonságok és adott hitelesítési típusok JSON-példák a táblázat alatti részek kulcsattribútumokkal. | Igen |
| enableServerCertificateValidation | Adja meg, hogy a kiszolgálói SSL-tanúsítvány hitelesítése engedélyezése, ha a forrás HTTPS webkiszolgáló | Nem, alapértelmezett érték true |
| gatewayName | Neve az adatkezelési átjáró HTTP a helyszíni adatforráshoz kapcsolódni. | Igen, ha a helyszíni HTTP forrásból származó adatok másolása. |
| encryptedCredential | Titkosított hitelesítő adatokat a HTTP-végpont elérésére. Automatikusan létrehozott másolása varázsló vagy a ClickOnce felugró párbeszédpanel a hitelesítő adatok konfigurálásakor. | Nem. Csak akkor, ha az adatok másolása helyi HTTP-kiszolgáló alkalmazni. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Példa: Basic, a kivonatoló vagy a Windows-hitelesítés használatával
Állítsa be `authenticationType` , `Basic`, `Digest`, vagy `Windows`, és adja meg a HTTP-összekötő fent bevezetett általános ők mellett az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| felhasználónév | Felhasználónév a HTTP-végpont elérésére. | Igen |
| jelszó | A felhasználó (felhasználónév) jelszavát. | Igen |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Példa: ClientCertificate hitelesítés használatával

Egyszerű hitelesítést használ, állítsa be `authenticationType` , `ClientCertificate`, és adja meg a HTTP-összekötő fent bevezetett általános ők mellett az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| embeddedCertData | A személyes információcseréhez kapcsolódó (PFX) fájl bináris adatok Base64-kódolású tartalmát. | Adja meg a `embeddedCertData` vagy `certThumbprint`. |
| certThumbprint | A tanúsítványtároló átjáró számítógépre telepített tanúsítvány ujjlenyomatát. Csak akkor, ha a helyszíni HTTP forrásból származó adat másolása alkalmazni. | Adja meg a `embeddedCertData` vagy `certThumbprint`. |
| jelszó | A tanúsítványhoz tartozó jelszót. | Nem |

Ha `certThumbprint` hitelesítés és a tanúsítvány telepítése a helyi számítógép személyes tanúsítványokat tartalmazó tárolójában kell az olvasási engedélyt az átjárószolgáltatás:

1. Indítsa el a Microsoft Management Console (MMC). Adja hozzá a **tanúsítványok** beépülő modul, amelynek célpontja a **helyi számítógép**.
2. Bontsa ki a **tanúsítványok**, **személyes**, és kattintson a **tanúsítványok**.
3. Kattintson a jobb gombbal a tanúsítványt a személyes tárolóba, és válassza ki **feladataival**->**titkos kulcsok kezelése...**
3. Az a **biztonsági** lapon maradva adja hozzá a felhasználói fiók, amely alatt az adatkezelési átjáró gazdaszolgáltatása fut az olvasási joggal rendelkező tanúsítvány.  

**Példa: ügyfél-tanúsítványt használ:** ez kapcsolódó szolgáltatás hivatkozások a data factory egy helyszíni HTTP-webkiszolgálón. Az adatkezelési átjáró telepítve a számítógépen telepített ügyféltanúsítványt használ.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Példa: ügyfél-tanúsítványt használ egy fájlban
A kapcsolódó szolgáltatás hivatkozások a data factory egy helyszíni HTTP-webkiszolgálón. Az adatkezelési átjáró telepítése egy ügyfél tanúsítványfájlt, a gép használ.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

További információkért lásd: [HTTP összekötő](data-factory-http-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Adja meg a HTTP-adatkészletet, állítsa be a **típus** a DataSet **Http**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| relativeUrl | Az erőforrás adatokat tartalmazó relatív URL-CÍMÉT. Ha nincs megadva, csak a megadott URL-cím a társított szolgáltatás definíciójának használja. <br><br> Dinamikus URL-cím létrehozásához használható [adat-előállító funkciók és rendszerváltozók](data-factory-functions-variables.md), például: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Nem |
| requestMethod | HTTP-metódus. Két érték engedélyezett **beolvasása** vagy **POST**. | Nem. Az alapértelmezett szint a `GET`. |
| additionalHeaders | További HTTP-kérelemfejlécekben. | Nem |
| requestBody | A HTTP-kérelmek törzsében. | Nem |
| Formátumban | Ha azt szeretné, hogy egyszerűen **lekérik az adatokat, HTTP-végpont-van** nélkül elemzés azt, hagyja ki a formátumot beállítások. <br><br> Ha azt szeretné, a HTTP-válasz tartalom elemzése során másolása, a következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. |Nem |
| tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

#### <a name="example-using-the-get-default-method"></a>Példa: a GET (alapértelmezett) metódussal

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Példa: a POST metódussal

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
További információkért lásd: [HTTP összekötő](data-factory-http-connector.md#dataset-properties) cikk.

### <a name="http-source-in-copy-activity"></a>A másolási tevékenység HTTP-forrás
Adatok másolása egy HTTP-bejegyzéseket, állítsa be a **adatforrástípust** a másolási tevékenység **HttpSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| httpRequestTimeout | Időtúllépés (időtartam) válaszol a HTTP-kérelem. Az időtúllépés is válaszol, nem lehet olvasni a válasz adatokat időtúllépés. | Nem. Alapértelmezett érték: 00:01:40 |


#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
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
        }]
    }
}
```

További információkért lásd: [HTTP összekötő](data-factory-http-connector.md#copy-activity-properties) cikk.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Társított szolgáltatások
Egy OData meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **OData**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| url |Az OData szolgáltatási URL-címét. |Igen |
| authenticationType |Az OData-forrásra való kapcsolódáshoz használt hitelesítés típusa. <br/><br/> A felhőbeli OData a lehetséges értékek: névtelen, alapszintű és OAuth (Megjegyzés: jelenleg csak Azure Data Factory támogatási Azure Active Directory-alapú OAuth). <br/><br/> A helyszíni OData lehetséges értékei a névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Ha egyszerű hitelesítést használ, adja meg a felhasználónevet. |Igen (Ha csak az egyszerű hitelesítés használata esetén) |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Igen (Ha csak az egyszerű hitelesítés használata esetén) |
| authorizedCredential |Ha OAuth használ, kattintson a **engedélyezés** gombra a Data Factory másolása varázsló vagy a szerkesztőben, majd adja meg a hitelesítő adatok, akkor ez a tulajdonság értékének lesz automatikusan generált. |Igen (csak ha OAuth-hitelesítés használata esetén) |
| gatewayName |Az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia a helyszíni OData-szolgáltatás neve. Csak adja meg, ha a másolt adatokat a helyszíni OData-forrásra. |Nem |

#### <a name="example---using-basic-authentication"></a>Példa – egyszerű hitelesítés használata
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Példa - névtelen hitelesítés használatával

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Példa - használatával Windows hitelesítés használata a helyszíni OData-forrásra

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Példa - felhő OData-forrásra elérése OAuth-hitelesítés használatával
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

További információkért lásd: [OData összekötő](data-factory-odata-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Adja meg egy OData-adatkészlet, állítsa be a **típus** a DataSet **ODataResource**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| elérési út |Az OData-erőforrás elérési útja |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

További információkért lásd: [OData összekötő](data-factory-odata-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs adatforrás
OData forrásból származó adatok másolása, állítsa be a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Példa | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |"? $select neve, leírása és $top = = 5" |Nem |

#### <a name="example"></a>Példa

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

További információkért lásd: [OData összekötő](data-factory-odata-connector.md#copy-activity-properties) cikk.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Társított szolgáltatások
Az ODBC meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **OnPremisesOdbc**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| connectionString |A kapcsolati karakterlánc és egy opcionális titkosított hitelesítő adat nem hozzáférési hitelesítő adatok része. Példák az alábbi szakaszokban található. |Igen |
| hitelesítő adat |A hozzáférési hitelesítő adatok része illesztőprogram-specifikus tulajdonság-érték formátumban megadott kapcsolódási karakterlánc. Példa: "Uid =<user ID>; Pwd =<password>; RefreshToken =<secret refresh token>; ". |Nem |
| authenticationType |Az ODBC-adattár eléréséhez használt hitelesítés típusa. Lehetséges értékek a következők: névtelen és alapvető. |Igen |
| felhasználónév |Ha egyszerű hitelesítést használ, adja meg a felhasználónevet. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| gatewayName |Az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia az ODBC-adattárolóhoz neve. |Igen |

#### <a name="example---using-basic-authentication"></a>Példa – egyszerű hitelesítés használata

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Példa – egyszerű hitelesítés használata a titkosított hitelesítő adatokat
A hitelesítő adatokat titkosíthatja a [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) (Azure PowerShell 1.0-ás verziója) parancsmag vagy [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9 vagy korábbi verzióját az Azure PowerShell).  

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Példa: A névtelen hitelesítést használó

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

További információkért lásd: [ODBC összekötő](data-factory-odbc-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg egy ODBC-adatkészlet, állítsa be a **típus** a DataSet **RelationalTable**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |Az ODBC-tárolóban a tábla neve. |Igen |


#### <a name="example"></a>Példa

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
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

További információkért lásd: [ODBC összekötő](data-factory-odbc-connector.md#dataset-properties) cikk. 

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs adatforrás
Adatok másolása egy ODBC adattárból, állítsa be a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz :

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: `select * from MyTable`. |Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
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
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
``` 

További információkért lásd: [ODBC összekötő](data-factory-odbc-connector.md#copy-activity-properties) cikk.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Társított szolgáltatások
A Salesforce meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **Salesforce**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| environmentUrl | Adja meg az URL-címet a Salesforce-példány. <br><br> -Alapértelmezett érték "https://login.salesforce.com". <br> -Adatok másolása az védőfal, adja meg a "https://test.salesforce.com". <br> -Adatok másolása az egyéni tartományt, adja meg, például "https://[domain].my.salesforce.com". |Nem |
| felhasználónév |Adja meg a felhasználói fiók felhasználói nevét. |Igen |
| jelszó |Adja meg a felhasználói fiókhoz tartozó jelszót. |Igen |
| securityToken |Adja meg a felhasználói fiók biztonsági jogkivonatot. Lásd: [biztonsági jogkivonatának beszerzéséhez](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) útmutatást, ha alaphelyzetbe állítása/get egy biztonsági jogkivonatot. Általános biztonsági jogkivonatokat kapcsolatos további tudnivalókért lásd: [biztonsági és az API-t](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

További információkért lásd: [Salesforce összekötő](data-factory-salesforce-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg a Salesforce-adatkészlet, állítsa be a **típus** a DataSet **RelationalTable**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A Salesforce-ban a tábla neve. |Nem (Ha egy **lekérdezés** a **RelationalSource** van megadva) |

#### <a name="example"></a>Példa

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
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

További információkért lásd: [Salesforce összekötő](data-factory-salesforce-connector.md#dataset-properties) cikk. 

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs adatforrás
Adatok másolása a Salesforce, állítsa be a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg a következő tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |Egy SQL-92 lekérdezés vagy [Salesforce objektum Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezés. Például: `select * from MyTable__c`. |Nem (Ha a **tableName** , a **dataset** van megadva) |

#### <a name="example"></a>Példa  



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
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
        }]
    }
}
```

> [!IMPORTANT]
> Az API neve "__c" részét bármilyen egyéni objektum szükséges.

További információkért lásd: [Salesforce összekötő](data-factory-salesforce-connector.md#copy-activity-properties) cikk. 

## <a name="web-data"></a>Webes adatok 

### <a name="linked-service"></a>Társított szolgáltatások
Egy webes meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **webes**, és adja meg a következő tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| URL-cím |A webes forrás URL-címe |Igen |
| authenticationType |Névtelen. |Igen |
 

#### <a name="example"></a>Példa


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

További információkért lásd: [webes tábla összekötő](data-factory-web-table-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Adja meg a webes adatkészletet, állítsa be a **típus** a DataSet **Webtábla**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A dataset típusa. meg kell **Webtábla** |Igen |
| elérési út |Az erőforrás, amely tartalmazza a tábla relatív URL-CÍMÉT. |Nem. Ha nincs megadva, csak a megadott URL-cím a társított szolgáltatás definíciójának használja. |
| index |Annak az erőforrás a táblának az indexe. Lásd: [Get index egy tábla egy HTML-lapon](#get-index-of-a-table-in-an-html-page) szakasz lépéseit egy tábla indexének első HTML-lapon. |Igen |

#### <a name="example"></a>Példa

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

További információkért lásd: [webes tábla összekötő](data-factory-web-table-connector.md#dataset-properties) cikk. 

### <a name="web-source-in-copy-activity"></a>A másolási tevékenység webes forrás
Adatok másolása egy webes táblából, állítsa be a **adatforrástípust** a másolási tevékenység **WebSource**. Ha a forrás, a másolási tevékenység jelenleg típusú **WebSource**, további tulajdonságok nem támogatottak.

#### <a name="example"></a>Példa

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
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
        }]
    }
}
```

További információkért lásd: [webes tábla összekötő](data-factory-web-table-connector.md#copy-activity-properties) cikk. 

## <a name="compute-environments"></a>SZÁMÍTÁSI KÖRNYEZETEK
Az alábbi táblázat a Data Factory és az átalakítási tevékenységek ezeken futó által támogatott számítási környezeteket. A számítási érdekli a JSON-sémák csatolni egy adat-előállító kapcsolódó szolgáltatás megjelenítéséhez a hivatkozásra kattintva. 

| Számítási környezet | Tevékenységek |
| --- | --- |
| [Igény szerinti HDInsight-fürt](#on-demand-azure-hdinsight-cluster) vagy [saját HDInsight-fürt](#existing-azure-hdinsight-cluster) |[.NET egyéni tevékenység](#net-custom-activity), [tevékenység Hive](#hdinsight-hive-activity), [tevékenység sertésfelmérés] (hdinsight-pig-tevékenység # [MapReduce művelethez](#hdinsight-mapreduce-activity), [Hadoop-Stream tevékenység](#hdinsight-streaming-activityd), [ A Spark-tevékenység](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET egyéni tevékenység](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Számítógép-kötegelt végrehajtási tevékenység tanulási](#machine-learning-batch-execution-activity), [gép tanulási Update-Erőforrástevékenység](#machine-learning-update-resource-activity) |
| [Az Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Az Azure SQL adatbázis](#azure-sql-database-1), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-1), [SQL Server](#sql-server-1) |[Tárolt eljárás](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Igény szerinti Azure HDInsight-fürt
Az Azure Data Factory szolgáltatásnak automatikusan hozhat létre egy Windows/Linux-alapú igény szerinti HDInsight-fürt adatfeldolgozásra történő. A fürt létrehozása a tárfiók (linkedServiceName tulajdonságot a JSON-ban) a fürthöz tartozó ugyanabban a régióban. Futtathatja a következő átalakító tevékenységek szolgáltatásnak: [.NET egyéni tevékenység](#net-custom-activity), [tevékenység Hive](#hdinsight-hive-activity), [tevékenység sertésfelmérés] (hdinsight-pig-tevékenység # [MapReduce művelethez ](#hdinsight-mapreduce-activity), [Hadoop-Stream tevékenység](#hdinsight-streaming-activityd), [tevékenység Spark](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Társított szolgáltatások 
A következő táblázat ismerteti az igény szerinti HDInsight társított szolgáltatásnak Azure JSON-definícióból használt tulajdonságok.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell megadni **HDInsightOnDemand**. |Igen |
| clusterSize |A fürt munkavégző/adatok csomópontok száma. A HDInsight-fürt együtt ez a tulajdonság a megadott munkavégző csomópontok száma 2 átjárócsomópontokkal hozza létre. A csomópontok egy 4 munkavégző csomópontot tartalmazó fürtben veszi 24 mag, 4 mag, rendelkező standard, D3 méretű vannak (4\*a munkavégző csomópontokról, valamint 2 processzormag, 4 = 16\*az átjárócsomópontokkal processzormag, 4 = 8). Lásd: [hdinsight létrehozása Linux-alapú Hadoop-fürtök](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) a standard, D3 réteg vonatkozó további információért. |Igen |
| a TimeToLive tulajdonság |A megengedett üresjárati idő az igény szerinti HDInsight-fürthöz. Meghatározza, mennyi ideig az igény szerinti HDInsight-fürt aktív marad egy tevékenység fut, ha nincsenek a fürt más aktív feladatok befejezése után.<br/><br/>Például ha egy tevékenység futott 6 percig tart, és az élettartam értéke 5 perc, a fürt marad, a figyelő életben 5 perc, a 6 percnél feldolgozásának a tevékenység futtatása után. Ha egy másik tevékenységfuttatási 6 percnél időkeretet, dolgoz fel ugyanabban a fürtben.<br/><br/>Igény szerinti HDInsight fürtök létrehozásával egy (igénybe vehet) drága művelet, ezt a beállítást, mint egy adat-előállító teljesítményének javításával újból felhasználja az igény szerinti HDInsight-fürtök által szükséges Igen használja.<br/><br/>A TimeToLive tulajdonság értékét 0-ra állítja be, ha törölni a fürtöt, amint a tevékenység futtatása feldolgozott. Másrészről Ha a magas érték, a fürt felfüggesztheti üresjárati feleslegesen magas költségeket eredményez. Ezért fontos, hogy beállította-e a megfelelő értéket a igényei szerint.<br/><br/>Több folyamatok is használ az igény szerinti HDInsight-fürt ugyanazt a példányát, ha a timetolive tulajdonság értékének megfelelően van beállítva. |Igen |
| verzió: |A HDInsight-fürt verziószáma. További információkért lásd: [HDInsight-verziókról támogatott az Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Nem |
| linkedServiceName |Az Azure tárolás társított szolgáltatásának történő tárolására és feldolgozására adatok az igény szerinti fürt által használható. <p>Jelenleg nem hozható létre, amely egy Azure Data Lake Store használ a tárolási igény szerinti HDInsight-fürtöt. Ha szeretné tárolni az eredményadatok a HDInsight-feldolgozás alatt álló egy Azure Data Lake Store-ból, a másolási tevékenység segítségével az adatok másolása az Azure Blob Storage-ból az Azure Data Lake Store.</p>  | Igen |
| additionalLinkedServiceNames |Adja meg a további tárfiókok a HDInsight a társított szolgáltatás, hogy a Data Factory szolgáltatásnak is regisztrálja őket az Ön nevében. |Nem |
| osType |Az operációs rendszer típusát. Két érték engedélyezett: (alapértelmezett) Windows és Linux |Nem |
| hcatalogLinkedServiceName |A név az Azure SQL társított szolgáltatásnak, mutasson az HCatalog-adatbázisra. Az igény szerinti HDInsight-fürt létrehozása az Azure SQL database segítségével a metaadattárhoz. |Nem |

### <a name="json-example"></a>JSON-példa
A következő JSON igény kapcsolódó HDInsight Linux-alapú szolgáltatás határozza meg. A Data Factory szolgáltatásnak automatikusan létrehoz egy **Linux-alapú** HDInsight-fürt adatok szelet feldolgozása közben. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

További információkért lásd: [összekapcsolt szolgáltatások számítási](data-factory-compute-linked-services.md) cikk. 

## <a name="existing-azure-hdinsight-cluster"></a>Meglévő Azure HDInsight-fürt
Létrehozhat saját HDInsight-fürt regisztrálni a Data Factory kapcsolt Azure HDInsight szolgáltatásnak. Futtathatja a következő adatok átalakítása tevékenységek szolgáltatásnak: [.NET egyéni tevékenység](#net-custom-activity), [tevékenység Hive](#hdinsight-hive-activity), [tevékenység sertésfelmérés] (hdinsight-pig-tevékenység # [MapReduce tevékenység](#hdinsight-mapreduce-activity), [Hadoop-Stream tevékenység](#hdinsight-streaming-activityd), [tevékenység Spark](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Társított szolgáltatások
A következő táblázat ismerteti az Azure HDInsight társított szolgáltatásnak Azure JSON-definícióból használt tulajdonságok.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell megadni **HDInsight**. |Igen |
| clusterUri |Az URI-je a HDInsight-fürthöz. |Igen |
| felhasználónév |Adja meg a felhasználó egy meglévő HDInsight-fürtre való kapcsolódáshoz használt nevét. |Igen |
| jelszó |Adja meg a felhasználói fiók jelszavát. |Igen |
| linkedServiceName | Az Azure blob storage a HDInsight-fürt által használt az Azure Storage társított szolgáltatás neve. <p>Jelenleg nem adhat meg egy Azure Data Lake Store társított szolgáltatás ehhez a tulajdonsághoz. Ha a HDInsight-fürt hozzáférése van a Data Lake Store adatokat az Azure Data Lake Store előfordulhat, hogy elérhető Hive/Pig-parancsfájlok. </p>  |Igen |

A HDInsight-fürtök támogatott verzióit lásd: [HDInsight-verziókról támogatott](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). 

#### <a name="json-example"></a>JSON-példa

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
A data Factory létrehozhat egy csatolt Azure Batch szolgáltatás regisztrálása virtuális gépek (VM) kötegelt készletét. .NET-egyéni tevékenységek Azure Batch vagy Azure HDInsight segítségével is futtathatja. Futtathatja a [.NET egyéni tevékenység](#net-custom-activity) a társított szolgáltatás. 

### <a name="linked-service"></a>Társított szolgáltatások
A következő táblázat ismerteti az Azure Batch társított szolgáltatásnak Azure JSON definíciójában használt tulajdonságok.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell megadni **AzureBatch**. |Igen |
| Fióknév |Az Azure Batch-fiók neve. |Igen |
| accessKey |Az Azure Batch-fiók elérési kulcsának. |Igen |
| poolName |A virtuálisgép-készlet neve. |Igen |
| linkedServiceName |Neve az Azure Storage társított szolgáltatásnak a kapcsolódó Azure-köteg szolgáltatással kapcsolatos. A társított szolgáltatás átmeneti fájlok kell futnia a tevékenység és a tevékenység végrehajtási naplók tárolására szolgál. |Igen |


#### <a name="json-example"></a>JSON-példa

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure Machine Learning
A Machine Learning kötegelt pontozási végpont a data Factory regisztrálni Azure Machine Learning társított szolgáltatás létrehozása. A társított szolgáltatás futtatható ez két adatok átalakítása tevékenységek: [Machine Learning kötegelt végrehajtási tevékenység](#machine-learning-batch-execution-activity), [Machine Learning Update-Erőforrástevékenység](#machine-learning-update-resource-activity). 

### <a name="linked-service"></a>Társított szolgáltatások
A következő táblázat ismerteti az Azure Machine Learning társított szolgáltatásnak Azure JSON-definícióból használt tulajdonságok.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| Típus |A type tulajdonságot kell megadni: **AzureML**. |Igen |
| mlEndpoint |A kötegelt pontozás URL-CÍMÉT. |Igen |
| apiKey |A közzétett munkaterület-modell API. |Igen |

#### <a name="json-example"></a>JSON-példa

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Létrehozhat egy **Azure Data Lake Analytics** társított szolgáltatás az Azure Data Lake Analytics csatolásához számítási egy az Azure data factory szolgáltatás használata előtt a [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md) egy folyamaton belül.

### <a name="linked-service"></a>Társított szolgáltatások

A következő táblázat ismerteti az Azure Data Lake Analytics társított szolgáltatás JSON-definícióból használt tulajdonságok. 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| Típus |A type tulajdonságot kell megadni: **AzureDataLakeAnalytics**. |Igen |
| Fióknév |Az Azure Data Lake Analytics-fiók neve. |Igen |
| dataLakeAnalyticsUri |Az Azure Data Lake Analytics URI. |Nem |
| Engedélyezési |Engedélyezési kód automatikusan beolvassa kattintás után **engedélyezés** a Data Factory Editor gombra, és az OAuth-bejelentkezés befejezése. |Igen |
| subscriptionId |Az Azure előfizetés-azonosító |Nem (Ha nincs megadva, a data factory-előfizetése szerepel). |
| resourceGroupName |Azure-erőforráscsoport neve |Nem (Ha nincs megadva, az adat-előállító erőforráscsoport szerepel). |
| sessionId |munkamenet-azonosító az OAuth hitelesítési munkamenetből. Minden munkamenet-azonosító egyedi, és előfordulhat, hogy csak egyszer használható. A Data Factory Editor használata esetén ezt az Azonosítót jön létre automatikusan. |Igen |


#### <a name="json-example"></a>JSON-példa
Az alábbi példa biztosít az Azure Data Lake Analytics társított szolgáltatás JSON-definícióból.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="azure-sql-database"></a>Azure SQL Database
Hozzon létre egy Azure SQL társított szolgáltatást, és együtt használja a [tárolt eljárási tevékenység](#stored-procedure-activity) meghívni a Data Factory-folyamat a tárolt eljárást. 

### <a name="linked-service"></a>Társított szolgáltatások
Egy Azure SQL Database meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AzureSqlDatabase**, és adja meg a következő tulajdonságokat a **typeProperties** a szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| connectionString |Adja meg az Azure SQL Database-példányt a connectionString tulajdonság való kapcsolódáshoz szükséges adatokat. |Igen |

#### <a name="json-example"></a>JSON-példa

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Lásd: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#linked-service-properties) szóló cikkben olvashat a szolgáltatásnak.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Hozzon létre egy Azure SQL Data Warehouse társított szolgáltatást, és együtt használja a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) meghívni a Data Factory-folyamat a tárolt eljárást. 

### <a name="linked-service"></a>Társított szolgáltatások
Azure SQL Data Warehouse meghatározásához társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AzureSqlDW**, és adja meg a következő tulajdonságokat a **typeProperties** a szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| connectionString |Adja meg a connectionString tulajdonság az Azure SQL Data Warehouse-példány való kapcsolódáshoz szükséges adatokat. |Igen |

#### <a name="json-example"></a>JSON-példa

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

További információkért lásd: [Azure SQL Data Warehouse-összekötő](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) cikk. 

## <a name="sql-server"></a>SQL Server 
Hozzon létre csatolt SQL Server szolgáltatást, és együtt használja a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) meghívni a Data Factory-folyamat a tárolt eljárást. 

### <a name="linked-service"></a>Társított szolgáltatások
Típusú társított szolgáltatás létrehozása **OnPremisesSqlServer** a helyszíni SQL Server adatbázis összekapcsolása egy adat-előállítóban. A következő táblázat a JSON-elemek szerepelnek a helyszíni SQL Server kapcsolódó szolgáltatásra vonatkozó leírást.

A következő táblázat a JSON-elemek szerepelnek kapcsolódó SQL Server-szolgáltatásra vonatkozó leírást.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell megadni: **OnPremisesSqlServer**. |Igen |
| connectionString |Az SQL-hitelesítéssel vagy a Windows-hitelesítés a helyszíni SQL Server adatbázishoz való kapcsolódáshoz szükséges connectionString információkat adják meg. |Igen |
| gatewayName |Neve az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia kell a helyszíni SQL Server adatbázishoz. |Igen |
| felhasználónév |Adja meg a felhasználónevet, ha a Windows-hitelesítést használ. Példa: **tartománynév\\felhasználónév**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |

Hitelesítő adatok használatával titkosíthatja az **New-AzureRmDataFactoryEncryptValue** parancsmag és a következő példában látható módon használhatja őket a kapcsolódási karakterláncban (**EncryptedCredential** tulajdonság):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Példa: JSON az SQL-hitelesítéssel

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Példa: JSON a Windows-hitelesítés használatával

Ha a felhasználónév és jelszó van adva, átjáró őket a megszemélyesítéshez használ a megadott felhasználói fióknak a helyi SQL Server-adatbázishoz való kapcsolódáshoz. Ellenkező esetben átjáró csatlakozik az SQL Server közvetlenül az átjáró (az indítási fiókjához) biztonsági környezetében.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

További információkért lásd: [SQL Server-összekötő](data-factory-sqlserver-connector.md#linked-service-properties) cikk.

## <a name="data-transformation-activities"></a>ADATOK ÁTALAKÍTÁSA TEVÉKENYSÉGEK

Tevékenység | Leírás
-------- | -----------
[HDInsight Hive tevékenység](#hdinsight-hive-activity) | A HDInsight Hive tevékenység a Data Factory-folyamat saját Hive-lekérdezéseket vagy igény szerinti Windows/Linux-alapú HDInsight-fürt hajt végre. 
[HDInsight Pig tevékenység](#hdinsight-pig-activity) | A HDInsight Pig tevékenység a Data Factory-folyamat saját Pig lekérdezések vagy igény szerinti Windows/Linux-alapú HDInsight-fürt hajt végre.
[HDInsight MapReduce-tevékenység](#hdinsight-mapreduce-activity) | A HDInsight MapReduce művelethez a Data Factory-folyamat saját MapReduce programok vagy igény szerinti Windows/Linux-alapú HDInsight-fürt hajt végre.
[HDInsight Streaming-tevékenység](#hdinsight-streaming-activity) | A HDInsight Streamelési tevékenységben a Data Factory-folyamat saját Hadoop Streamelési programok vagy igény szerinti Windows/Linux-alapú HDInsight-fürt hajt végre.
[HDInsight Spark-tevékenység](#hdinsight-spark-activity) | A HDInsight Spark tevékenység a Data Factory-folyamat saját HDInsight-fürt végrehajtja a Spark programokat. 
[Machine Learning kötegelt végrehajtási tevékenység](#machine-learning-batch-execution-activity) | Az Azure Data Factory lehetővé teszi, hogy könnyen létrehozhat egy közzétett Azure Machine Learning webszolgáltatás prediktív elemzési használó folyamatok. A kötegelt végrehajtási tevékenység használja az Azure Data Factory-folyamathoz, hívhat meg a Machine Learning webszolgáltatás előrejelzéseket készítsen a kötegben lévő adatokat. 
[Machine Learning Update-erőforrástevékenység](#machine-learning-update-resource-activity) | Az idő múlásával a Machine Learning scoring-kísérletek a prediktív modellek kell kell retrained új bemeneti adatkészletek használata. Miután elkészült, az átképezési, retrained gépi tanulási modell a pontozási webszolgáltatás frissíteni kívánt. A frissítés erőforrás tevékenység segítségével a webszolgáltatás frissítenie az újonnan betanított modell.
[Tárolt eljárási tevékenység](#stored-procedure-activity) | Segítségével a tárolt eljárási tevékenység a Data Factory-folyamat a következő adatokat tárolja egyikében tárolt eljárás hívása: Azure SQL Database, Azure SQL Data Warehouse szolgáltatásban a vállalat SQL Server-adatbázis vagy egy Azure virtuális Gépen. 
[Data Lake Analytics U-SQL-tevékenység](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL-tevékenység egy Azure Data Lake Analytics-fürt U-SQL parancsfájlt futtat.  
[.NET egyéni tevékenység](#net-custom-activity) | Ha úgy, hogy nem támogatja a Data Factory adatok átalakítása van szüksége, hozzon létre egy egyéni tevékenység saját adatokat feldolgozó logika, és használja a tevékenységet a feldolgozási. Az egyéni .NET tevékenység segítségével futtatja, az Azure Batch szolgáltatás vagy az Azure HDInsight-fürtöt is konfigurálhat. 

     
## <a name="hdinsight-hive-activity"></a>HDInsight Hive-tevékenység
A következő tulajdonságok is megadhatók a Hive tevékenység JSON-definícióban. A type tulajdonságot a tevékenységnek kell lennie: **HDInsightHive**. Létre kell hoznia egy HDInsight kapcsolódó szolgáltatás először és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** szakasz tevékenység HDInsightHive beállításakor:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| parancsfájl |Adja meg a Hive parancsfájl beágyazott |Nem |
| parancsfájl elérési útja |A Hive-parancsfájl az Azure blob Storage tárolóban tárolja, és adja meg a fájl elérési útját. Használja a "script" vagy "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájlnév pedig kis-és nagybetűket. |Nem |
| határozza meg |Kulcs/érték párok paraméterek meghatározni a Hive-parancsfájl segítségével történő "hiveconf" belül hivatkozik |Nem |

A típus tulajdonságokat. a struktúra tevékenység vonatkoznak. Az összes tevékenység egyéb tulajdonságok (kívül a typeProperties szakaszban) támogatottak.   

### <a name="json-example"></a>JSON-példa
A következő JSON egy HDInsight Hive tevékenységet egy folyamaton belül határozza meg.  

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

További információkért lásd: [Hive tevékenység](data-factory-hive-activity.md) cikk. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-tevékenység
A következő tulajdonságok is megadhatók a Pig tevékenység JSON-definícióban. A type tulajdonságot a tevékenységnek kell lennie: **HDInsightPig**. Létre kell hoznia egy HDInsight kapcsolódó szolgáltatás először és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** szakasz tevékenység HDInsightPig beállításakor: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| parancsfájl |Adja meg a Pig-parancsprogram beágyazott |Nem |
| parancsfájl elérési útja |A Pig-parancsprogram egy Azure blob Storage tárolóban tárolja, és adja meg a fájl elérési útját. Használja a "script" vagy "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájlnév pedig kis-és nagybetűket. |Nem |
| határozza meg |Kulcs/érték párok paraméterek meghatározni a Pig-parancsprogram belül hivatkozik |Nem |

A típus tulajdonságokat. a Pig tevékenység vonatkoznak. Az összes tevékenység egyéb tulajdonságok (kívül a typeProperties szakaszban) támogatottak.   

### <a name="json-example"></a>JSON-példa

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

További információkért lásd: [Pig tevékenység](#data-factory-pig-activity.md) cikk. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-tevékenység
A következő tulajdonságok is megadhatók a MapReduce tevékenység JSON-definícióban. A type tulajdonságot a tevékenységnek kell lennie: **HDInsightMapReduce**. Létre kell hoznia egy HDInsight kapcsolódó szolgáltatás először és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** szakasz tevékenység HDInsightMapReduce beállításakor: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| jarLinkedService | Az Azure Storage a JAR-fájlt tartalmazó a társított szolgáltatás neve. | Igen |
| jarFilePath | Az Azure Storage a JAR-fájl elérési útja. | Igen | 
| Osztálynév | Neve a fő osztály a JAR-fájlra. | Igen | 
| argumentumok | A MapReduce program argumentumai vesszővel tagolt listája. Futásidőben, néhány további argumentumok látja (például: mapreduce.job.tags) a a MapReduce keretrendszer. A MapReduce argumentumok argumentumok megkülönböztetéséhez érdemes beállítás és value elemeit is argumentumként a következő példában látható módon (- s,--bemeneti,--kimeneti stb., amelyet közvetlenül az értékeik lehetőség áll) | Nem | 

### <a name="json-example"></a>JSON-példa

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

További információkért lásd: [MapReduce művelethez](data-factory-map-reduce.md) cikk. 

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming-tevékenység
A következő tulajdonságok is megadhatók a Hadoop Streamelési tevékenység JSON-definícióban. A type tulajdonságot a tevékenységnek kell lennie: **HDInsightStreaming**. Létre kell hoznia egy HDInsight kapcsolódó szolgáltatás először és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** szakasz tevékenység HDInsightStreaming beállításakor: 

| Tulajdonság | Leírás | 
| --- | --- |
| eseményleképező | A végrehajtható hozzárendelő neve. A példában a cat.exe végrehajtható leképező.| 
| Nyomáscsökkentő | A végrehajtható nyomáscsökkentő neve. A példában a wc.exe végrehajtható nyomáscsökkentő. | 
| bemenet | A leképező bemeneti (beleértve a hely) fájl. A példában: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample a blob-tároló, például/data/Gutenberg az a mappa, pedig davinci.txt a blob. |
| output | Kimeneti fájl (beleértve) a nyomáscsökkentő. Az adatfolyam-Hadoop-feladat eredményének írása ehhez a tulajdonsághoz megadott helyre. |
| filePaths | Elérési utak a hozzárendelést és nyomáscsökkentő végrehajtható fájlok számára. A példában: "adfsample/example/apps/wc.exe" adfsample a blob-tároló, például/alkalmazások az a mappa, pedig wc.exe a végrehajtható fájl. | 
| fileLinkedService | Az Azure tárolás társított szolgáltatása, amely az Azure storage filePaths szakaszában megadott fájlt tartalmazó jelöli. | 
| argumentumok | A MapReduce program argumentumai vesszővel tagolt listája. Futásidőben, néhány további argumentumok látja (például: mapreduce.job.tags) a a MapReduce keretrendszer. A MapReduce argumentumok argumentumok megkülönböztetéséhez érdemes beállítás és value elemeit is argumentumként a következő példában látható módon (- s,--bemeneti,--kimeneti stb., amelyet közvetlenül az értékeik lehetőség áll) | 
| getDebugInfo | Választható eleme. Hiba-ra van állítva, a naplók csak az hiba lesznek letöltve. Ha ezt a beállítást az összes, a naplók mindig letöltődnek függetlenül a végrehajtási állapotot. | 

> [!NOTE]
> A Hadoop Streamelési tevékenységben egy kimeneti adatkészletet kell megadni a **kimenete** tulajdonság. Ez az adatkészlet csak egy üres adatkészlet szükséges ahhoz, hogy az adatcsatorna ütemezés (óránként, naponta, stb.) a meghajtó is lehet. A tevékenység bemeneti nem veszi, kihagyhatja, ha egy bemeneti adatkészlet a tevékenység megadása a **bemenetek** tulajdonság.  

## <a name="json-example"></a>JSON-példa

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

További információkért lásd: [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md) cikk. 

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-tevékenység
A következő tulajdonságok is megadhatók a Spark tevékenység JSON-definícióban. A type tulajdonságot a tevékenységnek kell lennie: **HDInsightSpark**. Létre kell hoznia egy HDInsight kapcsolódó szolgáltatás először és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** szakasz tevékenység HDInsightSpark beállításakor: 

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| rootPath | Az Azure Blob-tároló és a Spark-fájlt tartalmazó mappát. A fájlnév pedig kis-és nagybetűket. | Igen |
| entryFilePath | A gyökérmappában található azon a Spark kódcsomag relatív elérési útja. | Igen |
| Osztálynév | Az alkalmazás Java/Spark fő osztály | Nem | 
| argumentumok | A Spark program parancssori argumentumokat listáját. | Nem | 
| proxyUser | A Spark program végrehajtásának megszemélyesíteni a felhasználói fiók | Nem | 
| sparkConfig | Spark konfigurációs tulajdonságaiban. | Nem | 
| getDebugInfo | Itt adhatja meg, amikor a Spark naplófájlok kerülnek a HDInsight-fürt által használt Azure storage (vagy) leírt módon sparkJobLinkedService. Megengedett értékek: None, mindig, vagy sikertelen. Alapértelmezett érték: nincs. | Nem | 
| sparkJobLinkedService | Az Azure Storage társított szolgáltatás, amely tárolja a Spark feladat fájl, a függőségeket és a naplókat.  Ha nem ad meg egy értéket ehhez a tulajdonsághoz, a HDInsight-fürthöz társított tárolót használja a rendszer. | Nem |

### <a name="json-example"></a>JSON-példa

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Vegye figyelembe a következő szempontokat: 

- A **típus** tulajdonsága **HDInsightSpark**.
- A **rootPath** értéke **adfspark\\pyFiles** ahol adfspark az Azure Blob-tárolóba, pyFiles pedig az adott tároló finom mappát. Ebben a példában az Azure Blob Storage lesz a Spark-fürt társított. Feltöltheti a fájlt egy másik Azure-tárhelyre. Ha így tesz, hozzon létre egy Azure tárolás társított szolgáltatása, hogy a storage-fiók összekapcsolása az adat-előállítóban. Ezt követően adja meg a társított szolgáltatás neve értékeként a **sparkJobLinkedService** tulajdonság. Lásd: [Spark-tevékenység tulajdonságai](#spark-activity-properties) Ez a tulajdonság és az egyéb Spark tevékenység által támogatott tulajdonságokról.
- A **entryFilePath** értékre van állítva a **test.py**, vagyis a python-fájl. 
- A **getDebugInfo** tulajdonsága **mindig**, ami azt jelenti, a naplófájlok helyét a rendszer mindig generált (sikeres vagy sikertelen).  

    > [!IMPORTANT]
    > Azt javasoljuk, hogy nincs megadva ehhez a tulajdonsághoz mindig az éles környezetben kivéve, ha a probléma hibaelhárítást. 
- A **kimenete** szakasz tartalmaz egy kimeneti adatkészletet. Meg kell adnia egy kimeneti adatkészletet, még akkor is, ha a spark program nem ad kimenetet. A kimeneti adatkészlet meghajtók az ütemezés a következő feldolgozási sor (óránként, naponta, stb.).

A tevékenység kapcsolatos további információkért lásd: [Spark tevékenység](data-factory-spark.md) cikk.  

## <a name="machine-learning-batch-execution-activity"></a>Machine Learning kötegelt végrehajtási tevékenység
A következő tulajdonságok is megadhatók az Azure ML kötegelt végrehajtási tevékenység JSON-definícióban. A type tulajdonságot a tevékenységnek kell lennie: **AzureMLBatchExecution**. Létre kell hoznia az Azure Machine Learning-szolgáltatásnak először és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** szakasz tevékenység AzureMLBatchExecution beállításakor:

Tulajdonság | Leírás | Szükséges 
-------- | ----------- | --------
webServiceInput | Az adatkészlet átadását az Azure gépi tanulás webszolgáltatás bemeneteként. Ez az adatkészlet a tevékenység bemenetei is kell szerepelnie. |Használja a típus vagy webServiceInputs. | 
webServiceInputs | Adja meg az Azure gépi tanulás webszolgáltatás bemeneteként átadni adatkészletek. A webszolgáltatás több bemeneti adatokat fogad, ha a webServiceInputs tulajdonsággal típus tulajdonság használata helyett. Által hivatkozott adatkészletek a **webServiceInputs** is szerepelnie kell a tevékenység **bemenetek**. | Használja a típus vagy webServiceInputs. | 
webServiceOutputs | Az Azure ML web service kimeneti rendelt adathalmazokat. A webszolgáltatás kimeneti adatait jeleníti meg az ehhez az adatkészlethez. | Igen | 
globalParameters | Ez a szakasz a webszolgáltatás-paramétereket értékeket megadni. | Nem | 

### <a name="json-example"></a>JSON-példa
Ebben a példában a tevékenység rendelkezik az adatkészlet **MLSqlInput** bemenetként és **MLSqlOutput** kimeneteként. A **MLSqlInput** átadása a webszolgáltatás által bemeneteként használja a **típus** JSON tulajdonság. A **MLSqlOutput** objektumnak átadott kimenetként a webszolgáltatás által használ a **webServiceOutputs** JSON tulajdonság. 

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }              
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

A JSON példában a telepített Azure Machine Learning Web service használ egy olvasó és író modul adatokat az vagy egy Azure SQL-adatbázis írható/olvasható. Ez a webszolgáltatás mutatja meg a következő négy paraméterek: adatbázis-kiszolgáló neve, a adatbázis neve, a kiszolgáló felhasználói fiók nevét és a kiszolgáló felhasználói fiók jelszavát.

> [!NOTE]
> Csak be- és kimenetekkel a AzureMLBatchExecution tevékenység argumentumként átadhatók paraméterek a webszolgáltatással. Például a fenti JSON-részlet MLSqlInput a AzureMLBatchExecution tevékenység, amelyet a rendszer továbbad a webszolgáltatás bemeneteként típus paraméteren keresztül bemenete.

## <a name="machine-learning-update-resource-activity"></a>Machine Learning Update-erőforrástevékenység
A következő tulajdonságok is megadhatók az Azure ML Update erőforrás tevékenység JSON-definícióban. A type tulajdonságot a tevékenységnek kell lennie: **AzureMLUpdateResource**. Létre kell hoznia az Azure Machine Learning-szolgáltatásnak először és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** szakasz AzureMLUpdateResource tevékenység beállításakor:

Tulajdonság | Leírás | Szükséges 
-------- | ----------- | --------
trainedModelName | A retrained modell neve. | Igen |  
trainedModelDatasetName | A DataSet adatkészlet mutat a megőrzési művelet által visszaadott iLearner-fájlt. | Igen | 

### <a name="json-example"></a>JSON-példa
A folyamat két tevékenység rendelkezik: **AzureMLBatchExecution** és **AzureMLUpdateResource**. Az Azure ML kötegelt végrehajtási tevékenység lekéri a tanítási adatokat bemeneti adatként, és egy kimenetként iLearner-fájlt hoz létre. A tevékenység hív meg, a képzési webszolgáltatás (a tanítási kísérletet webszolgáltatásként kitett) a bemeneti betanítási adatok, és megkapja a webservice ilearner-fájlt. A placeholderBlob csak az Azure Data Factory szolgáltatásnak a feldolgozási sor futtatásához szükséges üres kimeneti adatkészlet.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
A következő tulajdonságok is megadhatók a U-SQL tevékenység JSON-definícióban. A type tulajdonságot a tevékenységnek kell lennie: **DataLakeAnalyticsU-SQL**. Létre kell hoznia egy Azure Data Lake Analytics kapcsolódó szolgáltatás és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** szakasz tevékenység DataLakeAnalyticsU-SQL beállításakor: 

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| scriptPath |A U-SQL parancsfájlt tartalmazó mappa elérési útja. A fájl neve nem kis-és nagybetűket. |Nem (Ha a parancsfájl használata) |
| scriptLinkedService |Kapcsolódó szolgáltatás, amely a tárolóban, amely tartalmazza az adat-előállító parancsfájl |Nem (Ha a parancsfájl használata) |
| parancsfájl |Adja meg a beágyazott parancsfájlja scriptPath és a scriptLinkedService megadása helyett. Például: "script": "Adatbázis létrehozása test". |Nem (Ha a ScriptPath tulajdonságot is és a scriptLinkedService használ) |
| degreeOfParallelism |A feladat futtatásához egyidejűleg használt csomópontok maximális száma. |Nem |
| prioritás |Azt határozza meg, melyet futtatni kíván szereplő várólistáján szereplő feladatok közül melyeket. Az alacsonyabb a szám, annál magasabb a prioritás. |Nem |
| paraméterek |A U-SQL parancsfájl paraméterek |Nem |

### <a name="json-example"></a>JSON-példa

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

További információkért lásd: [Data Lake Analytics U-SQL tevékenység](data-factory-usql-activity.md). 

## <a name="stored-procedure-activity"></a>Tárolt eljárási tevékenység
A következő tulajdonságok is megadhatók a tárolt eljárás tevékenység JSON-definícióban. A type tulajdonságot a tevékenységnek kell lennie: **SqlServerStoredProcedure**. Létre kell hoznia egy, a következő összekapcsolt szolgáltatások, és adja meg a társított szolgáltatás neve értékének a **linkedServiceName** tulajdonság:

- SQL Server 
- Azure SQL Database
- Azure SQL Data Warehouse

A következő tulajdonságok támogatottak a **typeProperties** szakasz tevékenység SqlServerStoredProcedure beállításakor:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| storedProcedureName |Adja meg a tárolt eljárás neve a Azure SQL database vagy az Azure SQL Data Warehouse, amely a társított szolgáltatás, amely a bemeneti tábla használja. |Igen |
| storedProcedureParameters |Adja meg a tárolt eljárás paraméter értékét. Ha az egyik paraméter null értéket átadni van szüksége, használja a szintaxist: "param1": (összes kisbetű) NULL értékű. Tekintse meg az alábbi minta tájékozódhat az e tulajdonság használatával. |Nem |

Ha megad egy bemeneti adatkészlet, elérhetőnek kell lennie (a "Kész" állapotú) a tárolt eljárás tevékenység futtatásához. A bemeneti adatkészletet a tárolt eljárás nem használható paraméterként. Csak a tárolt eljárási tevékenység megkezdése előtt ellenőrizze, a függőség szolgál. Meg kell adnia egy tárolt eljárás tevékenység egy kimeneti adatkészletet. 

Kimeneti adatkészlet határozza meg a **ütemezés** a tárolt eljárás tevékenység (óránként, heti, havi, stb.). A kimeneti adatkészlet kell használnia egy **társított szolgáltatás** , amely egy Azure SQL Database vagy az Azure SQL Data Warehouse vagy a tárolt eljárás futtatásához használni kívánt SQL Server-adatbázis hivatkozik. A kimeneti adatkészlet felelt meg a tárolt eljárás eredményét a későbbi feldolgozásra, amelyet egy másik tevékenység módon működhetnek ([tevékenységek láncolás](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) a feldolgozási. Azonban adat-előállító nem automatikusan kiírhatja a kimenetet tárolt eljárás ehhez a DataSet adatkészlethez. A tárolt eljárás, amely egy SQL táblázat, amely a kimeneti adatkészlet mutat ír. Bizonyos esetekben a kimeneti adatkészlet lehet egy **dummy dataset**, amellyel csak a tárolt eljárási tevékenység fut ütemezése.  

### <a name="json-example"></a>JSON-példa

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

További információkért lásd: [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) cikk. 

## <a name="net-custom-activity"></a>.NET egyéni tevékenység
A következő tulajdonságok is megadhatók a .NET egyéni tevékenység JSON-definícióból. A type tulajdonságot a tevékenységnek kell lennie: **DotNetActivity**. Létre kell hoznia egy Azure HDInsight kapcsolódó szolgáltatás, vagy egy társított Azure Batch szolgáltatásra, és adja meg a társított szolgáltatás neve értékének a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** szakasz tevékenység DotNetActivity beállításakor:
 
| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| AssemblyName | A szerelvény neve. A példában van: **MyDotnetActivity.dll**. | Igen |
| EntryPoint |Az IDotNetActivity illesztőfelületet megvalósító osztály neve. A példában van: **MyDotNetActivityNS.MyDotNetActivity** ahol MyDotNetActivityNS az a névtér pedig MyDotNetActivity az osztály.  | Igen | 
| PackageLinkedService | Az Azure Storage társított szolgáltatás mutat, a blob-tároló, amely tartalmazza az egyéni tevékenység zip-fájl neve. A példában van: **AzureStorageLinkedService**.| Igen |
| PackageFile | A zip-fájl neve. A példában van: **customactivitycontainer/MyDotNetActivity.zip**. | Igen |
| extendedProperties | A kiterjesztett tulajdonságok, amely meghatározza, és át a .NET-kódot. Ebben a példában a **SliceStart** változó értéke a SliceStart rendszerváltozó alapuló értéket. | Nem | 

### <a name="json-example"></a>JSON-példa

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Részletes információkért lásd: [egyéni tevékenységeket felhasználni a Data Factory](data-factory-use-custom-activities.md) cikk. 

## <a name="next-steps"></a>További lépések
Lásd az alábbi oktatóanyagokat: 

- [Oktatóanyag: hozzon létre egy folyamatot a másolási tevékenység](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Oktatóanyag: hozzon létre egy folyamatot egy hive-tevékenység](data-factory-build-your-first-pipeline-using-editor.md)

---
title: Az Azure Data Factory - JSON-Parancsprogramokról |} A Microsoft Docs
description: Data Factory-entitások JSON-sémákat biztosít.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: c61612bad181eb600f449fea7eb22ca2abc17a12
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020455"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Az Azure Data Factory - JSON-Parancsprogramokról
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik.


Ez a cikk JSON-sémáinak és példák az Azure Data Factory-entitások (pipeline, tevékenység, adatkészlet és társított szolgáltatás) meghatározása.  

## <a name="pipeline"></a>Folyamat 
A folyamathoz tartozó definíció magas szintű struktúráját a következőképpen történik: 

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

Következő táblázat ismerteti a tulajdonságokat a folyamat JSON-definíciót:

| Tulajdonság | Leírás | Szükséges
-------- | ----------- | --------
| név | A folyamat neve. Adjon meg egy műveletet jelölő nevet, hogy a tevékenység vagy a folyamat erre van konfigurálva<br/><ul><li>Karakterek maximális száma: 260</li><li>Betűvel, számmal vagy aláhúzásjellel (_) kell kezdődnie</li><li>A következő karakterek nem engedélyezettek: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Igen |
| leírás |Mi az a tevékenység vagy a folyamat használatos leíró szöveg | Nem |
| tevékenységek | A tevékenységek listáját tartalmazza. | Igen |
| start |Kezdő dátum-idő a folyamat. Kell [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601). Példa: 2014-10-14T16:32:41. <br/><br/>Adja meg a helyi időt, például egy becsült idő, lehetőség. Íme egy példa: `2016-02-27T06:00:00**-05:00`, azaz 6 Órakor becsült<br/><br/>A kezdő és záró tulajdonságok együtt a a folyamat aktív időszakát határozzák meg. Kimeneti szeleteket csak előállítása az aktív ebben az időszakban. |Nem<br/><br/>Ha megad egy záró tulajdonság értéke, kezdő tulajdonság értékének kell megadnia.<br/><br/>A kezdési és befejezési idejének is lehet üres folyamat létrehozása. A folyamat futtatásához egy aktív időszak beállítása mindkét értéket meg kell adnia. Ha nem adja meg a kezdő és befejező időpontok egy folyamat létrehozásakor beállíthatja azokat később a Set-AzureRmDataFactoryPipelineActivePeriod parancsmaggal. |
| vége |Záró dátum és időpont a folyamat. Ha meg van adva, ISO formátumban kell megadni. Példa: 2014-10-14T17:32:41 <br/><br/>Adja meg a helyi időt, például egy becsült idő, lehetőség. Íme egy példa: `2016-02-27T06:00:00**-05:00`, azaz 6 Órakor becsült<br/><br/>A folyamat határozatlan ideig történő futtatásához, adja meg a 9999-09-09 end tulajdonság értékeként. |Nem <br/><br/>Ha megadja a (Kezdés) tulajdonság értékét, a végfelhasználók tulajdonság értéke kell megadnia.<br/><br/>Tekintse meg a megjegyzéseit a **start** tulajdonság. |
| isPaused |Ha nem fut a folyamatok true értékre kell állítani. Alapértelmezett érték = false. Ez a tulajdonság segítségével engedélyezheti vagy tilthatja le. |Nem |
| pipelineMode |Az ütemezés a folyamat futtatásának módja. Engedélyezett értékek a következők: ütemezett (alapértelmezett), Item parancsot.<br/><br/>"Ütemezett", az azt jelzi, hogy a folyamat megadott időközönként az aktív időtartam (kezdő és záró idő) megfelelően fut-e. "Egyszeri" azt jelzi, hogy a folyamat csak egyszer fut-e. Ezután onetime folyamatok nem lehet módosítani vagy frissíteni jelenleg. Lásd: [Onetime folyamat](data-factory-create-pipelines.md#onetime-pipeline) onetime beállítás részleteit. |Nem |
| expirationTime |Időtartam, amelynek a folyamat érvényes, és továbbra is kiépített létrehozása után. Ha nem rendelkezik minden aktív, sikertelen, vagy függőben lévő futtatásának, a folyamat automatikusan törlődik, miután a lejárati idő elér. |Nem |


## <a name="activity"></a>Tevékenység 
A magas szintű struktúra folyamathoz tartozó definíció (tevékenységek elem) tevékenységet a következőképpen történik:

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

Az alábbi táblázat belül a tevékenység JSON-definíció tulajdonságainak leírása:

| Címke | Leírás | Szükséges |
| --- | --- | --- |
| név |A tevékenység neve. Adjon meg egy műveletet jelölő nevet, amely a tevékenység van konfigurálva<br/><ul><li>Karakterek maximális száma: 260</li><li>Betűvel, számmal vagy aláhúzásjellel (_) kell kezdődnie</li><li>A következő karakterek nem engedélyezettek: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Igen |
| leírás |Mire használható a tevékenységet leíró szöveg. |Nem |
| type |Megadja a tevékenység típusát. Tekintse meg a [ADATTÁRAK](#data-stores) és [adat-ÁTALAKÍTÁSI tevékenységeket](#data-transformation-activities) tevékenységek a különböző tevékenységtípusokkal kapcsolatban. |Igen |
| bemenetek |A tevékenység által használt bemeneti táblák<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |A HDInsightStreaming és SqlServerStoredProcedure tevékenységek nem <br/> <br/> Minden egyéb esetében: Igen |
| kimenetek |A tevékenység által használt kimeneti táblák.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Igen |
| linkedServiceName |A tevékenység által használt társított szolgáltatás neve. <br/><br/>Egy adott tevékenység megkövetelheti annak a társított szolgáltatásnak a megadását, amely a szükséges számítási környezethez kapcsolódik. |Igen, a HDInsight-tevékenységek, Azure Machine Learning-tevékenységek és tárolt eljárási tevékenység. <br/><br/>Minden egyéb esetében: nem |
| typeProperties |A typeProperties szakasz tulajdonságai a tevékenység-típustól függnek. |Nem |
| szabályzat |Olyan szabályzatok, amelyek az adott tevékenység futásidejű viselkedését befolyásolják. Ha nincs megadva, az alapértelmezett házirendek használhatók. |Nem |
| a Scheduler |"a scheduler" tulajdonság a tevékenység kívánt ütemezés meghatározására szolgál. A altulajdonságok ugyanazok, mint az a [rendelkezésre állási tulajdonságot egy adatkészlet](data-factory-create-datasets.md#dataset-availability). |Nem |

### <a name="policies"></a>Házirendek
Házirendek egy tevékenység futásidejű viselkedését befolyásolják, kifejezetten egy tábla a szelet feldolgozása során. Az alábbi táblázatban a részleteket.

| Tulajdonság | Megengedett értékek | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| Egyidejűség |Egész szám <br/><br/>A maximális érték: 10 |1 |A tevékenység párhuzamos végrehajtások száma.<br/><br/>Ez határozza meg, amely akkor fordulhat elő, a másik szeletek párhuzamos tevékenység-végrehajtások száma. Például ha egy tevékenység kell áthaladnia rengeteg rendelkezésre álló adatok, a nagyobb párhuzamosság értéke felgyorsítja az adatfeldolgozás. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Meghatározza, hogy a feldolgozott adatszelet rendezése.<br/><br/>Például ha 2 szeletekre (du. 4: az egyik oka és a egy másik, 17: 00), és mindkettő végrehajtás függőben. A executionPriorityOrder NewestFirst kell állít be, ha a szelet délután 5-kor lesz elsőként feldolgozva. Hasonlóképpen ha beállította a executionPriorityORder OldestFIrst kell, majd du. 4: a szeletet dolgoz fel. |
| retry |Egész szám<br/><br/>A maximális érték 10 lehet. |0 |Mielőtt az adatfeldolgozás a szelet hiba van megjelölve. az újrapróbálkozások száma. Tevékenység-végrehajtási adatszelet rendszer legfeljebb a megadott újrapróbálkozások számát. Az újrapróbálkozás történik, a hiba után minél hamarabb. |
| timeout |Időtartam |00:00:00 |A tevékenység időkorlátja. Példa: 00:10:00 (magában foglalja a időkorlátja 10 perc)<br/><br/>Ha egy érték nincs megadva vagy 0, az időtúllépési érték a végtelen.<br/><br/>Ha a szelet adatok feldolgozási idő meghaladja az időtúllépés értéke, meg lett szakítva, és a rendszer megpróbálja próbálkozzon újra a feldolgozást. Az újrapróbálkozások száma attól függ, hogy az újrapróbálkozási tulajdonság. Időtúllépés történik, ha az állapot értéke időtúllépés miatt megszakadt. |
| késleltetés |Időtartam |00:00:00 |Adja meg a késleltetés, elindul a szelet feldolgozásának előtt.<br/><br/>Adatszelet tevékenység végrehajtása után a késleltetési idő legyen a várt végrehajtási időn túli elindult.<br/><br/>Példa: 00:10:00 (magában foglalja a késés 10 perc) |
| longRetry |Egész szám<br/><br/>A maximális érték: 10 |1 |A szelet végrehajtása előtt hosszú újrapróbálkozási kísérletek száma.<br/><br/>longRetry kísérletek által longRetryInterval elosztásban. Ezért ha egy újrapróbálkozási kísérletek közötti időre van szüksége, a longRetry. Ha az újrapróbálkozás és longRetry is meg van adva, egyes longRetry kísérletek magában foglalja az újrapróbálkozási kísérletek és kísérletek maximális számát. a rendszer újrapróbálkozik * longRetry.<br/><br/>Például ha a tevékenységszabályzat is van a következő beállításokat:<br/>Ismételje meg: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Tegyük fel, hogy csak egyetlen szeletet végrehajtására van (Várakozás állapot) és a tevékenység-végrehajtási minden alkalommal sikertelen lesz. Kezdetben lenne 3 egymást követő végrehajtási kísérlet. Minden kísérlet után a szelet állapota lenne, próbálkozzon újra. Első 3 kísérletek esnek, miután a szelet állapota LongRetry lehet.<br/><br/>Egy óra (azaz longRetryInteval a érték) egy másik hárompéldányos készletet 3 egymást követő végrehajtási kísérlet lenne. Ezt követően a szelet állapota szeretné végrehajtani, és nincs további újrapróbálkozások volna lehetséges. Ezért a teljes 6 kísérlet történt.<br/><br/>Minden olyan végrehajtása sikeres, ha a szelet állapota kész lenne, és nincs további próbálkozások nem megkísérlése.<br/><br/>a függő adatok érkeznek nem determinisztikus időpontokban vagy a teljes környezet nem flaky akkor fordul elő, hogy mely adatok feldolgozása a longRetry használni. Ezekben az esetekben újrapróbálkozások egymás után nem segíthet ezzel és a egy időszak után így időben a kívánt kimenetet eredményez.<br/><br/>Legyen körültekintő, Word: nincs beállítva a longRetry, longRetryInterval vagy nagy értékeket. Általában a magasabb értékek hasonló más rendszerből adódó problémákat. |
| longRetryInterval |Időtartam |00:00:00 |Hosszú újrapróbálkozás kísérletek közötti késleltetés |

### <a name="typeproperties-section"></a>a typeProperties szakasz
A typeProperties szakasz eltér minden egyes tevékenységhez. Adatátalakítási tevékenységekről van csak a tulajdonságait. Lásd: [adat-ÁTALAKÍTÁSI tevékenységeket](#data-transformation-activities) szakasz ebben a cikkben az Adatátalakítási tevékenységeket határozzák meg a folyamat JSON-minták. 

**Másolási tevékenység** két alszakaszok rendelkezik a typeProperties szakasz: **forrás** és **fogadó**. Lásd: [ADATTÁRAK](#data-stores) JSON-minták azt mutatják be, hogyan használható egy tárolót, mint a forrás és fogadó vagy ebben a cikkben szakaszban látható. 

### <a name="sample-copy-pipeline"></a>Minta másolási folyamat
Az alábbi mintafolyamat **tevékenységek** szakaszában egyetlen **Másolás** típusú tevékenység található. Ebben a példában a [másolási tevékenység](data-factory-data-movement-activities.md) adatokat másol egy Azure blobtárolóból egy Azure SQL Database-adatbázishoz. 

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

Lásd: [ADATTÁRAK](#data-stores) JSON-minták azt mutatják be, hogyan használható egy tárolót, mint a forrás és fogadó vagy ebben a cikkben szakaszban látható.    

Ez a folyamat létrehozásának részletes útmutatást lásd: [oktatóanyag: Adatok másolása Blob Storage-ból az SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

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
* A **meghatározása** szakasz segítségével meghatározza a futásidő beállításait, a hive-parancsfájlnak átadott Hive konfigurációs értékekként (például: `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Lásd: [adat-ÁTALAKÍTÁSI tevékenységeket](#data-transformation-activities) szakasz ebben a cikkben az Adatátalakítási tevékenységeket határozzák meg a folyamat JSON-minták.

Ez a folyamat létrehozásának részletes útmutatást lásd: [oktatóanyag: Hadoop-fürt használatával dolgozza fel az adatokat az első folyamat létrehozását](data-factory-build-your-first-pipeline.md). 

## <a name="linked-service"></a>Társított szolgáltatások
A magas szintű struktúra egy társított szolgáltatás definíciójában a következőképpen történik:

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

Az alábbi táblázat belül a tevékenység JSON-definíció tulajdonságainak leírása:

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- | 
| név | A társított szolgáltatás neve. | Igen | 
| Tulajdonságok - típus | A társított szolgáltatás típusa. Példa: Az Azure Storage, az Azure SQL Database. |
| typeProperties | A typeProperties szakasz eltérő az összes adattárat vagy számítási környezet elemek rendelkezik. Lásd: [adattárak](#datastores) szakaszban látható összes adatot adattárhoz társított szolgáltatás és [számítási környezetek](#compute-environments) minden számítási társított szolgáltatás |   

## <a name="dataset"></a>Adathalmaz 
Az Azure Data Factory-adatkészlet a következőképpen van meghatározva:

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

A következő táblázat ismerteti a fenti JSON-tulajdonságok:   

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| név | Az adatkészlet nevét. Lásd: [Azure Data Factory – elnevezési szabályok](data-factory-naming-rules.md) elnevezési szabályait. |Igen |NA |
| type | Az adatkészlet típusa. Adjon meg egy Azure Data Factory által támogatott fájltípusok (például: Azure Blobba, AzureSqlTable). Lásd: [ADATTÁRAK](#data-stores) az adattárak és a Data Factory által támogatott típus a következő szakaszban. | 
| struktúra | Az adatkészlet sémája. Tartalmaz oszlopokat, azok típusok, stb. | Nem |NA |
| typeProperties | A kijelölt típushoz tartozó tulajdonságok. Lásd: [ADATTÁRAK](#data-stores) támogatott típusok és azok tulajdonságait a következő szakaszban. |Igen |NA |
| external | Adja meg, hogy data factory-folyamatok explicit módon előállított adatkészlet vagy nem a logikai jelzőt. |Nem |false |
| rendelkezésre állás | A feldolgozási ablakban vagy a slicing az adatkészlet üzemi modell határoz meg. További információ az adatkészlet felosztási modelljét: [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) cikk. |Igen |NA |
| szabályzat |Határozza meg a feltételeket és a feltétellel, hogy az adatkészlet szeleteit meg kell felelniük. <br/><br/>További információkért lásd: [adatkészlet házirend](#Policy) szakaszban. |Nem |NA |

Minden egyes oszlopának a **struktúra** szakaszban a következő tulajdonságokat tartalmazza:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| név |Az oszlop neve. |Igen |
| type |Az oszlop adattípusát.  |Nem |
| kulturális környezet |.NET-alapú kulturális környezet esetén a típus van megadva, és a .NET-típus használandó `Datetime` vagy `Datetimeoffset`. Az alapértelmezett szint a `en-us`. |Nem |
| Formátum |Formázó karakterlánc típus van megadva, és a .NET-típus esetén használandó `Datetime` vagy `Datetimeoffset`. |Nem |

Az adatkészlet a következő példában három oszlop van `slicetimestamp`, `projectname`, és `pageviews` és típusa van: Karakterlánc, karakterlánc és tizedes tört jelölik.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

A következő táblázat ismerteti a használható tulajdonságok a **rendelkezésre állási** szakaszban:

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| frequency |Megadja az adatkészlet szelet éles üzemi környezetek részei.<br/><br/><b>Támogatott gyakoriság</b>: Perc, óra, nap, hét, hónap |Igen |NA |
| interval |Megadja egy szorzóval gyakoriság<br/><br/>"X időköz" határozza meg, hogy milyen gyakran a szelet előállítása.<br/><br/>Ha az adatkészlet óradíjat kell szeletelt van szüksége, akkor be <b>gyakorisága</b> való <b>óra</b>, és <b>időköz</b> való <b>1</b>.<br/><br/><b>Megjegyzés</b>: Perces gyakoriságot ad meg, ha azt javasoljuk, hogy legalább 15-re állítsa be az időköz |Igen |NA |
| stílus |Itt adhatja meg, hogy a szelet intervallum kezdő/záró lehet termelni.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Ha a gyakoriság értéke hónap és EndOfInterval van beállítva, a szelet előállítása a hónap utolsó napján. Ha a stílus StartOfInterval értékre van állítva, a szelet előállítása a hónap első napján.<br/><br/>Gyakoriságát napi értékre van állítva, és EndOfInterval van beállítva, ha a szelet előállítása a nap az elmúlt órában.<br/><br/>Ha a stílus beállítása EndOfInterval Frequency értéke Hour, a szelet előállítása a óra végén. Például egy szelet du. 1 – 2 PM időszakban, a rendszer óránként létrehoz egy 2-kor. |Nem |EndOfInterval |
| anchorDateTime |Az ütemező által használt adatkészlet szelet határok számítási idő abszolút pozícióját határozza meg. <br/><br/><b>Megjegyzés</b>: Ha a AnchorDateTime dátum részei, amelyek részletesebben, mint a gyakorisága, majd a rendszer figyelmen kívül hagyja a részletesebb részeket. <br/><br/>Például ha a <b>időköz</b> van <b>óránként</b> (frequency: hour és interval: 1.) és a <b>AnchorDateTime</b> tartalmaz <b>perceket és másodperceket</b> , majd a <b>perceket és másodperceket</b> a AnchorDateTime részei a rendszer figyelmen kívül hagyja. |Nem |01/01/0001 |
| offset |Időtartam, amely szerint a kezdő és befejező az összes adatkészlet szeleteit áttért. <br/><br/><b>Megjegyzés</b>: Ha anchorDateTime és az offset is meg van adva, a kombinált shift jön létre. |Nem |NA |

A következő rendelkezésre állási szakasz meghatározza, hogy a kimeneti adatkészlet előállított óránként (vagy) bemeneti adatkészlet óránként áll rendelkezésre:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

A **házirend** az adatkészlet-definícióban szakasz definiálja a feltételeket és a feltétellel, hogy az adatkészlet szeleteit meg kell felelniük.

| Házirend neve | Leírás | A alkalmazni | Szükséges | Alapértelmezett |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Azt ellenőrzi, hogy az adatok egy **az Azure blob** megfelel a minimális méret (megabájtban). |Azure-blob |Nem |NA |
| minimumRows |Azt ellenőrzi, hogy az adatok egy **Azure SQL database** vagy egy **Azure-tábla** sorok legkisebb számát tartalmazza. |<ul><li>Azure SQL Database</li><li>Azure-tábla</li></ul> |Nem |NA |

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

Azure Data Factory által adatkészlet előállítása, hacsak azt kell megjelölni **külső**. Ez a beállítás általában az első tevékenységet a folyamat bemeneti adatai vonatkozik, kivéve, ha a tevékenység vagy csővezeték-láncolás használatban van.

| Name (Név) | Leírás | Szükséges | Alapértelmezett érték |
| --- | --- | --- | --- |
| dataDelay |Az ellenőrzés az adott szeletre vonatkozó a külső adatok rendelkezésre állását a késleltetés ideje. Ha például óránként érhető el az adatokat, ha a ellenőrzi, hogy a külső adatok érhető el, és a megfelelő szelet kész az dataDelay használatával késleltethető.<br/><br/>Csak a jelenlegi idő vonatkozik.  Például ha 1:00 Órakor most, és ez az érték 10 perc, az érvényesítési kezdődik, 1:10 PM.<br/><br/>Ez a beállítás nem befolyásolja a szeletek múltbeli (Adatszelet befejezési időpontja + dataDelay szeletek < most) dolgozzák fel késedelem nélkül.<br/><br/>Idő nagyobb, mint a 23:59 óra kell megadni, használja a `day.hours:minutes:seconds` formátumban. Például adja meg a 24 órát, ne használja 24:00:00. Ehelyett használjon 1.00:00:00. Ha 24:00:00 használ, azt számít 24 nap (24.00:00:00). 1 nap és 4 órán keresztül adja meg a 1:04:00:00. |Nem |0 |
| retryInterval |A várakozási idő közötti hiba, a következő újrapróbálkozás. Ha nem sikerül próbálja ki, a következő kísérlet után retryInterval nincs. <br/><br/>Ha 1:00 Órakor most, hogy első próbálkozáskor kezdődik. Ha az első érvényesítési ellenőrzés idejére 1 perc, és a művelet sikertelen volt, a következő újrapróbálkozás jelenleg 1:00 + 1 perc (időtartam) + 1 perc (újrapróbálkozási időköz) = 13:02-kor. <br/><br/>A múltban szeletek nem lesz késleltetés. Az újrapróbálkozás akkor történik meg azonnal. |Nem |00:01:00 (1 perc) |
| retryTimeout |Minden egyes újrapróbálkozási kísérlet időtúllépés.<br/><br/>Ez a tulajdonság értéke 10 perc, ha az érvényesítés 10 percen belül be kell. Az érvényesítés végrehajtásához 10 percnél hosszabb ideig tart, ha az újrapróbálkozás időkorlátja.<br/><br/>Ha az érvényesítés minden kísérlet időkorlátja lejár, a szelet időtúllépés miatt megszakadt van megjelölve. |Nem |00:10:00 (10 perc) |
| maximumRetry |Száma a rendelkezésre állás, a külső adatok kereséséhez. Az engedélyezett maximális értéke 10. |Nem |3 |


## <a name="data-stores"></a>ADATTÁROLÓK
A [társított szolgáltatás](#linked-service) szakaszban megadott közös minden típusú társított szolgáltatás JSON-elemek leírásait. Ez a szakasz a JSON-elemek, amelyek minden adattárhoz bizonyos részletesen ismerteti.

A [adatkészlet](#dataset) közös minden típusú adatkészletek JSON-elemek leírásait szakaszban megadott. Ez a szakasz a JSON-elemek, amelyek minden adattárhoz bizonyos részletesen ismerteti.

A [tevékenység](#activity) szakaszban megadott közös minden JSON-elemek leírásait. Ez a szakasz ismerteti, amelyek minden adattárhoz bizonyos, ha használatban van egy forrásként/fogadóként másolási tevékenység JSON-elemek részleteit.  

Kattintson a hivatkozásra az Önt érdeklő megtekintéséhez a társított szolgáltatás, adatkészlet és a forrás/fogadó a másolási tevékenység a JSON-sémáinak tárolóhoz.

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
| &nbsp; |[Webtábla](#web-table) |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatásokat két típusa van: Az Azure Storage társított szolgáltatás és az Azure Storage SAS társított szolgáltatás.

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Azure storage-fiók összekapcsolása a data factory használatával az **fiókkulcs**, egy Azure Storage társított szolgáltatás létrehozásához. Adja meg egy Azure Storage társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AzureStorage**. Ezt követően megadhatja az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| kapcsolati Sztringje |Adja meg a connectionString tulajdonság az Azure storage való kapcsolódáshoz szükséges adatokat. |Igen |

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

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS társított szolgáltatás
Az Azure Storage SAS társított szolgáltatás egy Azure Storage-fiók összekapcsolása az Azure data factory egy közös hozzáférésű Jogosultságkód (SAS) használatával teszi lehetővé. Az adat-előállító all/adott erőforrásokhoz (a blob/tároló) a storage-ban korlátozott/időhöz kötött hozzáférést biztosít. A Azure storage-fiók összekapcsolása az adat-előállító közös hozzáférésű Jogosultságkód használatával hozzon létre egy Azure Storage SAS társított szolgáltatást. Adja meg a Azure Storage SAS társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AzureStorageSas**. Ezt követően megadhatja az alábbi tulajdonságokat a **typeProperties** szakaszban:   

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| sasUri |Adja meg a megosztott hozzáférési Jogosultságkód URI az Azure Storage-erőforrások, például blob, tárolót vagy tábla. |Igen |

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

A társított szolgáltatásokkal kapcsolatos további információkért lásd: [Azure Blob Storage-összekötő](data-factory-azure-blob-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Egy Azure Blob-adatkészletek definiálásához, állítsa be a **típus** a az adatkészlet **AzureBlob**. Ezután adja meg a következő Azure-Blob konkrét tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |A tároló és a blob Storage-mappa elérési útja. Példa: myblobcontainer\myblobfolder\ |Igen |
| fileName |A blob nevével. a fájlnév paraméter nem kötelező, és a kis-és nagybetűket.<br/><br/>Ha megadja a FileName paramétert, a Blobra a (beleértve a másolási) tevékenység működik.<br/><br/>Ha a fájlnév nincs megadva, példány összes BLOB bemeneti adatkészlet a folderPath tartalmazza.<br/><br/>Ha a fájlnév nincs megadva a kimeneti adatkészletek, a létrehozott fájl neve a következő lenne ebben a formátumban: Adatok. <Guid>.txt (például:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| partitionedBy |partitionedBy tulajdonság megadása nem kötelező. Használhatja a dinamikus folderPath és fájlnevét, idősorozat-adatok megadása. Ha például folderPath rendelkeznek az adatok minden óra. |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimális** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

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


További információkért lásd: [Azure Blob-összekötő](data-factory-azure-blob-connector.md#dataset-properties) cikk.

### <a name="blobsource-in-copy-activity"></a>A másolási tevékenység BlobSource
Adatokat másol egy Azure Blob Storage-ból, ha a **forrás típusa** a másolási tevékenység **BlobSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákban vagy csak a megadott mappába. |(Alapértelmezett érték), true a False |Nem |

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
Adatokat másol egy Azure Blob Storage, ha a **fogadó típusa** a másolási tevékenység **BlobSink**, és adja meg az alábbi tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a copyBehavior |A másolási viselkedés határozza meg, ha a forrás BlobSource vagy fájlrendszer. |<b>PreserveHierarchy</b>: megőrzi a hierarchiája a célmappában. A forrásmappa forrás-fájl elérési útja megegyezik a célmappában a célfájl elérési útja.<br/><br/><b>FlattenHierarchy</b>: a forrásmappából a fájlok a célmappában az első szintjét is. A cél fájlok automatikusan létrehozott nevet adni. <br/><br/><b>MergeFiles (alapértelmezett):</b> egyesíti a forrásmappából egy fájl összes fájlt. Ha a fájl/Blob neve van megadva, az egyesített fájl neve lesz a megadott név; Ellenkező esetben lenne automatikusan létrehozott fájl nevét. |Nem |

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

További információkért lásd: [Azure Blob-összekötő](data-factory-azure-blob-connector.md#copy-activity-properties) cikk. 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg az Azure Data Lake Store, az a társított szolgáltatás típusának beállítása **AzureDataLakeStore**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureDataLakeStore** | Igen |
| dataLakeStoreUri | Adja meg az Azure Data Lake Store-fiók adatait. A következő formátumban van: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/`. | Igen |
| subscriptionId | Azure-előfizetés azonosítója, amelyhez a Data Lake Store tartozik. | A fogadó megadása kötelező |
| resourceGroupName | Azure erőforráscsoport neve, amelyhez a Data Lake Store tartozik. | A fogadó megadása kötelező |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen (egyszerű szolgáltatásnév hitelesítése) |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. | Igen (egyszerű szolgáltatásnév hitelesítése) |
| bérlő | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen (egyszerű szolgáltatásnév hitelesítése) |
| Engedélyezési | Kattintson a **engedélyezés** gombra a **Data Factory Editor** , és adja meg a hitelesítő adat, amelyet az automatikusan generált engedélyezési URL-címet rendel hozzá ezt a tulajdonságot. | Igen (a felhasználói hitelesítő)|
| sessionId | OAuth munkamenet-azonosító az OAuth hitelesítési munkamenetből. Minden munkamenet-azonosító egyedi, és előfordulhat, hogy csak egyszer használható. Ez a beállítás automatikusan jön létre Data Factory Editor használata esetén. | Igen (a felhasználói hitelesítő) |

#### <a name="example-using-service-principal-authentication"></a>Példa: használja az egyszerű szolgáltatásnév hitelesítése
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

#### <a name="example-using-user-credential-authentication"></a>Példa: felhasználói hitelesítő adatok hitelesítés használatával
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

További információkért lásd: [Azure Data Lake Store-összekötő](data-factory-azure-datalake-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Egy Azure Data Lake Store-adatkészletek definiálásához, állítsa be a **típusa** az adatkészlet, **AzureDataLakeStore**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| folderPath |Elérési út a tároló és az Azure Data Lake-mappában tárolhatja. |Igen |
| fileName |Az Azure Data Lake store a fájl nevét. a fájlnév paraméter nem kötelező, és a kis-és nagybetűket. <br/><br/>Ha megadja a FileName paramétert, a (beleértve a másolási) tevékenység működik, az adott fájlon.<br/><br/>Ha a fájlnév nincs megadva, másolási minden fájl a bemeneti adatkészlet folderPath tartalmazza.<br/><br/>Ha a fájlnév nincs megadva a kimeneti adatkészletek, a létrehozott fájl neve a következő lenne ebben a formátumban: Adatok. <Guid>.txt (például:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| partitionedBy |partitionedBy tulajdonság megadása nem kötelező. Használhatja a dinamikus folderPath és fájlnevét, idősorozat-adatok megadása. Ha például folderPath rendelkeznek az adatok minden óra. |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimális** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

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

További információkért lásd: [Azure Data Lake Store-összekötő](data-factory-azure-datalake-connector.md#dataset-properties) cikk. 

### <a name="azure-data-lake-store-source-in-copy-activity"></a>A másolási tevékenység az Azure Data Lake Store-forrás
Adatokat másol egy Azure Data Lake Store az, ha a **forrás típusa** a másolási tevékenység **AzureDataLakeStoreSource**, és adja meg az alábbi tulajdonságokat a **forrás**szakaszban:

**AzureDataLakeStoreSource** támogatja a következő tulajdonságok **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákban vagy csak a megadott mappába. |(Alapértelmezett érték), true a False |Nem |

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

További információkért lásd: [Azure Data Lake Store-összekötő](data-factory-azure-datalake-connector.md#copy-activity-properties) cikk.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>A másolási tevékenység az Azure Data Lake Store fogadó
Ha adatokat másol egy Azure Data Lake Store, állítsa be a **fogadó típusa** a másolási tevékenység **AzureDataLakeStoreSink**, és adja meg az alábbi tulajdonságokat az a **fogadó** a szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a copyBehavior |Meghatározza a másolási viselkedés. |<b>PreserveHierarchy</b>: megőrzi a hierarchiája a célmappában. A forrásmappa forrás-fájl elérési útja megegyezik a célmappában a célfájl elérési útja.<br/><br/><b>FlattenHierarchy</b>: minden fájl a forrásmappából az első szintű célmappában jönnek létre. A cél fájlok jönnek létre automatikusan létrehozott névvel.<br/><br/><b>MergeFiles</b>: egyesíti a forrásmappából egy fájl összes fájlt. Ha a fájl/Blob neve van megadva, az egyesített fájl neve lesz a megadott név; Ellenkező esetben lenne automatikusan létrehozott fájl nevét. |Nem |

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

További információkért lásd: [Azure Data Lake Store-összekötő](data-factory-azure-datalake-connector.md#copy-activity-properties) cikk. 

## <a name="azure-cosmos-db"></a>Azure Cosmos DB  

### <a name="linked-service"></a>Társított szolgáltatások
Meghatározásához egy Azure Cosmos DB-hez társított szolgáltatást, állítsa be a **típusa** a társított szolgáltatás **DocumentDb**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| **Tulajdonság** | **Leírás** | **Kötelező** |
| --- | --- | --- |
| kapcsolati Sztringje |Azure Cosmos DB-adatbázishoz való kapcsolódáshoz szükséges információkat adják meg. |Igen |

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
További információkért lásd: [Azure Cosmos DB-összekötő](data-factory-azure-documentdb-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Egy Azure Cosmos DB-adatkészletek definiálásához, állítsa be a **típusa** a az adatkészlet **DocumentDbCollection**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| **Tulajdonság** | **Leírás** | **Kötelező** |
| --- | --- | --- |
| collectionName |Az Azure Cosmos DB-gyűjtemény neve. |Igen |

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
További információkért lásd: [Azure Cosmos DB-összekötő](data-factory-azure-documentdb-connector.md#dataset-properties) cikk.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>A másolási tevékenység az Azure Cosmos DB-gyűjtemény forrás
Adatokat másol egy Azure Cosmos DB-ből, ha a **forrás típusa** a másolási tevékenység **DocumentDbCollectionSource**, és adja meg az alábbi tulajdonságokat a **forrás**szakaszban:


| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Kötelező** |
| --- | --- | --- | --- |
| lekérdezés |Adja meg a lekérdezés adatokat olvasni. |Lekérdezés-karakterlánc, az Azure Cosmos DB által támogatott. <br/><br/>Például: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, az SQL-utasítást, amely hajtja végre: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Jelzi, hogy a dokumentum van beágyazva speciális karakter |Bármely karakter. <br/><br/>Azure Cosmos DB a NoSQL-alapú tárolót a JSON-dokumentumok, amelyben beágyazott struktúrák engedélyezettek. Az Azure Data Factory lehetővé teszi, hogy a felhasználó nestingSeparator, amely használatával a hierarchia jelölésére "." a fenti példákban. Az elválasztó, a másolási tevékenység hoz létre három gyermeket elem "Name" objektum először, középső és az utolsó, "Name.First", "Name.Middle" és "Name.Last" tábla definíciójában. |Nem |

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

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Az Azure Cosmos DB-gyűjtemény fogadó a másolási tevékenység
Adatok másolása az Azure Cosmos DB, állítsa be a **fogadó típusa** a másolási tevékenység **DocumentDbCollectionSink**, és adja meg az alábbi tulajdonságokat a **fogadó** szakasz :

| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Kötelező** |
| --- | --- | --- | --- |
| nestingSeparator |A forrás oszlop nevét jelzi, hogy a beágyazott dokumentum egy speciális karaktert van szükség. <br/><br/>Például a fent: `Name.First` a kimeneti tábla hoz létre a következő JSON-struktúrát a Cosmos DB-dokumentumban:<br/><br/>"Name": {}<br/>    "First": "János"<br/>}, |A beágyazási szinteket elválasztó karakter.<br/><br/>Alapértelmezett érték `.` (pont). |A beágyazási szinteket elválasztó karakter. <br/><br/>Alapértelmezett érték `.` (pont). |
| WriteBatchSize |Dokumentumok létrehozása az Azure Cosmos DB szolgáltatás párhuzamos kérelmek száma.<br/><br/>A teljesítmény finomhangolására, példatípust az adatok és-tárolókról az Azure Cosmos DB használatával ezt a tulajdonságot. A jobb teljesítmény várható writeBatchSize növelése, mert az Azure Cosmos DB több párhuzamos kérés lesz elküldve. Azonban, amely szabályozás elkerülése érdekében kell nagyvállalat a hibaüzenet: "Meg túl sok kérelmet adott".<br/><br/>Szabályozás számos tényezőtől, beleértve a dokumentumokat, a feltételek a dokumentumok száma méretét, indexelési szabályzat célgyűjtemény stb határoz meg. A másolási műveletek segítségével jobb gyűjtemény (például S3 szintű) rendelkezik a legtöbb számára elérhető adatátviteli mennyiség (2500 kérés kérelemegység/s). |Egész szám |Nem (alapértelmezett: 5.) |
| writeBatchTimeout |Várjon, amíg a művelet befejezését, mielőtt azt az időkorlátot. |Időtartam<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |

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

További információkért lásd: [Azure Cosmos DB-összekötő](data-factory-azure-documentdb-connector.md#copy-activity-properties) cikk.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Társított szolgáltatások
Adja meg az Azure SQL Database társított szolgáltatás, és állítsa a **típus** a társított szolgáltatás **AzureSqlDatabase**, és adja meg az alábbi tulajdonságokat a **typeProperties** a szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kapcsolati Sztringje |Adja meg a connectionString tulajdonság az Azure SQL Database-példányhoz való kapcsolódáshoz szükséges adatokat. |Igen |

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
Egy Azure SQL Database-adatkészletek definiálásához, állítsa be a **típusa** a az adatkészlet **AzureSqlTable**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla vagy nézet az Azure SQL Database-példányban, amelyek a társított szolgáltatás neve hivatkozik. |Igen |

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

### <a name="sql-source-in-copy-activity"></a>A másolási tevékenység az SQL-forrás
Adatokat másol egy Azure SQL Database-ből, ha a **forrás típusa** a másolási tevékenység **SqlSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Példa: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |

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

### <a name="sql-sink-in-copy-activity"></a>A másolási tevékenység az SQL-fogadó
Adatok másolása az Azure SQL Database, állítsa be a **fogadó típusa** a másolási tevékenység **SqlSink**, és adja meg az alábbi tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| writeBatchTimeout |Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot. |Időtartam<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |
| WriteBatchSize |Amikor a puffer mérete eléri a writeBatchSize adatok beszúrása SQL-táblát. |Egész szám (sorok száma) |Nem (alapértelmezett: 10 000) |
| sqlWriterCleanupScript |Adjon meg egy lekérdezést a másolási tevékenység végrehajtásához úgy, hogy az adott szeletre vonatkozó adatok törlődnek. |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Adja meg az automatikusan generált szelet azonosítóval, amelyet egy adott szeletre mikor futtassa újra a adatainak töltse ki a másolási tevékenység oszlop nevét. |Egy oszlop binary(32) adattípusú oszlop neve. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárás neve a célként megadott táblába upserts (frissítés/Beszúrás) adatokat. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |
| sqlWriterTableType |Adjon meg egy tábla típusú név a tárolt eljárás használható. Másolási tevékenység egy ideiglenes táblát a tábla típusú elérhetővé teszi az adatok áthelyezését. Tárolt eljárás kód majd egyesítheti az adatok másolását a adatokkal. |Egy tábla típusú név. |Nem |

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
Társított szolgáltatás határozza meg az Azure SQL Data Warehouse, állítsa be a **típus** a társított szolgáltatás **AzureSqlDW**, és adja meg az alábbi tulajdonságokat a **typeProperties** a szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kapcsolati Sztringje |Adja meg a connectionString tulajdonság az Azure SQL Data Warehouse-példányhoz való kapcsolódáshoz szükséges adatokat. |Igen |



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
Egy Azure SQL Data Warehouse-adatkészletek definiálásához, állítsa be a **típusa** a az adatkészlet **AzureSqlDWTable**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla vagy nézet, amelyre a társított szolgáltatás hivatkozik az Azure SQL Data Warehouse-adatbázis neve. |Igen |

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

### <a name="sql-dw-source-in-copy-activity"></a>SQL DW-forrás a másolási tevékenység
Adatok másolása az Azure SQL Data Warehouse-ból, állítsa be a **adatforrástípust** a másolási tevékenység **SqlDWSource**, és adja meg az alábbi tulajdonságokat a **forrás** a szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |

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

### <a name="sql-dw-sink-in-copy-activity"></a>Az SQL DW fogadó a másolási tevékenység
Adatok másolása az Azure SQL Data Warehouse, állítsa be a **fogadó típusa** a másolási tevékenység **SqlDWSink**, és adja meg az alábbi tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Adjon meg egy lekérdezést a másolási tevékenység végrehajtásához úgy, hogy az adott szeletre vonatkozó adatok törlődnek. |A lekérdezési utasítást. |Nem |
| allowPolyBase |Azt jelzi, hogy (ha van ilyen), a PolyBase használata helyett BULKINSERT mechanizmust. <br/><br/> **Az ajánlott módszer az adatok betöltése az SQL Data Warehouse-bA a PolyBase.** |True (Igaz) <br/>FALSE (alapértelmezett) |Nem |
| polyBaseSettings |Egy csoport tulajdonságok is lehet megadni, ha a **allowPolybase** tulajdonsága **igaz**. |&nbsp; |Nem |
| rejectValue |Megadja a szám vagy százalékos aránya, amelyek is vissza kell utasítani, mielőtt a lekérdezés nem sikerült sorokat. <br/><br/>További információ a PolyBase visszautasítási lehetőségeit a a **argumentumok** szakaszában [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) témakör. |0 (alapértelmezett), 1, 2... |Nem |
| rejectType |Itt adhatja meg, e a rejectValue kapcsoló Szövegkonstansérték vagy százalékban megadva. |Érték (alapértelmezett), százalékos aránya |Nem |
| rejectSampleValue |Mielőtt a PolyBase újraszámítja a visszautasított sorok aránya beolvasandó sorok számát határozza meg. |1, 2, … |Igen, ha **rejectType** van **százalékos aránya** |
| useTypeDefault |Itt adhatja meg, hogyan szeretné kezelni a PolyBase kér le adatokat a szövegfájl elválasztójellel tagolt szöveges fájlok a hiányzó értékeket.<br/><br/>További tudnivalók a ezt a tulajdonságot a következő argumentumok szakaszában [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |TRUE, False (alapértelmezett) |Nem |
| WriteBatchSize |Adatok beszúrása SQL-táblát, amikor a puffer mérete eléri a writeBatchSize |Egész szám (sorok száma) |Nem (alapértelmezett: 10 000) |
| writeBatchTimeout |Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot. |Időtartam<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |

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
Társított szolgáltatás határozza meg az Azure Search, és állítsa a **típus** a társított szolgáltatás **AzureSearch**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| url | Az Azure Search szolgáltatás URL-címe. | Igen |
| kulcs | Az Azure Search szolgáltatás rendszergazdai kulcsa. | Igen |

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

További információkért lásd: [Azure Search-összekötő](data-factory-azure-search-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Állítsa be az Azure Search-adatkészletek definiálásához a **típusa** az adatkészlet, **AzureSearchIndex**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| type | A type tulajdonságot állítsa **AzureSearchIndex**.| Igen |
| indexName | Az Azure Search-index neve. Adat-előállító nem hoz létre az indexet. Az index léteznie kell az Azure Search szolgáltatásban. | Igen |

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

További információkért lásd: [Azure Search-összekötő](data-factory-azure-search-connector.md#dataset-properties) cikk.

### <a name="azure-search-index-sink-in-copy-activity"></a>A másolási tevékenység az Azure Search-Index fogadó
Adatok másolása az Azure Search-index, állítsa be a **fogadó típusa** a másolási tevékenység **AzureSearchIndexSink**, és adja meg az alábbi tulajdonságokat a **fogadó** a szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Megadja, hogy egyesítéséhez, vagy cserélje le, amikor a dokumentum az indexben már létezik. | Egyesítés (alapértelmezett)<br/>Feltöltés| Nem |
| WriteBatchSize | Amikor a puffer mérete eléri a writeBatchSize feltölti az adatokat az Azure Search-indexbe. | az 1000 1. Alapértelmezett értéke 1000. | Nem |

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

További információkért lásd: [Azure Search-összekötő](data-factory-azure-search-connector.md#copy-activity-properties) cikk.

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatásokat két típusa van: Az Azure Storage társított szolgáltatás és az Azure Storage SAS társított szolgáltatás.

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Azure storage-fiók összekapcsolása a data factory használatával az **fiókkulcs**, egy Azure Storage társított szolgáltatás létrehozásához. Adja meg egy Azure Storage társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AzureStorage**. Ezt követően megadhatja az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **AzureStorage** |Igen |
| kapcsolati Sztringje |Adja meg a connectionString tulajdonság az Azure storage való kapcsolódáshoz szükséges adatokat. |Igen |

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

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS társított szolgáltatás
Az Azure Storage SAS társított szolgáltatás egy Azure Storage-fiók összekapcsolása az Azure data factory egy közös hozzáférésű Jogosultságkód (SAS) használatával teszi lehetővé. Az adat-előállító all/adott erőforrásokhoz (a blob/tároló) a storage-ban korlátozott/időhöz kötött hozzáférést biztosít. A Azure storage-fiók összekapcsolása az adat-előállító közös hozzáférésű Jogosultságkód használatával hozzon létre egy Azure Storage SAS társított szolgáltatást. Adja meg a Azure Storage SAS társított szolgáltatás, állítsa be a **típus** a társított szolgáltatás **AzureStorageSas**. Ezt követően megadhatja az alábbi tulajdonságokat a **typeProperties** szakaszban:   

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **AzureStorageSas** |Igen |
| sasUri |Adja meg a megosztott hozzáférési Jogosultságkód URI az Azure Storage-erőforrások, például blob, tárolót vagy tábla. |Igen |

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

A társított szolgáltatásokkal kapcsolatos további információkért lásd: [Azure Table Storage-összekötő](data-factory-azure-table-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Állítsa be az Azure Table-adatkészletek definiálásához a **típusa** a az adatkészlet **AzureTable**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |Az Azure Table-adatbázispéldányban, amelyek a társított szolgáltatás hivatkozik a tábla neve. |Igen. A tableName egy azureTableSourceQuery nélkül van megadva, a tábla minden rekordját a célhelyre másolódnak. Ha egy azureTableSourceQuery is meg van adva, a cél, amely eleget tesz a lekérdezést a tábla rekordjai lesz másolva. |

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

A társított szolgáltatásokkal kapcsolatos további információkért lásd: [Azure Table Storage-összekötő](data-factory-azure-table-connector.md#dataset-properties) cikk. 

### <a name="azure-table-source-in-copy-activity"></a>A másolási tevékenység az Azure tábla forrása
Adatok másolása az Azure Table Storage-ból, állítsa be a **adatforrástípust** a másolási tevékenység **AzureTableSource**, és adja meg az alábbi tulajdonságokat a **forrás** a szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| azureTableSourceQuery |Az egyéni lekérdezés segítségével olvassa el az adatokat. |Azure-tábla lekérdezési karakterláncot. Példák a következő szakaszban talál. |Nem. A tableName egy azureTableSourceQuery nélkül van megadva, a tábla minden rekordját a célhelyre másolódnak. Ha egy azureTableSourceQuery is meg van adva, a cél, amely eleget tesz a lekérdezést a tábla rekordjai lesz másolva. |
| azureTableSourceIgnoreTableNotFound |Adja meg, hogy swallow a kivétel a tábla nem létezik. |IGAZ<br/>FALSE (HAMIS) |Nem |

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

A társított szolgáltatásokkal kapcsolatos további információkért lásd: [Azure Table Storage-összekötő](data-factory-azure-table-connector.md#copy-activity-properties) cikk. 

### <a name="azure-table-sink-in-copy-activity"></a>A másolási tevékenység fogadó Azure-tábla
Adatok másolása az Azure Table Storage, állítsa be a **fogadó típusa** a másolási tevékenység **AzureTableSink**, és adja meg az alábbi tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Alapértelmezett partíciós kulcsérték, amely a fogadó által használható. |Egy karakterláncértéket. |Nem |
| azureTablePartitionKeyName |Adja meg az oszlop, amelynek az értékekkel, partíciókulcsok nevét. Ha nincs megadva, a partíciókulcs AzureTableDefaultPartitionKeyValue lesz. |Egy oszlop neve. |Nem |
| azureTableRowKeyName |Adja meg az oszlop, amelynek oszlopértékek sor kulcsaként használt nevét. Ha nincs megadva, használjon egy GUID Azonosítót minden egyes sorára. |Egy oszlop neve. |Nem |
| azureTableInsertType |Adatok beszúrása az Azure-tábla a módot.<br/><br/>Ez a tulajdonság szabja meg, hogy rendelkeznek-e létező sorok egyeztetésével partíció-és a kimeneti tábla cserélni vagy egyesített értékekre. <br/><br/>(Egyesítési és cserélje ki) ezen beállítások működésének kapcsolatos további információkért lásd: [vagy egyesítési entitás beszúrása](https://msdn.microsoft.com/library/azure/hh452241.aspx) és [entitás cseréje vagy beszúrása](https://msdn.microsoft.com/library/azure/hh452242.aspx) témaköröket. <br/><br> Ez a beállítás a sor szintjén, a tábla szintjén nem vonatkozik, és sem a lehetőség törli a kimeneti tábla sorait, amelyek a bemeneti adatok nem léteznek. |Egyesítés (alapértelmezett)<br/>cserélje le |Nem |
| WriteBatchSize |Szúr be az Azure-tábla adatait, ha elérte a writeBatchSize vagy writeBatchTimeout. |Egész szám (sorok száma) |Nem (alapértelmezett: 10 000) |
| writeBatchTimeout |Adatok beszúrása az Azure-tábla, ha elérte a writeBatchSize vagy writeBatchTimeout |Időtartam<br/><br/>Példa: "00: 20:00" (20 perc) |Nem (az alapértelmezett tároló ügyfél alapértelmezett időtúllépési érték 90 másodperc) |

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
A társított szolgáltatásokkal kapcsolatos további információkért lásd: [Azure Table Storage-összekötő](data-factory-azure-table-connector.md#copy-activity-properties) cikk. 

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg az Amazon Redshift, állítsa be a **típus** a társított szolgáltatás **AmazonRedshift**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz :  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |IP-cím vagy a gazdagép neve az Amazon Redshift-kiszolgáló. |Igen |
| port |Az Amazon Redshift-kiszolgáló az ügyfélkapcsolatok figyeléséhez használt TCP-port száma. |Nem, az alapértelmezett érték: 5439 |
| adatbázis |Az Amazon Redshift-adatbázis neve. |Igen |
| felhasználónév |Az adatbázishoz hozzáféréssel rendelkező felhasználó nevét. |Igen |
| jelszó |A felhasználói fiókhoz tartozó jelszót. |Igen |

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

További információkért lásd: [Amazon Redshift-összekötő](#data-factory-amazon-redshift-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Az Amazon Redshift-adatkészletek definiálásához, állítsa be a **típusa** az adatkészlet, **RelationalTable**, és adja meg a következő tulajdonságok a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |Az Amazon Redshift-adatbázisban, amelyek a társított szolgáltatás hivatkozik a tábla neve. |Nem (Ha **lekérdezés** , **RelationalSource** van megadva) |


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
További információkért lásd: [Amazon Redshift-összekötő](#data-factory-amazon-redshift-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs forrásként 
Adatok másolása az Amazon Redshift, állítsa be a **forrás típusa** a másolási tevékenység **RelationalSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: `select * from MyTable`. |Nem (Ha **tableName** , **adatkészlet** van megadva) |

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
További információkért lásd: [Amazon Redshift-összekötő](#data-factory-amazon-redshift-connector.md#copy-activity-properties) cikk.

## <a name="ibm-db2"></a>IBM DB2-HÖZ

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg az IBM DB2-höz, és állítsa a **típus** a társított szolgáltatás **OnPremisesDB2**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |A DB2-kiszolgáló neve. |Igen |
| adatbázis |A DB2-adatbázis neve. |Igen |
| séma |A séma az adatbázis neve. A séma neve a kis-és nagybetűket. |Nem |
| authenticationType |A DB2-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjáró neve |Az átjáró által a Data Factory szolgáltatás a helyszíni DB2-adatbázishoz való csatlakozáshoz használandó neve. |Igen |

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
Egy DB2-adatkészletek definiálásához, állítsa be a **típus** a az adatkészlet **RelationalTable**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla a DB2-adatbázis példányában, amelyre a társított szolgáltatás neve hivatkozik. A tableName a kis-és nagybetűket. |Nem (Ha **lekérdezés** , **RelationalSource** van megadva) 

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

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs forrásként
Adatok másolása az IBM DB2, állítsa be a **forrás típusa** a másolási tevékenység **RelationalSource**, és adja meg az alábbi tulajdonságokat az a **forrás** szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: `"query": "select * from "MySchema"."MyTable""`. |Nem (Ha **tableName** , **adatkészlet** van megadva) |

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
Társított szolgáltatás határozza meg a MySQL, állítsa be a **típus** a társított szolgáltatás **OnPremisesMySql**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |A MySQL-kiszolgáló neve. |Igen |
| adatbázis |A MySQL-adatbázis neve. |Igen |
| séma |A séma az adatbázis neve. |Nem |
| authenticationType |A MySQL-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek a következők: `Basic`. |Igen |
| felhasználónév |Adja meg a felhasználónevet, a MySQL-adatbázishoz való csatlakozáshoz. |Igen |
| jelszó |Adja meg a megadott felhasználói fiókhoz tartozó jelszót. |Igen |
| átjáró neve |Az átjáró által a Data Factory szolgáltatás a helyi MySQL-adatbázishoz való kapcsolódáshoz használandó neve. |Igen |

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

További információkért lásd: [MySQL-összekötő](data-factory-onprem-mysql-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Egy MySQL-adatkészletek definiálásához, állítsa be a **típus** , az adatkészlet **RelationalTable**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A MySQL-adatbázispéldányban, amelyek a társított szolgáltatás hivatkozik a tábla neve. |Nem (Ha **lekérdezés** , **RelationalSource** van megadva) |

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
További információkért lásd: [MySQL-összekötő](data-factory-onprem-mysql-connector.md#dataset-properties) cikk. 

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs forrásként
Ha egy MySQL-adatbázisból másol adatokat, állítsa a **forrás típusa** a másolási tevékenység **RelationalSource**, és adja meg az alábbi tulajdonságokat az a **forrás** szakasz:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: `select * from MyTable`. |Nem (Ha **tableName** , **adatkészlet** van megadva) |


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

További információkért lásd: [MySQL-összekötő](data-factory-onprem-mysql-connector.md#copy-activity-properties) cikk. 

## <a name="oracle"></a>Oracle 

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg az Oracle, állítsa be a **típus** a társított szolgáltatás **OnPremisesOracle**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| driverType | Adja meg, mely illesztőprogram-adatok másolása Azure blobból vagy az Oracle-adatbázis használatával. Engedélyezett értékek a következők **Microsoft** vagy **ODP** (alapértelmezett). Lásd: [verziójától és a telepítés támogatott](#supported-versions-and-installation) illesztőprogram adatai szakaszán. | Nem |
| kapcsolati Sztringje | Adja meg a connectionString tulajdonság az Oracle Database-példányhoz való kapcsolódáshoz szükséges adatokat. | Igen |
| átjáró neve | Az átjáró a helyszíni Oracle-kiszolgálóhoz való csatlakozáshoz használt név |Igen |

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

További információkért lásd: [Oracle-összekötő](data-factory-onprem-oracle-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Egy Oracle-adatkészletek definiálásához, állítsa be a **típusa** a az adatkészlet **OracleTable**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |Az Oracle-adatbázis, amelyre a társított szolgáltatás hivatkozik a tábla neve. |Nem (Ha **oracleReaderQuery** , **OracleSource** van megadva) |

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
További információkért lásd: [Oracle-összekötő](data-factory-onprem-oracle-connector.md#dataset-properties) cikk.

### <a name="oracle-source-in-copy-activity"></a>A másolási tevékenység Oracle-forrás
Oracle-adatbázisból másol adatokat, ha a **adatforrástípust** a másolási tevékenység **OracleSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| oracleReaderQuery |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például:`select * from MyTable` <br/><br/>Ha nincs megadva, az SQL-utasítást, amely hajtja végre: `select * from MyTable` |Nem (Ha **tableName** , **adatkészlet** van megadva) |

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

További információkért lásd: [Oracle-összekötő](data-factory-onprem-oracle-connector.md#copy-activity-properties) cikk.

### <a name="oracle-sink-in-copy-activity"></a>A másolási tevékenység Oracle fogadó
Adatok másolása az Oracle database am, állítsa be a **fogadó típusa** a másolási tevékenység **Oraclesinkben**, és adja meg az alábbi tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| writeBatchTimeout |Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot. |Időtartam<br/><br/> Példa: 00:30:00 (30 perc). |Nem |
| WriteBatchSize |Amikor a puffer mérete eléri a writeBatchSize adatok beszúrása SQL-táblát. |Egész szám (sorok száma) |Nem (alapértelmezett: 100) |
| sqlWriterCleanupScript |Adjon meg egy lekérdezést a másolási tevékenység végrehajtásához úgy, hogy az adott szeletre vonatkozó adatok törlődnek. |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Adja meg az oszlop nevét adja meg az automatikusan generált szelet azonosítóval, amelyet egy adott szeletre mikor futtassa újra a adatainak a másolási tevékenység. |Egy oszlop binary(32) adattípusú oszlop neve. |Nem |

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
További információkért lásd: [Oracle-összekötő](data-factory-onprem-oracle-connector.md#copy-activity-properties) cikk.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Társított szolgáltatások
Egy PostgreSQL meghatározásához társított szolgáltatást, állítsa be a **típus** a társított szolgáltatás **OnPremisesPostgreSql**, és adja meg az alábbi tulajdonságokat a **typeProperties** a szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |A PostgreSQL-kiszolgáló neve. |Igen |
| adatbázis |A PostgreSQL-adatbázis neve. |Igen |
| séma |A séma az adatbázis neve. A séma neve a kis-és nagybetűket. |Nem |
| authenticationType |A PostgreSQL-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjáró neve |Az átjáró által a Data Factory szolgáltatás a helyi PostgreSQL-adatbázishoz való kapcsolódáshoz használandó neve. |Igen |

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
Egy PostgreSQL-adatkészletek definiálásához, állítsa be a **típus** , az adatkészlet **RelationalTable**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A PostgreSQL-adatbázis példányában, amelyek a társított szolgáltatás hivatkozik a tábla neve. A tableName a kis-és nagybetűket. |Nem (Ha **lekérdezés** , **RelationalSource** van megadva) |

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

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs forrásként
Ha egy PostgreSQL-adatbázisból másol adatokat, állítsa be a **forrás típusa** a másolási tevékenység **RelationalSource**, és adja meg az alábbi tulajdonságokat az a **forrás** a szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: "query": "kiválasztása * a \"MySchema\".\" Táblanév\"". |Nem (Ha **tableName** , **adatkészlet** van megadva) |

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
Társított szolgáltatás határozza meg az SAP Business Warehouse (BW), és állítsa a **típus** a társított szolgáltatás **SapBw**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz :  

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
kiszolgáló | A kiszolgálóra, amelyen az SAP BW-példány neve. | sztring | Igen
systemNumber | Az SAP BW-rendszer rendszer száma. | Kétjegyű tizedes tört egy karakterláncból. | Igen
clientId | Az SAP W rendszerben az ügyfél ügyfél-azonosítója. | Három számjegyű tizedes tört egy karakterláncból. | Igen
felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Igen
jelszó | A felhasználó jelszava. | sztring | Igen
átjáró neve | Az átjáró által a Data Factory szolgáltatás a helyszíni SAP BW-példányhoz való csatlakozáshoz használandó neve. | sztring | Igen
encryptedCredential | A titkosított hitelesítő adatok karakterlánca. | sztring | Nem

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
Egy SAP BW-adatkészletek definiálásához, állítsa be a **típus** a az adatkészlet **RelationalTable**. Nincsenek az SAP BW-adatkészlet típusa támogatott típus-specifikus tulajdonságai **RelationalTable**.  

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

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs forrásként
Adatok másolása az SAP Business warehouse-hoz, állítsa be a **forrás típusa** a másolási tevékenység **RelationalSource**, és adja meg az alábbi tulajdonságokat az a **forrás** a szakaszban:


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
Társított szolgáltatás határozza meg egy SAP HANA, és állítsa a **típusa** a társított szolgáltatás **SapHana**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
kiszolgáló | A kiszolgálóra, amelyen az SAP HANA-példány neve. Ha a kiszolgáló egy egyéni portot használ, adja meg a `server:port`. | sztring | Igen
authenticationType | Hitelesítés típusa. | karakterlánc. "Alapszintű" vagy "Windows" | Igen 
felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Igen
jelszó | A felhasználó jelszava. | sztring | Igen
átjáró neve | Az átjáró által a Data Factory szolgáltatás a helyszíni SAP HANA-példányhoz való csatlakozáshoz használandó neve. | sztring | Igen
encryptedCredential | A titkosított hitelesítő adatok karakterlánca. | sztring | Nem

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
Egy SAP HANA-adatkészletek definiálásához, állítsa be a **típus** a az adatkészlet **RelationalTable**. Nincsenek az SAP HANA-adatkészlet típusa támogatott típus-specifikus tulajdonságai **RelationalTable**. 

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

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs forrásként
Adatokat másolhat egy SAP HANA adatokat az adattárból, ha a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg az alábbi tulajdonságokat a **forrás** a szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés | Adja meg az SQL-lekérdezést az SAP HANA-példány adatokat olvasni. | SQL-lekérdezést. | Igen |


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
Létrehoz egy társított szolgáltatást típusú **OnPremisesSqlServer** egy helyszíni SQL Server-adatbázis összekapcsolása a data factoryt. A következő táblázat a JSON-elemeket a helyszíni SQL Server-alapú társított szolgáltatás leírását.

Az alábbi táblázatban az adott SQL Server-alapú társított szolgáltatás JSON-elemek leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **OnPremisesSqlServer**. |Igen |
| kapcsolati Sztringje |Adja meg a connectionString adatokat a helyszíni SQL Server-adatbázis SQL-hitelesítés vagy a Windows-hitelesítés használatával való kapcsolódáshoz szükséges. |Igen |
| átjáró neve |Az átjáró által a Data Factory szolgáltatás a helyszíni SQL Server adatbázishoz való csatlakozáshoz használandó neve. |Igen |
| felhasználónév |Ha Windows-hitelesítést használ, adja meg a felhasználónevet. Példa: **domainname\\felhasználónév**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |

A hitelesítő adatok titkosíthatók a **New-AzureRmDataFactoryEncryptValue** parancsmag és a kapcsolati karakterláncot használja őket az alábbi példában látható módon (**EncryptedCredential** tulajdonság):  

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Példa: JSON-t az SQL-hitelesítés használata

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
#### <a name="example-json-for-using-windows-authentication"></a>Példa: JSON-t a Windows-hitelesítés használatával

Ha a felhasználónév és jelszó meg van adva, átjáró használja ezeket a megszemélyesíteni a megadott felhasználói fióknak a helyi SQL Server adatbázishoz való csatlakozáshoz. Átjáró ellenkező esetben az SQL Server közvetlenül a biztonsági környezet (saját indítófiókjának) átjáró csatlakozik.

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
Egy SQL Server-adatkészletek definiálásához, állítsa be a **típus** a az adatkészlet **SqlServerTable**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla vagy nézet a SQL Server-adatbázis példányában, amelyre a társított szolgáltatás neve hivatkozik. |Igen |

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

### <a name="sql-source-in-copy-activity"></a>A másolási tevékenység az SQL-forrás
Ha SQL Server-adatbázisból másol adatokat, állítsa be a **forrás típusa** a másolási tevékenység **SqlSource**, és adja meg az alábbi tulajdonságokat az a **forrás** szakaszban:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: `select * from MyTable`. Több táblát is hivatkozhatnak az adatbázisból, a bemeneti adatkészlet hivatkozik. Ha nincs megadva, az SQL-utasítás végrehajtott: válasszon a táblanév. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |

Ha a **sqlReaderQuery** van megadva a SqlSource, a másolási tevékenység a lekérdezés fut az SQL Server-adatbázis forrás, az adatok beolvasásához.

Másik lehetőségként megadhat egy tárolt eljárást megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméter szükséges).

Ha sqlReaderQuery vagy sqlReaderStoredProcedureName nincs megadva, a struktúra szakaszban meghatározott oszlopokat hozhat létre egy választó lekérdezést az SQL Server-adatbázis futtatásához használja. Ha az adatkészlet definíciója nem rendelkezik a struktúrát, az összes oszlop ki van jelölve, a táblából.

> [!NOTE]
> Ha használ **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy értéket a **tableName** tulajdonságot az adatkészlet JSON. Nincsenek nem lettek elvégezve, mint ez a táblázat azonban ellenőrzések.


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

Ebben a példában **sqlReaderQuery** a SqlSource van megadva. A másolási tevékenység a lekérdezés fut az SQL Server-adatbázis forrás, az adatok beolvasásához. Másik lehetőségként megadhat egy tárolt eljárást megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméter szükséges). A sqlReaderQuery hivatkozhat több tábla a bemeneti adatkészlet által hivatkozott adatbázishoz. Nem korlátozódik az csak az a tábla, az adatkészlet tableName typeProperty állítja be.

Ha sqlReaderQuery vagy sqlReaderStoredProcedureName nincs megadva, a struktúra szakaszban meghatározott oszlopokat hozhat létre egy választó lekérdezést az SQL Server-adatbázis futtatásához használja. Ha az adatkészlet definíciója nem rendelkezik a struktúrát, az összes oszlop ki van jelölve, a táblából.

További információkért lásd: [SQL Server-összekötő](data-factory-sqlserver-connector.md#copy-activity-properties) cikk. 

### <a name="sql-sink-in-copy-activity"></a>A másolási tevékenység az SQL-fogadó
Adatokat másolhat az SQL Server-adatbázis, ha a **fogadó típusa** a másolási tevékenység **SqlSink**, és adja meg az alábbi tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| writeBatchTimeout |Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot. |Időtartam<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |
| WriteBatchSize |Amikor a puffer mérete eléri a writeBatchSize adatok beszúrása SQL-táblát. |Egész szám (sorok száma) |Nem (alapértelmezett: 10 000) |
| sqlWriterCleanupScript |Adja meg a lekérdezés végrehajtása úgy, hogy az adott szeletre vonatkozó adatok törlődnek a másolási tevékenységhez. További információkért lásd: [ismételhetőség](#repeatability-during-copy) szakaszban. |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Adja meg az oszlop nevét adja meg az automatikusan generált szelet azonosítóval, amelyet egy adott szeletre mikor futtassa újra a adatainak a másolási tevékenység. További információkért lásd: [ismételhetőség](#repeatability-during-copy) szakaszban. |Egy oszlop binary(32) adattípusú oszlop neve. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárás neve a célként megadott táblába upserts (frissítés/Beszúrás) adatokat. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |
| sqlWriterTableType |Adja meg a tábla neve a tárolt eljárás használható. Másolási tevékenység egy ideiglenes táblát a tábla típusú elérhetővé teszi az adatok áthelyezését. Tárolt eljárás kód majd egyesítheti az adatok másolását a adatokkal. |Egy tábla típusú név. |Nem |

#### <a name="example"></a>Példa
A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **BlobSource** és **fogadó** típusa **SqlSink**.

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
Társított szolgáltatás határozza meg a Sybase, állítsa be a **típus** a társított szolgáltatás **OnPremisesSybase**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |A Sybase-kiszolgáló neve. |Igen |
| adatbázis |A Sybase-adatbázis neve. |Igen |
| séma |A séma az adatbázis neve. |Nem |
| authenticationType |A Sybase-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjáró neve |Az átjáró által a Data Factory szolgáltatás a helyszíni Sybase-adatbázishoz való kapcsolódáshoz használandó neve. |Igen |

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

További információkért lásd: [Sybase-összekötő](data-factory-onprem-sybase-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Egy Sybase-adatkészletek definiálásához, állítsa be a **típus** a az adatkészlet **RelationalTable**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla a Sybase-adatbázis példányában, amelyre a társított szolgáltatás neve hivatkozik. |Nem (Ha **lekérdezés** , **RelationalSource** van megadva) |

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

További információkért lásd: [Sybase-összekötő](data-factory-onprem-sybase-connector.md#dataset-properties) cikk. 

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs forrásként
Egy Sybase-adatbázisból másol adatokat, ha a **forrás típusa** a másolási tevékenység **RelationalSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakasz :


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: `select * from MyTable`. |Nem (Ha **tableName** , **adatkészlet** van megadva) |

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

További információkért lásd: [Sybase-összekötő](data-factory-onprem-sybase-connector.md#copy-activity-properties) cikk.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg a Teradata, állítsa be a **típusa** a társított szolgáltatás **OnPremisesTeradata**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |A Teradata-kiszolgáló neve. |Igen |
| authenticationType |A Teradata-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjáró neve |Az átjáró által a Data Factory szolgáltatás a helyszíni Teradata-adatbázishoz való kapcsolódáshoz használandó neve. |Igen |

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

További információkért lásd: [Teradata-összekötő](data-factory-onprem-teradata-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
A Teradata Blob-adatkészletek definiálásához, állítsa be a **típus** a az adatkészlet **RelationalTable**. Jelenleg nem támogatott a Teradata-adatkészlet tulajdonságait. 

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

További információkért lásd: [Teradata-összekötő](data-factory-onprem-teradata-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs forrásként
Teradata-adatbázisból másol adatokat, ha a **adatforrástípust** a másolási tevékenység **RelationalSource**, és adja meg az alábbi tulajdonságokat a **forrás** a szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: `select * from MyTable`. |Igen |

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

További információkért lásd: [Teradata-összekötő](data-factory-onprem-teradata-connector.md#copy-activity-properties) cikk.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Társított szolgáltatások
Cassandra-beli társított szolgáltatás definiálásához, állítsa be a **típus** a társított szolgáltatás **OnPremisesCassandra**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| gazdagép |Egy vagy több IP-címek vagy kiszolgálók Cassandra gazdagép nevét.<br/><br/>Adja meg az IP-címek vagy az összes kiszolgálóhoz csatlakozzon egyszerre állomásnevek vesszővel tagolt listája. |Igen |
| port |A Cassandra-kiszolgáló az ügyfélkapcsolatok figyeléséhez használt TCP portra. |Nem, az alapértelmezett érték: 9042 |
| authenticationType |Alapszintű vagy névtelen |Igen |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha authenticationType beállítása alapszintű. |
| jelszó |Adja meg a felhasználói fiókhoz tartozó jelszót. |Igen, ha authenticationType beállítása alapszintű. |
| átjáró neve |Az átjáró a helyszíni Cassandra-adatbázishoz való csatlakozáshoz használt neve. |Igen |
| encryptedCredential |A hitelesítő adatok titkosítva, az átjáró. |Nem |

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
Egy Cassandra-adatkészletek definiálásához, állítsa be a **típus** , az adatkészlet **CassandraTable**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kulcstér |A kulcstér vagy a sémát a Cassandra-adatbázis neve. |Igen (Ha **lekérdezés** a **CassandraSource** nincs megadva). |
| tableName |A tábla, Cassandra-adatbázis neve. |Igen (Ha **lekérdezés** a **CassandraSource** nincs megadva). |

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

### <a name="cassandra-source-in-copy-activity"></a>A másolási tevékenység Cassandra-forrás
Adatok másolása a Cassandra, állítsa be a **forrás típusa** a másolási tevékenység **CassandraSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-92 vagy CQL lekérdezés. Lásd: [CQL referencia](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL-lekérdezés használata esetén adja meg a **kulcstér name.table neve** a lekérdezni kívánt tábla ábrázolásához. |Nem (ha van megadva a tableName és kulcstér adatkészlet). |
| consistencyLevel |A konzisztencia szintjét adja meg, hány replikák válaszolnia kell egy olvasási kérést előtt adatokat ad vissza az ügyfélalkalmazásnak. Cassandra ellenőrzi a megadott számú replikákat az adatok az olvasási kérelem teljesítéséhez. |EGY, KETTŐ, HÁROM, KVÓRUM, AZ ÖSSZES, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Lásd: [adatkonzisztencia konfigurálása](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) részleteiről. |Nem. Alapértelmezett érték: az egyik. |

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
Meghatározásához egy mongodb-hez társított szolgáltatást, állítsa be a **típus** a társított szolgáltatás **OnPremisesMongoDB**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kiszolgáló |IP-cím vagy a gazdagép neve a MongoDB-kiszolgáló. |Igen |
| port |A MongoDB-kiszolgáló az ügyfélkapcsolatok figyeléséhez használt TCP-port. |Nem kötelező, csak az alapértelmezett érték: 27017 |
| authenticationType |Alapszintű vagy névtelen. |Igen |
| felhasználónév |Felhasználói fiók MongoDB eléréséhez. |Igen (alapszintű hitelesítés használata esetén). |
| jelszó |A felhasználó jelszava. |Igen (alapszintű hitelesítés használata esetén). |
| authSource |A MongoDB-adatbázis, amely a hitelesítéshez a hitelesítő adatok ellenőrzésére használni kívánt nevét. |Nem kötelező, (ha az alapszintű hitelesítés használata). alapértelmezett: a rendszergazdai fiókkal és -databaseName tulajdonsággal megadott adatbázis használja. |
| databaseName |Az elérni kívánt MongoDB-adatbázis neve. |Igen |
| átjáró neve |Az átjáró, amely hozzáfér az adattár neve. |Igen |
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

További információkért lásd: [mongodb-hez csatlakozó cikk](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Adathalmaz
Egy MongoDB-adatkészletek definiálásához, állítsa be a **típus** , az adatkészlet **MongoDbCollection**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| collectionName |MongoDB-adatbázisban szereplő gyűjtemény neve. |Igen |

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

További információkért lásd: [mongodb-hez csatlakozó cikk](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>MongoDB-forráshoz, a másolási tevékenység
Adatmásolás MongoDB-ből, ha a **forrás típusa** a másolási tevékenység **MongoDbSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-92 lekérdezési karakterláncot. Például: `select * from MyTable`. |Nem (Ha **collectionName** , **adatkészlet** van megadva) |

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

További információkért lásd: [mongodb-hez csatlakozó cikk](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg az Amazon S3, állítsa be a **típus** a társított szolgáltatás **AwsAccessKey**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| accessKeyID |A titkos hozzáférési kulcs azonosítója. |sztring |Igen |
| secretAccessKey |A titkos hívóbetűje magát. |Titkosított titkos karakterlánc |Igen |

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

További információkért lásd: [Amazon S3-összekötő cikk](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Adathalmaz
Az Amazon S3-adatkészletek definiálásához, állítsa be a **típusa** a az adatkészlet **AmazonS3**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| bucketName |Az S3 gyűjtő neve. |Karakterlánc |Igen |
| kulcs |Az S3-objektum kulcsa. |Karakterlánc |Nem |
| előtag |Az S3-objektum kulcs előtag. Ezzel az előtaggal start amelynek kulcsok objektum van kijelölve. Érvényes, csak ha kulcsa üres. |Karakterlánc |Nem |
| version |Ha engedélyezve van a S3 versioning S3-objektum verziója. |Karakterlánc |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem | |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. A támogatott szintek a következők: **Optimális** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem | |


> [!NOTE]
> bucketName + kulcs helyét adja meg az S3-objektum, ahol a gyűjtő S3 objektumok legfelső szintű tárolója, és a kulcs S3-objektum teljes elérési útja.

#### <a name="example-sample-dataset-with-prefix"></a>Példa: Minta adatkészlet előtaggal

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
#### <a name="example-sample-data-set-with-version"></a>Példa: Minta adatkészlet (verzióval)

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

#### <a name="example-dynamic-paths-for-s3"></a>Példa: Az S3 dinamikus útvonalak
A mintában a rögzített értékeit a kulcs és bucketName tulajdonságokat az Amazon S3-adatkészletek a használjuk.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

A Data Factory, például a SliceStart rendszerváltozók használatával számítják ki a kulcsot, és dinamikusan, futásidőben bucketName rendelkezhet.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Az Amazon S3-adatkészletek előtag tulajdonságának azonos teheti meg. Lásd: [Data Factory-függvények és rendszerváltozók](data-factory-functions-variables.md) támogatott funkciók és a változók listáját.

További információkért lásd: [Amazon S3-összekötő cikk](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>A másolási tevékenység rendszer Fájlforrással
Adatok másolása az Amazon S3-ból, állítsa be a **forrás típusa** a másolási tevékenység **FileSystemSource**, és adja meg az alábbi tulajdonságokat az a **forrás** szakasz:


| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a rekurzív |Itt adhatja meg, hogy rekurzív listában S3 objektumok a könyvtárban. |Igaz/hamis |Nem |


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

További információkért lásd: [Amazon S3-összekötő cikk](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Fájlrendszer


### <a name="linked-service"></a>Társított szolgáltatások
Az Azure data factory egy helyszíni fájlrendszer kapcsolat a **helyi fájlkiszolgáló** társított szolgáltatást. Az alábbi táblázat ismerteti az On-Premises File Server társított szolgáltatásának adott JSON-elemek.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |Győződjön meg arról, hogy a type tulajdonság értéke **OnPremisesFileServer**. |Igen |
| gazdagép |Megadja a gyökér elérési útja a másolni kívánt mappa. Használja az escape-karaktert "\" a speciális karakterek a karakterláncban. Lásd: [minta társított szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat. |Igen |
| felhasználói azonosító |Adja meg a felhasználó, aki hozzáfér a server azonosítója. |Nem (Ha úgy dönt, hogy encryptedCredential) |
| jelszó |Adja meg a jelszót a felhasználó (felhasználóazonosító). |Nem (Ha úgy dönt, hogy encryptedCredential |
| encryptedCredential |Adja meg a titkosított hitelesítő adatokat, amelyeket lekérhet a New-AzureRmDataFactoryEncryptValue parancsmag futtatásával. |Nem (Ha úgy dönt, hogy adja meg a felhasználói azonosítót és jelszót a szövegként) |
| átjáró neve |Itt adhatja meg, amelyet a Data Factory a helyszíni fájl-kiszolgálóhoz való csatlakozáshoz használnia kell az átjárója nevére. |Igen |

#### <a name="sample-folder-path-definitions"></a>Mintául szolgáló mappa elérési útja definíciók 
| Forgatókönyv | A társított szolgáltatás definíciójában üzemeltetése | Az adatkészlet-definícióban folderPath |
| --- | --- | --- |
| Helyi mappa adatkezelési átjárót a gépen: <br/><br/>Példák: D:\\ \* vagy D:\folder\subfolder\\* |D:\\ \\ (a Data Management Gateway 2.0-s és újabb verziók) <br/><br/> a localhost (a Data Management Gateway 2.0, mint a korábbi verziók) |. \\ \\ vagy mappa\\\\almappát (a Data Management Gateway 2.0-s és újabb verziók) <br/><br/>D:\\ \\ vagy D:\\\\mappa\\\\almappát (az átjáró 2.0-s verzió) |
| Távoli megosztott mappa: <br/><br/>Példák: \\ \\myserver\\megosztása\\ \* vagy \\ \\myserver\\megosztása\\mappa\\almappa\\* |\\\\\\\\myserver\\\\megosztása |. \\ \\ vagy mappa\\\\almappa |


#### <a name="example-using-username-and-password-in-plain-text"></a>Példa: Egyszerű szöveges felhasználónév és jelszó használatával

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

További információkért lásd: [fájlrendszer-összekötő cikk](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Adathalmaz
Egy fájlrendszer-adatkészletek definiálásához, állítsa be a **típusa** az adatkészlet, **fájlmegosztás**, és adja meg a következő tulajdonságok a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Itt adhatja meg azt a mappát a adatútvonalának. Használja az escape-karaktert "\" a speciális karakterek a karakterláncban. Lásd: [minta társított szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat.<br/><br/>Ennek a tulajdonságnak kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg semmilyen értéket ehhez a tulajdonsághoz, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Fájlnév nincs megadva egy kimeneti adatkészletet, amikor a létrehozott fájl neve a következő formátumban van: <br/><br/>`Data.<Guid>.txt` (Példa: Data.0a405f8a-93ff-4c6f-B3BE-f69616f1df7a.txt) |Nem |
| fileFilter |Adjon meg egy szűrőt használt összes fájlja helyett a folderPath lévő fájlok egy adott sorkészletét jelölik ki. <br/><br/>Engedélyezett értékek a következők: `*` (több karakter) és `?` (egyetlen karakter).<br/><br/>1. példa: "fileFilter": "* .log"<br/>2. példa: "fileFilter": 2016 - 1-?. "txt<br/><br/>Vegye figyelembe, hogy fileFilter a fájlmegosztás a bemeneti adatkészletek vonatkozik. |Nem |
| partitionedBy |PartitionedBy segítségével adja meg a dinamikus folderPath/fileName idősorozat-adatok. Ilyen például az adatok óránkénti paraméteres folderPath. |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**; és a támogatott szintek a következők: **Optimális** és **leggyorsabb**. Lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

> [!NOTE]
> Fájlnév és fileFilter egyidejűleg nem használható.

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

További információkért lásd: [fájlrendszer-összekötő cikk](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>A másolási tevékenység rendszer Fájlforrással
Adatok másolása a fájlrendszer, állítsa be a **forrás típusa** a másolási tevékenység **FileSystemSource**, és adja meg az alábbi tulajdonságokat az a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. |TRUE, False (alapértelmezett) |Nem |

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
További információkért lásd: [fájlrendszer-összekötő cikk](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>A másolási tevékenység fogadó fájlrendszer
Adatok másolása fájlrendszerre, állítsa be a **fogadó típusa** a másolási tevékenység **FileSystemSink**, és adja meg az alábbi tulajdonságokat az a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a copyBehavior |A másolási viselkedés határozza meg, ha a forrás BlobSource vagy fájlrendszer. |**PreserveHierarchy:** Megőrzi a hierarchiája a célmappában. Hogy a relatív elérési útját a forrásfájl, a megadott forrásmappához ugyanaz, mint a célmappában cél fájl relatív elérési útját.<br/><br/>**FlattenHierarchy:** Minden fájl a forrásmappából az első szintű célmappában jönnek létre. A cél fájlt hoz létre egy automatikusan létrehozott névvel.<br/><br/>**MergeFiles:** Egy fájl összes fájlt a forrásmappából egyesíti. A fájl neve és a blob neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. |Nem |
automaticky

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

További információkért lásd: [fájlrendszer-összekötő cikk](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg az FTP, állítsa be a **típus** a társított szolgáltatás **FTP-kiszolgáló**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| gazdagép |Az FTP-kiszolgáló neve vagy IP-címe |Igen |&nbsp; |
| authenticationType |A hitelesítés típusának megadása |Igen |Alapszintű, a névtelen |
| felhasználónév |Az FTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Nem |&nbsp; |
| jelszó |A felhasználó (felhasználónév) jelszava |Nem |&nbsp; |
| encryptedCredential |Az FTP-kiszolgáló eléréséhez használt titkosított hitelesítő adatok |Nem |&nbsp; |
| átjáró neve |Az adatkezelési átjárót szeretne csatlakozni a helyszíni FTP-kiszolgáló neve |Nem |&nbsp; |
| port |A port, amelyet az FTP-kiszolgáló figyel |Nem |21 |
| enableSsl |Adja meg, hogy a TLS/SSL csatornán keresztül FTP használata |Nem |true |
| enableServerCertificateValidation |Adja meg, hogy engedélyezze a kiszolgálói SSL-tanúsítvány hitelesítése a TLS/SSL csatornán keresztül FTP használatával |Nem |true |

#### <a name="example-using-anonymous-authentication"></a>Példa: A névtelen hitelesítés használatával

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

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Példa: Felhasználónév és jelszó használatával egyszerű szöveges az alapszintű hitelesítés

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

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Példa: A hitelesítéshez és az átjáró encryptedCredential használatával

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
Egy FTP-adatkészletek definiálásához, állítsa be a **típusa** a az adatkészlet **fájlmegosztás**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Sub mappa elérési útját. Használja az escape-karaktert "\" a karakterláncban szereplő speciális karakterek. Lásd: [minta társított szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat.<br/><br/>Ennek a tulajdonságnak kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen 
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg semmilyen értéket ehhez a tulajdonsághoz, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha a fájlnév nincs megadva a kimeneti adatkészletek, a létrehozott fájl neve a következő lenne ebben a formátumban: <br/><br/>Adatok. <Guid>.txt (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| fileFilter |Adjon meg egy szűrőt használt összes fájlja helyett a folderPath lévő fájlok egy adott sorkészletét jelölik ki.<br/><br/>Engedélyezett értékek a következők: `*` (több karakter) és `?` (egyetlen karakter).<br/><br/>1. példa: `"fileFilter": "*.log"`<br/>2. példa: `"fileFilter": 2016-1-?.txt"`<br/><br/> a bemeneti adatkészlethez FileShare fileFilter akkor. Ez a tulajdonság a HDFS nem támogatott. |Nem |
| partitionedBy |Adjon meg egy dinamikus folderPath, az idősorozat-adatok filename partitionedBy használható. Ha például folderPath paraméteres az adatok minden óra. |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**; és a támogatott szintek a következők: **Optimális** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli üzemmódot használja. A bináris módú és hamis értéket ASCII igaz. Alapértelmezett érték: Értéke TRUE. Ez a tulajdonság csak akkor használható, ha típusú társított társított szolgáltatás típusa: FTP-kiszolgáló. |Nem |

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

### <a name="file-system-source-in-copy-activity"></a>A másolási tevékenység rendszer Fájlforrással
Adatok másolása az FTP-kiszolgálóról, állítsa be a **forrás típusa** a másolási tevékenység **FileSystemSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákban vagy csak a megadott mappába. |TRUE, False (alapértelmezett) |Nem |

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
Társított szolgáltatás határozza meg a HDFS, állítsa be a **típus** a társított szolgáltatás **Hdfs**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **Hdfs** |Igen |
| URL-cím |A HDFS URL-címe |Igen |
| authenticationType |Névtelen, vagy Windows. <br><br> Használandó **Kerberos-hitelesítés** HDFS-összekötőhöz, tekintse meg [ebben a szakaszban](#use-kerberos-authentication-for-hdfs-connector) , ennek megfelelően állítsa be a helyszíni környezetet. |Igen |
| Felhasználónév |Felhasználónév a Windows-hitelesítés. |Igen (a Windows-hitelesítés) |
| jelszó |Windows-hitelesítés jelszava. |Igen (a Windows-hitelesítés) |
| átjáró neve |Neve az átjáró, amely a Data Factory szolgáltatás csatlakozik a HDFS csatlakoznia kell. |Igen |
| encryptedCredential |[Új AzureRMDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactoryencryptvalue) a hozzáférési hitelesítő adatok kimenetét. |Nem |

#### <a name="example-using-anonymous-authentication"></a>Példa: A névtelen hitelesítés használatával

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

#### <a name="example-using-windows-authentication"></a>Példa: Windows-hitelesítés használatával

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
A HDFS-adatkészletek definiálásához, állítsa be a **típusa** az adatkészlet, **fájlmegosztás**, és adja meg a következő tulajdonságok a **typeProperties** szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |A mappa elérési útját. Például: `myfolder`<br/><br/>Használja az escape-karaktert "\" a karakterláncban szereplő speciális karakterek. Például: folder\subfolder, adja meg a mappa\\\\almappát, és a d:\samplefolder, adja meg a d:\\\\mappába.<br/><br/>Ennek a tulajdonságnak kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg semmilyen értéket ehhez a tulajdonsághoz, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha a fájlnév nincs megadva a kimeneti adatkészletek, a létrehozott fájl neve a következő lenne ebben a formátumban: <br/><br/>Adatok. <Guid>.txt (például:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| partitionedBy |Adjon meg egy dinamikus folderPath, az idősorozat-adatok filename partitionedBy használható. Példa: folderPath paraméteres az adatok minden óra. |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimális** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

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

### <a name="file-system-source-in-copy-activity"></a>A másolási tevékenység rendszer Fájlforrással
Adatokat másolhat HDFS-ből, ha a **forrás típusa** a másolási tevékenység **FileSystemSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakaszban:

**FileSystemSource** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákban vagy csak a megadott mappába. |TRUE, False (alapértelmezett) |Nem |

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
Egy SFTP meghatározásához társított szolgáltatást, állítsa be a **típusa** a társított szolgáltatás **Sftp**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- | --- |
| gazdagép | Az SFTP-kiszolgáló neve vagy IP-címe. |Igen |
| port |A port, amelyen az SFTP-kiszolgáló figyel. Az alapértelmezett érték a következő: 21 |Nem |
| authenticationType |Adja meg a hitelesítés típusát. Megengedett értékek: **Alapszintű**, **SshPublicKey**. <br><br> Tekintse meg [alapszintű hitelesítés használata](#using-basic-authentication) és [használatával SSH nyilvános kulcs alapú hitelesítés](#using-ssh-public-key-authentication) további tulajdonságok és JSON-minták részei. |Igen |
| skipHostKeyValidation | Adja meg, hogy állomáskulcsok ellenőrzésének kihagyása. | Nem. Az alapértelmezett érték: False (hamis) |
| hostKeyFingerprint | Adja meg a gazdagép-kulcs az ujjlenyomat. | Igen, ha a `skipHostKeyValidation` hamis értékre van állítva.  |
| átjáró neve |Az adatkezelési átjárót szeretne csatlakozni egy helyszíni SFTP-kiszolgáló neve. | Igen, ha az adatok másolása helyszíni SFTP-kiszolgálóra. |
| encryptedCredential | Titkosított hitelesítő adatokat az SFTP-kiszolgáló eléréséhez. Automatikusan létrehozott meghatározásakor az egyszerű hitelesítés (felhasználónév és jelszó) vagy SshPublicKey hitelesítési (felhasználónév és titkos kulcs elérési útja vagy tartalom) a másolás varázsló vagy a ClickOnce előugró párbeszédpanelen. | Nem. Csak akkor, ha az adatok másolása helyszíni SFTP-kiszolgálóra vonatkoznak. |

#### <a name="example-using-basic-authentication"></a>Példa: Alapszintű hitelesítés használata

Alapszintű hitelesítés használatához állítsa `authenticationType` , `Basic`, és adja meg az SFTP-összekötővel az előző szakaszban bemutatott általános eszközök mellett a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- | --- |
| felhasználónév | SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó. |Igen |
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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Példa: **Alapszintű hitelesítés, a titkosított hitelesítő adatokat**

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

Alapszintű hitelesítés használatához állítsa `authenticationType` , `SshPublicKey`, és adja meg az SFTP-összekötővel az előző szakaszban bemutatott általános eszközök mellett a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- | --- |
| felhasználónév |SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Igen |
| privateKeyPath | Adja meg, hogy az átjáró hozzáférhet a titkos kulcs fájlját abszolút elérési útját. | Adja meg a `privateKeyPath` vagy `privateKeyContent`. <br><br> Csak akkor, ha az adatok másolása helyszíni SFTP-kiszolgálóra vonatkoznak. |
| privateKeyContent | A titkos kulcs tartalmát, szerializált karakterlánc. A másolás varázsló olvashatja a titkos kulcs fájlját, és csomagolja ki automatikusan a titkos kulcs tartalmát. Ha bármilyen más eszköz/SDK-t használ, használja a privateKeyPath tulajdonságot. | Adja meg a `privateKeyPath` vagy `privateKeyContent`. |
| hozzáférési kód | Adja meg a pass kifejezés/jelszót a titkos kulcs visszafejtésére, ha a kulcs fájlját egy hozzáférési kódot védi. | Igen, ha a titkos kulcs fájlját egy hozzáférési kódot védi. |

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Példa: **Titkos kulcs tartalmát SshPublicKey hitelesítéshez**

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

További információkért lásd: [SFTP-összekötővel](data-factory-sftp-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Egy SFTP-adatkészletek definiálásához, állítsa be a **típusa** a az adatkészlet **fájlmegosztás**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Sub mappa elérési útját. Használja az escape-karaktert "\" a karakterláncban szereplő speciális karakterek. Lásd: [minta társított szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat.<br/><br/>Ennek a tulajdonságnak kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg semmilyen értéket ehhez a tulajdonsághoz, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha a fájlnév nincs megadva a kimeneti adatkészletek, a létrehozott fájl neve a következő lenne ebben a formátumban: <br/><br/>Adatok. <Guid>.txt (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| fileFilter |Adjon meg egy szűrőt használt összes fájlja helyett a folderPath lévő fájlok egy adott sorkészletét jelölik ki.<br/><br/>Engedélyezett értékek a következők: `*` (több karakter) és `?` (egyetlen karakter).<br/><br/>1. példa: `"fileFilter": "*.log"`<br/>2. példa: `"fileFilter": 2016-1-?.txt"`<br/><br/> a bemeneti adatkészlethez FileShare fileFilter akkor. Ez a tulajdonság a HDFS nem támogatott. |Nem |
| partitionedBy |Adjon meg egy dinamikus folderPath, az idősorozat-adatok filename partitionedBy használható. Ha például folderPath paraméteres az adatok minden óra. |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimális** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli üzemmódot használja. A bináris módú és hamis értéket ASCII igaz. Alapértelmezett érték: Értéke TRUE. Ez a tulajdonság csak akkor használható, ha típusú társított társított szolgáltatás típusa: FTP-kiszolgáló. |Nem |

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

További információkért lásd: [SFTP-összekötővel](data-factory-sftp-connector.md#dataset-properties) cikk. 

### <a name="file-system-source-in-copy-activity"></a>A másolási tevékenység rendszer Fájlforrással
SFTP forrásból másol adatokat, ha a **forrás típusa** a másolási tevékenység **FileSystemSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákban vagy csak a megadott mappába. |TRUE, False (alapértelmezett) |Nem |



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

További információkért lásd: [SFTP-összekötővel](data-factory-sftp-connector.md#copy-activity-properties) cikk.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg a HTTP, állítsa be a **típus** a társított szolgáltatás **Http**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| url | A webkiszolgáló kiindulási URL-cím | Igen |
| authenticationType | A hitelesítési típust határoz meg. Engedélyezett értékek a következők: **Névtelen**, **alapszintű**, **kivonatoló**, **Windows**, **ClientCertificate**. <br><br> Tekintse meg a további tulajdonságok és hitelesítési típusokhoz JSON-minták a táblázat alatti részek jelölik. | Igen |
| enableServerCertificateValidation | Adja meg, hogy engedélyezze a kiszolgálói SSL-tanúsítvány hitelesítése, ha a forrása a HTTPS-webkiszolgáló | Nem, alapértelmezett érték az IGAZ |
| átjáró neve | Az adatkezelési átjárót szeretne csatlakozni egy helyszíni HTTP-forrás neve. | Igen, ha adatokat másol egy helyszíni HTTP-forrás. |
| encryptedCredential | Titkosított hitelesítő adatokat a HTTP-végpontot. Automatikusan generált hitelesítési adatainak konfigurálásakor másolás varázsló vagy a ClickOnce előugró párbeszédpanelen. | Nem. Csak akkor, ha az adatok másolása helyszíni HTTP-kiszolgáló vonatkoznak. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Példa: Alapszintű, kivonatoló vagy Windows-hitelesítés használatával
Állítsa be `authenticationType` , `Basic`, `Digest`, vagy `Windows`, és adja meg a HTTP-összekötő általános azokat a fent bemutatott mellett a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| felhasználónév | A felhasználónév a HTTP-végpontot. | Igen |
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

Alapszintű hitelesítés használatához állítsa `authenticationType` , `ClientCertificate`, és adja meg a HTTP-összekötő általános azokat a fent bemutatott mellett a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| embeddedCertData | Bináris adatok a személyes információcsere (PFX) fájl Base64-kódolású tartalmát. | Adja meg a `embeddedCertData` vagy `certThumbprint`. |
| certThumbprint | Az átjáró számítógépre tanúsítványtár telepített tanúsítvány ujjlenyomatával. Csak akkor, amikor adatokat másol egy helyszíni HTTP-forrás vonatkoznak. | Adja meg a `embeddedCertData` vagy `certThumbprint`. |
| jelszó | A tanúsítványhoz tartozó jelszót. | Nem |

Ha `certThumbprint` hitelesítést és a tanúsítvány a helyi számítógép személyes tárolójában van telepítve, az átjáró szolgáltatás az olvasási engedélyeket kell:

1. Indítsa el a Microsoft Management Console (MMC). Adja hozzá a **tanúsítványok** beépülő modul, amely célozza meg benne a **helyi számítógép**.
2. Bontsa ki a **tanúsítványok**, **személyes**, és kattintson a **tanúsítványok**.
3. Kattintson a jobb gombbal a tanúsítványt a személyes, és válassza ki **feladatok**->**titkos kulcsok kezelése...**
3. Az a **biztonsági** lapon maradva adja hozzá a felhasználói fiók, amelyben Data Management Gateway gazdagép szolgáltatás fut, az olvasási hozzáférés a tanúsítványt.  

**Példa: az ügyféltanúsítványt használja:** A társított szolgáltatás társítja az adat-előállító egy helyszíni HTTP-webkiszolgáló. Az adatkezelési átjáró telepítve van a gépen telepített ügyfél-tanúsítványt használ.

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

#### <a name="example-using-client-certificate-in-a-file"></a>Példa: az ügyféltanúsítványt használja, egy fájlban
A társított szolgáltatás társítja az adat-előállító egy helyszíni HTTP-webkiszolgáló. Egy ügyfél tanúsítványfájlt a gépen telepített adatkezelési átjáró használja.

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

További információkért lásd: [HTTP-összekötő](data-factory-http-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Egy HTTP-adatkészletek definiálásához, állítsa be a **típus** , az adatkészlet **Http**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| relativeurl tulajdonságok közül | Az erőforrás, amely tartalmazza az adatok relatív URL-CÍMÉT. Ha nincs megadva elérési út, csak az URL-címet a társított szolgáltatás definíciójában megadott szolgál. <br><br> Dinamikus URL-cím létrehozásához, használhatja a [Data Factory-függvények és rendszerváltozók](data-factory-functions-variables.md), például: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Nem |
| requestMethod | HTTP-metódust. Engedélyezett értékek a következők **első** vagy **POST**. | Nem. Az alapértelmezett szint a `GET`. |
| additionalHeaders | További HTTP-kérelemfejlécek. | Nem |
| RequestBody | HTTP-kérelem törzse. | Nem |
| Formátum | Ha szeretné egyszerűen **lekérik az adatokat, HTTP-végpont-van** nélkül, elemzés, hagyja ki a fájlformátum beállításai. <br><br> Ha meg szeretné elemezni a HTTP-válasz tartalma másolása során, a következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimális** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

#### <a name="example-using-the-get-default-method"></a>Példa: a metódussal a GET (alapértelmezett)

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
További információkért lásd: [HTTP-összekötő](data-factory-http-connector.md#dataset-properties) cikk.

### <a name="http-source-in-copy-activity"></a>A másolási tevékenység HTTP-forrás
Egy HTTP-forrásból másol adatokat, ha a **forrás típusa** a másolási tevékenység **HttpSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| httpRequestTimeout | Időtúllépés (időtartam) válaszol a HTTP-kérés. Az időkorlát kapott választ, nem választ adatokat olvasni az időkorlátot. | Nem. Alapértelmezett érték: 00:01:40 |


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

További információkért lásd: [HTTP-összekötő](data-factory-http-connector.md#copy-activity-properties) cikk.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg az OData, állítsa be a **típus** a társított szolgáltatás **OData**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| url |Az OData-szolgáltatás URL-címe. |Igen |
| authenticationType |Az OData-forráshoz való kapcsolódáshoz használt hitelesítés típusa. <br/><br/> Felhőalapú OData a lehetséges értékek: névtelen, alapszintű és OAuth (Megjegyzés: az Azure Data Factory jelenleg csak támogatás az Azure Active Directory-alapú OAuth). <br/><br/> A helyszíni OData a lehetséges értékek: névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. |Igen (csak akkor, ha az egyszerű hitelesítés használata esetén) |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Igen (csak akkor, ha az egyszerű hitelesítés használata esetén) |
| authorizedCredential |Ha OAuth használ, kattintson a **engedélyezés** gombra a Data Factory Copy varázslót vagy a szerkesztőben, és adja meg a hitelesítő adatok a tulajdonság értéke lesz automatikusan létrehozott. |Igen (csak akkor, ha OAuth-hitelesítés használata esetén) |
| átjáró neve |Az átjáró, amely a Data Factory szolgáltatás segítségével csatlakozhat a helyszíni OData-szolgáltatás neve. Csak adja meg, ha a helyszíni OData forrásból származó adatokat másolhat. |Nem |

#### <a name="example---using-basic-authentication"></a>Példa – egyszerű hitelesítés használatával
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

#### <a name="example---using-anonymous-authentication"></a>Például: a névtelen hitelesítés használatával

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

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Példa – használatával Windows authentication fér hozzá a helyszíni OData-erőforrás

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

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Példa – felhőalapú OData-erőforrás eléréséhez az OAuth-hitelesítés használatával
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

További információkért lásd: [OData-összekötő](data-factory-odata-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adathalmaz
Egy OData-adatkészletek definiálásához, állítsa be a **típus** , az adatkészlet **ODataResource**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

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

További információkért lásd: [OData-összekötő](data-factory-odata-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs forrásként
OData forrásból másol adatokat, ha a **forrás típusa** a másolási tevékenység **RelationalSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Példa | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |"? $select neve, leírása és $top = = 5" |Nem |

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

További információkért lásd: [OData-összekötő](data-factory-odata-connector.md#copy-activity-properties) cikk.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg az ODBC, állítsa be a **típusa** a társított szolgáltatás **OnPremisesOdbc**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakasz:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kapcsolati Sztringje |A kapcsolati karakterláncot, és a egy nem kötelező titkosított hitelesítő adatokat nem eléréséhez szükséges hitelesítő adatokat része. Lásd az alábbi szakaszokban található példákat. |Igen |
| hitelesítő adat |A hozzáférési hitelesítő adatok része, a kapcsolati karakterláncot a megadott illesztőprogram-specifikus tulajdonság-érték formátuma. Példa: "Uid =<user ID>; Pwd =<password>; RefreshToken =<secret refresh token>; ". |Nem |
| authenticationType |Az ODBC-adattárban való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen és alapszintű. |Igen |
| felhasználónév |Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjáró neve |Az átjáró, amely a Data Factory szolgáltatás használatával kell kapcsolódni az ODBC-adattár neve. |Igen |

#### <a name="example---using-basic-authentication"></a>Példa – egyszerű hitelesítés használatával

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
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Példa – alapszintű hitelesítést használ, a titkosított hitelesítő adatokkal
A hitelesítő adatok használatával titkosíthatók a [New-AzureRMDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactoryencryptvalue) (az Azure PowerShell 1.0-ás verziójú) parancsmaggal vagy [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9-es vagy korábbi verzióját a következő, Azure (PowerShell).  

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

#### <a name="example-using-anonymous-authentication"></a>Példa: A névtelen hitelesítés használatával

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

További információkért lásd: [ODBC-összekötő](data-factory-odbc-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Egy ODBC-adatkészletek definiálásához, állítsa be a **típus** , az adatkészlet **RelationalTable**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla az ODBC-adattár neve. |Igen |


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

További információkért lásd: [ODBC-összekötő](data-factory-odbc-connector.md#dataset-properties) cikk. 

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs forrásként
Adatokat másolhat egy ODBC adatokat az adattárból, ha a **forrás típusa** a másolási tevékenység **RelationalSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakasz :

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: `select * from MyTable`. |Igen |

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

További információkért lásd: [ODBC-összekötő](data-factory-odbc-connector.md#copy-activity-properties) cikk.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg a Salesforce-ban, és állítsa a **típus** a társított szolgáltatás **Salesforce**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| environmentUrl | Adja meg az URL-címet a Salesforce-példány. <br><br> – Alapértelmezett érték a "https://login.salesforce.com". <br> – Adatok másolása a tesztkörnyezetből, adja meg a "https://test.salesforce.com". <br> – Adatok másolása az egyéni tartományt, adja meg, például "https://[domain].my.salesforce.com". |Nem |
| felhasználónév |Adja meg a felhasználói fiók felhasználói nevét. |Igen |
| jelszó |Adja meg a felhasználói fiókhoz tartozó jelszót. |Igen |
| securityToken |Adja meg a felhasználói fiók biztonsági jogkivonatot. Lásd: [biztonsági jogkivonatának beszerzéséhez](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) alaphelyzetbe állítása/lekérése a biztonsági jogkivonat való létrehozásával kapcsolatos útmutatást. Az általános biztonsági jogkivonatokat kapcsolatos további információkért lásd: [biztonsági és API-val](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Igen |

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

További információkért lásd: [Salesforce-összekötő](data-factory-salesforce-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Egy Salesforce-adatkészletek definiálásához, állítsa be a **típusa** a az adatkészlet **RelationalTable**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A Salesforce-ban a tábla neve. |Nem (Ha egy **lekérdezés** , **RelationalSource** van megadva) |

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

További információkért lásd: [Salesforce-összekötő](data-factory-salesforce-connector.md#dataset-properties) cikk. 

### <a name="relational-source-in-copy-activity"></a>A másolási tevékenység relációs forrásként
Adatok másolása a Salesforce-ból, állítsa be a **forrás típusa** a másolási tevékenység **RelationalSource**, és adja meg az alábbi tulajdonságokat a **forrás** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |Egy SQL-92 lekérdezés vagy [Salesforce objektum Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezés. Például: `select * from MyTable__c`. |Nem (Ha a **tableName** , a **adatkészlet** van megadva) |

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
> Az API neve "__c" részét egyéni objektumokra van szükség.

További információkért lásd: [Salesforce-összekötő](data-factory-salesforce-connector.md#copy-activity-properties) cikk. 

## <a name="web-data"></a>Webes adatok 

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg a webes, állítsa be a **típus** a társított szolgáltatás **webes**, és adja meg az alábbi tulajdonságokat a **typeProperties** szakaszban:  

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

További információkért lásd: [Webtábla összekötő](data-factory-web-table-connector.md#linked-service-properties) cikk. 

### <a name="dataset"></a>Adathalmaz
Webes adatkészletet adja meg, állítsa a **típusa** a az adatkészlet **Webtábla**, és adja meg a következő tulajdonságok a **typeProperties** szakaszban: 

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |Az adatkészlet típusa. meg kell **Webtábla** |Igen |
| elérési út |Az erőforrás, amely tartalmazza a tábla relatív URL-CÍMÉT. |Nem. Ha nincs megadva elérési út, csak az URL-címet a társított szolgáltatás definíciójában megadott szolgál. |
| index |Az erőforrás a tábla indexe. Lásd: [egy tábla egy HTML-oldalt a Get-index](#get-index-of-a-table-in-an-html-page) szakasz lépéseit egy tábla indexének első HTML-lapon. |Igen |

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

További információkért lásd: [Webtábla összekötő](data-factory-web-table-connector.md#dataset-properties) cikk. 

### <a name="web-source-in-copy-activity"></a>A másolási tevékenység webes forrás
Ha egy webes táblából másolt adatokat, állítsa be a **forrás típusa** a másolási tevékenység **WebSource**. Ha a másolási tevékenység a forrás jelenleg típusú **WebSource**, nincs további tulajdonságok támogatottak.

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

További információkért lásd: [Webtábla összekötő](data-factory-web-table-connector.md#copy-activity-properties) cikk. 

## <a name="compute-environments"></a>SZÁMÍTÁSI KÖRNYEZETEK
Az alábbi táblázat a Data Factory és a rajtuk futó átalakítási tevékenységeket által támogatott számítási környezetek. Kattintson a hivatkozásra a számítási erőforrások is érdeklik a társított szolgáltatást, hogy egy adat-előállítóhoz tartozó JSON-sémáinak megtekintéséhez. 

| Számítási környezet | Tevékenységek |
| --- | --- |
| [Igény szerinti HDInsight-fürt](#on-demand-azure-hdinsight-cluster) vagy [a saját HDInsight-fürt](#existing-azure-hdinsight-cluster) |[.NET egyéni tevékenység](#net-custom-activity), [Hive-tevékenység](#hdinsight-hive-activity), [Pig-tevékenység alapú](#hdinsight-pig-activity), [MapReduce-tevékenység](#hdinsight-mapreduce-activity), [Hadoop-tartalomközvetítő tevékenység](#hdinsight-streaming-activityd), [Spark-tevékenység](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET egyéni tevékenység](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Machine Learning kötegelt végrehajtási tevékenység](#machine-learning-batch-execution-activity), [a Machine Learning Update-Erőforrástevékenység](#machine-learning-update-resource-activity) |
| [Az Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Az Azure SQL Database](#azure-sql-database-1), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-1), [az SQL Server](#sql-server-1) |[Tárolt eljárás](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Igény szerinti Azure HDInsight-fürt
Az Azure Data Factory szolgáltatás automatikusan létrehozhat egy Windows/Linux-alapú igény szerinti HDInsight-fürt adatok feldolgozásához. A fürt a tárfiókjával (linkedServiceName tulajdonságot a JSON-) a fürthöz társított ugyanabban a régióban jön létre. A következő átalakítási tevékenységeket futtathatja ezt a társított szolgáltatást: [.NET egyéni tevékenység](#net-custom-activity), [Hive-tevékenység](#hdinsight-hive-activity), [Pig-tevékenység alapú](#hdinsight-pig-activity), [MapReduce tevékenység](#hdinsight-mapreduce-activity), [Hadoop-tartalomközvetítő tevékenység](#hdinsight-streaming-activityd), [Spark-tevékenység](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Társított szolgáltatások 
Az alábbi táblázat ismerteti az Azure JSON egy igény szerinti HDInsight társított szolgáltatás definíciójában használt tulajdonságokat.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell megadni **HDInsightOnDemand**. |Igen |
| clusterSize |A fürt feldolgozó-és adatcsomópontok száma. A HDInsight-fürt 2 fő csomóponttal, ez a tulajdonság adja meg a munkavégző csomópontok számával együtt jön létre. A csomópontok mérete 4 mag, így egy 4 feldolgozó csomópontot tartalmazó fürtben 24 mag szükséges rendelkező Standard_D3 vannak (4\*4 = 16 mag, a feldolgozó csomópontokat, valamint 2\*4 = 8 mag fő csomópontok esetében). Lásd: [Linux-alapú Hadoop-fürtök a HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) a D3 csomag részleteit. |Igen |
| az élettartam |A megengedett üresjárati idő az igény szerinti HDInsight-fürt számára. Itt adhatja meg, mennyi ideig az igény szerinti HDInsight-fürt aktív marad egy tevékenység fut, ha nincsenek a fürt más aktív feladatok befejezése után.<br/><br/>Például ha egy tevékenység futtatása 6 percig tart, és az élettartam értéke 5 perc, a fürt marad, a figyelő életben 5 perc, a 6 percnek feldolgozása a tevékenység futtatása után. Ha egy másik tevékenység-végrehajtásonként 6 percig időkeretet, dolgoz fel ugyanazon a fürtön.<br/><br/>Egy igény szerinti HDInsight-fürt létrehozása egy (eltarthat egy ideig) drága művelet, így használja ezt a beállítást a szükséges adat-előállító egy igény szerinti HDInsight-fürt újrafelhasználásával teljesítményét.<br/><br/>Ha timetolive az érték 0, a fürt, amint az a tevékenység futtatása a feldolgozott törlődik. Másrészről Ha a magas érték, a fürt felfüggesztheti üresjárati feleslegesen magas költségeket eredményez. Ezért fontos, hogy beállította-e a megfelelő értéket a saját igényei szerint.<br/><br/>Több folyamatot is megosztása a az igény szerinti HDInsight-fürt ugyanazon a timetolive tulajdonság értéke megfelelően van beállítva. |Igen |
| version |A HDInsight-fürt verzióját. További információkért lásd: [támogatott a HDInsight-verziók az Azure Data Factoryban](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Nem |
| linkedServiceName |Az Azure Storage társított szolgáltatás, tárolására és az adatok feldolgozása az igény szerinti fürt használni. <p>Jelenleg nem hozható létre egy igény szerinti HDInsight-fürtöt, amely egy Azure Data Lake Store a tárolót használja. A feldolgozás alatt egy Azure Data Lake Store HDInsight eredmény adatokat tárolni szeretné, ha a másolási tevékenység használatával másolja az adatokat az Azure Blob Storage-ból az Azure Data Lake Store.</p>  | Igen |
| additionalLinkedServiceNames |Itt adhatja meg, további tárfiókok esetében a HDInsight társított szolgáltatás, így a Data Factory szolgáltatás segítségével regisztrálja őket az Ön nevében. |Nem |
| osType |Operációs rendszer típusát. Engedélyezett értékek a következők: (Alapértelmezett) Windows és Linux |Nem |
| hcatalogLinkedServiceName |A név az Azure SQL társított szolgáltatás, amely a HCatalog adatbázis mutasson. Az igény szerinti HDInsight-fürtöt az Azure SQL database használatával, mint a metaadattár jön létre. |Nem |

### <a name="json-example"></a>Példa JSON
A következő JSON egy Linux-alapú igény szerinti HDInsight társított szolgáltatás határozza meg. A Data Factory szolgáltatás automatikusan létrehoz egy **Linux-alapú** HDInsight-fürt adatszelet feldolgozásakor. 

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

További információkért lásd: [társított szolgáltatások számítása](data-factory-compute-linked-services.md) cikk. 

## <a name="existing-azure-hdinsight-cluster"></a>Meglévő Azure HDInsight-fürt
Létrehozhat egy Azure HDInsight társított szolgáltatás regisztrálni a saját HDInsight-fürt a Data Factory. A következő adat-átalakítási tevékenységeket futtathatja ezt a társított szolgáltatást: [.NET egyéni tevékenység](#net-custom-activity), [Hive-tevékenység](#hdinsight-hive-activity), [Pig-tevékenység alapú](#hdinsight-pig-activity), [ MapReduce-tevékenység](#hdinsight-mapreduce-activity), [Hadoop-tartalomközvetítő tevékenység](#hdinsight-streaming-activityd), [Spark-tevékenység](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Társított szolgáltatások
Az alábbi táblázat ismerteti az Azure JSON-definíciót egy Azure HDInsight társított szolgáltatás tulajdonságait.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell megadni **HDInsight**. |Igen |
| clusterUri |A HDInsight-fürt URI azonosítója. |Igen |
| felhasználónév |Adja meg a felhasználó egy meglévő HDInsight-fürthöz való kapcsolódáshoz használt nevét. |Igen |
| jelszó |Adja meg a felhasználói fiókhoz tartozó jelszót. |Igen |
| linkedServiceName | Az Azure blob storage a HDInsight-fürt által használt az Azure Storage társított szolgáltatás neve. <p>Jelenleg nem adhat meg egy Azure Data Lake Store-beli társított szolgáltatást ehhez a tulajdonsághoz. Ha a HDInsight-fürt hozzáfér ehhez a Data Lake Store az Azure Data Lake Store az adatok előfordulhat, hogy elérhető Hive és Pig-parancsfájlok. </p>  |Igen |

HDInsight-fürtök támogatott verzióiért lásd: [támogatott HDInsight-verziók](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). 

#### <a name="json-example"></a>Példa JSON

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
Létrehozhat egy adat-előállítót egy társított Azure Batch szolgáltatás regisztrálja a Batch-készlet, a virtuális gépek (VM). .NET egyéni tevékenység Azure Batch vagy az Azure HDInsight segítségével is futtathatja. Futtathat egy [.NET egyéni tevékenység](#net-custom-activity) a társított szolgáltatás. 

### <a name="linked-service"></a>Társított szolgáltatások
Az alábbi táblázat ismerteti az Azure JSON-definícióját egy társított Azure Batch szolgáltatás tulajdonságait.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell megadni **AzureBatch**. |Igen |
| Fióknév |Az Azure Batch-fiók nevére. |Igen |
| accessKey |Az Azure Batch-fiók hozzáférési kulcsa. |Igen |
| poolName |A virtuálisgép-készlet neve. |Igen |
| linkedServiceName |Neve az Azure Storage társított szolgáltatást, a társított Azure Batch szolgáltatáshoz társított. Ezt a társított szolgáltatást a tevékenység és a tevékenység végrehajtási naplók tárolásához futtatásához szükséges átmeneti fájlok szolgál. |Igen |


#### <a name="json-example"></a>Példa JSON

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
Létrehoz egy társított Azure Machine Learning szolgáltatást a Machine Learning kötegelt pontozási végpontjához az adat-előállító regisztrálni. Két az Adatátalakítási tevékenységeket futtathatja ezt a társított szolgáltatást: [Machine Learning kötegelt végrehajtási tevékenység](#machine-learning-batch-execution-activity), [a Machine Learning Update-Erőforrástevékenység](#machine-learning-update-resource-activity). 

### <a name="linked-service"></a>Társított szolgáltatások
Az alábbi táblázat ismerteti az Azure JSON-definícióját egy társított Azure Machine Learning szolgáltatás tulajdonságait.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| Típus |A type tulajdonságot kell beállítani: **Az AzureML**. |Igen |
| mlEndpoint |A kötegelt pontozás URL-CÍMÉT. |Igen |
| apiKey |A közzétett munkaterület-modell API-t. |Igen |

#### <a name="json-example"></a>Példa JSON

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
Létrehoz egy **Azure Data Lake Analytics** társított szolgáltatást, az Azure Data Lake Analytics számítási szolgáltatás az Azure data factory használata előtt a [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md) egy folyamatban.

### <a name="linked-service"></a>Társított szolgáltatások

Az alábbi táblázat ismerteti az Azure Data Lake Analytics hivatkozott szolgáltatást a JSON-definíciójában használt tulajdonságok. 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| Típus |A type tulajdonságot kell beállítani: **AzureDataLakeAnalytics**. |Igen |
| Fióknév |Az Azure Data Lake Analytics-fiók neve. |Igen |
| dataLakeAnalyticsUri |Az Azure Data Lake Analytics URI. |Nem |
| Engedélyezési |Kattintás után automatikusan lekéri a hozzáférési kód **engedélyezés** gombra a Data Factory szerkesztőjében, és az OAuth-bejelentkezés befejezése. |Igen |
| subscriptionId |Azure-előfizetés azonosítója |Nem (Ha nincs megadva, a data Factory előfizetés szerepel). |
| resourceGroupName |Azure-erőforráscsoport neve |Nem (Ha nincs megadva, a data Factory erőforrás-csoport szerepel). |
| sessionId |munkamenet-azonosító az OAuth hitelesítési munkamenetből. Minden munkamenet-azonosító egyedi, és előfordulhat, hogy csak egyszer használható. Ha a Data Factory Editor eszközét használja, ezt az Azonosítót jön létre automatikusan. |Igen |


#### <a name="json-example"></a>Példa JSON
Az alábbi példa JSON-definíciót egy Azure Data Lake Analytics hivatkozott szolgáltatást biztosít.

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
Hozzon létre egy Azure SQL társított szolgáltatás, és együtt használja, a [tárolt eljárási tevékenység](#stored-procedure-activity) egy tárolt eljárást a Data Factory-folyamatok meghívásához. 

### <a name="linked-service"></a>Társított szolgáltatások
Adja meg az Azure SQL Database társított szolgáltatás, és állítsa a **típus** a társított szolgáltatás **AzureSqlDatabase**, és adja meg az alábbi tulajdonságokat a **typeProperties** a szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kapcsolati Sztringje |Adja meg a connectionString tulajdonság az Azure SQL Database-példányhoz való kapcsolódáshoz szükséges adatokat. |Igen |

#### <a name="json-example"></a>Példa JSON

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

Lásd: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#linked-service-properties) részleteivel kapcsolatos ezt a társított szolgáltatást.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Hozzon létre egy Azure SQL Data Warehouse társított szolgáltatást, és együtt használja, a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) egy tárolt eljárást a Data Factory-folyamatok meghívásához. 

### <a name="linked-service"></a>Társított szolgáltatások
Társított szolgáltatás határozza meg az Azure SQL Data Warehouse, állítsa be a **típus** a társított szolgáltatás **AzureSqlDW**, és adja meg az alábbi tulajdonságokat a **typeProperties** a szakaszban:  

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kapcsolati Sztringje |Adja meg a connectionString tulajdonság az Azure SQL Data Warehouse-példányhoz való kapcsolódáshoz szükséges adatokat. |Igen |

#### <a name="json-example"></a>Példa JSON

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
Az SQL Server-alapú társított szolgáltatás létrehozása, és együtt használja, a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) egy tárolt eljárást a Data Factory-folyamatok meghívásához. 

### <a name="linked-service"></a>Társított szolgáltatások
Létrehoz egy társított szolgáltatást típusú **OnPremisesSqlServer** egy helyszíni SQL Server-adatbázis összekapcsolása a data factoryt. A következő táblázat a JSON-elemeket a helyszíni SQL Server-alapú társított szolgáltatás leírását.

Az alábbi táblázatban az adott SQL Server-alapú társított szolgáltatás JSON-elemek leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **OnPremisesSqlServer**. |Igen |
| kapcsolati Sztringje |Adja meg a connectionString adatokat a helyszíni SQL Server-adatbázis SQL-hitelesítés vagy a Windows-hitelesítés használatával való kapcsolódáshoz szükséges. |Igen |
| átjáró neve |Az átjáró által a Data Factory szolgáltatás a helyszíni SQL Server adatbázishoz való csatlakozáshoz használandó neve. |Igen |
| felhasználónév |Ha Windows-hitelesítést használ, adja meg a felhasználónevet. Példa: **domainname\\felhasználónév**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |

A hitelesítő adatok titkosíthatók a **New-AzureRmDataFactoryEncryptValue** parancsmag és a kapcsolati karakterláncot használja őket az alábbi példában látható módon (**EncryptedCredential** tulajdonság):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Példa: JSON-t az SQL-hitelesítés használata

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
#### <a name="example-json-for-using-windows-authentication"></a>Példa: JSON-t a Windows-hitelesítés használatával

Ha a felhasználónév és jelszó meg van adva, átjáró használja ezeket a megszemélyesíteni a megadott felhasználói fióknak a helyi SQL Server adatbázishoz való csatlakozáshoz. Átjáró ellenkező esetben az SQL Server közvetlenül a biztonsági környezet (saját indítófiókjának) átjáró csatlakozik.

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

## <a name="data-transformation-activities"></a>ADATÁTALAKÍTÁSI TEVÉKENYSÉGEK

Tevékenység | Leírás
-------- | -----------
[HDInsight Hive-tevékenység](#hdinsight-hive-activity) | A HDInsight Hive tevékenység, a Data Factory-folyamatok futtatják a Hive-lekérdezések saját maga, vagy igény szerinti Windows/Linux-alapú HDInsight-fürt. 
[HDInsight Pig-tevékenység](#hdinsight-pig-activity) | A HDInsight Pig-tevékenység, a Data Factory-folyamatok Pig-lekérdezéseket a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre.
[HDInsight MapReduce-tevékenység](#hdinsight-mapreduce-activity) | A HDInsight MapReduce-tevékenység, a Data Factory-folyamatok MapReduce-programok saját maga, vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre.
[HDInsight Streaming-tevékenység](#hdinsight-streaming-activity) | A HDInsight Streaming-tevékenység a Data Factory-folyamatok Hadoop Streamelési programok saját maga vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre.
[HDInsight Spark-tevékenység](#hdinsight-spark-activity) | A HDInsight Spark-tevékenység, Data Factory-folyamatok a Spark-programok saját HDInsight-fürtön hajtja végre. 
[Machine Learning kötegelt végrehajtási tevékenység](#machine-learning-batch-execution-activity) | Az Azure Data Factory lehetővé teszi, hogy egyszerűen hozzon létre egy közzétett Azure Machine Learning webszolgáltatás prediktív elemzési használó folyamatok. A kötegelt végrehajtási tevékenység segítségével az Azure Data Factory-folyamatot, hívhat egy Machine Learning webszolgáltatás, hogy előrejelzéseket végezzen az adatokon, a Batch szolgáltatásban. 
[Machine Learning Update-erőforrástevékenység](#machine-learning-update-resource-activity) | Az idő múlásával a prediktív modelleket a Machine Learning pontozási kísérletek kell kell retrained új bemeneti adatkészletek használatával. Miután elkészült, az átképezési, frissítse a pontozási webszolgáltatás retrained Machine Learning-modellhez szeretne. A frissítés Erőforrástevékenység használatával frissítse a web service a újonnan betanított modell.
[Tárolt eljárási tevékenység](#stored-procedure-activity) | A tárolt eljárási tevékenység használhatja a Data Factory-folyamat a következő adattárakat egyikét a tárolt eljárás meghívása: Az Azure SQL Database, Azure SQL Data Warehouse, SQL Server-adatbázis a vállalati vagy egy Azure virtuális Gépen. 
[Data Lake Analytics U-SQL-tevékenység](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL-tevékenység egy U-SQL-szkriptet futtat egy Azure Data Lake Analytics-fürt.  
[.NET egyéni tevékenység](#net-custom-activity) | Adatok átalakítása a Data Factory által nem támogatott módon van szüksége, ha egyéni tevékenységek létrehozása a saját adatok feldolgozási logikáját, és használja a tevékenységet a folyamat. Konfigurálhatja úgy az egyéni .NET-tevékenységet egy Azure Batch szolgáltatás vagy egy Azure HDInsight-fürt futtatásához. 

     
## <a name="hdinsight-hive-activity"></a>HDInsight Hive-tevékenység
A következő tulajdonságokat is megadhat egy Hive-tevékenység JSON-definíciójában. A type tulajdonság a tevékenységhez kell lennie: **HDInsightHive**. Először hozzon létre egy HDInsight társított szolgáltatást kell és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** HDInsightHive tevékenység típusa beállításakor. szakasz:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| parancsfájl |Adja meg a Hive-parancsfájl beágyazott |Nem |
| parancsprogram elérési útja |Store a Hive-szkript egy Azure blob Storage, és adja meg a fájl elérési útját. Használja a "script" vagy "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájlnév formátuma a kis-és nagybetűket. |Nem |
| határozza meg |Adja meg a paramétereket a kulcs/érték párokként való belül a Hive-parancsfájl használatával "hiveconf: |Nem |

Ezek a Tulajdonságok típusa csak a Hive-tevékenység vonatkoznak. Egyéb tulajdonságok (kívül a typeProperties szakaszon) minden tevékenység esetén támogatottak.   

### <a name="json-example"></a>Példa JSON
A következő JSON HDInsight Hive-tevékenységet a folyamat határozza meg.  

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

További információkért lásd: [Hive-tevékenység](data-factory-hive-activity.md) cikk. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-tevékenység
A következő tulajdonságokat is megadhat egy Pig-tevékenység JSON-definíciójában. A type tulajdonság a tevékenységhez kell lennie: **HDInsightPig**. Először hozzon létre egy HDInsight társított szolgáltatást kell és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** tevékenység típusa HDInsightPig beállításakor. szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| parancsfájl |Adja meg a Pig-parancsprogram beágyazott |Nem |
| parancsprogram elérési útja |A Pig-parancsprogram Store egy Azure blob Storage, és adja meg a fájl elérési útját. Használja a "script" vagy "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájlnév formátuma a kis-és nagybetűket. |Nem |
| határozza meg |Adja meg a paramétereket a kulcs/érték párokként való belül a Pig-parancsprogram |Nem |

Ezek a Tulajdonságok típusa csak a Pig-tevékenység vonatkoznak. Egyéb tulajdonságok (kívül a typeProperties szakaszon) minden tevékenység esetén támogatottak.   

### <a name="json-example"></a>Példa JSON

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

További információkért lásd: [Pig-tevékenység](#data-factory-pig-activity.md) cikk. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-tevékenység
A következő tulajdonságokat is megadhat egy MapReduce tevékenység JSON-definíciójában. A type tulajdonság a tevékenységhez kell lennie: **HDInsightMapReduce**. Először hozzon létre egy HDInsight társított szolgáltatást kell és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** tevékenység típusa HDInsightMapReduce beállításakor. szakasz: 

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| jarLinkedService | A JAR-fájlt tartalmazó Azure Storage társított szolgáltatás neve. | Igen |
| jarFilePath | Az Azure Storage-ban a JAR-fájl elérési útja. | Igen | 
| Osztálynév | A JAR-fájlt a fő osztály neve. | Igen | 
| argumentumok | A MapReduce-programot argumentumai vesszővel tagolt listája. Futásidőben, néhány további argumentumok láthatja (például: mapreduce.job.tags), a MapReduce keretrendszer. Az argumentumok a MapReduce argumentumokkal megkülönböztetéséhez, fontolja meg a beállítás és az értéket argumentumként a következő példában látható módon (- s használata esetén – azonnal követ értékekre bemeneti, a--output stb., opció) | Nem | 

### <a name="json-example"></a>Példa JSON

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

További információkért lásd: [MapReduce-tevékenység](data-factory-map-reduce.md) cikk. 

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming-tevékenység
A következő tulajdonságokat is megadhat egy Hadoop Streamelési tevékenység JSON-definíciójában. A type tulajdonság a tevékenységhez kell lennie: **A HDInsightStreaming**. Először hozzon létre egy HDInsight társított szolgáltatást kell és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** szakaszt, ha beállította a HDInsightStreaming tevékenység típusa: 

| Tulajdonság | Leírás | 
| --- | --- |
| eseményleképező | A végrehajtható eseményleképező neve. A példában cat.exe végrehajtható eseményleképező.| 
| Nyomáscsökkentő | A végrehajtható nyomáscsökkentő neve. A példában wc.exe végrehajtható nyomáscsökkentő. | 
| bemenet | A teljesítményleképező bemeneti (beleértve a hely) fájl. A példában: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample blob-tárolóban, például/data/Gutenberg az a mappa, és davinci.txt a blobot. |
| output | Kimeneti fájlja (beleértve a hely) a nyomáscsökkentő. A Hadoop Streamelési feladat kimenetének írt ehhez a tulajdonsághoz megadott helyen. |
| filePaths | Elérési utak a hozzárendelést és nyomáscsökkentő végrehajtható fájlok számára. A példában: "adfsample/example/apps/wc.exe", adfsample a blob-tároló, például/alkalmazások pedig a mappa, és wc.exe a végrehajtható fájlt. | 
| fileLinkedService | Az Azure Storage társított szolgáltatás, amely az Azure storage filePaths szakaszában megadott fájlt tartalmazó jelöli. | 
| argumentumok | A MapReduce-programot argumentumai vesszővel tagolt listája. Futásidőben, néhány további argumentumok láthatja (például: mapreduce.job.tags), a MapReduce keretrendszer. Az argumentumok a MapReduce argumentumokkal megkülönböztetéséhez, fontolja meg a beállítás és az értéket argumentumként a következő példában látható módon (- s használata esetén – azonnal követ értékekre bemeneti, a--output stb., opció) | 
| getDebugInfo | Nem kötelező eleme. Ha ezt a beállítást a hiba, a naplók letöltődnek csak a hibával kapcsolatban. Ha ezt a beállítást az összes, a naplók mindig letöltődnek attól függetlenül, a végrehajtási állapotot. | 

> [!NOTE]
> A Hadoop Streamelési tevékenységben esetében meg kell adnia egy kimeneti adatkészletet a **kimenete** tulajdonság. Ez az adatkészlet csak egy helyőrző adatkészletet, amely a folyamat ütemezését (óránként, naponta stb.) meghajtó szükséges lehet. Ha a tevékenység nem fogad bemenetként, adja meg a tevékenység a bemeneti adatkészlet kihagyhatja a **bemenetek** tulajdonság.  

## <a name="json-example"></a>Példa JSON

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
A következő tulajdonságokat is megadhat egy Spark-tevékenység JSON-definíciójában. A type tulajdonság a tevékenységhez kell lennie: **HDInsightSpark**. Először hozzon létre egy HDInsight társított szolgáltatást kell és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** tevékenység típusa HDInsightSpark beállításakor. szakasz: 

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| rootPath | Az Azure Blob-tároló és a Spark-fájlt tartalmazó mappát. A fájlnév formátuma a kis-és nagybetűket. | Igen |
| entryFilePath | A gyökérmappában található azon a Spark kódcsomag relatív elérési útja. | Igen |
| Osztálynév | Alkalmazás Java/Spark main osztály | Nem | 
| argumentumok | A Spark-program parancssori argumentumokat listája. | Nem | 
| proxyUser | A felhasználói fiók megszemélyesítése a Spark-program végrehajtása | Nem | 
| sparkConfig | Spark-konfigurációs tulajdonságaiban. | Nem | 
| getDebugInfo | Itt adhatja meg, ha a Spark naplófájlok másolja az Azure Storage HDInsight-fürt által használt (vagy) sparkJobLinkedService által megadott. Megengedett értékek: Nincs, mindig, vagy sikertelen. Alapértelmezett érték: Nincs. | Nem | 
| sparkJobLinkedService | Az Azure Storage társított szolgáltatás, amely tartalmazza a Spark, feladat-fájlt, a függőségeket és a naplókat.  Ha nem ad meg egy értéket ehhez a tulajdonsághoz, a HDInsight-fürthöz társított tárolót használja. | Nem |

### <a name="json-example"></a>Példa JSON

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
- A **rootPath** értékre van állítva **adfspark\\pyFiles** ahol adfspark az Azure Blob-tárolóba, pyFiles pedig finom mappát a tárolóban. Ebben a példában az Azure Blob Storage lesz, amely a Spark-fürt társítva van. Egy másik Azure-Storage feltöltheti a fájlt. Ha így tesz, a storage-fiók összekapcsolása a data factory az Azure Storage társított szolgáltatás létrehozása. Ezután adja meg a társított szolgáltatás neve értékeként a **sparkJobLinkedService** tulajdonság. Lásd: [Spark-tevékenység tulajdonságai](#spark-activity-properties) ezt a tulajdonságot, és egyéb tulajdonságok a Spark-tevékenység által támogatott.
- A **entryFilePath** értékre van állítva a **test.py**, azaz a python-fájlt. 
- A **getDebugInfo** tulajdonsága **mindig**, ami azt jelenti, a naplófájlok mindig létrehozott (sikeres vagy sikertelen).  

    > [!IMPORTANT]
    > Azt javasoljuk, hogy nem ezzel a tulajdonsággal mindig éles környezetben, ha a probléma hibaelhárítást. 
- A **kimenete** szakasz tartalmaz egy kimeneti adatkészletet. Meg kell adnia egy kimeneti adatkészletet, akkor is, ha a spark-program nem állít elő semmilyen kimenetet. A kimeneti adatkészlet határozza az ütemezés a folyamat (óránként, naponta stb.).

A tevékenységgel kapcsolatos további információkért lásd: [Spark-tevékenység](data-factory-spark.md) cikk.  

## <a name="machine-learning-batch-execution-activity"></a>Machine Learning kötegelt végrehajtási tevékenység
A következő tulajdonságokat is megadhat az Azure ML kötegelt végrehajtási tevékenység JSON-definícióban. A type tulajdonság a tevékenységhez kell lennie: **AzureMLBatchExecution**. Kell az Azure Machine Learning először a társított szolgáltatás létrehozása és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** tevékenység típusa AzureMLBatchExecution beállításakor. szakasz:

Tulajdonság | Leírás | Szükséges 
-------- | ----------- | --------
webServiceInput | Az adatkészlet, az Azure ML web service a bemenetnek átadni. Ez az adatkészlet is szerepelnie kell a tevékenység bemenetei között. |Használja a webServiceInput és a webServiceInputs. | 
webServiceInputs | Adja meg az adatkészleteket az Azure Machine Learning webszolgáltatás bemenetként átadni. Ha a webszolgáltatás több bemenet tart, használja a webServiceInputs tulajdonságot a webServiceInput tulajdonság használata helyett. Az adatkészletek által hivatkozott a **webServiceInputs** is szerepelnie kell a tevékenység **bemenetek**. | Használja a webServiceInput és a webServiceInputs. | 
webServiceOutputs | Az adatkészletek az Azure Machine Learning webszolgáltatás kimenetként rendelt. A web service Ez az adatkészlet kimeneti adatokat adja vissza. | Igen | 
globalParameters | Ebben a szakaszban adja meg a webszolgáltatás-paraméterek értékeit. | Nem | 

### <a name="json-example"></a>Példa JSON
Ebben a példában a tevékenység rendelkezik az adatkészlet **MLSqlInput** bemenetként, és **MLSqlOutput** a kimenetként. A **MLSqlInput** átadott, a web Service a bemenetnek használatával a **webServiceInput** JSON-tulajdonságot. A **MLSqlOutput** átadott kimenetként a webalkalmazás-szolgáltatás használatával a **webServiceOutputs** JSON-tulajdonságot. 

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

A JSON a példában a telepített Azure Machine Learning Web service olvasási/írási adatokat, és az Azure SQL Database egy olvasó és a egy író modul használja. A webszolgáltatás tünteti fel a következő négy paraméterek:  Adatbázis-kiszolgáló nevét, adatbázisnevet, Server felhasználói fiók nevét és kiszolgáló felhasználói fiók jelszavát.

> [!NOTE]
> Csak bemenetek és kimenetek AzureMLBatchExecution tevékenység argumentumként átadhatók paraméterek a webszolgáltatást. Ha például a fenti JSON-kódrészletben MLSqlInput a AzureMLBatchExecution tevékenység, amelyet a webszolgáltatás bemeneteként webServiceInput paraméteren keresztül egy bemeneti.

## <a name="machine-learning-update-resource-activity"></a>Machine Learning Update-erőforrástevékenység
Az Azure Machine Learning Update Resource tevékenység JSON-definíció az alábbi tulajdonságokat is megadhat. A type tulajdonság a tevékenységhez kell lennie: **AzureMLUpdateResource**. Kell az Azure Machine Learning először a társított szolgáltatás létrehozása és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** AzureMLUpdateResource tevékenység típusa beállításakor. szakasz:

Tulajdonság | Leírás | Szükséges 
-------- | ----------- | --------
trainedModelName | A retrained modell neve. | Igen |  
trainedModelDatasetName | A megőrzési művelet által visszaadott iLearner-fájlt mutató adatkészletet. | Igen | 

### <a name="json-example"></a>Példa JSON
A folyamat két tevékenységet tartalmaz: **AzureMLBatchExecution** és **AzureMLUpdateResource**. Az Azure ML kötegelt végrehajtási tevékenység bemeneteként a betanítási adatok vesz igénybe, és állít elő kimenetként egy iLearner-fájlt. A tevékenység hívja meg a képzés webszolgáltatás (betanítási kísérlet webszolgáltatásként közzétéve) és a bemeneti betanítási adatok, és fogadja a webszolgáltatás a ilearner-fájlt. A placeholderBlob csak egy helyőrző kimeneti adatkészletet, amely az Azure Data Factory szolgáltatás által a folyamat futtatásához szükséges.


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
A következő tulajdonságokat is megadhat egy U-SQL-tevékenység JSON-definíciójában. A type tulajdonság a tevékenységhez kell lennie: **Az SQL-DataLakeAnalyticsU**. Kell hoznia egy Azure Data Lake Analytics hivatkozott szolgáltatást, és értékeként adja meg a nevét, a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** DataLakeAnalyticsU-SQL-tevékenység típusa beállításakor. szakasz: 

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| scriptPath |A U-SQL parancsfájlt tartalmazó mappa elérési útja. A fájl neve, a kis-és nagybetűket. |Nem (Ha a parancsfájl használata) |
| scriptLinkedService |A tároló, amely tartalmazza a szkriptet az adat-előállító mutató társított szolgáltatás |Nem (Ha a parancsfájl használata) |
| parancsfájl |Adja meg a beágyazott parancsfájlja scriptPath és a scriptLinkedService megadása helyett. Például: "parancsfájl": "Test-adatbázis létrehozása". |Nem (Ha a scriptPath és a scriptLinkedService használ) |
| degreeOfParallelism |A feladat futtatásához egyidejűleg használt csomópontok maximális száma. |Nem |
| prioritás |Meghatározza, hogy mely feladatok közül, hogy a rendszer várólistára helyezi, melyet futtatni kíván van kiválasztva. Az alacsonyabb a szám, annál magasabb a prioritás. |Nem |
| paraméterek |A U-SQL-szkript paramétereit |Nem |

### <a name="json-example"></a>Példa JSON

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

További információkért lásd: [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md). 

## <a name="stored-procedure-activity"></a>Tárolt eljárási tevékenység
A következő tulajdonságokat is megadhat egy tárolt eljárás tevékenység JSON-definíciójában. A type tulajdonság a tevékenységhez kell lennie: **SqlServerStoredProcedure**. Kell egy meglévőt az alábbi társított szolgáltatások létrehozása és a társított szolgáltatás neve meg értéket a **linkedServiceName** tulajdonság:

- SQL Server 
- Azure SQL Database
- Azure SQL Data Warehouse

A következő tulajdonságok támogatottak a **typeProperties** tevékenység típusa SqlServerStoredProcedure beállításakor. szakasz:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| storedProcedureName |Adja meg a tárolt eljárás neve az Azure SQL database vagy Azure SQL Data Warehouse a kimeneti tábla használó társított szolgáltatás által jelölt. |Igen |
| storedProcedureParameters |Adja meg a tárolt eljárás paramétereihez tartozó értékek. Adja át az egyik paraméter null értékű kell, ha a szintaxissal: "param1": null (csupa kisbetű). Tekintse meg a következő mintát, ez a tulajdonság használatát mutatja. |Nem |

Ha megad egy bemeneti adatkészlet, elérhetőnek kell lennie (a "Kész" állapot) a tárolt eljárás tevékenység futtatásához. A bemeneti adatkészlet paraméterként az a tárolt eljárás nem felhasznált. Csak a tárolt eljárási tevékenység megkezdése előtt ellenőrizze, a függőség szolgál. Meg kell adnia egy tárolt eljárási tevékenység a kimeneti adatkészletet. 

Kimeneti adatkészlet határozza meg a **ütemezés** a tárolt eljárás tevékenység (óránként, heti, havi, stb.). A kimeneti adatkészlet kell használnia egy **társított szolgáltatás** , amely egy Azure SQL Database vagy az Azure SQL Data Warehouse vagy a tárolt eljárás futtatásához használni kívánt SQL Server-adatbázis utal. A kimeneti adatkészlet szolgálhat arra, hogy adja át az eredmény a tárolt eljárás az ezt követő feldolgozásának egy másik tevékenység ([láncolási tevékenységek](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) a folyamat. Azonban a Data Factory nem automatikusan írni a tárolt eljárás kimenete ehhez az adatkészlethez. A tárolt eljárást, amely egy SQL-tábla kimeneti adatkészlete mutató ír. Bizonyos esetekben a kimeneti adatkészlet lehet egy **helyőrző adatkészlet**, amely segítségével csak adja meg a tárolt eljárás tevékenység futtatásának ütemezését.  

### <a name="json-example"></a>Példa JSON

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
A .NET egyéni tevékenység JSON-definíciót az alábbi tulajdonságokat is megadhat. A type tulajdonság a tevékenységhez kell lennie: **DotNetActivity**. Létre kell hoznia egy Azure HDInsight társított szolgáltatás, vagy egy társított Azure Batch szolgáltatásra, és adja meg a társított szolgáltatás neve értékének a **linkedServiceName** tulajdonság. A következő tulajdonságok támogatottak a **typeProperties** tevékenység típusa DotNetActivity beállításakor. szakasz:
 
| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| AssemblyName | A szerelvény neve. A példában van: **MyDotnetActivity.dll**. | Igen |
| EntryPoint |Az osztály, amely megvalósítja az IDotNetActivity felület végrehajtási neve. A példában van: **MyDotNetActivityNS.MyDotNetActivity** ahol MyDotNetActivityNS a névtér, MyDotNetActivity pedig az osztály.  | Igen | 
| PackageLinkedService | Az Azure Storage társított szolgáltatás, amely a blob Storage, amely tartalmazza az egyéni tevékenység zip-fájl neve. A példában van: **AzureStorageLinkedService**.| Igen |
| PackageFile | A zip-fájl neve. A példában, a: **customactivitycontainer/MyDotNetActivity.zip**. | Igen |
| extendedProperties | Kiterjesztett tulajdonságok, amely meghatározza, és a .NET-kódon adja át. Ebben a példában a **SliceStart** változó értéke a SliceStart rendszerváltozóhoz alapuló értéket. | Nem | 

### <a name="json-example"></a>Példa JSON

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

Részletes információkért lásd: [egyéni tevékenységek használata Data Factory](data-factory-use-custom-activities.md) cikk. 

## <a name="next-steps"></a>További lépések
Lásd az alábbi oktatóanyagokat: 

- [Oktatóanyag: másolási tevékenységgel rendelkező folyamat létrehozása](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Oktatóanyag: egy hive-tevékenységgel rendelkező folyamat létrehozása](data-factory-build-your-first-pipeline-using-editor.md)

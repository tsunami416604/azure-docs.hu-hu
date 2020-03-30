---
title: Azure Data Factory - JSON parancsfájlok – útmutató
description: JSON-sémákat biztosít a Data Factory entitásokhoz.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 3492f917be8116d0eed0c7ec03ed8aa9ff506520
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346601"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory - JSON parancsfájlok – útmutató
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik.


Ez a cikk JSON-sémákat és példákat tartalmaz az Azure Data Factory-entitások (folyamat, tevékenység, adatkészlet és csatolt szolgáltatás) definiálására.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Folyamat
A csővezetékek definíciójának magas szintű szerkezete a következő:

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

Az alábbi táblázat a JSON-definíción belüli tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
| név | A folyamat neve. Adjon meg egy nevet, amely azt a műveletet jelöli, amelyhez a tevékenység vagy a folyamat konfigurálva van.<br/><ul><li>A karakterek maximális száma: 260</li><li>Betűszámmal vagy aláhúzásjellel\_( ) kell kezdődnie.</li><li>A következő karakterek nem engedélyezettek: ".",",","?","/", "<",">","""""%","&","","\\</li></ul> |Igen |
| leírás |A tevékenység vagy folyamat felhasználhatósítási szövege | Nem |
| tevékenységek | A tevékenységek listáját tartalmazza. | Igen |
| start |A folyamat kezdő dátum-időpontja. [ISO formátumúnak](https://en.wikipedia.org/wiki/ISO_8601)kell lennie. Például: 2014-10-14T16:32:41. <br/><br/>Meg lehet adni a helyi időt, például az EST időt. Íme egy példa: `2016-02-27T06:00:00**-05:00`, ami 06:00 EST.<br/><br/>A kezdő és záró tulajdonságok együttesen a folyamat aktív időszakát adják meg. A kimeneti szeletek csak ebben az aktív időszakban készülnek. |Nem<br/><br/>Ha értéket ad meg a záró tulajdonsághoz, akkor meg kell adnia a start tulajdonság értékét.<br/><br/>A kezdési és befejezési időpontok egyaránt üresek lehetnek a folyamat létrehozásához. Mindkét értéket meg kell adnia a folyamat futtatásához aktív időszak beállításához. Ha nem adja meg a folyamat létrehozásakor a kezdési és befejezési időpontokat, később a Set-AzDataFactoryPipelineActivePeriod parancsmag használatával állíthatja be őket. |
| befejezés |A folyamat dátumának záró dátuma. Ha meg van adva, ISO formátumban kell lennie. Például: 2014-10-14T17:32:41 <br/><br/>Meg lehet adni a helyi időt, például az EST időt. Íme egy példa: `2016-02-27T06:00:00**-05:00`, ami 06:00 EST.<br/><br/>A folyamat határozatlan ideig történő futtatásához adja meg a 9999-09-09 értéket az end (befejezés) tulajdonsághoz. |Nem <br/><br/>Ha értéket ad meg a start tulajdonsághoz, akkor meg kell adnia a záró tulajdonság értékét.<br/><br/>Lásd a **start** tulajdonságra vonatkozó megjegyzéseket. |
| isPaused |Ha értéke igaz, a folyamat nem fut. Alapértelmezett érték = hamis. Ezzel a tulajdonsággal engedélyezheti vagy letilthatja. |Nem |
| pipelineMode (folyamatmód) |Az ütemezés imasdula fut a folyamathoz. Az engedélyezett értékek a következők: ütemezett (alapértelmezett), egyszeri.<br/><br/>Az "Ütemezett" azt jelzi, hogy a csővezeték egy meghatározott időintervallumban fut az aktív időszak (kezdési és befejezési időpont) szerint. Az "egyszeri" azt jelzi, hogy a csővezeték csak egyszer fut. A létrehozott egyszeri folyamatok jelenleg nem módosíthatók/frissíthetők. Az egyszeri beállítással kapcsolatos részletekért tekintse meg az [Egyszeri folyamatot.](data-factory-create-pipelines.md#onetime-pipeline) |Nem |
| elévülési idő |A létrehozás után eltelt idő, amelyre a folyamat érvényes, és amelyet ki kell építeni. Ha nem rendelkezik aktív, sikertelen vagy függőben lévő futtatásokkal, a folyamat automatikusan törlődik, amint eléri a lejárati időt. |Nem |


## <a name="activity"></a>Tevékenység
Egy folyamatdefiníción (tevékenységelem) belüli tevékenység magas szintű struktúrája a következő:

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

Az alábbi táblázat a JSON-definíción belüli tulajdonságokat ismerteti:

| Címke | Leírás | Kötelező |
| --- | --- | --- |
| név |A tevékenység neve. Adjon meg egy nevet, amely azt a műveletet jelöli, amelyhez a tevékenység konfigurálva van.<br/><ul><li>A karakterek maximális száma: 260</li><li>Betűszámmal vagy aláhúzásjellel\_( ) kell kezdődnie.</li><li>A következő karakterek nem engedélyezettek: ".",",","?","/", "<",">","""""%","&","","\\</li></ul> |Igen |
| leírás |A tevékenységet leíró szöveg. |Nem |
| type |A tevékenység típusát adja meg. A különböző típusú tevékenységeket lásd a [DATA STORES](#data-stores) és DATA [TRANSFORMATION ACTIVITIES](#data-transformation-activities) szakaszokban. |Igen |
| Bemenetek |A tevékenység által használt beviteli táblák<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Nem a HDInsightStreaming és az SqlServerStoredProcedure tevékenységekhez <br/> <br/> Igen az összes többi számára |
| Kimenetek |A tevékenység által használt kimeneti táblák.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Igen |
| linkedServiceName |A tevékenység által használt társított szolgáltatás neve. <br/><br/>Egy adott tevékenység megkövetelheti annak a társított szolgáltatásnak a megadását, amely a szükséges számítási környezethez kapcsolódik. |Igen a HDInsight-tevékenységek, az Azure Machine Learning-tevékenységek és a tárolt eljárási tevékenység esetén. <br/><br/>Minden egyéb esetében: nem |
| typeProperties |A typeProperties szakasz tulajdonságai a tevékenység típusától függenek. |Nem |
| szabályzat |Olyan szabályzatok, amelyek az adott tevékenység futásidejű viselkedését befolyásolják. Ha nincs megadva, a rendszer alapértelmezett házirendeket használ. |Nem |
| scheduler |Az "ütemező" tulajdonság a tevékenység kívánt ütemezésének meghatározására szolgál. Altulajdonságai megegyeznek az [adatkészlet rendelkezésre állási tulajdonságában lévőtulajdonságokkal.](data-factory-create-datasets.md#dataset-availability) |Nem |

### <a name="policies"></a>Házirendek
A házirendek hatással vannak egy tevékenység futásidejű viselkedésére, különösen a táblázat szeletének feldolgozásakor. Az alábbi táblázat a részleteket tartalmazza.

| Tulajdonság | Megengedett értékek | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| Konkurencia |Egész szám <br/><br/>Maximális érték: 10 |1 |A tevékenység egyidejű végrehajtásai száma.<br/><br/>Ez határozza meg a párhuzamos tevékenység végrehajtások száma, amelyek különböző szeleteken fordulhat elő. Ha például egy tevékenységnek nagy mennyiségű rendelkezésre álló adatot kell átnéznie, a nagyobb egyidejűségi érték felgyorsítja az adatfeldolgozást. |
| executionPriorityOrder |LegújabbElső<br/><br/>LegrégebbiElső |LegrégebbiElső |A feldolgozás alatt álló adatszeletek sorrendjét határozza meg.<br/><br/>Ha például 2 szelete van (az egyik 16:00-kor, a másik 17:00-kor történik), és mindkettő végrehajtás rakoncát; Ha a executionPriorityOrder-t NewestFirst-re állítja be, a szelet feldolgozása 17:00 órakor lesz. Hasonlóképpen, ha a executionPriorityORder-t legrégebbiFIrst-re állítja be, akkor a szelet 16:00-kor kerül feldolgozásra. |
| retry |Egész szám<br/><br/>A maximális érték 10 lehet |0 |A szelet adatfeldolgozása előtt újrapróbálkozások száma sikertelenként. Az adatszelet tevékenységének végrehajtása a megadott újrapróbálkozások számáig újra próbálkozik. Az újrapróbálkozás a hiba után a lehető leghamarabb megtörténik. |
| timeout |időtartam |00:00:00 |A tevékenység időmeghosszabbítása. Példa: 00:10:00 (időout 10 mins)<br/><br/>Ha egy érték nincs megadva, vagy 0, az időtúlérték végtelen.<br/><br/>Ha egy szelet adatfeldolgozási ideje meghaladja az időtúllépési értéket, a rendszer megszakítja, és a rendszer megpróbálja újramegpróbálni a feldolgozást. Az újrapróbálkozások száma az újrapróbálkozási tulajdonságtól függ. Időeltoridő-elállás esetén az állapot Beállítása TimedOut. |
| Késleltetés |időtartam |00:00:00 |Adja meg a szelet adatfeldolgozásának elindulása előtti késleltetést.<br/><br/>Az adatszelet tevékenységének végrehajtása a késleltetés után kezdődik a várt végrehajtási idő után.<br/><br/>Példa: 00:10:00 (10 mins késéssel) |
| hosszúRetry |Egész szám<br/><br/>Maximális érték: 10 |1 |A szelet végrehajtása előtt a hosszú újrapróbálkozások száma.<br/><br/>A longRetry kísérleteket a longRetryInterval határozza meg. Tehát, ha meg kell adnia egy időt az újrapróbálkozások között, használja a longRetry.So if you need to specify a time between retry attempts, use longRetry. Ha mind az újrapróbálkozás, mind a longRetry meg van adva, minden longRetry kísérlet újrapróbálkozási kísérleteket tartalmaz, és a kísérletek maximális száma újra * longRetry.<br/><br/>Ha például a következő beállításokkal rendelkezünk a tevékenységi szabályzatban:<br/>Újrapróbálkozás: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Tegyük fel, hogy csak egy szeletet kell végrehajtani (az állapot várakozás), és a tevékenység végrehajtása minden alkalommal sikertelen lesz. Kezdetben 3 egymást követő végrehajtási kísérlet lenne. Minden kísérlet után a szelet állapota újra lesz. Miután az első 3 kísérlet véget ért, a szelet állapota longretry lesz.<br/><br/>Egy óra elteltével (azaz longRetryInteval értéke), nem lenne egy másik készlet 3 egymást követő végrehajtási kísérletek. Ezt követően a szelet állapota sikertelen lesz, és nem kísérelt meg több újrapróbálkozást. Ezért összességében 6 kísérlet történt.<br/><br/>Ha bármelyik végrehajtás sikeres, a szelet állapota kész lesz, és nem kísérelmeg több újrapróbálkozást.<br/><br/>A longRetry olyan helyzetekben alkalmazható, amikor a függő adatok nem determinisztikus időpontban érkeznek, vagy az általános környezet pelyhes, amely alatt az adatfeldolgozás történik. Ilyen esetekben az újrapróbálkozások egymás után nem biztos, hogy segít, és ezt időintervallum után eredményezi a kívánt kimenetet.<br/><br/>Figyelmeztető szó: ne állítson be magas értékeket a longRetry vagy longRetryInterval értékéhez. A magasabb értékek általában más rendszerszintű problémákat is jelentenek. |
| longRetryInterval között |időtartam |00:00:00 |A hosszú újrapróbálkozások közötti késleltetés |

### <a name="typeproperties-section"></a>typeProperties szakasz
A typeProperties szakasz minden tevékenységnél eltérő. Az átalakítási tevékenységek csak típustulajdonságokkal rendelkeznek. Ebben a cikkben a [DATA TRANSFORMATION ACTIVITIES](#data-transformation-activities) című témakört a folyamat átalakítási tevékenységeit definiáló JSON-mintákért.

**A másolási tevékenység** nek két alszakasza van a típusTulajdonságok szakaszban: **forrás** és **fogadó**. Ebben a cikkben a [DATA STORES](#data-stores) című szakaszban a JSON-mintákat, amelyek bemutatják, hogyan kell használni az adattárak forrásként és/vagy fogadóként.

### <a name="sample-copy-pipeline"></a>Minta másolási folyamat
Az alábbi mintafolyamat **tevékenységek** szakaszában egyetlen **Másolás** típusú tevékenység található. Ebben a példában a [másolási tevékenység](data-factory-data-movement-activities.md) adatokat másol egy Azure Blob storage egy Azure SQL-adatbázisba.

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

* A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa****Copy** értékre van beállítva.
* A tevékenység bemenetének beállítása **InputDataset**, a kimeneté pedig **OutputDataset**.
* A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva.

Ebben a cikkben a [DATA STORES](#data-stores) című szakaszban a JSON-mintákat, amelyek bemutatják, hogyan kell használni az adattárak forrásként és/vagy fogadóként.

A folyamat létrehozásának teljes forgatókönyvét az [Oktatóanyag: Adatok másolása a Blob Storage-ból az SQL Database-be](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)című témakörben ismerteti.

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

* A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa****HDInsightHive** értékre van beállítva.
* A **partitionweblogs.hql** Hive-parancsfájl tárolása az Azure Storage-fiókban (az **AzureStorageLinkedService** nevű scriptLinkedService szolgáltatás által megadva), és az **adfgetstarted** tároló **script** mappájában történik.
* A defines szakasz a hive-parancsfájlnak hive **konfigurációs** értékként (pl. `${hiveconf:inputtable}`) `${hiveconf:partitionedtable}`átadott futásidejű beállítások megadására szolgál.

Ebben a cikkben a [DATA TRANSFORMATION ACTIVITIES](#data-transformation-activities) című témakört a folyamat átalakítási tevékenységeit definiáló JSON-mintákért.

A folyamat létrehozásának teljes forgatókönyvét az [Oktatóanyag: Az első folyamat létrehozása az adatok Hadoop-fürt használatával történő feldolgozásához](data-factory-build-your-first-pipeline.md)olvassa el.

## <a name="linked-service"></a>Társított szolgáltatások
A csatolt szolgáltatásdefiníció magas szintű struktúrája a következő:

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

Az alábbi táblázat a JSON-definíción belüli tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| név | A csatolt szolgáltatás neve. | Igen |
| tulajdonságok - típus | A csatolt szolgáltatás típusa. Például: Azure Storage, Azure SQL Database. |
| typeProperties | A typeProperties szakasz olyan elemeket tartalmaz, amelyek az egyes adattár- vagy számítási környezetben eltérőek. Tekintse meg az adattárolók szakaszát az összes adattárhoz kapcsolódó szolgáltatáshoz és [számítási környezethez](#compute-environments) az összes számításhoz csatolt szolgáltatáshoz |

## <a name="dataset"></a>Adatkészlet
Az Azure Data Factory adatkészlete a következőképpen van meghatározva:

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

Az alábbi táblázat a fenti JSON-tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| név | Az adatkészlet neve. Lásd: [Azure Data Factory – elnevezési szabályok](data-factory-naming-rules.md) elnevezési szabályok. |Igen |NA |
| type | Az adatkészlet típusa. Adja meg az Azure Data Factory által támogatott típusok egyikét (például: AzureBlob, AzureSqlTable). Lásd: [DATA STORES](#data-stores) szakasz a Data Factory által támogatott összes adattárak és adatkészlettípusok. |
| Szerkezet | Az adatkészlet sémája. Oszlopokat, típusokat stb. | Nem |NA |
| typeProperties | A kiválasztott típusnak megfelelő tulajdonságok. A támogatott típusokat és azok tulajdonságait a [DATA STORES](#data-stores) című szakaszban található. |Igen |NA |
| external | Logikai jelző annak megadásához, hogy egy adatkészletet kifejezetten egy adat-előállító folyamat hoz-e létre. |Nem |hamis |
| availability | Az adatkészlet előállításához a feldolgozási ablakot vagy a szeletelési modellt határozza meg. Az adatkészlet szeletelési modelljéről az Ütemezés és végrehajtás című cikkben olvashat [részletesen.](data-factory-scheduling-and-execution.md) |Igen |NA |
| szabályzat |Meghatározza azokat a feltételeket vagy feltételt, amelyeknek az adatkészlet szeleteinek meg kell felelniük. <br/><br/>További információt az Adatkészlet-házirend szakaszban talál. |Nem |NA |

A **műtárgyszakasz** minden oszlopa a következő tulajdonságokat tartalmazza:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| név |Az oszlop neve. |Igen |
| type |Az oszlop adattípusa.  |Nem |
| Kultúra |A típus megadásakor és a .NET típus `Datetime` vagy `Datetimeoffset`a . Az alapértelmezett szint a `en-us`. |Nem |
| Formátum |A típus megadása esetén használandó formátumkarakterlánc , `Datetime` `Datetimeoffset`a .NET vagy a . |Nem |

A következő példában az adatkészlet `slicetimestamp`három `projectname`oszlopból áll, és `pageviews` a következő típusúak: Karakterlánc, Karakterlánc és Decimális.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Az alábbi táblázat a **rendelkezésre állási** szakaszban használható tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| frequency |Megadja az adatkészletszelet előállításának időegységét.<br/><br/><b>Támogatott gyakoriság:</b>Perc, Óra, Nap, Hét, Hónap |Igen |NA |
| interval |A frekvencia szorzóját adja meg<br/><br/>A "Gyakoriság x intervallum" határozza meg, hogy milyen gyakran keletkezik a szelet.<br/><br/>Ha az adatkészletet óránként kell szeletelni, akkor a <b>Gyakoriság értékét</b> <b>óra</b>értékre, az <b>intervallumot</b> pedig <b>1-re</b>kell állítani.<br/><br/><b>Megjegyzés:</b>Ha a Gyakoriság szót adja meg percként, javasoljuk, hogy az időközt legalább 15 |Igen |NA |
| stílus |Itt adható meg, hogy a szeletet az intervallum elején/végén kell-e előállítani.<ul><li>StartOfInterval (Kezdési intervallum)</li><li>EndOfInterval (Időköz vége)</li></ul><br/><br/>Ha a Gyakoriság beállítása Hónap, és a stílus beállítása EndOfInterval, a szelet a hónap utolsó napján keletkezik. Ha a stílus startofinterval értékre van állítva, a szelet a hónap első napján jön létre.<br/><br/>Ha a Gyakoriság érték Nap, a stílus pedig EndOfInterval értékre van állítva, akkor a szelet a nap utolsó órájában keletkezik.<br/><br/>Ha a Gyakoriság érték Óra, a stílus pedig EndOfInterval értékre van állítva, akkor a szelet az óra végén keletkezik. Például egy 1:00–2:00-as időszakra szóló szelet esetében a szelet 14:00 órakor keletkezik. |Nem |EndOfInterval (Időköz vége) |
| anchorDateTime |Az adathalmazszelet-határok kiszámításához az ütemező által használt abszolút időbeosztást határozza meg. <br/><br/><b>Megjegyzés:</b>Ha az AnchorDateTime dátumrészei részletesebbek, mint a gyakoriság, akkor a részletesebb részeket figyelmen kívül hagyja. <br/><br/>Ha például az <b>időköz</b> <b>óránkénti</b> (gyakoriság: óra és időköz: 1), és az <b>AnchorDateTime</b> <b>perceket és másodperceket</b> tartalmaz, akkor az AnchorDateTime <b>perc és másodperc</b> részeit figyelmen kívül hagyja a rendszer. |Nem |01/01/0001 |
| offset |Az az időtartam, amely alatt az összes adatkészletszelet kezdő és záróeleme eltolódik. <br/><br/><b>Megjegyzés:</b>Ha mind a anchorDateTime, mind az eltolás meg van adva, az eredmény a kombinált eltolódás. |Nem |NA |

A következő rendelkezésre állási szakasz azt határozza meg, hogy a kimeneti adatkészlet óránként (vagy) a bemeneti adatkészlet óránként érhető el:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Az adatkészlet-definíció **házirendszakasza** határozza meg azokat a feltételeket vagy feltételt, amelyeknek az adatkészletszeleteknek meg kell felelniük.

| Házirend neve | Leírás | Alkalmazott: | Kötelező | Alapértelmezett |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Ellenőrzi, hogy az **Azure blobban** lévő adatok megfelelnek-e a minimális méretkövetelményeknek (megabájtban). |Azure-blob |Nem |NA |
| minimumSorok |Ellenőrzi, hogy az **Azure SQL-adatbázisban** vagy egy **Azure-táblában** lévő adatok tartalmazzák-e a sorok minimális számát. |<ul><li>Azure SQL Database</li><li>Azure-tábla</li></ul> |Nem |NA |

**Példa:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

Hacsak az Azure Data Factory nem állít elő egy adatkészletet, **külsőként**kell megjelölni. Ez a beállítás általában a folyamat első tevékenységének bemeneteire vonatkozik, kivéve, ha tevékenység vagy csővezeték láncolása van használatban.

| Név | Leírás | Kötelező | Alapértelmezett érték |
| --- | --- | --- | --- |
| dataDelay (adatkésleltetés) |Az adott szelet külső adatainak rendelkezésre állásának késleltetésének ideje. Ha például az adatok óránként állnak rendelkezésre, a külső adatok megtekintéséhez szükséges ellenőrzés elérhető, és a megfelelő szelet Kész, késleltethető a dataDelay használatával.<br/><br/>Csak a jelen időre vonatkozik.  Ha például jelenleg 13:00 óra, és ez az érték 10 perc, az ellenőrzés 13:10-kor kezdődik.<br/><br/>Ez a beállítás nincs hatással a korábbi szeletekre (a szeletbefejezési idővel + dataDelay < Most) lévő szeletek feldolgozása késedelem nélkül történik.<br/><br/>A 23:59 óránál hosszabb időt `day.hours:minutes:seconds` a formátum használatával kell megadni. Ha például 24 órát szeretne megadni, ne használja a 24:00:00; ehelyett használja az 1.00:00:00. A 24:00:00 használata esetén a kezelés 24 nap (24.00:00:00). 1 nap és 4 óra esetén adja meg az 1:04:00:00 értéket. |Nem |0 |
| retryInterval |A hiba és a következő újrapróbálkozási kísérlet közötti várakozási idő. Ha egy próbálkozás sikertelen, a következő próbálkozás az újrapróbálkozások után lesz. <br/><br/>Ha most délután 1 óra van, kezdjük az első próbálkozást. Ha az első érvényesítési ellenőrzés befejezésének időtartama 1 perc, és a művelet sikertelen, a következő újrapróbálkozás 1:00 + 1 perc (időtartam) + 1 perc (újrapróbálkozási időköz) = 1:02 DU. <br/><br/>A korábbi szeletek esetében nincs késés. Az újrapróbálkozás azonnal megtörténik. |Nem |00:01:00 (1 perc) |
| újrapróbálkozásidő-ki( újrapróbálkozás) |Az egyes újrapróbálkozási próbálkozások időmeghosszabbítása.<br/><br/>Ha ez a tulajdonság 10 percre van állítva, az ellenőrzést 10 percen belül be kell fejezni. Ha az ellenőrzés végrehajtása 10 percnél tovább tart, az újrapróbálkozás időtúllépése.<br/><br/>Ha az ellenőrzésre irányuló összes kísérlet idővel eljár, a szelet TimedOut-ként lesz megjelölve. |Nem |00:10:00 (10 perc) |
| maximumRetry |A külső adatok elérhetőségének ellenőrzése. Az engedélyezett maximális érték 10. |Nem |3 |


## <a name="data-stores"></a>ADATTÁROLÓK
A [Csatolt szolgáltatás](#linked-service) szakasz a JSON-elemek leírását adta meg, amelyek a csatolt szolgáltatások minden típusára vonatkozóak közösek. Ez a szakasz az egyes adattakra jellemző JSON-elemek részleteit tartalmazza.

Az [Adatkészlet](#dataset) szakasz olyan JSON-elemek leírását adta meg, amelyek minden adatkészlettípusra közösek. Ez a szakasz az egyes adattakra jellemző JSON-elemek részleteit tartalmazza.

A [Tevékenység](#activity) szakasz olyan JSON-elemek leírását tartalmazza, amelyek minden tevékenységtípusra közösek. Ez a szakasz az egyes adattatokra jellemző JSON-elemek részleteit tartalmazza, ha azokat egy másolási tevékenység forrása/fogadója ként használja.

Kattintson a kívánt üzlet hivatkozására a csatolt szolgáltatás JSON-sémák, az adatkészlet és a másolási tevékenység forrás/fogadó megtekintéséhez.

| Kategória | Adattár
|:--- |:--- |
| **Azure** |[Azure Blob-tárhely](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure SQL adattárház](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
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
| **Mások** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Webtábla |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Társított szolgáltatások
A csatolt szolgáltatásoknak két típusa van: az Azure Storage-kapcsolt szolgáltatás és az Azure Storage SAS-alapú szolgáltatás.

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Ha az Azure storage-fiókot egy adat-előállítóhoz szeretné kapcsolni a **fiókkulcs**használatával, hozzon létre egy Azure Storage-kapcsolt szolgáltatást. Azure Storage-hoz csatolt szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** az **AzureStorage-ra.** Ezután megadhatja a következő tulajdonságokat a **típusTulajdonságok** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| connectionString (kapcsolati karakterlánc) |Adja meg a connectionString tulajdonság Azure storage-hoz való csatlakozásához szükséges információkat. |Igen |

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

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS csatolt szolgáltatás
Az Azure Storage SAS-alapú szolgáltatás lehetővé teszi, hogy egy Azure Storage-fiók összekapcsolásával egy Azure-adat-előállító egy megosztott hozzáférésű aláírás (SAS) használatával. Az adat-előállító korlátozott/időhöz kötött hozzáférést biztosít a tárolóban lévő összes/adott erőforrásokhoz (blob/tároló). Ha az Azure storage-fiókját megosztott hozzáférésű aláírással egy adat-előállítóhoz szeretné kapcsolni, hozzon létre egy Azure Storage SAS-kapcsolt szolgáltatást. Azure Storage SAS-kapcsolt szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** az **AzureStorageSas**szolgáltatásra. Ezután megadhatja a következő tulajdonságokat a **típusTulajdonságok** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| sasUri között |Adja meg a megosztott hozzáférésű aláírás URI-t az Azure Storage-erőforrásokhoz, például blobhoz, tárolóhoz vagy táblához. |Igen |

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

Ezekről a csatolt szolgáltatásokról további információt az [Azure Blob Storage-összekötő](data-factory-azure-blob-connector.md#linked-service-properties) ről szóló cikkben talál.

### <a name="dataset"></a>Adatkészlet
Azure Blob-adatkészlet definiálásához állítsa be az adatkészlet **típusát** **az AzureBlob**beállításra. Ezután adja meg a következő Azure Blob-specifikus tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A blob storage-ban lévő tároló és mappa elérési útja. Példa: myblobcontainer\myblobfolder\ |Igen |
| fileName |A blob neve. a fájlnév megadása nem kötelező, és a kis- és nagybetűket is ki nem szokja.<br/><br/>Ha megad egy fájlnevet, a tevékenység (beleértve a másolást is) az adott Blobon működik.<br/><br/>Ha a fájlnév nincs megadva, a Másolás a folderPath a bemeneti adatkészlethez tartalmazza az összes blobot.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve `Data.<Guid>.txt` a következő formátumban lesz: (például: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| particionáltby |partitionedBy egy választható tulajdonság. Segítségével dinamikus mappátadhat megPath és fájlnév az idősorozat-adatokhoz. Például a folderPath paraméterezhető az adatok minden órájára. |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](data-factory-supported-file-and-compression-formats.md#text-format) [a Json formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [az Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [az Orc Formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [Parkettaformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. <br><br> Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**. Támogatott szintek: **Optimális** és **leggyorsabb**. További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |

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


További információ: [Azure Blob-összekötő](data-factory-azure-blob-connector.md#dataset-properties) cikk.

### <a name="blobsource-in-copy-activity"></a>BlobSource másolási tevékenységben
Ha egy Azure Blob Storage-ból másolja az adatokat, állítsa be a másolási tevékenység **forrástípusát** a **BlobSource**mezőre, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasása rekurzív an- vagy csak a megadott mappából történik. |Igaz (alapértelmezett érték), Hamis |Nem |

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
### <a name="blobsink-in-copy-activity"></a>BlobSink a másolási tevékenységben
Ha adatokat másol egy Azure Blob Storage-ba, állítsa be a másolási tevékenység **fogadótípusát** **a BlobSink**beállításra, és adja meg a következő tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| copyBehavior (Másként) |A másolási viselkedést határozza meg, ha a forrás BlobSource vagy FileSystem. |<b>Megőrzés:</b>megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl célmappához való relatív elérési útjával.<br/><br/><b>Összeolvasztás:</b>a forrásmappából származó összes fájl a célmappa első szintjén található. A célfájlok automatikusan létrehozott névvel rendelkeznek. <br/><br/><b>MergeFiles (alapértelmezett):</b> a forrásmappából származó összes fájlt egyetlen fájlba egyesíti. Ha a Fájl/Blob név meg van adva, az egyesített fájlnév lesz a megadott név; ellenkező esetben lenne automatikusan generált fájlnevét. |Nem |

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

További információ: [Azure Blob-összekötő](data-factory-azure-blob-connector.md#copy-activity-properties) cikk.

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Társított szolgáltatások
Az Azure Data Lake Store-hoz kapcsolt szolgáltatás definiálásához állítsa be a csatolt szolgáltatás típusát az **AzureDataLakeStore-ra,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **AzureDataLakeStore** | Igen |
| dataLakeStoreUri | Adja meg az Azure Data Lake Store-fiók adatait. Ez a következő formátumban: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/`. | Igen |
| subscriptionId | Az Azure-előfizetés azonosítója, amelyhez a Data Lake Store tartozik. | A mosogatóhoz szükséges |
| resourceGroupName | Az Azure erőforráscsoport neve, amelyhez a Data Lake Store tartozik. | A mosogatóhoz szükséges |
| servicePrincipalId | Adja meg az alkalmazás ügyfélazonosítóját. | Igen (egyszerű szolgáltatáshitelesítésesetén) |
| servicePrincipalKey | Adja meg az alkalmazás kulcsát. | Igen (egyszerű szolgáltatáshitelesítésesetén) |
| Bérlő | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér relai vonala az Azure Portal jobb felső sarkában lebeg. | Igen (egyszerű szolgáltatáshitelesítésesetén) |
| engedélyezés | Kattintson az **Engedélyezés** gombra a **Data Factory Editor** ban, és adja meg a hitelesítő adatait, amely hozzárendeli az automatikusan létrehozott engedélyezési URL-címet ehhez a tulajdonsághoz. | Igen (felhasználói hitelesítő adatok hitelesítéséhez)|
| Munkamenet | OAuth munkamenet-azonosító az OAuth engedélyezési munkamenetből. Minden munkamenet-azonosító egyedi, és csak egyszer használható. Ez a beállítás automatikusan létrejön a Data Factory Editor használatakor. | Igen (felhasználói hitelesítő adatok hitelesítéséhez) |

#### <a name="example-using-service-principal-authentication"></a>Példa: egyszerű szolgáltatáshitelesítés használata
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

#### <a name="example-using-user-credential-authentication"></a>Példa: felhasználói hitelesítő adatok hitelesítésének használata
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

További információ: [Azure Data Lake Store-összekötő cikk.](data-factory-azure-datalake-connector.md#linked-service-properties)

### <a name="dataset"></a>Adatkészlet
Az Azure Data Lake Store-adatkészlet definiálásához állítsa be az adatkészlet **típusát** az **AzureDataLakeStore -ra,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| folderPath |Az Azure Data Lake-tárolóban lévő tároló és mappa elérési útja. |Igen |
| fileName |A fájl neve az Azure Data Lake áruházban. a fájlnév megadása nem kötelező, és a kis- és nagybetűket is ki nem szokja. <br/><br/>Ha megad egy fájlnevet, a tevékenység (beleértve a Másolást is) az adott fájlon működik.<br/><br/>Ha a fájlnév nincs megadva, a Másolás a mappaösszes fájlját tartalmazza: A bemeneti adatkészlethez a Path.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve `Data.<Guid>.txt` a következő formátumban lesz: (például: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| particionáltby |partitionedBy egy választható tulajdonság. Segítségével dinamikus mappátadhat megPath és fájlnév az idősorozat-adatokhoz. Például a folderPath paraméterezhető az adatok minden órájára. |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](data-factory-supported-file-and-compression-formats.md#text-format) [a Json formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [az Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [az Orc Formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [Parkettaformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. <br><br> Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**. Támogatott szintek: **Optimális** és **leggyorsabb**. További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |

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

További információ: [Azure Data Lake Store-összekötő cikk.](data-factory-azure-datalake-connector.md#dataset-properties)

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Azure Data Lake Store-forrás másolási tevékenységben
Ha egy Azure Data Lake Store-ból másolja az adatokat, állítsa be a másolási tevékenység **forrástípusát** az **AzureDataLakeStoreSource mezőre,** és adja meg a következő tulajdonságokat a **forrásszakaszban:**

**Az AzureDataLakeStoreSource** a következő **tulajdonságtípusokat támogatja:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasása rekurzív an- vagy csak a megadott mappából történik. |Igaz (alapértelmezett érték), Hamis |Nem |

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

További információ: [Azure Data Lake Store-összekötő cikk.](data-factory-azure-datalake-connector.md#copy-activity-properties)

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Az Azure Data Lake Áruház fogadója a másolási tevékenységben
Ha adatokat másol egy Azure Data Lake Store-ba, állítsa be a másolási tevékenység **fogadótípusát** az **AzureDataLakeStoreSink**-re, és adja meg a következő tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| copyBehavior (Másként) |Megadja a másolási viselkedést. |<b>Megőrzés:</b>megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl célmappához való relatív elérési útjával.<br/><br/><b>Összeolvasztás:</b>a forrásmappából származó összes fájl a célmappa első szintjén jön létre. A célfájlok automatikusan létrehozott névvel jönnek létre.<br/><br/><b>MergeFiles</b>: a forrásmappából származó összes fájlt egyetlen fájlba egyesíti. Ha a Fájl/Blob név meg van adva, az egyesített fájlnév lesz a megadott név; ellenkező esetben lenne automatikusan generált fájlnevét. |Nem |

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

További információ: [Azure Data Lake Store-összekötő cikk.](data-factory-azure-datalake-connector.md#copy-activity-properties)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Társított szolgáltatások
Az Azure Cosmos DB kapcsolt szolgáltatásának definiálásához állítsa be a csatolt szolgáltatás **típusát** a **DocumentDb-re,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| connectionString (kapcsolati karakterlánc) |Adja meg az Azure Cosmos DB adatbázishoz való csatlakozáshoz szükséges információkat. |Igen |

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
További információ: [Azure Cosmos DB-összekötő](data-factory-azure-documentdb-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adatkészlet
Az Azure Cosmos DB-adatkészlet definiálásához állítsa az adatkészlet típusát **DocumentDbCollection (DocumentDbCollection)** **beállításra,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| Lekérdezés_neve |Az Azure Cosmos DB-gyűjtemény neve. |Igen |

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
További információ: [Azure Cosmos DB-összekötő](data-factory-azure-documentdb-connector.md#dataset-properties) cikk.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Az Azure Cosmos DB-gyűjtemény forrása a másolási tevékenységben
Ha egy Azure Cosmos DB-ből másolja az adatokat, állítsa a másolási tevékenység **forrástípusát** **documentDbCollectionSource (DokumentumDbCollectionSource)** beállításra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**


| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Szükséges** |
| --- | --- | --- | --- |
| lekérdezés |Adja meg az adatokolvasáshoz a lekérdezést. |Az Azure Cosmos DB által támogatott lekérdezési karakterlánc. <br/><br/>Például: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, a végrehajtásra kerül az SQL utasítás:`select <columns defined in structure> from mycollection` |
| nestingSeparator |A dokumentum beágyazottként való megjelölésére utaló speciális karakter |Bármilyen karaktert. <br/><br/>Az Azure Cosmos DB egy NoSQL-áruház A JSON-dokumentumok, ahol beágyazott struktúrák megengedettek. Az Azure Data Factory lehetővé teszi a felhasználó számára, hogy a hierarchiát a beágyazássaljelölőss, azaz "" jelöli. a fenti példákban. Az elválasztóval a másolási tevékenység a tábladefinícióban szereplő "Name.First", "Name.Middle" és "Name.Last" (Név) objektumhárom gyermekelemmel hozza létre az Első, a Középső és az Utolsó elemet a tábladefinícióban szereplő "Name.First", "Name.Middle" és "Name.Last" szerint. |Nem |

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

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Az Azure Cosmos DB-gyűjtemény fogadója a másolási tevékenységben
Ha adatokat másol az Azure Cosmos DB-be, állítsa a másolási tevékenység **fogadótípusát** **DocumentDbCollectionSink**-re, és adja meg a következő tulajdonságokat a **fogadó** szakaszban:

| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Szükséges** |
| --- | --- | --- | --- |
| nestingSeparator |A forrásoszlop nevében egy speciális karakter jelzi, hogy beágyazott dokumentumra van szükség. <br/><br/>Például a `Name.First` fenti: a kimeneti táblázat ban a következő JSON-struktúra a Cosmos DB dokumentumban:<br/><br/>"Név": {<br/>    "Első": "John"<br/>}, |A beágyazási szinteket elválasztó karakter.<br/><br/>Az alapértelmezett `.` érték (pont). |A beágyazási szinteket elválasztó karakter. <br/><br/>Az alapértelmezett `.` érték (pont). |
| writeBatchSize |Az Azure Cosmos DB szolgáltatáshoz a dokumentumok létrehozásához szükséges párhuzamos kérelmek száma.<br/><br/>A teljesítmény finomhangolásával adatok másolásakor/ az Azure Cosmos DB-ből ezzel a tulajdonsággal. A writeBatchSize növelése esetén jobb teljesítményre számíthat, mivel az Azure Cosmos DB-nek további párhuzamos kérelmeket küld. Azonban el kell kerülnie a szabályozást, amely a következő hibaüzenetet dobhatja: "A kérelmek aránya nagy".<br/><br/>A szabályozást számos tényező határozza meg, beleértve a dokumentumok méretét, a dokumentumokban lévő kifejezések számát, a célgyűjtés indexelési politikáját stb. Másolási műveletekhez használhatja a jobb gyűjtemény (például S3) a legtöbb átviteli rendelkezésre álló (2500 kérelem egység/másodperc). |Egész szám |Nem (alapértelmezett: 5) |
| writeBatchTimeout |Várjon időt a művelet befejezésére, mielőtt az időtúljárna. |időtartomány<br/><br/> Példa: "00:30:00" (30 perc). |Nem |

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

További információ: [Azure Cosmos DB-összekötő](data-factory-azure-documentdb-connector.md#copy-activity-properties) cikk.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Társított szolgáltatások
Az Azure SQL Database-hez csatolt szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** az **AzureSqlDatabase-re,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| connectionString (kapcsolati karakterlánc) |Adja meg az Azure SQL Database-példányhoz való csatlakozáshoz szükséges információkat a connectionString tulajdonsághoz. |Igen |

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

További információ: [Azure SQL connector](data-factory-azure-sql-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adatkészlet
Az Azure SQL Database-adatkészlet definiálásához állítsa az adatkészlet **típusát** az **AzureSqlTable beállításra,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla vagy nézet neve az Azure SQL Database-példányban, amelyre a csatolt szolgáltatás hivatkozik. |Igen |

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
További információ: [Azure SQL connector](data-factory-azure-sql-connector.md#dataset-properties) cikk.

### <a name="sql-source-in-copy-activity"></a>SQL-forrás a másolási tevékenységben
Ha egy Azure SQL-adatbázisból másol adatokat, állítsa a másolási tevékenység **forrástípusát** **az SqlSource**-ra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Példa: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |Annak a tárolt eljárásnak a neve, amely adatokat olvas be a forrástáblából. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. |Nem |

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
További információ: [Azure SQL connector](data-factory-azure-sql-connector.md#copy-activity-properties) cikk.

### <a name="sql-sink-in-copy-activity"></a>SQL-fogadó a másolási tevékenységben
Ha adatokat másol az Azure SQL Database-be, állítsa a másolási tevékenység **fogadótípusát** **az SqlSink**beállításra, és adja meg a következő tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| writeBatchTimeout |Várjon időt a kötegbehelyezési művelet befejezésére, mielőtt az időtúljárna. |időtartomány<br/><br/> Példa: "00:30:00" (30 perc). |Nem |
| writeBatchSize |Adatokat szúr be az SQL-táblába, amikor a puffer mérete eléri a WriteBatchSize-ot. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |Adjon meg egy lekérdezést a Másolási tevékenységhez, hogy egy adott szelet adatait töröljék. |Lekérdezési utasítás. |Nem |
| sliceIdentifierColumnName |Adja meg az automatikusan létrehozott szeletazonosítóval kitöltendő Másolási tevékenység oszlopnevét, amely egy adott szelet adatainak tisztítására szolgál az újrafuttatáskor. |A bináris adattípusú oszlop oszlopneve [32]. |Nem |
| sqlWriterDProcedureName |Annak a tárolt eljárásnak a neve, amely upserts (updates /inserts) adatokat a céltáblába. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. |Nem |
| sqlWriterTableType típus |Adja meg a tárolt eljárásban használandó táblatípus nevét. A másolási tevékenység lehetővé teszi az áthelyezett adatokat egy ideiglenes táblában ezzel a táblatípussal. A tárolt eljáráskód ezután egyesítheti a másolt adatokat a meglévő adatokkal. |Táblatípus neve. |Nem |

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

További információ: [Azure SQL connector](data-factory-azure-sql-connector.md#copy-activity-properties) cikk.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Társított szolgáltatások
Az Azure SQL Data Warehouse-hoz csatolt szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** az **AzureSqlDW-re,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| connectionString (kapcsolati karakterlánc) |Adja meg az Azure SQL Data Warehouse-példányhoz való csatlakozáshoz szükséges információkat a connectionString tulajdonsághoz. |Igen |



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

További információ: [Azure SQL Data Warehouse connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) article.

### <a name="dataset"></a>Adatkészlet
Az Azure SQL Data Data Warehouse-adatkészlet definiálásához állítsa be az adatkészlet **típusát** **az AzureSqlDWTable beállításra,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla vagy a nézet neve az Azure SQL Data Warehouse adatbázisban, amelyre a csatolt szolgáltatás hivatkozik. |Igen |

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

További információ: [Azure SQL Data Warehouse connector](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) article.

### <a name="sql-dw-source-in-copy-activity"></a>SQL DW-forrás a másolási tevékenységben
Ha adatokat másol az Azure SQL Data Warehouse-ból, állítsa be a másolási tevékenység **forrástípusát** **sqldwsource**-ra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |Annak a tárolt eljárásnak a neve, amely adatokat olvas be a forrástáblából. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. |Nem |

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

További információ: [Azure SQL Data Warehouse connector](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) article.

### <a name="sql-dw-sink-in-copy-activity"></a>SQL DW-fogadó a másolási tevékenységben
Ha adatokat másol az Azure SQL Data Warehouse-ba, állítsa a másolási tevékenység **fogadótípusát** **SqlDWSink**-re, és adja meg a következő tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Adjon meg egy lekérdezést a Másolási tevékenységhez, hogy egy adott szelet adatait töröljék. |Lekérdezési utasítás. |Nem |
| allowPolyBase |Azt jelzi, hogy a POLYBase-t (adott esetben) kell-e használni a BULKINSERT mechanizmus helyett. <br/><br/> **A PolyBase használata az ajánlott módja az adatok SQL Data Warehouse-ba való betöltésének.** |True (Igaz) <br/>False (alapértelmezett) |Nem |
| poliBaseSettings |Tulajdonságok olyan csoportja, amely akkor adható meg, ha az **allowPolybase** tulajdonság **értéke true .** |&nbsp; |Nem |
| rejectValue |Megadja a lekérdezés sikertelensedése előtt elutasítható sorok számát vagy százalékos arányát. <br/><br/>További információ a PolyBase elutasítási beállításairól a [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) témakör **Argumentumok** szakaszában olvashat bővebben. |0 (alapértelmezett), 1, 2, ... |Nem |
| rejectType |Itt adható meg, hogy a rejectValue beállítás literális értékként vagy százalékként legyen megadva. |Érték (alapértelmezett), Százalék |Nem |
| rejectSampleValue |Azt határozza meg, hogy a PolyBase hány sort kell beolvasnia, mielőtt a PolyBase újraszámítja az elutasított sorok százalékos arányát. |1, 2, ... |Igen, ha a **rejectType** **százalék** |
| useTypeDefault |Itt adható meg, hogyan kezelje a tagolt szövegfájlokban hiányzó értékeket, amikor a PolyBase adatokat olvas le a szövegfájlból.<br/><br/>Erről a tulajdonságról a [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)argumentumok szakaszában olvashat bővebben. |Igaz, Hamis (alapértelmezett) |Nem |
| writeBatchSize |Adatok beszúrása az SQL táblába, amikor a puffer mérete eléri a writeBatchSize-ot |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Várjon időt a kötegbehelyezési művelet befejezésére, mielőtt az időtúljárna. |időtartomány<br/><br/> Példa: "00:30:00" (30 perc). |Nem |

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

További információ: [Azure SQL Data Warehouse connector](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) article.

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Társított szolgáltatások
Az Azure Cognitive Search kapcsolt szolgáltatásának definiálásához állítsa be a csatolt szolgáltatás **típusát** az **AzureSearch szolgáltatásra,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| url | A keresési szolgáltatás URL-címe. | Igen |
| kulcs | A keresési szolgáltatás felügyeleti kulcsa. | Igen |

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

További információ: [Azure Cognitive Search-összekötő](data-factory-azure-search-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adatkészlet
Az Azure Cognitive Search-adatkészlet definiálásához állítsa be az adatkészlet **típusát** az **AzureSearchIndex**beállításra, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| type | A típustulajdonságot **az AzureSearchIndex**beállításra kell állítani.| Igen |
| indexName | A keresési index neve. A Data Factory nem hozza létre az indexet. Az indexnek léteznie kell az Azure Cognitive Search szolgáltatásban. | Igen |

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

További információ: [Azure Cognitive Search-összekötő](data-factory-azure-search-connector.md#dataset-properties) cikk.

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Az Azure Cognitive Search Index fogadója a másolási tevékenységben
Ha adatokat másol egy keresési indexbe, állítsa be a másolási tevékenység **fogadótípusát** az **AzureSearchIndexSink**-re, és adja meg a következő tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| -------- | ----------- | -------------- | -------- |
| Írási viselkedés | Itt adható meg, hogy egyesítse vagy cserélje le a programot, ha már létezik dokumentum az indexben. | Egyesítés (alapértelmezett)<br/>Feltöltés| Nem |
| WriteBatchSize írás | Adatokat tölt fel a keresési indexbe, amikor a puffer mérete eléri a writeBatchSize-ot. | 1-ről 1000-re. Az alapértelmezett érték 1000. | Nem |

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

További információ: [Azure Cognitive Search-összekötő](data-factory-azure-search-connector.md#copy-activity-properties) cikk.

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Társított szolgáltatások
A csatolt szolgáltatásoknak két típusa van: az Azure Storage-kapcsolt szolgáltatás és az Azure Storage SAS-alapú szolgáltatás.

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Ha az Azure storage-fiókot egy adat-előállítóhoz szeretné kapcsolni a **fiókkulcs**használatával, hozzon létre egy Azure Storage-kapcsolt szolgáltatást. Azure Storage-hoz csatolt szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** az **AzureStorage-ra.** Ezután megadhatja a következő tulajdonságokat a **típusTulajdonságok** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type |A típustulajdonságot a következőre kell állítani: **AzureStorage** |Igen |
| connectionString (kapcsolati karakterlánc) |Adja meg a connectionString tulajdonság Azure storage-hoz való csatlakozásához szükséges információkat. |Igen |

**Példa:**

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

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS csatolt szolgáltatás
Az Azure Storage SAS-alapú szolgáltatás lehetővé teszi, hogy egy Azure Storage-fiók összekapcsolásával egy Azure-adat-előállító egy megosztott hozzáférésű aláírás (SAS) használatával. Az adat-előállító korlátozott/időhöz kötött hozzáférést biztosít a tárolóban lévő összes/adott erőforrásokhoz (blob/tároló). Ha az Azure storage-fiókját megosztott hozzáférésű aláírással egy adat-előállítóhoz szeretné kapcsolni, hozzon létre egy Azure Storage SAS-kapcsolt szolgáltatást. Azure Storage SAS-kapcsolt szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** az **AzureStorageSas**szolgáltatásra. Ezután megadhatja a következő tulajdonságokat a **típusTulajdonságok** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type |A típustulajdonságot a következőre kell állítani: **AzureStorageSas** |Igen |
| sasUri között |Adja meg a megosztott hozzáférésű aláírás URI-t az Azure Storage-erőforrásokhoz, például blobhoz, tárolóhoz vagy táblához. |Igen |

**Példa:**

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

Ezekről a csatolt szolgáltatásokról további információt az [Azure Table Storage-összekötő ről](data-factory-azure-table-connector.md#linked-service-properties) szóló cikkben talál.

### <a name="dataset"></a>Adatkészlet
Az Azure Table adatkészletének definiálásához állítsa az adatkészlet **típusát** az **AzureTable**beállításra, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve az Azure Table Database-példányban, amelyre a csatolt szolgáltatás hivatkozik. |Igen. Ha egy tableName azureTableSourceQuery nélkül van megadva, a tábla összes rekordja a célhelyre kerül. Ha egy azureTableSourceQuery is meg van adva, a tábla azon rekordjai, amelyek megfelelnek a lekérdezésnek, a rendszer a célhelyre másolja. |

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

Ezekről a csatolt szolgáltatásokról további információt az [Azure Table Storage-összekötő ről](data-factory-azure-table-connector.md#dataset-properties) szóló cikkben talál.

### <a name="azure-table-source-in-copy-activity"></a>Azure-táblaforrás a másolási tevékenységben
Ha adatokat másol az Azure Table Storage-ból, állítsa be a másolási tevékenység **forrástípusát** az **AzureTableSource mezőre,** és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| azureTableSourceQuery |Az adatok olvasásához használja az egyéni lekérdezést. |Azure-tábla lekérdezési karakterlánc. Tekintse meg a következő szakaszban található példákat. |Nem. Ha egy tableName azureTableSourceQuery nélkül van megadva, a tábla összes rekordja a célhelyre kerül. Ha egy azureTableSourceQuery is meg van adva, a tábla azon rekordjai, amelyek megfelelnek a lekérdezésnek, a rendszer a célhelyre másolja. |
| azureTableSourceIgnoreTableNotFound |Adja meg, hogy a tábla kivételének kivétele nem létezik-e. |IGAZ<br/>HAMIS |Nem |

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

Ezekről a csatolt szolgáltatásokról további információt az [Azure Table Storage-összekötő ről](data-factory-azure-table-connector.md#copy-activity-properties) szóló cikkben talál.

### <a name="azure-table-sink-in-copy-activity"></a>Az Azure-tábla fogadója a másolási tevékenységben
Ha adatokat másol az Azure Table Storage-ba, állítsa be a másolási tevékenység **fogadótípusát** az **AzureTableSink**-re, és adja meg a következő tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue érték |A fogadó által használható alapértelmezett partíciókulcs-érték. |Karakterlánc-érték. |Nem |
| azureTablePartitionKeyName |Adja meg annak az oszlopnak a nevét, amelynek értékeit partíciókulcsként használják. Ha nincs megadva, az AzureTableDefaultPartitionKeyValue lesz a partíciókulcs. |Oszlopnév. |Nem |
| azureTableRowKeyName |Adja meg annak az oszlopnak a nevét, amelynek oszlopértékeit sorkulcsként használják. Ha nincs megadva, minden sorhoz használjon GUID azonosítót. |Oszlopnév. |Nem |
| azureTableInsertType |Az Azure-táblába adatok beszúrásának módja.<br/><br/>Ez a tulajdonság azt szabályozza, hogy a kimeneti tábla egyező partícióval és sorkulcsokkal rendelkező meglévő sorai tették-e lecserélve vagy egyesítve a kimeneti tábla meglévő sorait. <br/><br/>Ha többet szeretne tudni arról, hogyan működnek ezek a beállítások (egyesítés e és csere), olvassa el az [Entitás beszúrása vagy egyesítése,](https://msdn.microsoft.com/library/azure/hh452241.aspx) valamint [az Entitás beszúrása vagy cseréje](https://msdn.microsoft.com/library/azure/hh452242.aspx) témakört. <br/><br> Ez a beállítás a sor szintjén érvényes, nem a táblázat szintjén, és egyik lehetőség sem törli a kimeneti tábla olyan sorait, amelyek nem léteznek a bemenetben. |egyesítés (alapértelmezett)<br/>Helyettesít |Nem |
| writeBatchSize |Adatokat szúr be az Azure-táblába, ha a writeBatchSize vagy writeBatchTimeout levan lépve. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Adatok beszúrása az Azure-táblába a writeBatchSize vagy writeBatchTimeout leütése esetén |időtartomány<br/><br/>Példa: "00:20:00" (20 perc) |Nem (Alapértelmezett a tárolóügyfél alapértelmezett időtúlértéke 90 mp) |

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
Ezekről a csatolt szolgáltatásokról további információt az [Azure Table Storage-összekötő ről](data-factory-azure-table-connector.md#copy-activity-properties) szóló cikkben talál.

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Társított szolgáltatások
Amazon Redshift csatolt szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** az **AmazonRedshift**szolgáltatásra, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |Az Amazon Redshift kiszolgáló IP-címe vagy állomásneve. |Igen |
| port |Az Amazon Redshift kiszolgáló által az ügyfélkapcsolatok figyelésére használt TCP-port száma. |Nem, alapértelmezett érték: 5439 |
| adatbázis |Az Amazon Redshift adatbázis neve. |Igen |
| felhasználónév |Az adatbázishoz hozzáféréssel rendelkező felhasználó neve. |Igen |
| jelszó |A felhasználói fiók jelszava. |Igen |

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

További információ: Amazon Redshift connector article.

### <a name="dataset"></a>Adatkészlet
Amazon redshift adatkészlet definiálásához állítsa az adatkészlet típusát **RelationalTable**( kapcsolat ) **beállítására,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A csatolt szolgáltatás által hivatkozott Amazon Redshift adatbázis táblájának neve. |Nem (ha a **RelationalSource** **lekérdezése** meg van adva) |


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
További információ: Amazon Redshift connector article.

### <a name="relational-source-in-copy-activity"></a>Relációs forrás a másolási tevékenységben
Ha az Amazon Redshift-ből másolja az adatokat, állítsa a másolási tevékenység **forrástípusát** **RelationalSource (Reszkeso)** mezőre, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: `select * from MyTable`. |Nem (ha **a tableName** of **dataset** meg van adva) |

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
További információ: Amazon Redshift connector article.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Társított szolgáltatások
IBM DB2 csatolt szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** **az OnPremisesDB2**eszközre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |A DB2 kiszolgáló neve. |Igen |
| adatbázis |A DB2 adatbázis neve. |Igen |
| Séma |A séma neve az adatbázisban. A sémaneve a kis- és nagybetűket nem figyelembe. |Nem |
| authenticationType |A DB2 adatbázishoz való csatlakozáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen, Egyszerű és Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet, ha alapfokú vagy Windows-hitelesítést használ. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni DB2-adatbázishoz való csatlakozáshoz kell használnia. |Igen |

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
További információ: IBM DB2 connector article.

### <a name="dataset"></a>Adatkészlet
Db2 adatkészlet definiálásához állítsa az adatkészlet típusát **RelationalTable**( kapcsolat ) **beállítására,** és adja meg a következő tulajdonságokat a **Típustulajdonságok** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A csatolt szolgáltatás által hivatkozott DB2 adatbázispéldány táblájának neve. A táblanév a kis- és nagybetűk et is figyelembe nem egyezteti. |Nem (ha a **RelationalSource** **lekérdezése** meg van adva)

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

További információ: IBM DB2 connector article.

### <a name="relational-source-in-copy-activity"></a>Relációs forrás a másolási tevékenységben
Ha az IBM DB2 rendszerből másolja az adatokat, állítsa a másolási tevékenység **forrástípusát** **RelationalSource (Reszkesszió)** beállításra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: `"query": "select * from "MySchema"."MyTable""`. |Nem (ha **a tableName** of **dataset** meg van adva) |

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
További információ: IBM DB2 connector article.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Társított szolgáltatások
MySQL csatolt szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** **az OnPremisesMySql**-re, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |A MySQL-kiszolgáló neve. |Igen |
| adatbázis |A MySQL adatbázis neve. |Igen |
| Séma |A séma neve az adatbázisban. |Nem |
| authenticationType |A MySQL-adatbázishoz való csatlakozáshoz használt hitelesítés típusa. Lehetséges értékek: `Basic`. . |Igen |
| userName (Felhasználónév) |Adja meg a MySQL-adatbázishoz való csatlakozáshoz kívánt felhasználónevet. |Igen |
| jelszó |Adja meg a megadott felhasználói fiók jelszavát. |Igen |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni MySQL-adatbázishoz való csatlakozáshoz kell használnia. |Igen |

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

További információ: [MySQL-összekötő](data-factory-onprem-mysql-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adatkészlet
MySQL-adatkészlet definiálásához állítsa az adatkészlet típusát **RelationalTable**( kapcsolat ) **beállítására,** és adja meg a következő tulajdonságokat a **TypeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve a MySQL adatbázis-példányban, amelyre a csatolt szolgáltatás hivatkozik. |Nem (ha a **RelationalSource** **lekérdezése** meg van adva) |

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
További információ: [MySQL-összekötő](data-factory-onprem-mysql-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>Relációs forrás a másolási tevékenységben
Ha MySQL-adatbázisból másol adatokat, állítsa a másolási tevékenység **forrástípusát** **RelationalSource (Reszkeso)-ra,** és adja meg a következő tulajdonságokat a **forrásszakaszban:**


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: `select * from MyTable`. |Nem (ha **a tableName** of **dataset** meg van adva) |


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

További információ: [MySQL-összekötő](data-factory-onprem-mysql-connector.md#copy-activity-properties) cikk.

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Társított szolgáltatások
Oracle-alapú szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** az **OnPremisesOracle**eszközre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| driverType (illesztőprogram típusa) | Adja meg, hogy melyik illesztőprogramot szeretné adatokat másolni/át az Oracle Database-be. Az engedélyezett értékek a **Microsoft** vagy **az ODP** (alapértelmezett). Lásd: A támogatott verzió és telepítés szakasz az illesztőprogram részleteiről. | Nem |
| connectionString (kapcsolati karakterlánc) | Adja meg a connectionString tulajdonság Oracle Database példányához való csatlakozáshoz szükséges adatokat. | Igen |
| átjárónév | A helyszíni Oracle-kiszolgálóhoz való csatlakozáshoz használt átjáró neve |Igen |

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

További információt az [Oracle connector](data-factory-onprem-oracle-connector.md#linked-service-properties) cikkében talál.

### <a name="dataset"></a>Adatkészlet
Oracle adatkészlet definiálásához állítsa az adatkészlet **típusát** **OracleTable**beállításra, és adja meg a következő tulajdonságokat a **TypeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A csatolt szolgáltatás által hivatkozott Oracle Database tábla neve. |Nem (ha meg van adva az **OracleSource oracleReaderQuery** tulajdonsága) **oracleReaderQuery** |

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
További információt az [Oracle connector](data-factory-onprem-oracle-connector.md#dataset-properties) cikkében talál.

### <a name="oracle-source-in-copy-activity"></a>Oracle-forrás a másolási tevékenységben
Ha Oracle-adatbázisból másol adatokat, állítsa a másolási tevékenység **forrástípusát** **oraclesource**mezőre, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| oracleReaderQuery |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például:`select * from MyTable` <br/><br/>Ha nincs megadva, a végrehajtásra kerül az SQL utasítás:`select * from MyTable` |Nem (ha **a tableName** of **dataset** meg van adva) |

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

További információt az [Oracle connector](data-factory-onprem-oracle-connector.md#copy-activity-properties) cikkében talál.

### <a name="oracle-sink-in-copy-activity"></a>Oracle mosogató másolási tevékenység
Ha adatokat másol az Am Oracle adatbázisba, állítsa a másolási tevékenység **fogadótípusát** OracleSink ( **másolási**tevékenység ) beállítására, és adja meg a következő tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| writeBatchTimeout |Várjon időt a kötegbehelyezési művelet befejezésére, mielőtt az időtúljárna. |időtartomány<br/><br/> Példa: 00:30:00 (30 perc). |Nem |
| writeBatchSize |Adatokat szúr be az SQL-táblába, amikor a puffer mérete eléri a WriteBatchSize-ot. |Egész szám (sorok száma) |Nem (alapértelmezett: 100) |
| sqlWriterCleanupScript |Adjon meg egy lekérdezést a Másolási tevékenységhez, hogy egy adott szelet adatait töröljék. |Lekérdezési utasítás. |Nem |
| sliceIdentifierColumnName |Adja meg az oszlop nevét az automatikusan létrehozott szeletazonosítóval kitöltendő Másolási tevékenységhez, amely egy adott szelet adatainak tisztítására szolgál az újrafuttatáskor. |A bináris adattípusú oszlop oszlopneve [32]. |Nem |

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
További információt az [Oracle connector](data-factory-onprem-oracle-connector.md#copy-activity-properties) cikkében talál.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Társított szolgáltatások
PostgreSQL csatolt szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** az **OnPremisesPostgreSql -re,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |A PostgreSQL kiszolgáló neve. |Igen |
| adatbázis |A PostgreSQL adatbázis neve. |Igen |
| Séma |A séma neve az adatbázisban. A sémaneve a kis- és nagybetűket nem figyelembe. |Nem |
| authenticationType |A PostgreSQL adatbázishoz való csatlakozáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen, Egyszerű és Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet, ha alapfokú vagy Windows-hitelesítést használ. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni PostgreSQL-adatbázishoz való csatlakozáshoz kell használnia. |Igen |

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
További információ: [PostgreSQL-összekötő](data-factory-onprem-postgresql-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adatkészlet
PostgreSQL-adatkészlet definiálásához állítsa az adatkészlet típusát **RelationalTable**( kapcsolat ) **beállítására,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve a PostgreSQL adatbázis-példányban, amelyre a csatolt szolgáltatás hivatkozik. A táblanév a kis- és nagybetűk et is figyelembe nem egyezteti. |Nem (ha a **RelationalSource** **lekérdezése** meg van adva) |

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
További információ: [PostgreSQL-összekötő](data-factory-onprem-postgresql-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>Relációs forrás a másolási tevékenységben
Ha PostgreSQL-adatbázisból másol adatokat, állítsa a másolási tevékenység **forrástípusát** **RelationalSource (Reszkeso)** mezőre, és adja meg a következő tulajdonságokat a **forrásszakaszban:**


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: "lekérdezés": "válassza \"a *\"elemet a MySchema-ból . \"MyTable\"". |Nem (ha **a tableName** of **dataset** meg van adva) |

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

További információ: [PostgreSQL-összekötő](data-factory-onprem-postgresql-connector.md#copy-activity-properties) cikk.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Társított szolgáltatások
Sap Business Warehouse (BW) kapcsolt szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** **sapbw-re,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
kiszolgáló | Annak a kiszolgálónak a neve, amelyen az SAP BW-példány található. | sztring | Igen
systemNumber (rendszerszám) | Az SAP BW rendszer rendszerszáma. | Kétjegyű tizedesszám karakterláncként. | Igen
ügyfél-azonosító | Az SAP W rendszerben lévő ügyfél ügyfélazonosítója. | Háromjegyű decimális szám karakterláncként. | Igen
felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Igen
jelszó | A felhasználó jelszava. | sztring | Igen
átjárónév | Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni SAP BW-példányhoz való csatlakozáshoz kell használnia. | sztring | Igen
titkosított hitelesítő adatok | A titkosított hitelesítő adatok karakterlánca. | sztring | Nem

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

További információ: [SAP Business Warehouse összekötő](data-factory-sap-business-warehouse-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adatkészlet
SAP BW adatkészlet definiálásához állítsa az adatkészlet típusát RelationalTable .To define a SAP BW dataset, set the **type** of the dataset to **RelationalTable**. A **RelationalTable**típusú SAP BW adatkészlethez nem támogatottak típusspecifikus tulajdonságok.

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
További információ: [SAP Business Warehouse összekötő](data-factory-sap-business-warehouse-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>Relációs forrás a másolási tevékenységben
Ha az SAP Business Warehouse-ból másolja az adatokat, állítsa a másolási tevékenység **forrástípusát** **RelationalSource (Reszkeso)** mezőre, és adja meg a következő tulajdonságokat a **forrásszakaszban:**


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés | Megadja az SAP BW-példányból adatokat beolvasandó MDX-lekérdezést. | MDX lekérdezés. | Igen |

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

További információ: [SAP Business Warehouse összekötő](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) cikk.

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Társított szolgáltatások
SAP HANA csatolt szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** **saphana**, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
kiszolgáló | Annak a kiszolgálónak a neve, amelyen az SAP HANA-példány található. Ha a kiszolgáló testreszabott portot `server:port`használ, adja meg a megadását. | sztring | Igen
authenticationType | A hitelesítés típusa. | Karakterlánc. "Alap" vagy "Windows" | Igen
felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Igen
jelszó | A felhasználó jelszava. | sztring | Igen
átjárónév | Az átjáró neve, amelyet a Data Factory szolgáltatásnak a helyszíni SAP HANA-példányhoz való csatlakozáshoz kell használnia. | sztring | Igen
titkosított hitelesítő adatok | A titkosított hitelesítő adatok karakterlánca. | sztring | Nem

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
További információ: [SAP HANA-összekötő](data-factory-sap-hana-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adatkészlet
SAP HANA adatkészlet definiálásához állítsa az adatkészlet típusát RelationalTable .To define a SAP HANA dataset, set the **type** of the dataset to **RelationalTable**. A **RelationalTable**típusú SAP HANA adatkészlethez nem támogatottak típusspecifikus tulajdonságok.

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
További információ: [SAP HANA-összekötő](data-factory-sap-hana-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>Relációs forrás a másolási tevékenységben
Ha SAP HANA-adattárból másol adatokat, állítsa a másolási tevékenység **forrástípusát** **RelationalSource (Reszkeso-forrás)** beállításra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés | Megadja az SAP HANA-példány ból adatokat olvasandó SQL-lekérdezést. | SQL-lekérdezés. | Igen |


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

További információ: [SAP HANA-összekötő](data-factory-sap-hana-connector.md#copy-activity-properties) cikk.


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Társított szolgáltatások
Hozzon létre egy **onPremisesSqlServer** típusú kapcsolt szolgáltatást, amely egy helyszíni SQL Server-adatbázist egy adat-előállítóhoz kapcsol. Az alábbi táblázat a helyszíni SQL Server-hivatkozott szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

Az alábbi táblázat az SQL Server csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságot a következőre kell állítani: **OnPremisesSqlServer**. |Igen |
| connectionString (kapcsolati karakterlánc) |Adja meg a helyszíni SQL Server adatbázishoz SQL-hitelesítés vagy Windows-hitelesítés használatával történő csatlakozáshoz szükséges connectionString-adatokat. |Igen |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni SQL Server-adatbázishoz való csatlakozáshoz használnia kell. |Igen |
| felhasználónév |Ha Windows-hitelesítést használ, adja meg a felhasználónevet. Példa: **\\tartománynév felhasználóneve**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |

A hitelesítő adatokat a **New-AzDataFactoryEncryptValue** parancsmag használatával titkosíthatja, és a kapcsolati karakterláncban használhatja, ahogy az a következő példában (**EncryptedCredential** tulajdonság) látható:

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Példa: JSON az SQL-hitelesítés hez

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
#### <a name="example-json-for-using-windows-authentication"></a>Példa: JSON a Windows-hitelesítés hez

Ha meg van adva a felhasználónév és a jelszó, az átjáró azokat használja a megadott felhasználói fiók megszemélyesítésére a helyszíni SQL Server-adatbázishoz való csatlakozáshoz. Ellenkező esetben az átjáró közvetlenül csatlakozik az SQL Serverhez az Átjáró (indítófiókja) biztonsági környezetével.

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

További információt az [SQL Server-összekötőről](data-factory-sqlserver-connector.md#linked-service-properties) szóló cikkben talál.

### <a name="dataset"></a>Adatkészlet
SQL Server adatkészlet definiálásához állítsa az adatkészlet **típusát** **sqlServerTable**, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla vagy nézet neve a csatolt szolgáltatás által hivatkozott SQL Server Database-példányban. |Igen |

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

További információt az [SQL Server-összekötőről](data-factory-sqlserver-connector.md#dataset-properties) szóló cikkben talál.

### <a name="sql-source-in-copy-activity"></a>Sql forrás a másolási tevékenységben
Ha SQL Server-adatbázisból másol adatokat, állítsa a másolási tevékenység **forrástípusát** **sqlsource**-ra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: `select * from MyTable`. A bemeneti adatkészlet által hivatkozott adatbázisból több táblára is hivatkozhat. Ha nincs megadva, a végrehajtásra kerülő SQL utasítás: válassza ki a MyTable elemet. |Nem |
| sqlReaderStoredProcedureName |Annak a tárolt eljárásnak a neve, amely adatokat olvas be a forrástáblából. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. |Nem |

Ha az **sqlReaderQuery** meg van adva az SqlSource-hoz, a Másolási tevékenység futtatja ezt a lekérdezést az SQL Server Database forrásával az adatok lekérni.

Másik lehetőségként megadhatja a tárolt eljárást az **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paramétereket vesz igénybe).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName értéket, a struktúraszakaszban definiált oszlopok segítségével az SQL Server adatbázison futtatandó választó lekérdezést hoz létre. Ha az adatkészlet-definíció nem rendelkezik a szerkezettel, akkor a program az összes oszlopot kijelöli a táblából.

> [!NOTE]
> Az **sqlReaderStoredProcedureName**használatakor továbbra is meg kell adnia egy értéket a **TableName** tulajdonsághoz a JSON adatkészletben. Nincsenek érvényesítések végzett ezen a táblán mégis.


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

Ebben a példában az **sqlReaderQuery** az SqlSource-hoz van megadva. A Másolási tevékenység futtatja ezt a lekérdezést az SQL Server adatbázis forrásában az adatok lekérdezéséhez. Másik lehetőségként megadhatja a tárolt eljárást az **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paramétereket vesz igénybe). Az sqlReaderQuery a bemeneti adatkészlet által hivatkozott adatbázison belül több táblára is hivatkozhat. Ez nem korlátozódik csak a táblakészlet, mint az adatkészlet tableName typeProperty.

Ha nem adja meg az sqlReaderQuery vagy az sqlReaderStoredProcedureName értéket, a struktúraszakaszban definiált oszlopok segítségével az SQL Server-adatbázison futtatandó választó lekérdezést hoz létre. Ha az adatkészlet-definíció nem rendelkezik a szerkezettel, akkor a program az összes oszlopot kijelöli a táblából.

További információt az [SQL Server-összekötőről](data-factory-sqlserver-connector.md#copy-activity-properties) szóló cikkben talál.

### <a name="sql-sink-in-copy-activity"></a>Sql-fogadó a másolási tevékenységben
Ha SQL Server-adatbázisba másol adatokat, állítsa a másolási tevékenység **fogadótípusát** **sqlsink**re, és adja meg a következő tulajdonságokat a **fogadó** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| writeBatchTimeout |Várjon időt a kötegbehelyezési művelet befejezésére, mielőtt az időtúljárna. |időtartomány<br/><br/> Példa: "00:30:00" (30 perc). |Nem |
| writeBatchSize |Adatokat szúr be az SQL-táblába, amikor a puffer mérete eléri a WriteBatchSize-ot. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |Adja meg a lekérdezést a Másolási tevékenységhez, hogy egy adott szelet adatait töröljék. További információ: Ismételhetőségi szakasz. |Lekérdezési utasítás. |Nem |
| sliceIdentifierColumnName |Adja meg az oszlop nevét az automatikusan létrehozott szeletazonosítóval kitöltendő Másolási tevékenységhez, amely egy adott szelet adatainak tisztítására szolgál az újrafuttatáskor. További információ: Ismételhetőségi szakasz. |A bináris adattípusú oszlop oszlopneve [32]. |Nem |
| sqlWriterDProcedureName |Annak a tárolt eljárásnak a neve, amely upserts (updates /inserts) adatokat a céltáblába. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. |Nem |
| sqlWriterTableType típus |Adja meg a tárolt eljárásban használandó táblatípus nevét. A másolási tevékenység lehetővé teszi az áthelyezett adatokat egy ideiglenes táblában ezzel a táblatípussal. A tárolt eljáráskód ezután egyesítheti a másolt adatokat a meglévő adatokkal. |Táblatípus neve. |Nem |

#### <a name="example"></a>Példa
A folyamat tartalmaz egy másolási tevékenységet, amely úgy van beállítva, hogy használja ezeket a bemeneti és kimeneti adatkészletek, és az ütemezés szerint óránként fut. A folyamat JSON-definíciójában a **forrástípus** **BlobSource** lesz állítva, **a fogadó** típusa pedig **SqlSink**.

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

További információt az [SQL Server-összekötőről](data-factory-sqlserver-connector.md#copy-activity-properties) szóló cikkben talál.

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Társított szolgáltatások
Sybase csatolt szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** **az OnPremisesSybase -re,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |A Sybase-kiszolgáló neve. |Igen |
| adatbázis |A Sybase adatbázis neve. |Igen |
| Séma |A séma neve az adatbázisban. |Nem |
| authenticationType |A Sybase adatbázishoz való csatlakozáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen, Egyszerű és Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet, ha alapfokú vagy Windows-hitelesítést használ. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni Sybase-adatbázishoz való csatlakozáshoz kell használnia. |Igen |

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

További információ: [Sybase connector](data-factory-onprem-sybase-connector.md#linked-service-properties) article.

### <a name="dataset"></a>Adatkészlet
Sybase adatkészlet definiálásához állítsa az adatkészlet típusát **RelationalTable**( kapcsolat ) **beállítására,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve a Sybase adatbázis példányban, amelyre a csatolt szolgáltatás hivatkozik. |Nem (ha a **RelationalSource** **lekérdezése** meg van adva) |

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

További információ: [Sybase connector](data-factory-onprem-sybase-connector.md#dataset-properties) article.

### <a name="relational-source-in-copy-activity"></a>Relációs forrás a másolási tevékenységben
Ha Sybase-adatbázisból másol adatokat, állítsa a másolási tevékenység **forrástípusát** **RelationalSource (Reszkesszió)** beállításra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: `select * from MyTable`. |Nem (ha **a tableName** of **dataset** meg van adva) |

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

További információ: [Sybase connector](data-factory-onprem-sybase-connector.md#copy-activity-properties) article.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Társított szolgáltatások
Teradata csatolt szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** **az OnPremisesTeradata mezőre,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |A Teradata-kiszolgáló neve. |Igen |
| authenticationType |A Teradata adatbázishoz való csatlakozáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen, Egyszerű és Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet, ha alapfokú vagy Windows-hitelesítést használ. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni Teradata-adatbázishoz való csatlakozáshoz kell használnia. |Igen |

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

További információ: [Teradata connector](data-factory-onprem-teradata-connector.md#linked-service-properties) article.

### <a name="dataset"></a>Adatkészlet
Teradata Blob-adatkészlet definiálásához állítsa az adatkészlet típusát **RelationalTable (RelationalTable)** **beállításra.** Jelenleg nincsenek a Teradata adatkészlet hez támogatott típustulajdonságok.

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

További információ: [Teradata connector](data-factory-onprem-teradata-connector.md#dataset-properties) article.

### <a name="relational-source-in-copy-activity"></a>Relációs forrás a másolási tevékenységben
Ha Teradata-adatbázisból másol adatokat, állítsa a másolási tevékenység **forrástípusát** **RelationalSource (Reszkesszió)** beállításra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: `select * from MyTable`. |Igen |

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

További információ: [Teradata connector](data-factory-onprem-teradata-connector.md#copy-activity-properties) article.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Társított szolgáltatások
Cassandra csatolt szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** **az OnPremisesCassandra**mezőre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| gazda |Cassandra kiszolgálók egy vagy több IP-címe vagy állomásneve.<br/><br/>Adja meg az IP-címek vagy állomásnevek vesszővel tagolt listáját, hogy az összes kiszolgálóhoz egyidejűleg kapcsolódhassanak. |Igen |
| port |A Cassandra-kiszolgáló által az ügyfélkapcsolatok figyelésére használt TCP-port. |Nem, alapértelmezett érték: 9042 |
| authenticationType |Alapszintű vagy Névtelen |Igen |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha az authenticationType alapszintű. |
| jelszó |Adja meg a felhasználói fiók jelszavát. |Igen, ha az authenticationType alapszintű. |
| átjárónév |A helyszíni Cassandra-adatbázishoz való csatlakozáshoz használt átjáró neve. |Igen |
| titkosított hitelesítő adatok |Az átjáró által titkosított hitelesítő adat. |Nem |

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

További információ: [Cassandra connector](data-factory-onprem-cassandra-connector.md#linked-service-properties) article.

### <a name="dataset"></a>Adatkészlet
Cassandra adatkészlet definiálásához állítsa az adatkészlet típusát **CassandraTable**( CassandraTable ) **beállításra,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kulcstér |A Cassandra adatbázis kulcsterének vagy sémának a neve. |Igen (Ha a **CassandraSource** **lekérdezése** nincs definiálva). |
| tableName |A Cassandra adatbázis táblájának neve. |Igen (Ha a **CassandraSource** **lekérdezése** nincs definiálva). |

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

További információ: [Cassandra connector](data-factory-onprem-cassandra-connector.md#dataset-properties) article.

### <a name="cassandra-source-in-copy-activity"></a>Cassandra forrás másolási tevékenységben
Ha Cassandra adatait másolja, állítsa a másolási tevékenység **forrástípusát** **cassandraSource**,, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL-92 lekérdezés vagy CQL-lekérdezés. Lásd [cql hivatkozás](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL-lekérdezés használatakor adja meg **a keyspace name.table nevet** a lekérdezni kívánt tábla ábrázolására. |Nem (ha a tableName és a keyspace az adatkészleten definiálva van). |
| konzisztenciaSzint |A konzisztenciaszint azt határozza meg, hogy hány replikának kell válaszolnia egy olvasási kérelemre, mielőtt adatokat ad vissza az ügyfélalkalmazásnak. Cassandra ellenőrzi a megadott számú replikák adatok at az olvasási kérelem teljesítéséhez. |EGY, KETTŐ, HÁROM, KVÓRUM, MINDEN, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. A részleteket az [Adatkonzisztencia konfigurálása](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) című témakörben találja. |Nem. Az alapértelmezett érték ONE. |

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

További információ: [Cassandra connector](data-factory-onprem-cassandra-connector.md#copy-activity-properties) article.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Társított szolgáltatások
MongoDB csatolt szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** **az OnPremisesMongoDB-ra,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kiszolgáló |A MongoDB-kiszolgáló IP-címe vagy állomásneve. |Igen |
| port |TCP-port, amelyet a MongoDB-kiszolgáló az ügyfélkapcsolatok figyelésére használ. |Nem kötelező, alapértelmezett érték: 27017 |
| authenticationType |Alapszintű vagy Névtelen. |Igen |
| felhasználónév |Felhasználói fiók a MongoDB eléréséhez. |Igen (ha egyszerű hitelesítést használ). |
| jelszó |A felhasználó jelszava. |Igen (ha egyszerű hitelesítést használ). |
| authForrás |A hitelesítő adatok hitelesítéséhez használni kívánt MongoDB-adatbázis neve. |Nem kötelező (ha alapfokú hitelesítést használ). default: a rendszergazdai fiókot és az databaseName tulajdonsággal megadott adatbázist használja. |
| adatbázisneve |Az elérni kívánt MongoDB-adatbázis neve. |Igen |
| átjárónév |Az adattárhoz hozzáférő átjáró neve. |Igen |
| titkosított hitelesítő adatok |Az átjáró által titkosított hitelesítő adat. |Optional |

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

További információ: [MongoDB connector article](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Adatkészlet
MongoDB adatkészlet definiálásához állítsa az adatkészlet típusát **MongoDbCollection (MongoDbCollection)** **beállításra,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| Lekérdezés_neve |A gyűjtemény neve a MongoDB adatbázisban. |Igen |

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

További információ: [MongoDB connector article](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>MongoDB forrás másolási tevékenységben
Ha adatokat másol a MongoDB-ból, állítsa a másolási tevékenység **forrástípusát** a **MongoDbSource**mezőre, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL-92 lekérdezési karakterlánc. Például: `select * from MyTable`. |Nem (ha **gyűjteményAz** **adatkészlet** neve meg van adva) |

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

További információ: [MongoDB connector article](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Társított szolgáltatások
Amazon S3 csatolt szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** **AwsAccessKey**-re , és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| accessKeyID azonosító |A titkos hozzáférési kulcs azonosítója. |sztring |Igen |
| secretAccessKey |Maga a titkos hozzáférési kulcs. |Titkosított titkos karakterlánc |Igen |

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

További információ: [Amazon S3 connector article](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Adatkészlet
Amazon S3 adatkészlet definiálásához állítsa az adatkészlet **típusát** **az AmazonS3**-ra, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| bucketName (vödörneve) |Az S3 gyűjtő neve. |Sztring |Igen |
| kulcs |Az S3 objektumkulcs. |Sztring |Nem |
| Előtag |Az S3 objektumkulcs előtagja. Azok az objektumok, amelyek billentyűi ezzel az előtaggal kezdődnek, ki vannak jelölve. Csak akkor érvényes, ha a kulcs üres. |Sztring |Nem |
| version |Az S3 objektum verziója, ha az S3 verziószámozás engedélyezve van. |Sztring |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](data-factory-supported-file-and-compression-formats.md#text-format) [a Json formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [az Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [az Orc Formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [Parkettaformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. <br><br> Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. |Nem | |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**. A támogatott szintek a következők: **Optimális** és **leggyorsabb.** További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem | |


> [!NOTE]
> bucketName + kulcs az S3 objektum helyét adja meg, ahol a gyűjtő az S3 objektumok gyökértárolója, a kulcs pedig az S3 objektum teljes elérési útja.

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
#### <a name="example-sample-data-set-with-version"></a>Példa: Mintaadatkészlet (verzióval)

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

#### <a name="example-dynamic-paths-for-s3"></a>Példa: Dinamikus elérési utak az S3-hoz
A mintában rögzített értékeket használunk az Amazon S3 adatkészlet kulcs- és bucketName tulajdonságaihoz.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Beállíthatja, hogy a Data Factory futásidőben dinamikusan számítsa ki a kulcsot és a bucketName-t olyan rendszerváltozók használatával, mint például a SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Ugyanezt teheti meg az Amazon S3 adatkészlet előtag-tulajdonságával is. A támogatott függvények és változók listáját a [Data Factory függvények és rendszerváltozók](data-factory-functions-variables.md) című témakörben található.

További információ: [Amazon S3 connector article](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Fájlrendszer forrása a másolási tevékenységben
Ha az Amazon S3-ról másol adatokat, állítsa be a másolási tevékenység **forrástípusát** a **FileSystemSource**mezőre, és adja meg a következő tulajdonságokat a **forrásszakaszban:**


| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| Rekurzív |Itt adható meg, hogy rekurzív módon felsoroljon-e S3 objektumokat a könyvtár alatt. |igaz/hamis |Nem |


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

További információ: [Amazon S3 connector article](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Fájlrendszer


### <a name="linked-service"></a>Társított szolgáltatások
A helyszíni fájlrendszert összekapcsolhatja egy Azure-adat-előállítóval a **helyszíni fájlkiszolgálóhoz** csatolt szolgáltatással. Az alábbi táblázat a helyszíni fájlkiszolgálóhoz csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |Győződjön meg arról, hogy a típustulajdonság **OnPremisesFileServer**. |Igen |
| gazda |A másolni kívánt mappa gyökérelérési útját adja meg. Használja a " \ ' escape karaktert a karakterlánc speciális karaktereihez. Példákat lásd: Minta csatolt szolgáltatás és adatkészlet-definíciók. |Igen |
| Userid |Adja meg annak a felhasználónak az azonosítóját, aki hozzáfér a kiszolgálóhoz. |Nem (ha a encryptedCredential lehetőséget választja) |
| jelszó |Adja meg a felhasználó jelszavát (userid). |Nem (ha a encryptedCredential (titkosított hitelesítő adat) lehetőséget választja |
| titkosított hitelesítő adatok |Adja meg a New-AzDataFactoryEncryptValue parancsmag futtatásával beszerezhető titkosított hitelesítő adatokat. |Nem (ha úgy dönt, hogy a használati parancsot és a jelszót egyszerű szövegként adja meg) |
| átjárónév |Megadja annak az átjárónak a nevét, amelyet a Data Factory a helyszíni fájlkiszolgálóhoz való csatlakozáshoz kell használnia. |Igen |

#### <a name="sample-folder-path-definitions"></a>Mintamappa elérési útjai

| Forgatókönyv | Állomás a csatolt szolgáltatásdefinícióban | folderPath az adatkészlet definíciójában |
| --- | --- | --- |
| Helyi mappa az adatkezelési átjárógépen: <br/><br/>Példák:\\ \* D: vagy D:\folder\almappa\\* |D:\\ \\ (adatkezelési átjáró 2.0-s és újabb verzióihoz) <br/><br/> localhost (korábbi verziókhoz, mint az Adatkezelési átjáró 2.0) |. vagy\\\\mappaalmappa (Adatkezelési átjáró 2.0-s és újabb verzióihoz) \\ \\ <br/><br/>D:\\ \\ vagy\\\\D: mappa\\\\almappa (2.0 alatti átjáróverzió esetén) |
| Távoli megosztott mappa: <br/><br/>\\ \\Példák:\\myserver \\ \\megosztás\\\\ \* \\vagy\\myserver megosztási mappa almappája\\* |\\\\\\\\myserver\\\\megosztás |. vagy\\\\mappa almappája \\ \\ |


#### <a name="example-using-username-and-password-in-plain-text"></a>Példa: Felhasználónév és jelszó használata egyszerű szövegben

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

#### <a name="example-using-encryptedcredential"></a>Példa: Titkosított hitelesítő adatok használata

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

További információt a [Fájlrendszer-összekötő ről szóló cikkben](data-factory-onprem-file-system-connector.md#linked-service-properties)talál.

### <a name="dataset"></a>Adatkészlet
Fájlrendszer-adatkészlet definiálásához állítsa az adatkészlet típusát **FileShare (Fájlmegosztás**) **beállításra,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |Megadja a mappa részelérési útját. Használja a '\' escape karaktert a karakterlánc speciális karaktereihez. Példákat lásd: Minta csatolt szolgáltatás és adatkészlet-definíciók.<br/><br/>Ezt a tulajdonságot kombinálhatja **a partitionBy tulajdonsággal,** hogy a mappaelérési utak a szelet kezdő/záró dátum-időpontja alapján legyenek. |Igen |
| fileName |Ha azt szeretné, hogy a táblázat a mappában lévő fájlra hivatkozzon, adja meg a fájl nevét a **folder-ban.** Ha nem ad meg értéket ehhez a tulajdonsághoz, a táblázat a mappában lévő összes fájlra mutat.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve a következő formátumú: <br/><br/>`Data.<Guid>.txt`(Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nem |
| fileFilter |Adjon meg egy szűrőt, amelyet a mappapatikus ban lévő fájlok egy részhalmazának kijelölésére, nem pedig az összes fájlhoz kell használni. <br/><br/>Az engedélyezett `*` értékek a következők: (több karakter) és `?` (egy karakter).<br/><br/>1. példa: "fileFilter": "*.log"<br/>2. példa: "fileFilter": 2016-1-?. txt"<br/><br/>Ne feledje, hogy a fileFilter bemeneti FileShare adatkészletesetén alkalmazható. |Nem |
| particionáltby |A partitionedBy segítségével dinamikus folderPath/fileName mappát adhat meg az idősorozat-adatokhoz. Egy példa folderPath paraméterezett minden óra az adatok. |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](data-factory-supported-file-and-compression-formats.md#text-format) [a Json formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [az Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [az Orc Formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [Parkettaformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. <br><br> Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**; és támogatott szintek a következők: **Optimális** és **leggyorsabb.** Lásd: [Fájl- és tömörítési formátumok az Azure Data Factoryban.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |

> [!NOTE]
> A fájlnév és a fájlszűrő nem használható egyszerre.

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

További információt a [Fájlrendszer-összekötő ről szóló cikkben](data-factory-onprem-file-system-connector.md#dataset-properties)talál.

### <a name="file-system-source-in-copy-activity"></a>Fájlrendszer forrása a másolási tevékenységben
Ha adatokat másol a Fájlrendszerből, állítsa a másolási tevékenység **forrástípusát** **a FileSystemSource**mezőre, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. |Igaz, Hamis (alapértelmezett) |Nem |

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
További információt a [Fájlrendszer-összekötő ről szóló cikkben](data-factory-onprem-file-system-connector.md#copy-activity-properties)talál.

### <a name="file-system-sink-in-copy-activity"></a>Fájlrendszer-fogadó a másolási tevékenységben
Ha adatokat másol a Fájlrendszerbe, állítsa a másolási tevékenység **fogadótípusát** **a FileSystemSink (Fájlkezelő)** mezőre, és adja meg a következő tulajdonságokat a **fogadószakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| copyBehavior (Másként) |A másolási viselkedést határozza meg, ha a forrás BlobSource vagy FileSystem. |**Hierarchia megőrzése:** Megőrzi a fájlhierarchiát a célmappában. Ez azt illeti, a forrásfájl relatív elérési útja a forrásmappához megegyezik a célfájl célmappához való relatív elérési útvonalával.<br/><br/>**Hierarchia összeolvasztása:** A forrásmappából származó összes fájl a célmappa első szintjén jön létre. A célfájlok automatikusan létrehozott névvel jönnek létre.<br/><br/>**MergeFiles:** A forrásmappából származó összes fájl egyesítése egy fájlba. Ha meg van adva a fájlnév/blobnév, az egyesített fájlnév a megadott név. Ellenkező esetben ez egy automatikusan generált fájlnév. |Nem |

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

További információt a [Fájlrendszer-összekötő ről szóló cikkben](data-factory-onprem-file-system-connector.md#copy-activity-properties)talál.

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Társított szolgáltatások
FTP-kapcsolattal ellátott szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** **FtpServer**kiszolgálóra, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| gazda |Az FTP-kiszolgáló neve vagy IP-címe |Igen |&nbsp; |
| authenticationType |A hitelesítés típusának megadása |Igen |Alapszintű, Névtelen |
| felhasználónév |Az FTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Nem |&nbsp; |
| jelszó |A felhasználó jelszava (felhasználónév) |Nem |&nbsp; |
| titkosított hitelesítő adatok |Titkosított hitelesítő adat az FTP-kiszolgáló eléréséhez |Nem |&nbsp; |
| átjárónév |A helyszíni FTP-kiszolgálóhoz való csatlakozáshoz szolgáló adatkezelési átjáró neve |Nem |&nbsp; |
| port |Az a port, amelyen az FTP-kiszolgáló figyel |Nem |21 |
| enableSsl |Adja meg, hogy az FTP-t SSL/TLS csatornán keresztül szeretné-e használni |Nem |igaz |
| enableServerCertificateValidation |Adja meg, hogy engedélyezi-e a kiszolgáló TLS/SSL tanúsítványának érvényesítését AZ FTP SSL/TLS csatornán keresztüli használata esetén |Nem |igaz |

#### <a name="example-using-anonymous-authentication"></a>Példa: Névtelen hitelesítés használata

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

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Példa: Felhasználónév és jelszó használata egyszerű szövegként az egyszerű hitelesítéshez

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

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Példa: Port használatával enableSsl, enableServerCertificateValidation

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

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Példa: Titkosítotthitelesítő adatok használata hitelesítéshez és átjáróhoz

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

További információt az [FTP-összekötőről](data-factory-ftp-connector.md#linked-service-properties) szóló cikkben talál.

### <a name="dataset"></a>Adatkészlet
FTP-adatkészlet definiálásához állítsa az adatkészlet típusát **FileShare (Fájlmegosztás**) **beállításra,** és adja meg a következő tulajdonságokat a **TypeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A mappa alelérési útja. A karakterlánc speciális karaktereihez használja a ' \ ' escape karaktert. Példákat lásd: Minta csatolt szolgáltatás és adatkészlet-definíciók.<br/><br/>Ezt a tulajdonságot kombinálhatja **a partitionBy tulajdonsággal,** hogy a mappaelérési utak a szelet kezdő/záró dátum-időpontja alapján legyenek. |Igen
| fileName |Ha azt szeretné, hogy a táblázat a mappában lévő fájlra hivatkozzon, adja meg a fájl nevét a **folder-ban.** Ha nem ad meg értéket ehhez a tulajdonsághoz, a táblázat a mappában lévő összes fájlra mutat.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve a következő formátumban lesz: <br/><br/>`Data.<Guid>.txt`(Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nem |
| fileFilter |Adjon meg egy szűrőt, amelyet a mappapatikus ban lévő fájlok egy részhalmazának kijelölésére, nem pedig az összes fájlhoz kell használni.<br/><br/>Az engedélyezett `*` értékek a következők: (több karakter) és `?` (egy karakter).<br/><br/>1. példa:`"fileFilter": "*.log"`<br/>2. példa:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter bemeneti FileShare adatkészletesetén alkalmazható. Ez a tulajdonság nem támogatott a HDFS. |Nem |
| particionáltby |partitionedBy használható dinamikus folderPath, fájlnév az idősorozat adatait. Például folderPath paraméterezett minden óra az adatok. |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](data-factory-supported-file-and-compression-formats.md#text-format) [a Json formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [az Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [az Orc Formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [Parkettaformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. <br><br> Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**; és támogatott szintek a következők: **Optimális** és **leggyorsabb.** További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |
| useBinaryTransfer |Adja meg, hogy bináris átviteli módot használ-e. Igaz a bináris mód és a hamis ASCII. Alapértelmezett érték: Igaz. Ez a tulajdonság csak akkor használható, ha a csatolt szolgáltatás típusa FtpServer típusú. |Nem |

> [!NOTE]
> a fájlnév és a fájlszűrő nem használható egyszerre.

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

További információt az [FTP-összekötőről](data-factory-ftp-connector.md#dataset-properties) szóló cikkben talál.

### <a name="file-system-source-in-copy-activity"></a>Fájlrendszer forrása a másolási tevékenységben
Ha FTP-kiszolgálóról másol adatokat, állítsa a másolási tevékenység **forrástípusát** **filesystemsource**-ra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasása rekurzív an- vagy csak a megadott mappából történik. |Igaz, Hamis (alapértelmezett) |Nem |

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

További információt az [FTP-összekötőről](data-factory-ftp-connector.md#copy-activity-properties) szóló cikkben talál.


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Társított szolgáltatások
HdFS-alapú szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** **hdfs-re,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságnak a következő re van állítva: **Hdfs** |Igen |
| URL-cím |A HDFS URL-címe |Igen |
| authenticationType |Névtelen vagy Windows. <br><br> **Kerberos-hitelesítés** hdfs-összekötő használatához tekintse meg ezt a szakaszt a helyszíni környezet ennek megfelelő beállításához. |Igen |
| userName (Felhasználónév) |Felhasználónév a Windows-hitelesítéshez. |Igen (Windows-hitelesítéshez) |
| jelszó |Jelszó a Windows-hitelesítéshez. |Igen (Windows-hitelesítéshez) |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a HDFS-hez való csatlakozáshoz használnia kell. |Igen |
| titkosított hitelesítő adatok |A hozzáférési hitelesítő adatok [új-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) kimenete. |Nem |

#### <a name="example-using-anonymous-authentication"></a>Példa: Névtelen hitelesítés használata

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

#### <a name="example-using-windows-authentication"></a>Példa: Windows-hitelesítés használata

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

További információ: HDFS-összekötő cikk.

### <a name="dataset"></a>Adatkészlet
HdFS-adatkészlet definiálásához állítsa az adatkészlet **típusát** **FileShare**beállításra, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A mappa elérési útja. Például: `myfolder`<br/><br/>A karakterlánc speciális karaktereihez használja a ' \ ' escape karaktert. Például: mappa\almappa esetén\\\\adja meg a mappa almappáját,\\\\a d:\samplefolder esetében pedig adja meg a d: mintamappát.<br/><br/>Ezt a tulajdonságot kombinálhatja **a partitionBy tulajdonsággal,** hogy a mappaelérési utak a szelet kezdő/záró dátum-időpontja alapján legyenek. |Igen |
| fileName |Ha azt szeretné, hogy a táblázat a mappában lévő fájlra hivatkozzon, adja meg a fájl nevét a **folder-ban.** Ha nem ad meg értéket ehhez a tulajdonsághoz, a táblázat a mappában lévő összes fájlra mutat.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve a következő formátumban lesz: <br/><br/>`Data.<Guid>.txt`(például: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| particionáltby |partitionedBy használható dinamikus folderPath, fájlnév az idősorozat adatait. Példa: folderPath paraméterezve minden óra adathoz. |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](data-factory-supported-file-and-compression-formats.md#text-format) [a Json formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [az Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [az Orc Formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [Parkettaformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. <br><br> Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**. Támogatott szintek: **Optimális** és **leggyorsabb**. További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |

> [!NOTE]
> a fájlnév és a fájlszűrő nem használható egyszerre.

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

További információ: HDFS-összekötő cikk.

### <a name="file-system-source-in-copy-activity"></a>Fájlrendszer forrása a másolási tevékenységben
Ha a HDFS szolgáltatásból másol adatokat, állítsa a másolási tevékenység **forrástípusát** a **FileSystemSource**fájltípusra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

**A FileSystemSource** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasása rekurzív an- vagy csak a megadott mappából történik. |Igaz, Hamis (alapértelmezett) |Nem |

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

További információ: HDFS-összekötő cikk.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Társított szolgáltatások
SFTP-csatolt szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** **Sftp**-re, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| gazda | Az SFTP-kiszolgáló neve vagy IP-címe. |Igen |
| port |Az a port, amelyen az SFTP-kiszolgáló figyel. Az alapértelmezett érték: 21 |Nem |
| authenticationType |Adja meg a hitelesítés típusát. Engedélyezett értékek: **Alapszintű**, **SshPublicKey**. <br><br> Lásd: Az alapszintű hitelesítés és [az SSH nyilvános kulcsú hitelesítésszakaszainak használata](#using-ssh-public-key-authentication) további tulajdonságokon, illetve JSON-mintákon. |Igen |
| skipHostKeyValidation | Adja meg, hogy kihagyja-e az állomáskulcs-érvényesítést. | Nem. Az alapértelmezett érték: false |
| hostKeyFingerprint | Adja meg az állomáskulcs ujjlenyomatát. | Igen, `skipHostKeyValidation` ha a beállítás hamis.  |
| átjárónév |A helyszíni SFTP-kiszolgálóhoz való csatlakozáshoz való adatkezelési átjáró neve. | Igen, ha adatokat másol egy helyszíni SFTP-kiszolgálóról. |
| titkosított hitelesítő adatok | Titkosított hitelesítő adat az SFTP-kiszolgáló eléréséhez. Az alapfokú hitelesítés (felhasználónév + jelszó) vagy a SshPublicKey hitelesítés (felhasználónév + titkos kulcs elérési útja vagy tartalma) megadásakor jön létre a másolási varázslóban vagy a ClickOnce előugró ablakban. | Nem. Csak akkor alkalmazható, ha adatokat másol egy helyszíni SFTP-kiszolgálóról. |

#### <a name="example-using-basic-authentication"></a>Példa: Alapfokú hitelesítés használata

Az alapfokú hitelesítés `authenticationType` `Basic`használatához állítsa be a at, és adja meg a következő tulajdonságokat az utolsó szakaszban bevezetett SFTP-összekötő általános tulajdonságai mellett:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| felhasználónév | Az SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó. |Igen |
| jelszó | A felhasználó jelszava (felhasználónév). | Igen |

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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Példa: **Egyszerű hitelesítés titkosított hitelesítő adatokkal**

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

#### <a name="using-ssh-public-key-authentication"></a>**Az SSH nyilvános kulcsos hitelesítésének használata:**

Az alapfokú hitelesítés `authenticationType` `SshPublicKey`használatához állítsa be a at, és adja meg a következő tulajdonságokat az utolsó szakaszban bevezetett SFTP-összekötő általános tulajdonságai mellett:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| felhasználónév |Az SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Igen |
| privateKeyPath | Adja meg az átjáró által elérhető személyes kulcsfájl abszolút elérési útját. | Adja meg `privateKeyPath` `privateKeyContent`a vagy a . <br><br> Csak akkor alkalmazható, ha adatokat másol egy helyszíni SFTP-kiszolgálóról. |
| privateKeyContent | A személyes kulcs tartalmának szerializált karakterlánca. A Másolás varázsló képes olvasni a személyes kulcsfájlt, és automatikusan kibontani a személyes kulcs tartalmát. Ha bármilyen más eszközt/SDK-t használ, használja inkább a privateKeyPath tulajdonságot. | Adja meg `privateKeyPath` `privateKeyContent`a vagy a . |
| Jelszót | Adja meg a személyes kulcs visszafejtéséhez használt jelszót/jelszót, ha a kulcsfájlt jelmondat védi. | Igen, ha a személyes kulcsfájlt jelmondat védi. |

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Példa: **SshPublicKey hitelesítés titkos kulcstartalommal**

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

További információt az [SFTP-összekötőről](data-factory-sftp-connector.md#linked-service-properties) szóló cikkben talál.

### <a name="dataset"></a>Adatkészlet
SFTP-adatkészlet definiálásához állítsa az adatkészlet típusát **FileShare (Fájlmegosztás**) **beállításra,** és adja meg a következő tulajdonságokat a **TypeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A mappa alelérési útja. A karakterlánc speciális karaktereihez használja a ' \ ' escape karaktert. Példákat lásd: Minta csatolt szolgáltatás és adatkészlet-definíciók.<br/><br/>Ezt a tulajdonságot kombinálhatja **a partitionBy tulajdonsággal,** hogy a mappaelérési utak a szelet kezdő/záró dátum-időpontja alapján legyenek. |Igen |
| fileName |Ha azt szeretné, hogy a táblázat a mappában lévő fájlra hivatkozzon, adja meg a fájl nevét a **folder-ban.** Ha nem ad meg értéket ehhez a tulajdonsághoz, a táblázat a mappában lévő összes fájlra mutat.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve a következő formátumban lesz: <br/><br/>`Data.<Guid>.txt`(Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nem |
| fileFilter |Adjon meg egy szűrőt, amelyet a mappapatikus ban lévő fájlok egy részhalmazának kijelölésére, nem pedig az összes fájlhoz kell használni.<br/><br/>Az engedélyezett `*` értékek a következők: (több karakter) és `?` (egy karakter).<br/><br/>1. példa:`"fileFilter": "*.log"`<br/>2. példa:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter bemeneti FileShare adatkészletesetén alkalmazható. Ez a tulajdonság nem támogatott a HDFS. |Nem |
| particionáltby |partitionedBy használható dinamikus folderPath, fájlnév az idősorozat adatait. Például folderPath paraméterezett minden óra az adatok. |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](data-factory-supported-file-and-compression-formats.md#text-format) [a Json formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [az Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [az Orc Formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [Parkettaformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. <br><br> Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**. Támogatott szintek: **Optimális** és **leggyorsabb**. További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |
| useBinaryTransfer |Adja meg, hogy bináris átviteli módot használ-e. Igaz a bináris mód és a hamis ASCII. Alapértelmezett érték: Igaz. Ez a tulajdonság csak akkor használható, ha a csatolt szolgáltatás típusa FtpServer típusú. |Nem |

> [!NOTE]
> a fájlnév és a fájlszűrő nem használható egyszerre.

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

További információt az [SFTP-összekötőről](data-factory-sftp-connector.md#dataset-properties) szóló cikkben talál.

### <a name="file-system-source-in-copy-activity"></a>Fájlrendszer forrása a másolási tevékenységben
Ha SFTP-forrásból másol adatokat, állítsa a másolási tevékenység **forrástípusát** **a FileSystemSource**mezőre, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasása rekurzív an- vagy csak a megadott mappából történik. |Igaz, Hamis (alapértelmezett) |Nem |



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

További információt az [SFTP-összekötőről](data-factory-sftp-connector.md#copy-activity-properties) szóló cikkben talál.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Társított szolgáltatások
HTTP-hez csatolt szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** **http**-re, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| url | A webkiszolgáló alap URL-címe | Igen |
| authenticationType | A hitelesítés típusát adja meg. Az engedélyezett értékek a következők: **Névtelen**, **Alap,** **Kivonatoló**, **Windows**, **ClientCertificate**. <br><br> Tekintse meg az alábbi szakaszokat a táblázat ban több tulajdonság és JSON-minták az adott hitelesítési típusok, illetve. | Igen |
| enableServerCertificateValidation | Adja meg, hogy engedélyezi-e a kiszolgáló TLS/SSL tanúsítványának érvényesítését, ha a forrás HTTPS webkiszolgáló | Nem, az alapértelmezett érték igaz |
| átjárónév | A helyszíni HTTP-forráshoz való csatlakozáshoz való adatkezelési átjáró neve. | Igen, ha adatokmásolása helyszíni HTTP-forrásból. |
| titkosított hitelesítő adatok | Titkosított hitelesítő adat a HTTP-végpont eléréséhez. Automatikusan létrejön, amikor konfigurálja a hitelesítési adatokat a Másolás varázslóban vagy a Kattintási előugró ablakban. | Nem. Csak akkor alkalmazható, ha adatokat másol egy helyszíni HTTP-kiszolgálóról. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Példa: Alapszintű, kivonatoló vagy Windows-hitelesítés használata
Állítsa `authenticationType` `Basic`be `Digest`a `Windows`, vagy , és adja meg a következő tulajdonságokat a HTTP-összekötő általános a fent bevezetett mellett:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| felhasználónév | Felhasználónév a HTTP-végpont eléréséhez. | Igen |
| jelszó | A felhasználó jelszava (felhasználónév). | Igen |

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

#### <a name="example-using-clientcertificate-authentication"></a>Példa: ClientCertificate-hitelesítés használata

Az alapfokú hitelesítés `authenticationType` `ClientCertificate`használatához állítsa be a at, és adja meg a következő tulajdonságokat a HTTP-összekötő általános tulajdonságai mellett:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| embeddedCertData | A Személyes adatcsere (PFX) fájl bináris adatainak Base64 kódolású tartalma. | Adja meg `embeddedCertData` `certThumbprint`a vagy a . |
| certThumbprint | Az átjárógép tanúsítványtárolójára telepített tanúsítvány ujjlenyomata. Csak akkor alkalmazható, ha adatokat másol egy helyszíni HTTP-forrásból. | Adja meg `embeddedCertData` `certThumbprint`a vagy a . |
| jelszó | A tanúsítványhoz társított jelszó. | Nem |

Ha hitelesítésre használja, `certThumbprint` és a tanúsítvány telepítve van a helyi számítógép személyes tárolójában, olvasási engedélyt kell adnia az átjárószolgáltatásnak:

1. Indítsa el a Microsoft Management Console (MMC) konzolt. Adja hozzá a Helyi számítógépet megcélozó **Tanúsítványok** beépülő **modult.**
2. Bontsa ki **a Tanúsítványok**, **a Személyes**, majd a **Tanúsítványok (Tanúsítványok) csomópontot.**
3. Kattintson a jobb gombbal a személyes tárolótanúsítványra, és válassza **a Minden feladat**->**személyes kulcs kezelése parancsot...**
3. A **Biztonság** lapon adja hozzá azt a felhasználói fiókot, amely alatt az Adatkezelési átjárógazdaszolgáltatás fut a tanúsítvány olvasási hozzáférésével.

**Példa: ügyféltanúsítvány használata:** Ez a kapcsolt szolgáltatás az adat-előállítót egy helyszíni HTTP-webkiszolgálóhoz kapcsolja. Olyan ügyféltanúsítványt használ, amely telepítve van a számítógépen az Adatkezelési átjáró telepítve.

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

#### <a name="example-using-client-certificate-in-a-file"></a>Példa: ügyféltanúsítvány használata fájlban
Ez a kapcsolt szolgáltatás az adat-előállítót egy helyszíni HTTP-webkiszolgálóhoz kapcsolja. Ügyféltanúsítvány-fájlt használ a számítógépen, ha telepítve van az adatkezelési átjáró.

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

További információ: [HTTP connector](data-factory-http-connector.md#linked-service-properties) article.

### <a name="dataset"></a>Adatkészlet
HTTP-adatkészlet definiálásához állítsa az adatkészlet **típusát** **Http**beállításra, és adja meg a következő tulajdonságokat a **Típustulajdonságok** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| relativeUrl | Az adatokat tartalmazó erőforrás relatív URL-címe. Ha nincs megadva elérési út, csak a csatolt szolgáltatásdefinícióban megadott URL-címet használja a program. <br><br> Dinamikus URL létrehozásához használhatja [a Data Factory függvényeket és a rendszerváltozókat,](data-factory-functions-variables.md)példa: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Nem |
| requestMethod (requestMethod) | Http módszer. Az engedélyezett értékek **get** vagy **postai értékek.** | Nem. Az alapértelmezett szint a `GET`. |
| további fejlécek | További HTTP-kérelemfejlécek. | Nem |
| requestBody | HTTP-kérelem törzse. | Nem |
| Formátum | Ha egyszerűen be szeretné **olvasni az adatokat a HTTP-végpontból elemzés** nélkül, hagyja ki ezt a formátumbeállításokat. <br><br> Ha a HTTP-válasz tartalmat másolás közben szeretné elemezni, a következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. További információt a [Szövegformátum,](data-factory-supported-file-and-compression-formats.md#text-format) [a Json formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [az Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [az Orc Formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [Parkettaformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**. Támogatott szintek: **Optimális** és **leggyorsabb**. További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |

#### <a name="example-using-the-get-default-method"></a>Példa: a GET (alapértelmezett) metódus használata

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

#### <a name="example-using-the-post-method"></a>Példa: a POST módszer használata

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
További információ: [HTTP connector](data-factory-http-connector.md#dataset-properties) article.

### <a name="http-source-in-copy-activity"></a>HTTP-forrás a másolási tevékenységben
Ha HTTP-forrásból másol adatokat, állítsa a másolási tevékenység **forrástípusát** **httpsource**-ra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| httpRequestTimeout | A HTTP-kérelem időmegadása (TimeSpan) a válasz kéréséhez. Ez az időtúllépések a válasz hoz, nem a válaszadatok olvasásához szükséges időtúllépések. | Nem. Alapértelmezett érték: 00:01:40 |


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

További információ: [HTTP connector](data-factory-http-connector.md#copy-activity-properties) article.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Társított szolgáltatások
OData-csatolt szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** **OData**-ra, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| url |Az OData szolgáltatás url-címe. |Igen |
| authenticationType |Az OData-forráshoz való csatlakozáshoz használt hitelesítés típusa. <br/><br/> A felhőalapú OData lehetséges értékek névtelen, egyszerű és OAuth (megjegyzés Az Azure Data Factory jelenleg csak az Azure Active Directory alapú OAuth támogatja). <br/><br/> A helyszíni OData lehetséges értékek névtelen, egyszerű és Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet, ha alapfokú hitelesítést használ. |Igen (csak akkor, ha alapfokú hitelesítést használ) |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Igen (csak akkor, ha alapfokú hitelesítést használ) |
| authorizedCredential |OAuth használata esetén kattintson az **Engedélyezés** gombra a Data Factory Copy wizard vagy a Szerkesztő programban, és adja meg a hitelesítő adatait, majd a tulajdonság értéke automatikusan létrejön. |Igen (csak akkor, ha OAuth-hitelesítést használ) |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni OData szolgáltatáshoz való csatlakozáshoz kell használnia. Csak akkor adja meg, ha a helyszíni OData forrásból másolja az adatokat. |Nem |

#### <a name="example---using-basic-authentication"></a>Példa – Alapfokú hitelesítés használata
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Példa – Névtelen hitelesítés használata

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Példa – A helyszíni OData-forráshoz való hozzáféréshez hozzáférő Windows-hitelesítés használata

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

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Példa – A felhőbeli OData-forrás eléréséhez hozzáférő OAuth-hitelesítés használata
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

További információ: [OData-összekötő](data-factory-odata-connector.md#linked-service-properties) cikk.

### <a name="dataset"></a>Adatkészlet
OData-adatkészlet definiálásához állítsa az adatkészlet típusát **ODataResource (ODataResource)** **beállításra,** és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| path |Az OData erőforrás elérési útja |Nem |

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

További információ: [OData-összekötő](data-factory-odata-connector.md#dataset-properties) cikk.

### <a name="relational-source-in-copy-activity"></a>Relációs forrás a másolási tevékenységben
Ha OData-forrásból másol adatokat, állítsa a másolási tevékenység **forrástípusát** **RelationalSource (Reszkeseredforrás)** beállításra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Példa | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |"?$select=Név, leírás&$top=5" |Nem |

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

További információ: [OData-összekötő](data-factory-odata-connector.md#copy-activity-properties) cikk.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Társított szolgáltatások
ODBC-hez csatolt szolgáltatás definiálásához állítsa a csatolt szolgáltatás **típusát** **az OnPremisesOdbc**eszközre, és adja meg a következő tulajdonságokat a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| connectionString (kapcsolati karakterlánc) |A kapcsolati karakterlánc nem-hozzáférési hitelesítő adatok és egy opcionális titkosított hitelesítő adat. Tekintse meg a következő szakaszokban található példákat. |Igen |
| hitelesítő adat |Az illesztőprogram-specifikus tulajdonságérték-formátumban megadott kapcsolati karakterlánc hozzáférési hitelesítő adatrésze. Például: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Nem |
| authenticationType |Az ODBC-adattárhoz való csatlakozáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen és Alapszintű. |Igen |
| felhasználónév |Adja meg a felhasználónevet, ha alapfokú hitelesítést használ. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak az ODBC-adattárhoz való csatlakozáshoz használnia kell. |Igen |

#### <a name="example---using-basic-authentication"></a>Példa – Alapfokú hitelesítés használata

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
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Példa – Egyszerű hitelesítés használata titkosított hitelesítő adatokkal
A hitelesítő adatokat a [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) parancsmag használatával titkosíthatja.

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

#### <a name="example-using-anonymous-authentication"></a>Példa: Névtelen hitelesítés használata

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

További információ: [ODBC connector](data-factory-odbc-connector.md#linked-service-properties) article.

### <a name="dataset"></a>Adatkészlet
ODBC-adatkészlet definiálásához állítsa az adatkészlet típusát **RelationalTable**( kapcsolat ) **beállítására,** és adja meg a következő tulajdonságokat a **Típustulajdonságok** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve az ODBC adattárban. |Igen |


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

További információ: [ODBC connector](data-factory-odbc-connector.md#dataset-properties) article.

### <a name="relational-source-in-copy-activity"></a>Relációs forrás a másolási tevékenységben
Ha ODBC-adattárból másol adatokat, állítsa a másolási tevékenység **forrástípusát** **RelationalSource (Reszkeso)** mezőre, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: `select * from MyTable`. |Igen |

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

További információ: [ODBC connector](data-factory-odbc-connector.md#copy-activity-properties) article.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Társított szolgáltatások
Salesforce csatolt szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** a **Salesforce mezőre,** és adja meg a következő tulajdonságokat a **Típustulajdonságok** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| environmentUrl | Adja meg a Salesforce-példány URL-címét. <br><br> - Az alapértelmezett\/érték a "https: /login.salesforce.com". <br> - Adatok másolásához a sandboxból adja meg a "https://test.salesforce.com" (" (A" értéket. <br> - Adatok másolásához egyéni tartományból, adja meg például a "https://[domain].my.salesforce.com".- |Nem |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen |
| jelszó |Adja meg a felhasználói fiók jelszavát. |Igen |
| securityToken |Adja meg a felhasználói fiók biztonsági jogkivonatát. A biztonsági jogkivonat visszaállítása/bekérésa című témakörben található a [Biztonsági jogkivonat](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) visszaállítása és bekésezése című témakörben. A biztonsági jogkivonatokról általában a [Biztonság és az API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)című témakörben olvashat. |Igen |

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

További információ: [Salesforce connector](data-factory-salesforce-connector.md#linked-service-properties) article.

### <a name="dataset"></a>Adatkészlet
Salesforce adatkészlet definiálásához állítsa az adatkészlet típusát **RelationalTable**( kapcsolat ) **beállítására,** és adja meg a következő tulajdonságokat a **Típustulajdonságok** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve a Salesforce-ban. |Nem (ha meg van adva a **RelationalSource** **lekérdezése)** |

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

További információ: [Salesforce connector](data-factory-salesforce-connector.md#dataset-properties) article.

### <a name="relational-source-in-copy-activity"></a>Relációs forrás a másolási tevékenységben
Ha adatokat másol a Salesforce-ból, állítsa a másolási tevékenység **forrástípusát** **RelationalSource ( reklisztána)** beállításra, és adja meg a következő tulajdonságokat a **forrásszakaszban:**

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL-92 lekérdezés vagy [Salesforce Object query language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezés. Például: `select * from MyTable__c`. |Nem (ha az **adatkészlet** **táblaneve** meg van adva) |

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
> Az API-név "__c" része minden egyéni objektumhoz szükséges.

További információ: [Salesforce connector](data-factory-salesforce-connector.md#copy-activity-properties) article.

## <a name="web-data"></a>Webes adatok

### <a name="linked-service"></a>Társított szolgáltatások
Webalapú szolgáltatás definiálásához állítsa be a csatolt szolgáltatás **típusát** **a Web**szolgáltatásra, és adja meg a következő tulajdonságokat a **típustulajdonságok** szakaszban:

| Tulajdonság | Leírás | Kötelező |
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

További információt a [Web Table connector](data-factory-web-table-connector.md#linked-service-properties) cikkében talál.

### <a name="dataset"></a>Adatkészlet
Webes adatkészlet definiálásához állítsa az adatkészlet **típusát** **WebTable**, és adja meg a következő tulajdonságokat a **típusTulajdonságok** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type |az adatkészlet típusát. **webtable-re** kell állítani |Igen |
| path |A táblát tartalmazó erőforrás relatív URL-címe. |Nem. Ha nincs megadva elérési út, csak a csatolt szolgáltatásdefinícióban megadott URL-címet használja a program. |
| Index |Az erőforrás ban lévő tábla indexe. A HTML-lapok szakaszban lévő táblázat indexének beszerzése című témakörben a HTML-lapokban lévő táblázat indexindexének beszerzése című témakört találja. |Igen |

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

További információt a [Web Table connector](data-factory-web-table-connector.md#dataset-properties) cikkében talál.

### <a name="web-source-in-copy-activity"></a>Webes forrás a másolási tevékenységben
Ha webtáblából másol adatokat, állítsa be a másolási tevékenység **forrástípusát** a **WebSource**programba. Jelenleg, ha a másolási tevékenység forrása **WebSource**típusú, a további tulajdonságok nem támogatottak.

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

További információt a [Web Table connector](data-factory-web-table-connector.md#copy-activity-properties) cikkében talál.

## <a name="compute-environments"></a>COMPUTE KÖRNYEZETEK
Az alábbi táblázat a Data Factory által támogatott számítási környezeteket és az azokon futtatható átalakítási tevékenységeket sorolja fel. Kattintson a kívánt számítási adathivatkozásra a json-sémák megtekintéséhez a csatolt szolgáltatáshoz, hogy egy adat-előállítóhoz kapcsolódjon.

| Számítási környezet | Tevékenységek |
| --- | --- |
| [Igény szerinti HDInsight-fürt](#on-demand-azure-hdinsight-cluster) vagy [saját HDInsight-fürt](#existing-azure-hdinsight-cluster) |[.NET egyéni tevékenység](#net-custom-activity), [Hive tevékenység](#hdinsight-hive-activity), [Sertés tevékenység](#hdinsight-pig-activity), [MapReduce tevékenység](#hdinsight-mapreduce-activity), Hadoop streaming tevékenység, Spark [tevékenység](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET egyéni tevékenység](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Gépi tanulási kötegelt végrehajtási tevékenység](#machine-learning-batch-execution-activity), [gépi tanulási frissítési erőforrás-tevékenység](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database), [Azure SQL Data Warehouse](#azure-sql-data-warehouse), SQL [Server](#sql-server-stored-procedure) |[Tárolt eljárás](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Igény szerinti Azure HDInsight-fürt
Az Azure Data Factory szolgáltatás automatikusan létrehozhat egy Windows/Linux alapú, igény szerinti HDInsight-fürtöt az adatok feldolgozásához. A fürt ugyanabban a régióban jön létre, mint a fürthöz társított storage-fiók (linkedServiceName tulajdonság a JSON-ban). A következő átalakítási tevékenységeket futtathatja ezen a csatolt szolgáltatáson: [.NET egyéni tevékenység](#net-custom-activity), [Hive-tevékenység](#hdinsight-hive-activity), [Sertéstevékenység](#hdinsight-pig-activity), [MapReduce tevékenység](#hdinsight-mapreduce-activity), Hadoop-streamelési tevékenység, [Spark-tevékenység.](#hdinsight-spark-activity)

### <a name="linked-service"></a>Társított szolgáltatások
Az alábbi táblázat az igény szerinti HDInsight-alapú szolgáltatás Azure JSON-definíciójában használt tulajdonságok leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságot **HDInsightOnDemand (HDInsightOnDemand**) tulajdonságra kell állítani. |Igen |
| clusterSize |A fürt dolgozói/adatcsomópontjainak száma. A HDInsight-fürt kétfej-csomópontossal, valamint a tulajdonsághoz megadott munkavégző csomópontok számával jön létre. A csomópontok mérete Standard_D3, amely 4 maggal rendelkezik, így egy 4 feldolgozócsomópont-fürt 24 magot vesz fel\*\*(4 4 = 16 mag a munkavégző csomópontokhoz, plusz 2 4 = 8 mag a fő csomópontokhoz). A Standard_D3 réteg részleteiről [lásd: Linux-alapú Hadoop-fürtök létrehozása a HDInsightban.](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) |Igen |
| timetolive |Az igény szerinti HDInsight-fürt engedélyezett szabad ideje. Itt adható meg, hogy az igény szerinti HDInsight-fürt mennyi ideig maradjon életben egy tevékenységfuttatás befejezése után, ha nincs más aktív feladat a fürtben.<br/><br/>Ha például egy tevékenység futtatása 6 percet vesz igénybe, és a timetolive 5 perc, a fürt a tevékenység futtatásának 6 percután 5 percig életben marad. Ha egy másik tevékenységfuttatást hajt végre a 6 perces ablakkal, azt ugyanaz a fürt dolgozza fel.<br/><br/>Az igény szerinti HDInsight-fürt létrehozása költséges művelet (eltarthat egy ideig), ezért szükség szerint használja ezt a beállítást az adat-előállító teljesítményének javítására egy igény szerinti HDInsight-fürt újrafelhasználásával.<br/><br/>Ha az idő-élő értéket 0-ra állítja, a fürt a feldolgozás során a tevékenység futtatásakor azonnal törlődik. Másrészt, ha magas értéket állít be, a fürt feleslegesen maradhat tétlen, ami magas költségeket eredményezhet. Ezért fontos, hogy a megfelelő értéket az igényei nek megfelelően állítsa be.<br/><br/>Több folyamat is megoszthatja az igény szerinti HDInsight-fürt ugyanazon példányát, ha a timetolive tulajdonság értéke megfelelően be van állítva |Igen |
| version |A HDInsight-fürt verziója. További információt az [Azure Data Factory támogatott HDInsight-verziói című témakörben talál.](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory) |Nem |
| linkedServiceName |Az Azure Storage-hoz kapcsolódó szolgáltatás, amelyet az igény szerinti fürt az adatok tárolására és feldolgozására használ. <p>Jelenleg nem hozhat létre olyan igény szerinti HDInsight-fürtöt, amely egy Azure Data Lake Store-t használ tárolóként. Ha a HDInsight-feldolgozásból származó eredményadatokat egy Azure Data Lake Store-ban szeretné tárolni, egy másolási tevékenység segítségével másolja az adatokat az Azure Blob Storage-ból az Azure Data Lake Store-ba.</p>  | Igen |
| továbbiLinkedServiceNames |További tárfiókokat a HDInsight-csatolt szolgáltatás, hogy a Data Factory szolgáltatás regisztrálhatja őket az Ön nevében. |Nem |
| osType típus |Az operációs rendszer típusa. Az engedélyezett értékek a következők: Windows (alapértelmezett) és Linux |Nem |
| hcatalogLinkedServiceName |Az Azure SQL-hez csatolt szolgáltatás neve, amely a HCatalog-adatbázisra mutat. Az igény szerinti HDInsight-fürt az Azure SQL-adatbázis metatárolóként való használatával jön létre. |Nem |

### <a name="json-example"></a>JSON példa
A következő JSON egy Linux-alapú HDInsight-kapcsolt szolgáltatást határoz meg. A Data Factory szolgáltatás automatikusan létrehoz egy **Linux-alapú HDInsight-fürtöt** egy adatszelet feldolgozásakor.

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

További információ: [Compute linked services](data-factory-compute-linked-services.md) article.

## <a name="existing-azure-hdinsight-cluster"></a>Meglévő Azure HDInsight-fürt
Létrehozhat egy Azure HDInsight-kapcsolt szolgáltatást, amely regisztrálja saját HDInsight-fürtjeit a Data Factory szolgáltatással. A csatolt szolgáltatáson a következő adatátalakítási tevékenységeket futtathatja: [.NET egyéni tevékenység](#net-custom-activity), [Hive-tevékenység](#hdinsight-hive-activity), [Sertéstevékenység](#hdinsight-pig-activity), [MapReduce tevékenység](#hdinsight-mapreduce-activity), Hadoop-streamelési tevékenység, [Spark-tevékenység.](#hdinsight-spark-activity)

### <a name="linked-service"></a>Társított szolgáltatások
Az alábbi táblázat az Azure JSON-definícióban egy Azure HDInsight-kapcsolt szolgáltatás ban használt tulajdonságok leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságot **HDInsight (HDInsight**) tulajdonságra kell állítani. |Igen |
| clusterUri |A HDInsight-fürt URI-ja. |Igen |
| felhasználónév |Adja meg a meglévő HDInsight-fürthöz való csatlakozáshoz használandó felhasználó nevét. |Igen |
| jelszó |Adja meg a felhasználói fiók jelszavát. |Igen |
| linkedServiceName | Az Azure Storage-hoz kapcsolódó szolgáltatás neve, amely a HDInsight-fürt által használt Azure blob storage hivatkozik. <p>Jelenleg nem adhat meg Azure Data Lake Store-hoz kapcsolt szolgáltatást ehhez a tulajdonsághoz. Az Azure Data Lake Store-ban a Hive/Pig parancsfájlok ból is hozzáférhet adatokhoz, ha a HDInsight-fürt hozzáfér a Data Lake Store-hoz. </p>  |Igen |

A támogatott HDInsight-fürtök verzióit a [támogatott HDInsight-verziók című témakörben](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory)tési területen láthatja.

#### <a name="json-example"></a>JSON példa

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
Létrehozhat egy Azure Batch-kapcsolt szolgáltatást a virtuális gépek (VM-ek) batch készletének regisztrálásához egy adat-előállítóval. A .NET egyéni tevékenységeket az Azure Batch vagy az Azure HDInsight használatával futtathatja. A csatolt szolgáltatáson [.NET egyéni tevékenységet](#net-custom-activity) futtathat.

### <a name="linked-service"></a>Társított szolgáltatások
Az alábbi táblázat az Azure-köteghez csatolt szolgáltatás Azure JSON-definíciójában használt tulajdonságok leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságot **Az AzureBatch beállításra**kell állítani. |Igen |
| accountName |Az Azure Batch-fiók neve. |Igen |
| accessKey (accessKey) |Hozzáférési kulcs az Azure Batch-fiókhoz. |Igen |
| készletneve |A virtuális gépek készletének neve. |Igen |
| linkedServiceName |Az Azure Storage-hoz csatolt szolgáltatás neve ehhez az Azure Batch-hez csatolt szolgáltatáshoz társítva. Ez a csatolt szolgáltatás a tevékenység futtatásához és a tevékenységvégrehajtási naplók tárolásához szükséges átmeneti fájlokhoz használatos. |Igen |


#### <a name="json-example"></a>JSON példa

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
Hozzon létre egy Azure Machine Learning-alapú szolgáltatás egy Machine Learning-kötegpontozási végpont regisztrálásához egy adat-előállító. Két adatátalakítási tevékenység, amely futtatható ezen a csatolt szolgáltatáson: [Machine Learning Batch execution activity](#machine-learning-batch-execution-activity), Machine Learning Update Resource [Activity](#machine-learning-update-resource-activity).

### <a name="linked-service"></a>Társított szolgáltatások
Az alábbi táblázat az Azure Machine Learning-alapú szolgáltatás Azure JSON-definíciójában használt tulajdonságok leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| Típus |A típustulajdonságot a következőre kell állítani: **AzureML**. |Igen |
| mlVégpont |A kötegelt pontozási URL-cím. |Igen |
| apiKey (apiKey) |A közzétett munkaterületi modell API-ja. |Igen |

#### <a name="json-example"></a>JSON példa

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
Hozzon létre egy **Azure Data Lake Analytics-alapú** szolgáltatást, amely egy Azure Data Lake Analytics-számítási szolgáltatást kapcsol össze egy Azure-adatfeldolgozóval, mielőtt a Data Lake [Analytics U-SQL-tevékenységet](data-factory-usql-activity.md) egy folyamatban használná.

### <a name="linked-service"></a>Társított szolgáltatások

Az alábbi táblázat az Azure Data Lake Analytics-alapú szolgáltatás JSON-definíciójában használt tulajdonságok leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| Típus |A típustulajdonságot a következőre kell állítani: **AzureDataLakeAnalytics**. |Igen |
| accountName |Azure Data Lake Analytics-fiók neve. |Igen |
| dataLakeAnalyticsUri |Azure Data Lake Analytics URI. |Nem |
| engedélyezés |Az engedélyezési kód automatikusan beolvasásra kerül, miután a Data Factory Editor ban az **Engedélyezés** gombra kattintott, és befejezte az OAuth bejelentkezést. |Igen |
| subscriptionId |Azure-előfizetés azonosítója |Nem (ha nincs megadva, az adat-előállító előfizetését használja a rendszer). |
| resourceGroupName |Azure-erőforráscsoport neve |Nem (ha nincs megadva, az adat-előállító erőforráscsoportját használja a rendszer). |
| Munkamenet |munkamenet-azonosítót az OAuth engedélyezési munkamenetből. Minden munkamenet-azonosító egyedi, és csak egyszer használható. A Data Factory Editor használatakor ez az azonosító automatikusan létrejön. |Igen |


#### <a name="json-example"></a>JSON példa
A következő példa JSON-definíciót biztosít egy Azure Data Lake Analytics-alapú szolgáltatáshoz.

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

## <a name="sql-server-stored-procedure"></a>SQL Server tárolt eljárás

Hozzon létre egy SQL Server csatolt szolgáltatást, és használja azt a [tárolt eljárás tevékenység](data-factory-stored-proc-activity.md) meghívására tárolt eljárás egy Data Factory folyamat.

### <a name="linked-service"></a>Társított szolgáltatások
Hozzon létre egy **onPremisesSqlServer** típusú kapcsolt szolgáltatást, amely egy helyszíni SQL Server-adatbázist egy adat-előállítóhoz kapcsol. Az alábbi táblázat a helyszíni SQL Server-hivatkozott szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

Az alábbi táblázat az SQL Server csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságot a következőre kell állítani: **OnPremisesSqlServer**. |Igen |
| connectionString (kapcsolati karakterlánc) |Adja meg a helyszíni SQL Server adatbázishoz SQL-hitelesítés vagy Windows-hitelesítés használatával történő csatlakozáshoz szükséges connectionString-adatokat. |Igen |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni SQL Server-adatbázishoz való csatlakozáshoz használnia kell. |Igen |
| felhasználónév |Ha Windows-hitelesítést használ, adja meg a felhasználónevet. Példa: **\\tartománynév felhasználóneve**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |

A hitelesítő adatokat a **New-AzDataFactoryEncryptValue** parancsmag használatával titkosíthatja, és a kapcsolati karakterláncban használhatja, ahogy az a következő példában (**EncryptedCredential** tulajdonság) látható:

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Példa: JSON az SQL-hitelesítés hez

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
#### <a name="example-json-for-using-windows-authentication"></a>Példa: JSON a Windows-hitelesítés hez

Ha meg van adva a felhasználónév és a jelszó, az átjáró azokat használja a megadott felhasználói fiók megszemélyesítésére a helyszíni SQL Server-adatbázishoz való csatlakozáshoz. Ellenkező esetben az átjáró közvetlenül csatlakozik az SQL Serverhez az Átjáró (indítófiókja) biztonsági környezetével.

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

További információt az [SQL Server-összekötőről](data-factory-sqlserver-connector.md#linked-service-properties) szóló cikkben talál.

## <a name="data-transformation-activities"></a>ADATÁTALAKÍTÁSI TEVÉKENYSÉGEK

Tevékenység | Leírás
-------- | -----------
[HDInsight Hive-tevékenység](#hdinsight-hive-activity) | A DATA Factory-folyamat HDInsight-struktúra működése hive-lekérdezéseket hajt végre a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön.
[HDInsight Pig tevékenység](#hdinsight-pig-activity) | A Data Factory-folyamat HDInsight Pig-tevékenysége saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre a Pig-lekérdezéseket.
[HDInsight MapReduce tevékenység](#hdinsight-mapreduce-activity) | A DATA Factory-folyamat HDInsight MapReduce tevékenysége a MapReduce programokat saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre.
[HDInsight-streamelési tevékenység](#hdinsight-streaming-activity) | A DATA Factory-folyamat HDInsight-streamelési tevékenysége saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre a Hadoop-streamelési programokat.
[HDInsight Spark-tevékenység](#hdinsight-spark-activity) | A Data Factory-folyamat HDInsight Spark-tevékenysége spark-programokat hajt végre a saját HDInsight-fürtén.
[Machine Learning kötegelt végrehajtási tevékenység](#machine-learning-batch-execution-activity) | Az Azure Data Factory lehetővé teszi, hogy könnyedén hozzon létre folyamatokat, amelyek egy közzétett Azure Machine Learning webszolgáltatást használnak a prediktív elemzéshez. A batch-végrehajtási tevékenység egy Azure Data Factory-folyamat használatával egy Machine Learning-webszolgáltatás meghívásával előrejelzéseket készíthet a kötegben lévő adatokról.
[Machine Learning Update-erőforrástevékenység](#machine-learning-update-resource-activity) | Idővel a machine learning-pontozási kísérletek prediktív modelljeit újra kell képezni az új bemeneti adatkészletek használatával. Miután végzett az átképzés, frissíteni szeretné a pontozási webszolgáltatás az átedzett Machine Learning modell. Az erőforrás-tevékenység frissítése segítségével frissítheti a webszolgáltatást az újonnan betanított modellel.
[Tárolt eljárási tevékenység](#stored-procedure-activity) | A Data Factory-folyamat tárolt eljárási tevékenységével meghívhatja a tárolt eljárást a következő adattárak egyikében: Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database a vállalaton belül vagy egy Azure virtuális gép.
[Data Lake Analytics U-SQL tevékenység](#data-lake-analytics-u-sql-activity) | A Data Lake Analytics U-SQL Activity egy U-SQL-parancsfájlt futtat egy Azure Data Lake Analytics-fürtön.
[.NET egyéni tevékenység](#net-custom-activity) | Ha olyan módon kell átalakítania az adatokat, amelyet a Data Factory nem támogat, létrehozhat egy egyéni tevékenységet a saját adatfeldolgozási logikájával, és használhatja a folyamatban lévő tevékenységet. Az egyéni .NET-tevékenység konfigurálása azure Batch-szolgáltatás vagy egy Azure HDInsight-fürt használatával történő futtatásra konfigurálható.


## <a name="hdinsight-hive-activity"></a>HDInsight Hive-tevékenység
A hive-tevékenység JSON-definíciójában a következő tulajdonságokadhatók meg. A tevékenység típustulajdonságának a következőnek kell lennie: **HDInsightHive**. Először létre kell hoznia egy HDInsight-csatolt szolgáltatást, és meg kell adnia annak nevét a **linkedServiceName** tulajdonság értékeként. A következő tulajdonságokat támogatja a **typeProperties** szakasz, ha a tevékenység típusát HDInsightHive-re állítja:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| szkriptet. |Adja meg a Hive parancsfájl szövegközi |Nem |
| parancsfájl elérési útja |A Hive-parancsfájlt egy Azure blobstorage-ban tárolja, és adja meg a fájl elérési útját. Használja a "script" vagy a "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájlnév nem imitáta a kis- és nagybetűk között. |Nem |
| Meghatározza |Paraméterek megadása kulcs-/értékpárokként a Hive-parancsfájlon belüli hivatkozáshoz a "hiveconf" használatával |Nem |

Ezek a típustulajdonságok a Hive-tevékenységre jellemzőek. Más tulajdonságok (a typeProperties szakaszon kívül) minden tevékenységesetében támogatottak.

### <a name="json-example"></a>JSON példa
A következő JSON egy HDInsight-hive-tevékenységet határoz meg egy folyamatban.

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

További információ: [Hive Activity](data-factory-hive-activity.md) cikk.

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-tevékenység
A Pig Activity JSON-definícióban a következő tulajdonságokat adhatja meg. A tevékenység típustulajdonságának a következőnek kell lennie: **HDInsightPig**. Először létre kell hoznia egy HDInsight-csatolt szolgáltatást, és meg kell adnia annak nevét a **linkedServiceName** tulajdonság értékeként. A következő tulajdonságokat támogatja a **typeProperties** szakasz, ha a tevékenység típusát HDInsightPig-ra állítja:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| szkriptet. |A Pig parancsfájl szövegközi megadása |Nem |
| parancsfájl elérési útja |A Pig-parancsfájlt egy Azure blob-tárolóban tárolja, és adja meg a fájl elérési útját. Használja a "script" vagy a "scriptPath" tulajdonságot. Mindkettő nem használható együtt. A fájlnév nem imitáta a kis- és nagybetűk között. |Nem |
| Meghatározza |Paraméterek megadása kulcs-/értékpárokként a Pig parancsfájlon belüli hivatkozáshoz |Nem |

Ezek a típustulajdonságok a Pig tevékenységre jellemzőek. Más tulajdonságok (a typeProperties szakaszon kívül) minden tevékenységesetében támogatottak.

### <a name="json-example"></a>JSON példa

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

További információ: Pig Activity article.

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-tevékenység
A MapReduce tevékenység JSON-definíciójában a következő tulajdonságokadhatók meg. A tevékenység típustulajdonságának a következőnek kell lennie: **HDInsightMapReduce**. Először létre kell hoznia egy HDInsight-csatolt szolgáltatást, és meg kell adnia annak nevét a **linkedServiceName** tulajdonság értékeként. A következő tulajdonságokat támogatja a **typeProperties** szakasz, ha a tevékenység típusát HDInsightMapReduce-re állítja:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| jarLinkedService | A JAR-fájlt tartalmazó Azure Storage csatolt szolgáltatásának neve. | Igen |
| jarFilePath | A JAR-fájl elérési útja az Azure Storage-ban. | Igen |
| Osztálynév | A JAR-fájl fő osztályának neve. | Igen |
| Érvek | A MapReduce program vesszővel tagolt argumentumainak listája. Futásidőben néhány további argumentum (például mapreduce.job.tags) a MapReduce keretrendszerből. Ha meg szeretné különböztetni az argumentumokat a MapReduce argumentumokkal, fontolja meg mind a beállítás, mind az érték argumentumként való használatát a következő példában látható módon (-s, --input, --output stb., olyan beállítások, amelyeket közvetlenül az értékeik követnek) | Nem |

### <a name="json-example"></a>JSON példa

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

További információ: [MapReduce Activity](data-factory-map-reduce.md) article.

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming-tevékenység
A Következő tulajdonságokat adhatja meg a Hadoop streamelési tevékenység JSON-definíciójában. A tevékenység típustulajdonságának a következőnek kell lennie: **HDInsightStreaming**. Először létre kell hoznia egy HDInsight-csatolt szolgáltatást, és meg kell adnia annak nevét a **linkedServiceName** tulajdonság értékeként. A következő tulajdonságokat támogatja a **typeProperties** szakasz, ha a tevékenység típusát HDInsightStreaming-re állítja:

| Tulajdonság | Leírás |
| --- | --- |
| Mapper | A leképező végrehajtható fájl neve. A példában a cat.exe a leképező végrehajtható.|
| Szűkítő | A szűkítő végrehajtható fájljának neve. A példában a wc.exe a szűkítő végrehajtható fájlja. |
| bemenet | A leképező bemeneti fájlja (helyével együtt). A példában: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"`: adfsample a blob tároló, példa/data/Gutenberg a mappa, és davinci.txt a blob. |
| output | A szűkítő kimeneti fájlja (helyével együtt). A Hadoop-streamelési feladat kimenete a tulajdonsághoz megadott helyre íródik. |
| filePaths | A leképező és a szűkítő végrehajtható fájljainak elérési útjai. A példában: "adfsample/example/apps/wc.exe", adfsample a blob tároló, példa/alkalmazások a mappa, és wc.exe a végrehajtható. |
| fájlLinkedService | Az Azure Storage-hoz kapcsolódó szolgáltatás, amely az Azure storage, amely a filePaths szakaszban megadott fájlokat tartalmazza. |
| Érvek | A MapReduce program vesszővel tagolt argumentumainak listája. Futásidőben néhány további argumentum (például mapreduce.job.tags) a MapReduce keretrendszerből. Ha meg szeretné különböztetni az argumentumokat a MapReduce argumentumokkal, fontolja meg mind a beállítás, mind az érték argumentumként való használatát a következő példában látható módon (-s, --input, --output stb., olyan beállítások, amelyeket közvetlenül az értékeik követnek) |
| getDebugInfo | Egy választható elem. Ha hiba, a naplók csak hiba esetén töltődnek le. Ha a minden, naplók mindig letöltődik, függetlenül a végrehajtási állapot. |

> [!NOTE]
> Meg kell adnia egy kimeneti adatkészletet a Hadoop streamelési tevékenység a **kimenetek** tulajdonság. Ez az adatkészlet lehet csak egy dummy adatkészlet, amely szükséges a folyamat ütemezése (óránként, napi stb.). Ha a tevékenység nem vesz bemenetet, kihagyhatja a bemeneti adatkészlet megadását a **bemeneti** tulajdonság tevékenységéhez.

## <a name="json-example"></a>JSON példa

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

További információ: [Hadoop streaming tevékenység](data-factory-hadoop-streaming-activity.md) cikk.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-tevékenység
A Spark-tevékenység JSON-definíciójában a következő tulajdonságokat adhatja meg. A tevékenység típustulajdonságának a következőnek kell lennie: **HDInsightSpark**. Először létre kell hoznia egy HDInsight-csatolt szolgáltatást, és meg kell adnia annak nevét a **linkedServiceName** tulajdonság értékeként. A következő tulajdonságokat a **typeProperties** szakasz támogatja, ha a tevékenység típusát HDInsightSpark értékre állítja:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| rootPath | Az Azure Blob-tároló és a Spark-fájlt tartalmazó mappa. A fájlnév nem imitáta a kis- és nagybetűk között. | Igen |
| entryFilePath | A Spark-kód/-csomag gyökérmappájának relatív elérési útja. | Igen |
| Osztálynév | Az alkalmazás Java/Spark főosztálya | Nem |
| Érvek | A Spark-program parancssori argumentumainak listája. | Nem |
| proxyUser | A Spark-program végrehajtásához megszemélyesített felhasználói fiók | Nem |
| sparkConfig | Spark konfigurációs tulajdonságai. | Nem |
| getDebugInfo | Itt adható meg, hogy a Spark-naplófájlok másolása a sparkJobLinkedService által megadott HDInsight-fürt (vagy) által használt Azure-tárolóba kerül-e. Engedélyezett értékek: Nincs, Mindig vagy Sikertelen. Alapértelmezett érték: Nincs. | Nem |
| sparkJobLinkedService | Az Azure Storage-hoz kapcsolódó szolgáltatás, amely rendelkezik a Spark-feladat fájl, függőségek és naplók.  Ha nem ad meg értéket ehhez a tulajdonsághoz, a rendszer a HDInsight-fürthöz társított tárolót használja. | Nem |

### <a name="json-example"></a>JSON példa

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

- A **típustulajdonság** **HDInsightSpark**értékre van állítva.
- A **rootPath** van beállítva **adfspark\\pyFiles,** ahol adfspark az Azure Blob-tároló és pyFiles a finom mappát a tárolóban. Ebben a példában az Azure Blob Storage a Spark-fürthöz társított. Feltöltheti a fájlt egy másik Azure Storage-ba. Ha így tesz, hozzon létre egy Azure Storage-kapcsolt szolgáltatást, amely a tárfiókot az adat-előállítóhoz kapcsolja. Ezután adja meg a csatolt szolgáltatás nevét a **sparkJobLinkedService** tulajdonság értékeként. A Spark-tevékenység tulajdonságai a tulajdonságés a Spark-tevékenység által támogatott egyéb tulajdonságok részleteiért tekintse meg a Spark-tevékenység tulajdonságait.
- A **entryFilePath** a **test.py**, amely a python fájl.
- A **getDebugInfo** tulajdonság mindig , ami azt **jelenti,** hogy a naplófájlok mindig létrejönnek (sikeres vagy sikertelen).

    > [!IMPORTANT]
    > Azt javasoljuk, hogy csak akkor állítsa ezt a tulajdonságot mindig éles környezetben, ha egy probléma elhárítása.
- A **kimenetek** szakasz egy kimeneti adatkészlettel rendelkezik. Meg kell adnia egy kimeneti adatkészletet akkor is, ha a spark program nem hoz létre kimenetet. A kimeneti adatkészlet hajtja a folyamat ütemezését (óránként, naponta stb.).

A tevékenységről további információt a [Spark-tevékenység](data-factory-spark.md) cikkben talál.

## <a name="machine-learning-batch-execution-activity"></a>Machine Learning kötegelt végrehajtási tevékenység
Az Azure Machine Learning studio Batch Execution Activity JSON-definíciójában a következő tulajdonságokat adhatja meg. A tevékenység típustulajdonságának a következőnek kell lennie: **AzureMLBatchExecution**. Először létre kell hoznia egy Azure Machine Learning-kapcsolt szolgáltatást, és meg kell adnia annak nevét a **linkedServiceName** tulajdonság értékeként. A következő tulajdonságokat a **typeProperties** szakasz támogatja, ha a tevékenység típusát AzureMLBatchExecution-re állítja be:

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
webServiceInput | Az Azure Machine Learning studio webszolgáltatás bemeneteként átadandó adatkészlet. Ezt az adatkészletet a tevékenység bemenetei között is szerepelnie kell. |Használja a webServiceInput vagy a webServiceInputs. |
webServiceInputs | Adja meg az Azure Machine Learning studio webszolgáltatás bemenetként átadandó adatkészleteit. Ha a webszolgáltatás több bemenetet vesz igénybe, használja a webServiceInputs tulajdonságot a webServiceInput tulajdonság használata helyett. A **webServiceInputs** által hivatkozott adatkészleteket a Tevékenység **bemeneteknek**is tartalmazniuk kell. | Használja a webServiceInput vagy a webServiceInputs. |
webServiceOutputs | Az Azure Machine Learning studio webszolgáltatás kimeneteiként hozzárendelt adatkészletek. A webszolgáltatás kimeneti adatokat ad vissza ebben az adatkészletben. | Igen |
globalParameters | Adja meg a webszolgáltatás paramétereinek értékeit ebben a szakaszban. | Nem |

### <a name="json-example"></a>JSON példa
Ebben a példában a tevékenység az **MLSqlInput** adatkészletet bemenetként, **az MLSqlOutput-ot** pedig kimenetként tartalmazza. Az **MLSqlInput** a **webServiceInput** JSON tulajdonság használatával kerül átadásra a webszolgáltatás bemeneteként. Az **MLSqlOutput** kimenetként kerül átadásra a webservice-szolgáltatásnak a **webServiceOutputs** JSON tulajdonság használatával.

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

A JSON-példában a telepített Azure Machine Learning webszolgáltatás egy olvasót és egy írómodult használ az adatok olvasásához/írásához/írásához egy Azure SQL-adatbázisba. Ez a webszolgáltatás a következő négy paramétert teszi elérhetővé: Adatbázis-kiszolgáló neve, Adatbázis neve, Kiszolgálói felhasználói fiók neve és Kiszolgálói felhasználói fiók jelszava.

> [!NOTE]
> Csak az AzureMLBatchExecution tevékenység bemenetei és kimenetei adhatók át paraméterekként a webszolgáltatásnak. Például a fenti JSON-kódrészletben mlSqlInput egy bemenet az AzureMLBatchExecution tevékenység, amely a webServiceInput paraméteren keresztül a webservice bemenetként.

## <a name="machine-learning-update-resource-activity"></a>Machine Learning Update-erőforrástevékenység
Az Azure Machine Learning studio JSON-definíciójában a következő tulajdonságokadhatók meg. A tevékenység típustulajdonságának a következőnek kell lennie: **AzureMLUpdateResource**. Először létre kell hoznia egy Azure Machine Learning-kapcsolt szolgáltatást, és meg kell adnia annak nevét a **linkedServiceName** tulajdonság értékeként. A következő tulajdonságokat támogatja a typeProperties szakasz, ha a tevékenység típusát AzureMLUpdateResource:The following properties are supported in the **typeProperties** section when you set the type of activity to AzureMLUpdateResource:

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
trainedModelName | Az átedzett modell neve. | Igen |
betanítottModelDatasetName | Az átképzési művelet által visszaadott iLearner-fájlra mutató adatkészlet. | Igen |

### <a name="json-example"></a>JSON példa
A folyamat két tevékenységet kínál: **AzureMLBatchExecution** és **AzureMLUpdateResource.** Az Azure Machine Learning studio batch-végrehajtási tevékenység bemenetként veszi fel a betanítási adatokat, és egy iLearner-fájlt hoz létre kimenetként. A tevékenység meghívja a betanítási webszolgáltatás (webes szolgáltatásként elérhetővé tett betanítási kísérlet) a bemeneti betanítási adatokat, és megkapja az ilearner fájlt a webszolgáltatás. A placeholderBlob csak egy dummy kimeneti adatkészlet, amely az Azure Data Factory szolgáltatás futtatásához szükséges.


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
Az U-SQL Activity JSON-definícióban a következő tulajdonságokadhatók meg. A tevékenység típustulajdonságának a következőnek kell lennie: **DataLakeAnalyticsU-SQL**. Létre kell hoznia egy Azure Data Lake Analytics csatolt szolgáltatást, és meg kell adnia a nevét a **linkedServiceName** tulajdonság értékeként. A következő tulajdonságokat a **typeProperties** szakasz támogatja, ha a tevékenység típusát DataLakeAnalyticsU-SQL-re állítja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| scriptPath |Az U-SQL parancsfájlt tartalmazó mappa elérési útja. A fájl neve nem imitot jelent a kis- és nagybetűk között. |Nem (parancsfájl használata esetén) |
| parancsfájlLinkedService |Csatolt szolgáltatás, amely a parancsfájlt tartalmazó tárolót az adat-előállítóhoz kapcsolja |Nem (parancsfájl használata esetén) |
| szkriptet. |A parancsfájlPath és a scriptLinkedService megadása helyett adja meg a szövegközi parancsfájlt. Például: "script": "CREATE DATABASE test". |Nem (ha scriptPath és scriptLinkedService) |
| fokOfParallelism |A feladat futtatásához egyidejűleg használt csomópontok maximális száma. |Nem |
| Prioritás |Azt határozza meg, hogy a várólistán lévő feladatok közül mely feladatokat kell először futtatni. Minél alacsonyabb a szám, annál magasabb a prioritás. |Nem |
| paraméterek |Az U-SQL parancsfájl paraméterei |Nem |

### <a name="json-example"></a>JSON példa

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

További információ: [Data Lake Analytics U-SQL Activity](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Tárolt eljárási tevékenység
A tárolt eljárási tevékenység JSON-definíciójában a következő tulajdonságokadhatók meg. A tevékenység típustulajdonságának a következőnek kell lennie: **SqlServerStoredProcedure**. Létre kell hoznia az alábbi csatolt szolgáltatások egyikét, és meg kell adnia a csatolt szolgáltatás nevét a **linkedServiceName** tulajdonság értékeként:

- SQL Server
- Azure SQL Database
- Azure SQL Data Warehouse

A következő tulajdonságokat támogatja a **typeProperties** szakasz, ha a tevékenység típusát SqlServerStoredProcedure-ra állítja:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| storedProcedureName |Adja meg a tárolt eljárás nevét az Azure SQL-adatbázisban vagy az Azure SQL Data Warehouse-ban, amelyet a kimeneti tábla által használt csatolt szolgáltatás képvisel. |Igen |
| storedProcedureParameters |Adja meg a tárolt eljárásparamétereinek értékeit. Ha null értéket kell átadnia egy paraméterhez, használja a következő szintaxist: "param1": null (minden kisbetű). A tulajdonság használatáról az alábbi mintában olvashat. |Nem |

Ha megad egy bemeneti adatkészletet, annak elérhetőnek kell lennie ("Kész" állapotban) a tárolt eljárási tevékenység futtatásához. A bemeneti adatkészlet nem használható fel a tárolt eljárásban paraméterként. Csak a függőség ellenőrzésére szolgál a tárolt eljárási tevékenység megkezdése előtt. Meg kell adnia egy kimeneti adatkészletet egy tárolt eljárástevékenységhez.

A kimeneti adatkészlet a tárolt eljárási tevékenység **ütemezését** határozza meg (óránkénti, heti, havi stb.). A kimeneti adatkészlet kell használnia egy **összekapcsolt szolgáltatás,** amely egy Azure SQL-adatbázis vagy egy Azure SQL Data Warehouse vagy egy SQL Server-adatbázis, amelyben szeretné, hogy a tárolt eljárás futtatásához. A kimeneti adatkészlet szolgálhat egy módja annak, hogy adja át az eredményt a tárolt eljárás későbbi feldolgozása egy másik tevékenység ([láncolási tevékenységek](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) a folyamatban. A Data Factory azonban nem írja automatikusan a tárolt eljárás kimenetét ebbe az adatkészletbe. Ez az a tárolt eljárás, amely egy SQL-táblába ír, amelyre a kimeneti adatkészlet mutat. Bizonyos esetekben a kimeneti adatkészlet lehet egy **dummy adatkészlet,** amely csak a tárolt eljárási tevékenység futtatásának ütemezését adja meg.

### <a name="json-example"></a>JSON példa

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
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

További információt a [Tárolt eljárási tevékenység című](data-factory-stored-proc-activity.md) cikkben talál.

## <a name="net-custom-activity"></a>.NET egyéni tevékenység
A következő tulajdonságokat adhatja meg a .NET egyéni tevékenység JSON-definíciójában. A tevékenység típustulajdonságának a következőnek kell lennie: **DotNetActivity**. Létre kell hoznia egy Azure HDInsight-kapcsolt szolgáltatást vagy egy Azure Batch-kapcsolt szolgáltatást, és meg kell adnia a csatolt szolgáltatás nevét a **linkedServiceName** tulajdonság értékeként. A következő tulajdonságokat támogatja a **typeProperties** szakasz, ha a tevékenység típusát DotNetActivity-re állítja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| Szerelvény neve | A szerelvény neve. A példában ez: **MyDotnetActivity.dll**. | Igen |
| EntryPoint |Az IDotNetActivity összeköttetést megvalósító osztály neve. A példában ez: **MyDotNetActivityNS.MyDotNetActivity** ahol MyDotNetActivityNS a névtér és A MyDotNetActivity az osztály.  | Igen |
| PackageLinkedService (Csomagcsatoló) | Az Azure Storage-hoz kapcsolódó szolgáltatás neve, amely az egyéni tevékenység zip-fájlt tartalmazó blobstorage-ra mutat. A példában ez: **AzureStorageLinkedService**.| Igen |
| PackageFile (csomagfájl) | A zip fájl neve. A példában ez: **customactivitycontainer/MyDotNetActivity.zip**. | Igen |
| extendedProperties (bővített tulajdonságok) | A . Ebben a példában a **SliceStart** változó a SliceStart rendszerváltozón alapuló értékre van beállítva. | Nem |

### <a name="json-example"></a>JSON példa

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

Részletes információt az [Egyéni tevékenységek használata a Data Factory cikkben](data-factory-use-custom-activities.md) című témakörben talál.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi oktatóanyagokat:

- [Oktatóanyag: folyamat létrehozása másolási tevékenységgel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Oktatóanyag: struktúratevékenységet létrehozó folyamat létrehozása](data-factory-build-your-first-pipeline.md)

---
title: Folyamatok létrehozása/ütemezése, lánctevékenységek az adatgyárban
description: Ismerje meg, hogyan hozhat létre adatfolyamatot az Azure Data Factoryban az adatok áthelyezéséhez és átalakításához. Hozzon létre egy adatvezérelt munkafolyamatot, amely kész az információk használatára.
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
ms.openlocfilehash: f93bea240ee3f139c9be84199d116f9f3f231261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281521"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Folyamatok és tevékenységek az Azure Data Factoryban
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-create-pipelines.md)
> * [2-es verzió (aktuális verzió)](../concepts-pipelines-activities.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a Folyamatok a [V2-ben című témakört.](../concepts-pipelines-activities.md)

Ennek a cikknek a segítségével megismerheti az Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját adatáthelyezési és -feldolgozási forgatókönyveihez.

> [!NOTE]
> Ez a cikk feltételezi, hogy az Azure Data Factory bevezetés című [szolgáltatáson](data-factory-introduction.md)keresztül ment. Ha nem rendelkezik gyakorlati tapasztalattal az adatgyárak létrehozásához, az [adatátalakítási oktatóanyag](data-factory-build-your-first-pipeline.md) és/vagy [az adatmozgásoktatóprogram](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) segít jobban megérteni ezt a cikket.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. A másolási tevékenység használatával például egy helyszíni SQL Serverből egy Azure Blob Storage-tárolóba másolhatja az adatokat. Ezt követően egy Hive-tevékenység segítségével futtasson egy Hive-szkriptet egy Azure HDInsight fürtön, amely a Blob Storage-ből származó adatokat feldolgozza vagy átalakítja a kimeneti adatok előállításához. Végül egy második másolási tevékenység használatával másolja át a kimeneti adatokat egy olyan Azure SQL Data Warehouse-ba, amelyre üzletiintelligencia-alapú (BI-) jelentéskészítési megoldások épülnek.

Minden tevékenység nulla vagy több bemeneti [adatkészletet](data-factory-create-datasets.md) képes fogadni, és egy vagy több kimeneti [adatkészletet](data-factory-create-datasets.md) képes előállítani. Az alábbi ábrán a folyamat, a tevékenység és az adat-előállító adatkészlete közötti kapcsolat látható:

![A folyamat, a tevékenység és az adatkészlet közötti kapcsolat](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

A folyamat lehetővé teszi, hogy a tevékenységeket külön-külön, hanem készletként kezelje. Például telepítheti, ütemezheti, felfüggesztheti és folytathatja a folyamatot, ahelyett, hogy a folyamatban lévő tevékenységeket egymástól függetlenül kezelne.

A Data Factory két típusú tevékenységet támogat: az adattovábbítási tevékenységeket és az adatátalakítási tevékenységeket. Minden tevékenység nulla vagy több bemeneti [adatkészlettel rendelkezhet,](data-factory-create-datasets.md) és egy vagy több kimeneti adatkészletet hozhat létre.

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
Ha olyan adattárba kell adatokat áthelyeznie, amelyet a Másolási tevékenység nem támogat, vagy saját logikával kell átalakítania az adatokat, hozzon létre **egyéni .NET tevékenységet.** További információ az egyéni tevékenységek létrehozásával és használatával kapcsolatban: [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Folyamatok ütemezése
Egy folyamat csak a **kezdési** és **befejezési** időpont között aktív. A végrehajtás nem történik meg a kezdési időpont előtt vagy a befejezési időpont után. Ha a folyamat szünetel, nem lesz végrehajtva, függetlenül annak kezdési és befejezési időpontjától. A folyamat futtatásához nem szabad szüneteltetni. Az Ütemezés és végrehajtás című témakörben [megtudhatja,](data-factory-scheduling-and-execution.md) hogyan működik az ütemezés és a végrehajtás az Azure Data Factoryban.

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

| Címke | Leírás | Kötelező |
| --- | --- | --- |
| név |A folyamat neve. Adjon meg egy, a folyamat által végrehajtandó műveletet jelölő nevet. <br/><ul><li>A karakterek maximális száma: 260</li><li>Betűszámmal vagy aláhúzásjellel\_( ) kell kezdődnie.</li><li>A következő karakterek nem engedélyezettek: ".",",","?","/",\*"<",">"," ""%","&",""\\</li></ul> |Igen |
| leírás | Adjon meg egy, az adott folyamat alkalmazását leíró szöveget. |Igen |
| tevékenységek | A **tevékenységek** szakaszon belül egy vagy több tevékenység is meghatározható. A következő szakaszban a tevékenységek JSON-elem részleteiről. | Igen |
| start | A folyamat kezdő dátum-időpontja. [ISO formátumúnak](https://en.wikipedia.org/wiki/ISO_8601)kell lennie. Például: `2016-10-14T16:32:41Z`. <br/><br/>Meg lehet adni a helyi időt, például az EST időt. Íme egy példa: `2016-02-27T06:00:00-05:00`", ami 06:00 EST.<br/><br/>A kezdő és záró tulajdonságok együttesen a folyamat aktív időszakát adják meg. A kimeneti szeletek csak ebben az aktív időszakban készülnek. |Nem<br/><br/>Ha értéket ad meg a záró tulajdonsághoz, akkor meg kell adnia a start tulajdonság értékét.<br/><br/>A kezdési és befejezési időpontok egyaránt üresek lehetnek a folyamat létrehozásához. Mindkét értéket meg kell adnia a folyamat futtatásához aktív időszak beállításához. Ha nem adja meg a folyamat létrehozásakor a kezdési és befejezési időpontokat, később a Set-AzDataFactoryPipelineActivePeriod parancsmag használatával állíthatja be őket. |
| befejezés | A folyamat dátumának záró dátuma. Ha meg van adva, ISO formátumban kell lennie. Például:`2016-10-14T17:32:41Z` <br/><br/>Meg lehet adni a helyi időt, például az EST időt. Íme egy példa: `2016-02-27T06:00:00-05:00`, ami 06:00 EST.<br/><br/>A folyamat határozatlan ideig történő futtatásához adja meg a 9999-09-09 értéket az end (befejezés) tulajdonsághoz. <br/><br/> Egy folyamat csak a kezdési és befejezési időpont között aktív. A végrehajtás nem történik meg a kezdési időpont előtt vagy a befejezési időpont után. Ha a folyamat szünetel, nem lesz végrehajtva, függetlenül annak kezdési és befejezési időpontjától. A folyamat futtatásához nem szabad szüneteltetni. Az Ütemezés és végrehajtás című témakörben [megtudhatja,](data-factory-scheduling-and-execution.md) hogyan működik az ütemezés és a végrehajtás az Azure Data Factoryban. |Nem <br/><br/>Ha értéket ad meg a start tulajdonsághoz, akkor meg kell adnia a záró tulajdonság értékét.<br/><br/>Lásd a **start** tulajdonságra vonatkozó megjegyzéseket. |
| isPaused | Ha értéke igaz, a folyamat nem fut. Szüneteltetve van. Alapértelmezett érték = hamis. Ezzel a tulajdonsággal engedélyezheti vagy letilthatja a folyamatot. |Nem |
| pipelineMode (folyamatmód) | Az ütemezés imasdula fut a folyamathoz. Az engedélyezett értékek a következők: ütemezett (alapértelmezett), egyszeri.<br/><br/>Az "Ütemezett" azt jelzi, hogy a csővezeték egy meghatározott időintervallumban fut az aktív időszak (kezdési és befejezési időpont) szerint. Az "egyszeri" azt jelzi, hogy a csővezeték csak egyszer fut. A létrehozott egyszeri folyamatok jelenleg nem módosíthatók/frissíthetők. Az egyszeri beállítással kapcsolatos részletekért tekintse meg az [Egyszeri folyamatot.](#onetime-pipeline) |Nem |
| elévülési idő | Az egyszeri [folyamat](#onetime-pipeline) érvényességét követő időtartam, amelynek kiépítésre szükséges. Ha nem rendelkezik aktív, sikertelen vagy függőben lévő futtatásokkal, a folyamat automatikusan törlődik, amint eléri a lejárati időt. Az alapértelmezett érték:`"expirationTime": "3.00:00:00"`|Nem |
| adathalmazok |A folyamatban definiált tevékenységek által használandó adatkészletek listája. Ez a tulajdonság olyan adatkészletek definiálására használható, amelyek erre a folyamatra jellemzőek, és nincsenek definiálva az adat-előállítóban. A folyamaton belül definiált adatkészleteket csak ez a folyamat használhatja, és nem oszthatók meg. A részleteket a [Scoped adatkészletek](data-factory-create-datasets.md#scoped-datasets) ben találja. |Nem |

## <a name="activity-json"></a>Tevékenység JSON-fájlja
A **tevékenységek** szakaszon belül egy vagy több tevékenység is meghatározható. Minden tevékenység a következő legfelső szintű struktúrával rendelkezik:

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

| Címke | Leírás | Kötelező |
| --- | --- | --- |
| név | A tevékenység neve. Adjon meg egy, a tevékenység által végrehajtandó műveletet jelölő nevet. <br/><ul><li>A karakterek maximális száma: 260</li><li>Betűszámmal vagy aláhúzásjellel\_( ) kell kezdődnie.</li><li>A következő karakterek nem engedélyezettek: ".",",","?","/", "<",">","""""%","&","","\\</li></ul> |Igen |
| leírás | Az adott tevékenységet vagy annak alkalmazását leíró szöveg |Igen |
| type | A tevékenység típusa. Tekintse meg az [adatmozgatási tevékenységek](#data-movement-activities) és [az adatátalakítási tevékenységek](#data-transformation-activities) szakaszt a különböző típusú tevékenységekhez. |Igen |
| Bemenetek |A tevékenység által használt beviteli táblák<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Igen |
| Kimenetek |A tevékenység által használt kimeneti táblák.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Igen |
| linkedServiceName |A tevékenység által használt társított szolgáltatás neve. <br/><br/>Egy adott tevékenység megkövetelheti annak a társított szolgáltatásnak a megadását, amely a szükséges számítási környezethez kapcsolódik. |Igen a HDInsight-tevékenységhez és az Azure Machine Learning batch-pontozási tevékenységéhez <br/><br/>Minden egyéb esetében: nem |
| typeProperties |A **typeProperties** szakasz tulajdonságai a tevékenység típusától függenek. Az adott tevékenység típustulajdonságainak megtekintéséhez kattintson az előző szakaszban szereplő tevékenységhivatkozásokra. | Nem |
| szabályzat |Olyan szabályzatok, amelyek az adott tevékenység futásidejű viselkedését befolyásolják. Ha nincs megadva, a rendszer alapértelmezett házirendeket használ. |Nem |
| scheduler | Az "ütemező" tulajdonság a tevékenység kívánt ütemezésének meghatározására szolgál. Altulajdonságai megegyeznek az [adatkészlet rendelkezésre állási tulajdonságában lévőtulajdonságokkal.](data-factory-create-datasets.md#dataset-availability) |Nem |

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

* A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa****Copy** értékre van beállítva.
* A tevékenység bemenetének beállítása **InputDataset**, a kimeneté pedig **OutputDataset**. Az adatkészletek JSON-fáljban történő meghatározását lásd az [Adatkészletek](data-factory-create-datasets.md) cikket.
* A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva. Az [Adatmozgatási tevékenységek](#data-movement-activities) csoportban kattintson a forrásként vagy fogadóként használni kívánt adattárra, ha többet szeretne megtudni az adatok nak az adott adattárba/az adattárból való áthelyezéséről.

A folyamat létrehozásának teljes forgatókönyvét az [Oktatóanyag: Adatok másolása a Blob Storage-ból az SQL Database-be](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)című témakörben ismerteti.

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

* A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa****HDInsightHive** értékre van beállítva.
* A **partitionweblogs.hql** Hive-parancsfájl tárolása az Azure Storage-fiókban (az **AzureStorageLinkedService** nevű scriptLinkedService szolgáltatás által megadva), és az **adfgetstarted** tároló **script** mappájában történik.
* A `defines` szakasz a hive-parancsfájlnak hive konfigurációs értékként (pl. `${hiveconf:inputtable}`) `${hiveconf:partitionedtable}`átadott futásidejű beállítások megadására szolgál.

A **typeProperties** szakasz eltérő az egyes átalakítási tevékenységek esetében. Ha meg szeretné tudni, hogy egy átalakítási tevékenység támogatott típustulajdonságok, kattintson az átalakítási tevékenység az [Adatátalakítási tevékenységek](#data-transformation-activities) táblában.

A folyamat létrehozásának teljes forgatókönyvét az [Oktatóanyag: Az első folyamat létrehozása az adatok Hadoop-fürt használatával történő feldolgozásához](data-factory-build-your-first-pipeline.md)olvassa el.

## <a name="multiple-activities-in-a-pipeline"></a>Több tevékenység egy adott folyamatban
Az előző két mintában a folyamatok csak egyetlen tevékenységet tartalmaztak. Egy folyamathoz azonban több tevékenység is tartozhat.

Ha egy folyamatban több tevékenység is található, és egy tevékenység kimenete nem egy másik tevékenység bemenete, a tevékenységek párhuzamosan futhatnak, ha a tevékenységek bemeneti adatszeletei készen állnak.

Két tevékenységláncolhat, ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként kell beállítani. A második tevékenység csak akkor hajtható végre, ha az első sikeresen befejeződik.

![Láncolási tevékenységek ugyanabban a csővezetékben](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Ebben a példában a folyamat két tevékenységet rendelkezik: Activity1 és Activity2. Az Activity1 a Dataset1-et bemenetként veszi fel, és kimeneti adatkészletet2 hoz létre. A tevékenység a Dataset2-t bemenetként veszi fel, és kimeneti adatkészletet3 hoz létre. Mivel az Activity1 (Dataset2) kimenete az Activity2 bemenete, az Activity2 csak akkor fut, ha a tevékenység sikeresen befejeződik, és létrehozza a Dataset2 szeletet. Ha a Activity1 valamilyen okból meghibásodik, és nem hozza létre a Dataset2 szeletet, a 2- es tevékenység nem fut az adott szelethez (például: 9:00 és 10:00 között).

A különböző folyamatokban lévő tevékenységeket is láncolhatja.

![Két csővezeték láncolása](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Ebben a példában a Pipeline1 csak egy tevékenység, amely a Dataset1 bemenetként, és létrehozza dataset2 kimenetként. A Pipeline2 is csak egy tevékenység, amely a Dataset2 bemeneti és Dataset3 kimenetként.

További információt az Ütemezés és végrehajtás című [témakörben talál.](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)
## <a name="create-and-monitor-pipelines"></a>Folyamatok létrehozása és figyelése
Folyamatokat ezen eszközök vagy SDK-k egyikével hozhat létre.

- Másolás varázsló
- Visual Studio
- Azure PowerShell
- Azure Resource Manager-sablon
- REST API
- .NET API

Az alábbi oktatóanyagokban részletes útmutatást talál a folyamatok ezen eszközök vagy SDK-k használatával történő létrehozásához.

- [Adatátalakítási tevékenységgel rendelkező folyamat létrehozása](data-factory-build-your-first-pipeline.md)
- [Folyamat létrehozása adatmozgatási tevékenységgel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

A folyamat létrehozása/üzembe helyezése után kezelheti és figyelheti a folyamatokat az Azure Portal blades vagy a Monitor and Manage App használatával. Az alábbi témakörökben részletes útmutatást talál.

- [Folyamatok figyelése és kezelése az Azure Portal blades használatával.](data-factory-monitor-manage-pipelines.md)
- [Folyamatok figyelése és kezelése a Figyelő és az Alkalmazás kezelése használatával](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Egyszeri folyamat
Létrehozhat és ütemezhet egy folyamatot, amely rendszeresen (például óránként vagy naponta) fut a folyamatdefinícióban megadott kezdési és befejezési időpontokban. A részleteket a Tevékenységek ütemezése című témakörben találja. Létrehozhat egy olyan folyamatot is, amely csak egyszer fut. Ehhez állítsa be a **pipelineMode** tulajdonságot a folyamatdefinícióban **egyszeri re,** ahogy az a következő JSON-mintában látható. A tulajdonság alapértelmezett értéke **ütemezve**van.

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

* A folyamat **kezdési** és **befejezési** időpontjanincs megadva.
* A bemeneti és kimeneti adatkészletek **rendelkezésre állása** meg van adva (**gyakoriság** és **intervallum**), annak ellenére, hogy a Data Factory nem használja az értékeket.
* A Diagram nézet nem jelenít meg egyszeri folyamatokat. Ez a működésmód szándékos.
* Az egyszeri folyamatok nem frissíthetők. Klónozhat egy egyszeri folyamatot, átnevezheti, frissítheti a tulajdonságokat, és üzembe helyezheti egy másik at.

## <a name="next-steps"></a>További lépések
- Az adatkészletekről az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat bővebben.
- A folyamatok ütemezéséről és végrehajtásáról az [Ütemezés és végrehajtás az Azure Data Factory cikkében](data-factory-scheduling-and-execution.md) című témakörben olvashat bővebben.

---
title: Az Azure Data Factory folyamatai és tevékenységei | Microsoft Docs
description: További információk a folyamatokról és tevékenységekről az Azure Data Factoryban.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 8ceae771f1a66f6d999dd0dc2b1f298d4aae8f86
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017293"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Az Azure Data Factory folyamatai és tevékenységei
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-create-pipelines.md)
> * [Aktuális verzió](concepts-pipelines-activities.md)

Ennek a cikknek a segítségével megismerheti az Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját adatáthelyezési és -feldolgozási forgatókönyveihez.

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. Például a folyamat tartalmazhat egy olyan tevékenységkészletet, amely naplóadatokat fogad és töröl, majd egy Spark-feladatot futtat egy HDInsight-fürtön a naplóadatok elemzéséhez. A folyamatok használatának az az előnye, hogy így a tevékenységek egy készletben kezelhetők, nem pedig külön-külön. Például maga a folyamat helyezhető üzembe és ütemezhető, nem a tevékenységek egymástól függetlenül.

A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. A másolási tevékenység használatával például egy helyszíni SQL Serverből egy Azure Blob Storage-tárolóba másolhatja az adatokat. Ezt követően egy Hive-tevékenység segítségével futtasson egy Hive-szkriptet egy Azure HDInsight fürtön, amely a Blob Storage-ből származó adatokat feldolgozza vagy átalakítja a kimeneti adatok előállításához. Végül egy második másolási tevékenység használatával másolja át a kimeneti adatokat egy olyan Azure SQL Data Warehouse-ba, amelyre üzletiintelligencia-alapú (BI-) jelentéskészítési megoldások épülnek.

A Data Factory három típusú tevékenységet támogat: az [adattovábbítási tevékenységeket](copy-activity-overview.md), az [adat-átalakítási tevékenységeket](transform-data.md) és a [vezérlési tevékenységeket](control-flow-web-activity.md). Minden tevékenység nulla vagy több bemeneti [adatkészletet](concepts-datasets-linked-services.md) képes fogadni, és egy vagy több kimeneti [adatkészletet](concepts-datasets-linked-services.md) képes előállítani. Az alábbi ábrán a folyamat, a tevékenység és az adat-előállító adatkészlete közötti kapcsolat látható:

![Az adatkészlet, a tevékenység és a folyamat közötti kapcsolat](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

A bemeneti adatkészletek a folyamattevékenységek bemenetét jelölik, a kimeneti adatkészletek pedig a tevékenységek kimeneteit. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Az adatkészlet létrehozását követően használhatja azt egy folyamat tevékenységei esetében. Az adatkészletek lehetnek például egy másolási tevékenység vagy egy HDInsightHive-tevékenység be- vagy kimeneti adatkészletei. Az adatkészletekről további információkat [Az Azure Data Factory adatkészletei](concepts-datasets-linked-services.md) cikkben talál.

## <a name="data-movement-activities"></a>Adattovábbítási tevékenységek
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a jelen szakaszban található táblában felsorolt adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

További információkat a [Másolási tevékenység áttekintése](copy-activity-overview.md) cikkben talál.

## <a name="data-transformation-activities"></a>Adatátalakítási tevékenységek
Az Azure Data Factory a következő átalakítási tevékenységeket támogatja, amelyek önállóan vagy más tevékenységekkel összekapcsolva adhatók hozzá a folyamatokhoz.

Adatátalakítási tevékenység | Számítási környezet
---------------------------- | -------------------
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop Streaming](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Machine Learning-tevékenységek: Kötegelt végrehajtás és erőforrás frissítése](transform-data-using-machine-learning.md) | Azure VM
[Tárolt eljárás](transform-data-using-stored-procedure.md) | Azure SQL, Azure SQL Data Warehouse vagy SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics
[Egyéni kód](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Databricks-jegyzetfüzet](transform-data-databricks-notebook.md) | Azure Databricks

További információkért tekintse meg az [adatátalakítási tevékenységekről](transform-data.md) szóló cikket.

## <a name="control-activities"></a>Vezérlési tevékenységek
A támogatott átvitelvezérlési tevékenységek a következők:

Vezérlési tevékenység | Leírás
---------------- | -----------
[Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md) | A folyamat végrehajtása tevékenység lehetővé teszi egy Data Factory-folyamat számára egy másik folyamat meghívását.
[ForEach tevékenység](control-flow-for-each-activity.md) | A ForEach tevékenység ismétlődő átvitelvezérlést határoz meg a folyamatban. Ez a tevékenység egy gyűjtemény megismétlésére, valamint egy megadott ciklustevékenység végrehajtására szolgál. E tevékenység ciklusos megvalósítása hasonló a Foreach ciklusos szerkezetéhez a programozási nyelvek esetében.
[Webes tevékenység](control-flow-web-activity.md) | A webes tevékenység segítségével meghívható egy egyéni REST-végpont egy Data Factory-folyamatból. Az adatkészleteket és a társított szolgáltatásokat továbbíthatja a tevékenység számára felhasználásra vagy elérés céljára.
[Keresési tevékenység](control-flow-lookup-activity.md) | A Keresési tevékenység segítségével bármely külső forrásból kiolvashat vagy megkereshet egy rekordot, táblanevet vagy értéket. Erre a kimenetre a későbbi tevékenységek is hivatkozhatnak.
[Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md) | A metaadatok beolvasása tevékenység segítségével az Azure Data Factory bármely metaadatát lekérheti.
[Until tevékenység](control-flow-until-activity.md) | A Do-Until ciklus megvalósítása hasonló a programozási nyelvek Do-Until ciklusos szerkezetéhez. Egy tevékenységkészletet futtat le ciklusosan addig, amíg a tevékenységhez rendelt feltétel igaz értéket nem ad vissza. Megadhat egy időtúllépési értéket az Until tevékenységhez a Data Factoryban.
[If Condition tevékenység](control-flow-if-condition-activity.md) | Az If Condition tevékenység igaz vagy hamis értéket visszaadó feltételek alapján történő elágaztatásra használható. Az If Condition tevékenység ugyanazokat a funkciókat biztosítja, mint a programnyelvek if utasítása. Egy tevékenységkészletet futtat le, ha a feltétel `true` értéket ad vissza, és egy másik tevékenységkészletet, ha a feltétel `false` értéket ad vissza.
[Wait tevékenység](control-flow-wait-activity.md) | Ha Wait tevékenységet használ egy folyamatban, akkor a folyamat kivárja a megadott időtartamot, és csak az után folytatja a további tevékenységek futtatását.

## <a name="pipeline-json"></a>A folyamat JSON-fájlja
Egy folyamat JSON-formátumban való meghatározása a következő módon történik:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
         }
    }
}
```

Címke | Leírás | Típus | Szükséges
--- | ----------- | ---- | --------
név | A folyamat neve. Adjon meg egy, a folyamat által végrehajtandó műveletet jelölő nevet. <br/><ul><li>Karakterek maximális száma: 140</li><li>Betűvel, számmal vagy aláhúzásjellel kell kezdődnie (\_)</li><li>A következő karakterek nem engedélyezettek: „.”, „+”, „?”, „/”, „<”, „>”, „ * ”, „%”, „&”, „:”, „\”</li></ul> | Karakterlánc | Igen
leírás | Adjon meg egy, az adott folyamat alkalmazását leíró szöveget. | Karakterlánc | Nem
tevékenységek | A **tevékenységek** szakaszon belül egy vagy több tevékenység is meghatározható. A tevékenységek JSON-elemeiről részletes információkat a [Tevékenység JSON-fájlja](#activity-json) szakaszban talál. | Tömb | Igen
paraméterek | Az adott folyamat **paraméterek** szakaszában egy vagy több paraméter adható meg, így a folyamat rugalmasan újrafelhasználható. | Lista | Nem

## <a name="activity-json"></a>Tevékenység JSON-fájlja
A **tevékenységek** szakaszon belül egy vagy több tevékenység is meghatározható. A tevékenységek két fő típusa van: Végrehajtási és vezérlési tevékenységek.

### <a name="execution-activities"></a>Végrehajtási tevékenységek
A végrehajtási tevékenységek közé az [adatáthelyezési](#data-movement-activities) és az [adatátalakítási tevékenységek](#data-transformation-activities) tartoznak. Ezek a következő felső szintű struktúrával rendelkeznek:

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

Az alábbi táblában a tevékenység JSON-definíciójában lévő tulajdonságok szerepelnek:

Címke | Leírás | Szükséges
--- | ----------- | ---------
név | A tevékenység neve. Adjon meg egy, a tevékenység által végrehajtandó műveletet jelölő nevet. <br/><ul><li>Karakterek maximális száma: 55</li><li>Betűvel, számmal vagy aláhúzásjellel kell kezdődnie (\_)</li><li>A következő karakterek nem engedélyezettek: „.”, „+”, „?”, „/”, „<”, „>”, „ * ”, „%”, „&”, „:”, „\” | Igen</li></ul>
leírás | Az adott tevékenységet vagy annak alkalmazását leíró szöveg | Igen
type | A tevékenység típusa. A különböző tevékenységtípusokkal kapcsolatban lásd az [adattovábbítási tevékenységeket](#data-movement-activities), az [adat-átalakítási tevékenységeket](#data-transformation-activities) és a [vezérlési tevékenységeket](#control-activities). | Igen
linkedServiceName | A tevékenység által használt társított szolgáltatás neve.<br/><br/>Egy adott tevékenység megkövetelheti annak a társított szolgáltatásnak a megadását, amely a szükséges számítási környezethez kapcsolódik. | HDInsight-tevékenységek, Azure Machine Learning kötegelt pontozási tevékenységek, tárolt eljárási tevékenységek esetében: igen. <br/><br/>Minden egyéb esetében: nem
typeProperties | A typeProperties szakasz tulajdonságai az egyes tevékenységtípusoktól függenek. Az adott tevékenység típustulajdonságainak megtekintéséhez kattintson az előző szakaszban szereplő tevékenységhivatkozásokra. | Nem
szabályzat | Olyan szabályzatok, amelyek az adott tevékenység futásidejű viselkedését befolyásolják. Ez a tulajdonság magában foglalja az időtúllépési és az újrapróbálkozási viselkedést is. Ha nincs megadva, a rendszer az alapértelmezett értékeket fogja használni. További információkat a [Tevékenységszabályzat](#activity-policy) szakaszban talál. | Nem
dependsOn | Ez a tulajdonság a tevékenységfüggőségek, valamint az egymást követő tevékenységek függőségeinek meghatározására szolgál. További információért lásd: [Tevékenységfüggőség](#activity-dependency) | Nem

### <a name="activity-policy"></a>Tevékenységszabályzat
A szabályzatok az adott tevékenység futásidejű viselkedését befolyásolják, beállíthatósági lehetőségeket biztosítva. A tevékenységszabályzatok kizárólag végrehajtási tevékenységek esetében állnak rendelkezésre.

### <a name="activity-policy-json-definition"></a>Tevékenységszabályzat JSON-definíciója

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity"
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```
JSON-név | Leírás | Megengedett értékek | Szükséges
--------- | ----------- | -------------- | --------
timeout | Megadja a futtatni kívánt tevékenység időtúllépését. | Időtartomány | Nem. Az alapértelmezett időtúllépés 7 nap.
retry | Újrapróbálkozási kísérletek maximális száma | Egész szám | Nem. Az alapértelmezett érték: 0
retryIntervalInSeconds | Az újrapróbálkozási kísérletek közötti késleltetés, másodpercben | Egész szám | Nem. Az alapértelmezett érték: 20 másodperc
secureOutput | Ha az értéke igaz, a tevékenység kimenete biztonságosnak minősül, és a rendszer nem naplózza a monitorozáshoz. | Logikai | Nem. Az alapértelmezett érték a false (hamis).

### <a name="control-activity"></a>Vezérlési tevékenység
A vezérlési tevékenységek az alábbi felső szintű struktúrával rendelkeznek:

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

Címke | Leírás | Szükséges
--- | ----------- | --------
név | A tevékenység neve. Adjon meg egy, a tevékenység által végrehajtandó műveletet jelölő nevet.<br/><ul><li>Karakterek maximális száma: 55</li><li>Betűvel, számmal vagy aláhúzásjellel kell kezdődnie (\_)</li><li>A következő karakterek nem engedélyezettek: „.”, „+”, „?”, „/”, „<”, „>”, „ * ”, „%”, „&”, „:”, „\” | Igen</li><ul>
leírás | Az adott tevékenységet vagy annak alkalmazását leíró szöveg | Igen
type | A tevékenység típusa. A különböző tevékenységtípusokkal kapcsolatban lásd az [adattovábbítási tevékenységeket](#data-movement-activities), az [adat-átalakítási tevékenységeket](#data-transformation-activities) és a [vezérlési tevékenységeket](#control-activities). | Igen
typeProperties | A typeProperties szakasz tulajdonságai az egyes tevékenységtípusoktól függenek. Az adott tevékenység típustulajdonságainak megtekintéséhez kattintson az előző szakaszban szereplő tevékenységhivatkozásokra. | Nem
dependsOn | Ez a tulajdonság a tevékenységfüggőség, valamint az egymást követő tevékenységek függőségeinek meghatározására szolgál. További információért lásd: [tevékenységfüggőség](#activity-dependency). | Nem

### <a name="activity-dependency"></a>Tevékenységfüggőség
A tevékenységfüggőség azt határozza meg, hogy az egymást követő tevékenységek milyen függőségi viszonyban vannak a megelőző tevékenységekkel, meghatározva azt a feltételt, amelytől a következő feladat végrehajtása függ. Egy adott tevékenység egy vagy több korábbi, eltérő függőségi feltétellel rendelkező tevékenységtől is függhet.

A különböző függőségi feltételek a következők: Sikeres, sikertelen, kihagyva, befejezve.

Ha például egy folyamat A tevékenység -> B tevékenység formában valósul meg, a következő forgatókönyvek lehetségesek:

- B tevékenység függőségi feltétellel rendelkezik az a tevékenység esetében **sikeres**: B tevékenység csak akkor fog futni, ha a tevékenység végállapota, sikeres volt
- B tevékenység függőségi feltétellel rendelkezik az a tevékenység esetében **sikertelen**: B tevékenység csak akkor fog futni, ha a tevékenység végállapota, nem sikerült
- B tevékenység függőségi feltétellel rendelkezik az a tevékenység esetében **befejeződött**: B tevékenység akkor fut, ha a tevékenység végállapota a sikeres vagy sikertelen
- B tevékenység függőségi feltétellel rendelkezik az a tevékenység esetében **kihagyva**: B tevékenység-végrehajtás A tevékenység végállapota, ha kihagyja. Kihagyva állapot következik be az X tevékenység -> Y tevékenység -> Z tevékenység forgatókönyvben, ahol minden egyes tevékenység csak akkor fut le, ha az előző tevékenység sikeresen lefutott. Ha az X tevékenység sikertelen, akkor az Y tevékenység „Kihagyva” állapotot vesz fel, mert azt a rendszer soha nem hajtja végre. Hasonlóképpen a Z tevékenység is „Kihagyva” állapotot vesz fel.

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Példa: 2 tevékenység az a tevékenység 1. tevékenység sikerességétől függ

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>Minta másolási folyamat
Az alábbi mintafolyamat **tevékenységek** szakaszában egyetlen **Másolás** típusú tevékenység található. Ebben a mintában a [másolási tevékenység](copy-activity-overview.md) adatokat másol egy Azure blobtárolóból egy Azure SQL-adatbázisba.

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
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Vegye figyelembe a következő szempontokat:

- A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa** **Copy** értékre van beállítva.
- A tevékenység bemenetének beállítása **InputDataset**, a kimeneté pedig **OutputDataset**. Az adatkészletek JSON-fáljban történő meghatározását lásd az [Adatkészletek](concepts-datasets-linked-services.md) cikket.
- A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva. Az [adattovábbítási tevékenységek](#data-movement-activities) szakaszban kattintson a forrásként vagy fogadóként használni kívánt adattárra, hogy további információkhoz jusson az adott adattár esetén a kifelé vagy befelé irányuló adatáthelyezési lehetőségekről.

E folyamat létrehozásának teljes leírását lásd: [Gyors útmutató: adat-előállító létrehozása](quickstart-create-data-factory-powershell.md).

## <a name="sample-transformation-pipeline"></a>Minta átalakítási folyamat
Az alábbi mintafolyamat **tevékenységek** szakaszában egyetlen **HDInsightHive** típusú tevékenység található. Ebben a mintában a [HDInsight Hive-tevékenység](transform-data-using-hadoop-hive.md) egy Azure blobtárolóból származó adatokat alakít át egy Hive-szkriptfájl Azure HDInsight Hadoop-fürtön történő futtatásával.

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
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
Vegye figyelembe a következő szempontokat:

- A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa** **HDInsightHive** értékre van beállítva.
- A **partitionweblogs.hql** Hive-parancsfájl tárolása az Azure Storage-fiókban (az AzureStorageLinkedService nevű scriptLinkedService szolgáltatás által megadva), és az `adfgetstarted` tároló script mappájában történik.
- A `defines` szakasz meghatározza a futásidő beállításait, amelyek Hive konfigurációs értékekként (például $`{hiveconf:inputtable}`, `${hiveconf:partitionedtable}`) lesznek átadva a Hive-parancsfájlnak.

A **typeProperties** szakasz eltérő az egyes átalakítási tevékenységek esetében. Ahhoz, hogy megismerkedhessen az egyes átalakítási tevékenységek által támogatott típustulajdonságokkal, kattintson az adott átalakítási tevékenységre az [Adatátalakítási tevékenységek](#data-transformation-activities) szakaszban.

E folyamat létrehozásának teljes leírását lásd: [Oktatóanyag: adatátalakítás a Spark használatával](tutorial-transform-data-spark-powershell.md).

## <a name="multiple-activities-in-a-pipeline"></a>Több tevékenység egy adott folyamatban
Az előző két mintában a folyamatok csak egyetlen tevékenységet tartalmaztak. Egy folyamathoz azonban több tevékenység is tartozhat. Ha az adott folyamatban több tevékenység is található, és az egymást követő tevékenységek nem függnek az azokat megelőző tevékenységektől, akkor ezek párhuzamosan is futtathatók.

A [tevékenységfüggőség](#activity-dependency) segítségével összefűzhet két tevékenységet. Ez a fajta függőség azt határozza meg, hogy az egymást követő tevékenységek milyen függőségi viszonyban vannak a megelőző tevékenységekkel, meghatározva azt a feltételt, amelytől a következő feladat végrehajtása függ. Egy adott tevékenység egy vagy több korábbi, eltérő függőségi feltétellel rendelkező tevékenységtől is függhet.

## <a name="scheduling-pipelines"></a>Folyamatok ütemezése
A folyamatok ütemezése eseményindítókkal történik. Az eseményindítóknak különböző típusai léteznek (ütemező eseményindító, amely a folyamatok indítását időpont alapján teszi ütemezhetővé, illetve manuális eseményindító, amely igény szerinti folyamatütemezést tesz lehetővé). Az eseményindítókról további információkat a [Folyamat futtatása és eseményindítók](concepts-pipeline-execution-triggers.md) cikkben talál.

Ahhoz, hogy az eseményindító kiváltsa egy folyamat indítását, az eseményindító meghatározásába bele kell foglalni az adott folyamat referenciáját. A folyamatok és az eseményindítók n-m kapcsolattal rendelkeznek. Egyetlen folyamatot több eseményindító is indíthat, és ugyanaz az eseményindító indíthat több folyamatot is. Ha az eseményindító meghatározása megtörtént, el kell indítania azt, hogy az képes legyen az adott folyamat indítására. Az eseményindítókról további információkat a [Folyamat futtatása és eseményindítók](concepts-pipeline-execution-triggers.md) cikkben talál.

Tegyük fel, hogy például van egy „A eseményindító” nevű ütemező eseményindítónk, és azzal kívánjuk indítani a „MyCopyPipeline” nevű folyamatot. Az eseményindító meghatározása az alábbi példában látható módon történik:

### <a name="trigger-a-definition"></a>Az A eseményindító meghatározása

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```



## <a name="next-steps"></a>További lépések
A folyamatok tevékenységekkel együtt történő létrehozásáról részletes útmutatást a következő oktatóanyagokban talál:

- [Másolási tevékenységgel rendelkező folyamat létrehozása](quickstart-create-data-factory-powershell.md)
- [Adatátalakítási tevékenységgel rendelkező folyamat létrehozása](tutorial-transform-data-spark-powershell.md)

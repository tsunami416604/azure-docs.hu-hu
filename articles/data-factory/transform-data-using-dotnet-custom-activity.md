---
title: "Egyéni tevékenységek használata Azure Data Factory-folyamatban"
description: "Megtudhatja, hogyan hozzon létre egyéni tevékenységeket, és használja őket az Azure Data Factory-folyamathoz."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 2674b431ba610bccb92f6b209970af1fab110f48
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Egyéni tevékenységek használata Azure Data Factory-folyamatban
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-use-custom-activities.md)
> * [2. verzió – Előzetes verzió](transform-data-using-dotnet-custom-activity.md)

Egy Azure Data Factory-folyamathoz használható tevékenységeknek két típusa van.

- [Adatok mozgása tevékenységek](copy-activity-overview.md) közötti áthelyezése [támogatott forrás és a fogadó adattárolókhoz](copy-activity-overview.md#supported-data-stores-and-formats).
- [Adatok átalakítása tevékenységek](transform-data.md) adatok átalakítására a számítási szolgáltatásokat, például Azure HDInsight, az Azure Batch és az Azure Machine Learning segítségével. 

Áthelyezése egy adatok az adattárolási, hogy a Data Factory nem támogatja, vagy átalakítási/folyamat adatokat úgy, hogy a Data Factory nem támogatja, létrehozhat egy **egyéni tevékenység** saját adatmozgás vagy átalakítási logika és használata a tevékenység egy folyamaton belül. Az egyéni tevékenység fut a testreszabott kód logika egy **Azure Batch** virtuális gépek készletét.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [(egyéni) DotNet tevékenység V1](v1/data-factory-use-custom-activities.md).
 

Lásd az alábbi témakörök, ha most ismerkedik az Azure Batch szolgáltatás:

* [Azure Batch alapjai](../batch/batch-technical-overview.md) az Azure Batch szolgáltatás áttekintését.
* [Új AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) parancsmaggal hozhat létre Azure Batch-fiók (vagy) [Azure-portálon](../batch/batch-account-create-portal.md) az Azure portál használata az Azure Batch-fiók létrehozásához. Lásd: [PowerShell használatával kezelheti az Azure Batch-fiók](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) témakör részletes útmutatást a parancsmag segítségével.
* [Új AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) parancsmaggal hozhat létre Azure Batch-készlet.

## <a name="azure-batch-linked-service"></a>Azure Batch társított szolgáltatás 
A következő JSON társított Azure Batch szolgáltatás minta határozza meg. További információkért lásd: [számítási környezetek Azure Data Factory által támogatott](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 Csatolt Azure Batch szolgáltatás kapcsolatos további információkért lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) cikk. 

## <a name="custom-activity"></a>Egyéni tevékenység

A következő JSON-részlet egy folyamatot egy egyszerű egyéni tevékenységeket definiálja. A tevékenység definíciójának a kapcsolódó Azure Batch szolgáltatás hivatkozás van. 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

Ez a példa a helloworld.exe egy egyéni a customactv2/helloworld mappában található a resourceLinkedService használt Azure Storage-fiók alkalmazást. Az egyéni tevékenység az egyéni alkalmazás hajtható végre az Azure Batch küldi el. Módosíthatja bármely elsődleges alkalmazás, amely a cél operációs rendszer az Azure Batch-készlet csomópontok hajtható végre a parancsot. 

A következő táblázat ismerteti a neveket és leírásokat erre a tevékenységre jellemző tulajdonságok. 

| Tulajdonság              | Leírás                              | Szükséges |
| :-------------------- | :--------------------------------------- | :------- |
| név                  | A tevékenység a folyamat neve.     | Igen      |
| leírás           | A tevékenység mit leíró szöveg.  | Nem       |
| type                  | Egyéni tevékenység, a tevékenység típusa nem **egyéni**. | Igen      |
| linkedServiceName     | Az Azure Batch társított szolgáltatást. A csatolt szolgáltatással kapcsolatos további tudnivalókért lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) cikk.  | Igen      |
| a parancs               | Az egyéni alkalmazás végrehajtandó parancs. Ha az alkalmazás már az Azure Batch alkalmazáskészlet-csomóponton elérhető, a resourceLinkedService és folderPath figyelmen kívül hagyja. Például megadhatja a parancs futtatásával kell `cmd /c dir`, amely natív módon támogatott a Windows Batch-készlet csomópont. | Igen      |
| resourceLinkedService | Az Azure Storage társított szolgáltatás a tárfiókhoz, az egyéni alkalmazás tárolására | Nem       |
| folderPath            | Az egyéni alkalmazás és annak függőségeit a mappa elérési útja | Nem       |
| referenceObjects      | Meglévő társított szolgáltatások és adatkészletek tömbjét. A hivatkozott társított szolgáltatások és adatkészletek átadott az egyéni alkalmazás JSON formátumban, egyéni kód is hivatkozni lehessen a Data Factory erőforrásai | Nem       |
| extendedProperties    | Egyéni kód is hivatkozhat olyan további, az egyéni alkalmazás JSON formátumban kell átadni, felhasználó által definiált tulajdonságok | Nem       |

## <a name="executing-commands"></a>Parancsok végrehajtása

A parancs az egyéni tevékenység közvetlenül hajthat végre. A következő példában azt "echo hello world" parancs futtatása a cél Azure Batch-készlet csomópontokon, és kiírja a stdout kimenet. 

  ```json
  {
    "name": "MyCustomActivity",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "cmd /c echo hello world"
        }
      }]
    }
  } 
  ```

## <a name="passing-objects-and-properties"></a>Objektumok és tulajdonságok

Ez a példa bemutatja, hogyan segítségével a referenceObjects és extendedProperties adat-előállító objektumok és a felhasználó által definiált tulajdonságok adhatók át az alkalmazást. 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            },
            "PropertyBagPropertyName1": "PropertyBagValue1",
            "propertyBagPropertyName2": "PropertyBagValue2",
            "dateTime1": "2015-04-12T12:13:14Z"              
        }
      }
    }]
  }
}
```

Ha a tevékenység végrehajtása, referenceObjects és extendedProperties következő fájlok tárolják, amely a SampleApp.exe végrehajtási mappában vannak telepítve: 

- activity.json

  Az extendedProperties és az egyéni tevékenység tulajdonságok tárolja. 

- linkedServices.json

  Tárolja a referenceObjects tulajdonságban definiált összekapcsolt szolgáltatások tömbjét. 

- datasets.json

  Tárolja a referenceObjects tulajdonságban definiált adatkészletek tömbjét. 

A következő példakód azt mutatják be, hogyan a SampleApp.exe érje el a szükséges adatokat JSON-fájlokból: 

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Végrehajtás kimenetének beolvasása

  A következő PowerShell-paranccsal folyamatot futtató megkezdése: 

  ```.powershell
  $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
  ```
  Amikor a folyamat fut, a végrehajtás kimenetének az alábbi parancsokkal ellenőrizheti: 

  ```.powershell
  while ($True) {
      $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

      if(!$result) {
          Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
      }
      elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
          Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
      }
      else {
          Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
          $result
          break
      }
      ($result | Format-List | Out-String)
      Start-Sleep -Seconds 15
  }

  Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
  $result.Output -join "`r`n"

  Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
  $result.Error -join "`r`n"
  ```

  A **stdout** és **stderr** az alkalmazást a rendszer menti a **adfjobs** az Azure Storage társított szolgáltatás Azure Batch csatolt létrehozásakor megadott tárolóhoz A feladat egy GUID-szolgáltatás. Ahogy az az alábbi kódrészletet, a részletes útvonal lekérheti kimeneti tevékenység fut: 

  ```shell
  Pipeline ' MyCustomActivity' run finished. Result:

  ResourceGroupName : resourcegroupname
  DataFactoryName   : datafactoryname
  ActivityName      : MyCustomActivity
  PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PipelineName      : MyCustomActivity
  Input             : {command}
  Output            : {exitcode, outputs, effectiveIntegrationRuntime}
  LinkedServiceName : 
  ActivityRunStart  : 10/5/2017 3:33:06 PM
  ActivityRunEnd    : 10/5/2017 3:33:28 PM
  DurationInMs      : 21203
  Status            : Succeeded
  Error             : {errorCode, message, failureType, target}

  Activity Output section:
  "exitcode": 0
  "outputs": [
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
  ]
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
  Activity Error section:
  "errorCode": ""
  "message": ""
  "failureType": ""
  "target": "MyCustomActivity"
  ```
Ha szeretné, hogy az alsóbb rétegbeli tevékenységet stdout.txt tartalmakat, a fájl elérési útját a stdout.txt kifejezésben kaphat "@activity(MyCustomActivity).output.outputs [0]". 

  > [!IMPORTANT]
  > - A activity.json linkedServices.json és datasets.json a fürdőbe tevékenység a futásidejű mappába kerülnek. Az ebben a példában a activity.json linkedServices.json és datasets.json vannak tárolva "https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/" elérési út. Ha szükséges, eltávolítással külön-külön szeretné. 
  > - Társított szolgáltatások felhasználásra Self-Hosted integrációs futásidejű, a bizalmas adatok, például a kulcsokat vagy jelszavakat a Self-Hosted integrációs futásidejű annak biztosítása érdekében a hitelesítő adatok titkosított marad az ügyfelek definiált titkos hálózati környezetben. Néhány időérzékeny mezőinek hiányzó lehet, amikor ezzel a módszerrel egyéni alkalmazáskódjában hivatkozik. Szükség esetén a társított szolgáltatás hivatkozása helyett extendedProperties SecureString használja. 

## <a name="difference-between-custom-activity-in-azure-data-factory-v2-and-custom-dotnet-activity-in-azure-data-factory-v1"></a>Az Azure Data Factory V2 egyéni tevékenységet és az Azure Data Factory 1-es verzió (egyéni) DotNet tevékenység közötti különbség 

  Az Azure Data Factory V1, hozzon létre egy .net implementálná (egyéni) DotNet tevékenység kód Class Library projekt, amely megvalósítja az Execute metódus IDotNetActivity interfész osztállyal. Az összekapcsolt szolgáltatások, az adatkészleteket és az Extended Properties (egyéni) DotNet tevékenység JSON-adattartalmat a rendszer a végrehajtási metódusnak átadott erős típusos objektumként. További információkért tekintse meg [(egyéni) DotNet a V1](v1/data-factory-use-custom-activities.md). Miatt, hogy a saját kódjára ki kellene írni a .net keretrendszer 4.5.2-es verzióját, és a Windows-alapú Azure Batch-készlet csomópontok hajtható végre. 

  Az Azure Data Factory V2 egyéni tevékenység meg nem kell egy .net felületet valósítja meg. Most közvetlenül futtathat parancsok, parancsprogramok, és futtassa a saját egyéni kód tartani, végrehajtható. Érhetők el, a parancs tulajdonság, és a folderPath tulajdonság megadásával. Egyéni tevékenység feltölti a végrehajtható fájl és a függőségek a folderpath, és végrehajtja a parancsot meg. 

  Az összekapcsolt szolgáltatások (referenceObjects meghatározott) adatkészletek és definiált egyéni tevékenység JSON-adattartalmat Extended Properties hozzáférhet a végrehajtható fájl JSON-fájlok formájában. A szükséges tulajdonságait, ahogy az előző SampleApp.exe kódminta JSON-szerializáló használatával érheti el. 

  Az Azure Data Factory V2 egyéni tevékenység bevezetett változások szabadon a egyéni kód logika írását a választott nyelven és a Windows és Linux művelet Azure Batch által támogatott hajthatnak végre. 

  A következő táblázat ismerteti a Data Factory V2 egyéni tevékenység és a Data Factory V1 közötti különbséget (egyéni) DotNet tevékenység: 


|Különbségek      |ADFv2 egyéni tevékenység      |ADFv1 (egyéni) DotNet tevékenység      |
| ---- | ---- | ---- |
|Hogyan egyéni logika van definiálva.      |Futtassa a végrehajtható fájlok (meglévő, vagy saját végrehajtható megvalósítása)      |A .net DLL-fájl végrehajtása      |
|Az egyéni logika végrehajtási felhasználásához      |A Windows vagy Linux      |Windows (.Net-keretrendszer 4.5.2)      |
|Parancsprogram végrehajtása      |Támogatja a parancsprogram végrehajtása közvetlenül (például "cmd /c echo hello world" a Windows virtuális gép)      |Szükséges a .net DLL-fájl végrehajtása      |
|A DataSet szükséges      |Optional      |Tanúsítványlánc-tevékenységek információkezelési és -átadási szükséges      |
|A tevékenységből át adatokat egyéni logika      |ReferenceObjects (LinkedServices és adatkészletek) és a ExtendedProperties (egyéni tulajdonságok) és      |Az ExtendedProperties (egyéni tulajdonságokat), valamint bemeneti és kimeneti adatkészletek keresztül      |
|Az egyéni logika adatbeolvasás      |Activity.json linkedServices.json és a végrehajtható fájl ugyanabban a mappában tárolt datasets.json elemzése      |A .net SDK (.Net keret 4.5.2.)      |
|Naplózás      |Közvetlenül STDOUT ír      |A .net DLL Implemeting naplózó      |


  Ha rendelkezik meglévő V1 készült .net-kódot (egyéni) DotNet tevékenység, módosítania kell a kódot, hogy V2 egyéni tevékenységeket dolgozni a következő általános irányelveket:  

   - Módosítsa a projekt egy .net Class Library konzol alkalmazásokhoz. 
   - Az alkalmazás a fő metódus indításához az Execute metódus IDotNetActivity interfész már nincs szükség. 
   - Olvassa el és elemezni az összekapcsolt szolgáltatások, az adatkészleteket és a tevékenység JSON-szerializáló ahelyett, hogy az erős típusos objektumként, és a szükséges tulajdonság értékét átadása a fő egyéni kód logika. Tekintse át a megelőző SampleApp.exe kód egy mintát. 
   - Naplózó objektum már nem támogatott, a végrehajtható program bejegyzésekor kimenetek nyomtatási konzolba, valamint az stdout.txt menti. 
   - Microsoft.Azure.Management.DataFactories NuGet-csomag már nincs szükség. 
   - A kód fordítása, töltse fel az executable és a függőségek Azure Storage és a folderPath tulajdonság határozza meg az elérési út. 

Egy teljes mintát bemutatja a végpontok közötti dll-fájl és a kimenetátirányítási minta Data Factory V1 a dokumentumban ismertetett [egyéni tevékenységeket használni egy Azure Data Factory-folyamathoz](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) lehet írja át a Data Factory V2 egyéni tevékenység stílusát. Tekintse meg a [Data Factory V2 egyéni tevékenység minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFv2CustomActivitySample). 

## <a name="auto-scaling-of-azure-batch"></a>Az Azure Batch automatikus skálázás
Az Azure Batch-készlet is létrehozhat **automatikus skálázás** szolgáltatás. Például létrehozhatja az azure batch-készlet 0 dedikált virtuális gépek és az automatikus skálázás képlet függőben lévő feladatok száma alapján. 

A minta képlet itt éri el a következő viselkedés: a készlet létrehozásakor 1 virtuális gép kezdődik. $PendingTasks metrika feladatok száma definiálja a futó + (aszinkron) aktív állapotban.  A képlet átlagos száma függőben lévő feladatok megkeresi az elmúlt 180 másodperc alatt, és ennek megfelelően állítja be TargetDedicated. Biztosítja, hogy TargetDedicated soha nem túllép 25 virtuális gépeket. Igen új feladatok nyújtják, készlet automatikusan növekszik és feladatok befejezését, mint a virtuális gépek válik a szabad egyenként és az automatikus skálázás zsugorítja a virtuális gépek. startingNumberOfVMs és maxNumberofVMs az igényeinek megfelelően kell beállítani.

Automatikus skálázás képlet:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Lásd: [automatikus méretezési számítási csomópontok az Azure Batch-készlet](../batch/batch-automatic-scaling.md) részleteiről.

Ha az alkalmazáskészlet által használt alapértelmezett [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), a Batch szolgáltatás a virtuális gép előkészítése az egyéni tevékenység futtatása előtt 15 – 30 percet vehet igénybe.  Ha a készlet egy másik autoScaleEvaluationInterval használ, a Batch szolgáltatás autoScaleEvaluationInterval + 10 percet is beletelhet.


## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkekben talál, amely ismerteti az adatok más módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [A Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce művelethez](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [A Spark-tevékenység](transform-data-using-spark.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [A tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)

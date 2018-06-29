---
title: Egyéni tevékenységek használata Azure Data Factory-folyamatban
description: Megtudhatja, hogyan hozzon létre egyéni tevékenységeket, és használja őket az Azure Data Factory-folyamathoz.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 2dab0adb0728a1fb5e8ac9bebe01f861ed8c7c3a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37058915"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Egyéni tevékenységek használata Azure Data Factory-folyamatban
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](v1/data-factory-use-custom-activities.md)
> * [Aktuális verzió](transform-data-using-dotnet-custom-activity.md)

Egy Azure Data Factory-folyamathoz használható tevékenységeknek két típusa van.

- [Adatok mozgása tevékenységek](copy-activity-overview.md) közötti áthelyezése [támogatott forrás és a fogadó adattárolókhoz](copy-activity-overview.md#supported-data-stores-and-formats).
- [Adatok átalakítása tevékenységek](transform-data.md) adatok átalakítására a számítási szolgáltatásokat, például Azure HDInsight, az Azure Batch és az Azure Machine Learning segítségével. 

Áthelyezése egy adatok az adattárolási, hogy a Data Factory nem támogatja, vagy átalakítási/folyamat adatokat úgy, hogy a Data Factory nem támogatja, létrehozhat egy **egyéni tevékenység** saját adatmozgás vagy átalakítási logika és használata a tevékenység egy folyamaton belül. Az egyéni tevékenység fut a testreszabott kód logika egy **Azure Batch** virtuális gépek készletét.

Lásd az alábbi cikkeket, ha most ismerkedik az Azure Batch szolgáltatás:

* [Azure Batch alapjai](../batch/batch-technical-overview.md) az Azure Batch szolgáltatás áttekintését.
* [Új AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) parancsmaggal hozhat létre Azure Batch-fiók (vagy) [Azure-portálon](../batch/batch-account-create-portal.md) az Azure portál használata az Azure Batch-fiók létrehozásához. Lásd: [PowerShell használatával kezelheti az Azure Batch-fiók](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) részletes információkra van szüksége a parancsmaggal a cikkben találhat.
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
| command               | Az egyéni alkalmazás végrehajtandó parancs. Ha az alkalmazás már az Azure Batch alkalmazáskészlet-csomóponton elérhető, a resourceLinkedService és folderPath figyelmen kívül hagyja. Például megadhatja a parancs futtatásával kell `cmd /c dir`, amely natív módon támogatott a Windows Batch-készlet csomópont. | Igen      |
| resourceLinkedService | Az Azure Storage társított szolgáltatás a tárfiókhoz, az egyéni alkalmazás tárolására | Nem       |
| folderPath            | Az egyéni alkalmazás és annak függőségeit a mappa elérési útja | Nem       |
| referenceObjects      | Meglévő társított szolgáltatások és adatkészletek tömbjét. A hivatkozott társított szolgáltatások és adatkészletek átadott az egyéni alkalmazás JSON formátumban, egyéni kód is hivatkozni lehessen a Data Factory erőforrásai | Nem       |
| extendedProperties    | Egyéni kód is hivatkozhat olyan további, az egyéni alkalmazás JSON formátumban kell átadni, felhasználó által definiált tulajdonságok | Nem       |

## <a name="executing-commands"></a>Parancsok végrehajtása

A parancs az egyéni tevékenység közvetlenül hajthat végre. Az alábbi példában a "echo hello world" parancsot futtatja, a cél Azure Batch-készlet csomópontokon, és kiírja a stdout kimenet. 

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

- Activity.JSON

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
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
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
Ha szeretné, hogy az alsóbb rétegbeli tevékenységet stdout.txt tartalmakat, a fájl elérési útját a stdout.txt kifejezésben kaphat "\@activity('MyCustomActivity').output.outputs [0]". 

  > [!IMPORTANT]
  > - A activity.json linkedServices.json és datasets.json a kötegelt tevékenység futásidejű mappában tárolják. Az ebben a példában a activity.json linkedServices.json és datasets.json vannak tárolva "https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/" elérési út. Ha szükséges, eltávolítással külön-külön szeretné. 
  > - Társított szolgáltatások felhasználásra Self-Hosted integrációs futásidejű, a bizalmas adatok, például a kulcsokat vagy jelszavakat a Self-Hosted integrációs futásidejű annak biztosítása érdekében a hitelesítő adatok titkosított marad az ügyfelek definiált titkos hálózati környezetben. Néhány időérzékeny mezőinek hiányzó lehet, amikor ezzel a módszerrel egyéni alkalmazáskódjában hivatkozik. Szükség esetén a társított szolgáltatás hivatkozása helyett extendedProperties SecureString használja. 

## <a name="compare-v2-v1"></a> Hasonlítsa össze v2 egyéni tevékenység és (egyéni) 1-es verziójú DotNet tevékenység

  Az Azure Data Factoryben az 1-es, hozzon létre egy .net alkalmazhat egy (egyéni) DotNet tevékenység Class Library a projekt egy osztály, amely megvalósítja a `Execute` metódusában a `IDotNetActivity` felületet. A társított szolgáltatások adatkészletek és a JSON-adattartalmat (egyéni) DotNet tevékenység az Extended Properties kerülnek átadásra a végrehajtási metódus szigorú típusmegadású objektumként. Az 1-es verziójú viselkedés kapcsolatos részletekért lásd: [az 1-es verziójú (egyéni) DotNet](v1/data-factory-use-custom-activities.md). Miatt ez a megvalósítás a 1-es verziójú DotNet tevékenység kódot tartalmaz, amelyekre a .net keretrendszer 4.5.2-es verzióját. 1-es verziójával DotNet tevékenység is rendelkezik Windows-alapú Azure Batch-készlet csomópontján hajthatnak végre. 

  Az Azure Data Factory V2 egyéni tevékenység, a meg nem kell egy .net felületet valósítja meg. Most már közvetlenül futtathatja parancsok, parancsprogramok és a saját egyéni kód a végrehajtható fájlként összeállított. Ez a megvalósítás konfigurálásához meg kell adnia a `Command` és a tulajdonság a `folderPath` tulajdonság. Az egyéni tevékenység feltölti a végrehajtható parancs és annak függőségeit a `folderpath` és végrehajtja a parancsot meg. 

  Az összekapcsolt szolgáltatások, adatkészleteket (referenceObjects meghatározott), és a Data Factory v2 egyéni tevékenység hozzáfér a végrehajtható fájl JSON-fájlok formájában JSON hasznos a meghatározott kiterjesztett tulajdonság. A szükséges tulajdonságait egy JSON-szerializáló az előző SampleApp.exe kód mintában látható módon használatával érheti el. 

  A Data Factory V2 egyéni tevékenység bevezetett változások az egyéni kód logika írását a választott nyelven, és végrehajtja a Windows és Linux művelet Azure Batch által támogatott. 

  A következő táblázat ismerteti a (egyéni) a Data Factory V2 egyéni tevékenység és a Data Factory 1-es verziójú közötti különbséget DotNet tevékenység: 


|Különbségek      | Egyéni tevékenység      | (egyéni) 1-es verziójú DotNet tevékenység      |
| ---- | ---- | ---- |
|Hogyan egyéni logika van definiálva.      |Ha egy végrehajtható fájl      |A .net DLL-fájl végrehajtása      |
|Az egyéni logikai a végrehajtási környezet      |A Windows vagy Linux      |Windows (.Net-keretrendszer 4.5.2)      |
|Parancsprogram végrehajtása      |Támogatja a közvetlen parancsfájlok (például "cmd /c echo hello world" a Windows virtuális gép) végrehajtása      |Szükséges a .net DLL-fájl végrehajtása      |
|A DataSet szükséges      |Optional      |Tanúsítványlánc-tevékenységek információkezelési és -átadási szükséges      |
|A tevékenységből át adatokat egyéni logika      |ReferenceObjects (LinkedServices és adatkészletek) és a ExtendedProperties (egyéni tulajdonságok)      |Az ExtendedProperties (egyéni tulajdonságok), bemeneti és kimeneti adatkészletek      |
|Az egyéni logika adatbeolvasás      |Activity.json linkedServices.json és a végrehajtható fájl ugyanabban a mappában tárolt datasets.json elemez      |A .net SDK (.Net keret 4.5.2.)      |
|Naplózás      |Közvetlenül STDOUT ír      |A .net DLL végrehajtási naplózó      |


  Ha meglévő .net kódok verziójához 1 (egyéni) DotNet tevékenység, módosítania az egyéni tevékenység az aktuális verzióval működéshez a kódot. Frissítse a kódot a következő általános iránymutatás:  

   - Módosítsa a projekt egy .net Class Library konzol alkalmazásokhoz. 
   - Indítsa el az alkalmazást a `Main` metódust. A `Execute` metódusában a `IDotNetActivity` felület már nincs szükség. 
   - Olvassa el, és elemezni a társított szolgáltatások, az adatkészletek és a tevékenység JSON-szerializáló, valamint nem szigorú típusmegadású objektumok. A szükséges tulajdonság értékét átadása a fő egyéni kód logika. Lásd az előző SampleApp.exe példaként. 
   - A tranzakciónaplókat tartalmazó objektum már nem támogatott. A konzol kimenetét a végrehajtható fájl nyomtatható és stdout.txt vannak mentve. 
   - A Microsoft.Azure.Management.DataFactories NuGet-csomag már nincs szükség. 
   - A kód fordítása, a végrehajtható parancs és annak függőségeit feltöltése az Azure Storage és az elérési utat határozza meg a `folderPath` tulajdonság. 

Egy teljes mintát, hogyan a végpont dll-fájl és a kimenetátirányítási minta ismertetett a Data Factory verzió 1 cikk [egyéni tevékenységeket használni egy Azure Data Factory-folyamathoz](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) is kell írni a Data Factory egyéni tevékenységként, Lásd:[ Data Factory egyéni tevékenység minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFv2CustomActivitySample). 

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

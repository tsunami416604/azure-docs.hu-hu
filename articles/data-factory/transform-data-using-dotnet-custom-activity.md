---
title: Egyéni tevékenységek használata Azure Data Factory-folyamatban
description: Ismerje meg, hogyan hozhat létre egyéni tevékenységeket, és használja őket az Azure Data Factory-folyamatot.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: douglasl
ms.openlocfilehash: 34a3b00fdc0644294a97272be7b3a06715c029a1
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121328"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Egyéni tevékenységek használata Azure Data Factory-folyamatban
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-use-custom-activities.md)
> * [Aktuális verzió](transform-data-using-dotnet-custom-activity.md)

Két típusa a tevékenységeket, az Azure Data Factory-folyamatban van.

- [Adattovábbítási tevékenységek](copy-activity-overview.md) közötti áthelyezése [támogatott forrás- és fogadó adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
- [Adat-átalakítási tevékenységeket](transform-data.md) adatok átalakításához a számítási szolgáltatások, például Azure HDInsight, az Azure Batch és az Azure Machine Learning használatával. 

Áthelyezése egy adatok tárolására, hogy nem támogatja a Data Factory, vagy úgy, hogy a Data Factory által nem támogatott az adatok átalakíthatók/feldolgozhatók, létrehozhat egy **egyéni tevékenység** saját adatáthelyezési vagy Adatátalakítási logikát és használata a tevékenységet a folyamat. Az egyéni tevékenység fut az egyéni kód logikára egy **Azure Batch** virtuálisgép-készletek.

Lásd az alábbi cikkeket, ha most ismerkedik az Azure Batch szolgáltatás:

* [Az Azure Batch alapjai](../batch/batch-technical-overview.md) az Azure Batch szolgáltatás áttekintése.
* [Új AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) parancsmaggal hozzon létre egy Azure Batch-fiók (vagy) [az Azure portal](../batch/batch-account-create-portal.md) létrehozása az Azure Batch-fiókot az Azure portal használatával. Lásd: [PowerShell használata kezelheti az Azure Batch-fiók](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) cikk nyújt részletes tájékoztatást a parancsmag használatával.
* [Új-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) parancsmaggal hozzon létre egy Azure Batch-készletben.

## <a name="azure-batch-linked-service"></a>Az Azure Batch-beli társított szolgáltatás 
A következő JSON egy minta Azure Batch társított szolgáltatás határozza meg. További információkért lásd: [számítási környezetek Azure Data Factory által támogatott](compute-linked-services.md)

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

 Társított Azure Batch szolgáltatással kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk. 

## <a name="custom-activity"></a>Egyéni tevékenység

A következő JSON-kódrészlet definiál egy egyszerű egyéni tevékenységgel rendelkező folyamatot. A tevékenység meghatározásában a társított Azure Batch szolgáltatás vonatkozó hivatkozás van. 

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

Ebben a példában a helloworld.exe egy egyéni alkalmazást a resourceLinkedService használt Azure Storage-fiók customactv2/helloworld mappában lesz tárolva. Az egyéni tevékenység küldi el az egyéni alkalmazást az Azure Batch hajtható végre. Lecserélheti a parancsot minden olyan előnyben részesített alkalmazás, amely a cél operációs rendszer, az Azure Batch Pool-csomópontokon is végrehajthatók. 

A következő táblázat ismerteti a neveket és leírásokat erre a tevékenységre jellemző tulajdonságok. 

| Tulajdonság              | Leírás                              | Szükséges |
| :-------------------- | :--------------------------------------- | :------- |
| név                  | A folyamat a tevékenység neve     | Igen      |
| leírás           | A tevékenység leírása leíró szöveg.  | Nem       |
| type                  | Egyéni tevékenység, a tevékenység típusa van **egyéni**. | Igen      |
| linkedServiceName     | Társított szolgáltatás az Azure Batch szolgáltatásban. Ezt a társított szolgáltatást kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk.  | Igen      |
| command               | Az egyéni alkalmazás futtatandó parancsot. Ha az alkalmazás már az Azure Batch-készlet csomópont elérhető, a resourceLinkedService és a folderPath lehet hagyni. Például megadhatja a parancsot kell `cmd /c dir`, amelyeket a Batch-készlet Windows csomópont natív módon támogat. | Igen      |
| resourceLinkedService | Az Azure Storage társított szolgáltatás az egyéni alkalmazást tároló Storage-fiókhoz | nem&#42;       |
| folderPath            | Az egyéni alkalmazást és annak összes függőségét a mappa elérési útja<br/><br/>Ha rendelkezik egy hierarchikus mapparendszert almappák – azaz tárolt függőségek *folderPath* – a gyökérmappa-szerkezetében jelenleg lett simítva, amikor a rendszer átmásolja a fájlokat az Azure Batch. Azt jelenti minden fájl átkerülnek egy mappát az almappák nélkül. Ez a probléma megkerüléséhez, fontolja meg a fájlok tömörítése, a tömörített fájl másolása és majd kicsomagolta egyéni kódot a kívánt helyre. | nem&#42;       |
| referenceObjects      | Meglévő társított szolgáltatásokat és adatkészleteket tömbje. A hivatkozott társított szolgáltatásokat és adatkészleteket lesznek átadva a egyéni alkalmazás JSON formátumban, az egyéni kódot is lehet hivatkozni az adat-előállító erőforrások | Nem       |
| extendedProperties    | Ezért az egyéni kódot is lehet hivatkozni a további tulajdonságok az egyéni alkalmazás JSON formátumban kell átadni, felhasználó által definiált tulajdonságai | Nem       |

&#42;A Tulajdonságok `resourceLinkedService` és `folderPath` kell adható meg egyszerre, vagy mindkettő ki lehet hagyni.

## <a name="custom-activity-permissions"></a>Egyéni tevékenység engedélyek

Az egyéni tevékenység állítja be az Azure Batch automatikusan-felhasználói fiók *a tevékenység hatóköre nem rendszergazda hozzáférési* (az alapértelmezett felhasználói automatikus specifikáció). Az automatikus felhasználói fiók jogosultsági szintje nem módosítható. További információ: [a felhasználói fiókok feladatok futtatása a Batchben |} Automatikus felhasználói fiókok](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Parancsok végrehajtása

Az egyéni tevékenység parancsot közvetlenül hajthat végre. Az alábbi példa a "echo hello world" parancsot futtatja, a cél Azure Batch Pool-csomópontokon, és kimeneten nyomtatja a kimenetet a stdout. 

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

## <a name="passing-objects-and-properties"></a>Objektumok és tulajdonságok átadása

Ez a példa mutatja be, hogyan használhatók a referenceObjects és extendedProperties átadni az adat-előállító objektumok és a felhasználó által definiált tulajdonságok az egyéni alkalmazás. 


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

Amikor a tevékenység végrehajtása, referenceObjects és extendedProperties következő fájlok tárolják a SampleApp.exe végrehajtási mappájában telepített: 

- Activity.JSON

  ExtendedProperties és az egyéni tevékenység tulajdonságainak tárolja. 

- linkedServices.json

  Tárolók társított szolgáltatásokat egy tömbjét referenceObjects tulajdonság definiálva. 

- datasets.json

  Tárolók egy tömb, az adatkészletek a referenceObjects tulajdonság definiálva. 

Az alábbi mintakód bemutatják, hogyan a SampleApp.exe el a szükséges adatokat a JSON-fájlokat: 

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

## <a name="retrieve-execution-outputs"></a>Futtatási kimenetek beolvasása

  A következő PowerShell-paranccsal folyamatfuttatás megkezdése: 

  ```.powershell
  $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
  ```
  Ha a folyamat fut, a végrehajtás kimenetének a következő parancsokkal ellenőrizheti: 

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

  A **stdout** és **stderr** az egyéni alkalmazás menti, és a **adfjobs** tárolót az Azure Storage társított szolgáltatás létrehozása Azure Batch társított során meghatározott A feladat egy GUID-szolgáltatás. Megtekintheti a részletes elérési Tevékenységfuttatás kimenetében, az alábbi kódrészletben látható módon: 

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
    "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
    "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
  ]
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
  Activity Error section:
  "errorCode": ""
  "message": ""
  "failureType": ""
  "target": "MyCustomActivity"
  ```
Ha szeretné az alsóbb rétegbeli tevékenység stdout.txt tartalmának felhasználására, beszerezheti a fájl elérési útját a stdout.txt kifejezésben "\@activity('MyCustomActivity').output.outputs [0]". 

  > [!IMPORTANT]
  > - A activity.json linkedServices.json és datasets.json a Batch-feladat a futtatókörnyezet mappában vannak tárolva. Ebben a példában a activity.json linkedServices.json és datasets.json vannak tárolva "https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/" elérési út. Szükség esetén meg kell törölnie őket külön. 
  > - A társított szolgáltatások, amelyek a helyi Integration Runtime a bizalmas adatokat, például kulcsokat vagy jelszavakat titkosítja a helyi Integration Runtime annak biztosítása érdekében a hitelesítő adatok marad az ügyfél határozza meg magánhálózati környezetben. Néhány időérzékeny mezőinek hiányzó lehet, amikor ezzel a módszerrel az egyéni alkalmazás kódjában hivatkozik. Használja a SecureString a extendedProperties helyett társított szolgáltatásra mutató hivatkozást, ha szükséges. 

## <a name="pass-outputs-to-another-activity"></a>Pass egy másik tevékenység kimenete

Küldhet egyéni értékeket az egyéni tevékenységek a kódból az Azure Data Factory biztonsági másolatot. Teheti őket írása `outputs.json` az alkalmazásból. A Data Factory tartalmát másolja `outputs.json` fűzi azokat hozzá azokat a tevékenység kimeneti értéket, és a `customOutput` tulajdonság. (A méretkorlát a 2MB.) Ha azt szeretné, a tartalom felhasználásához `outputs.json` alsóbb rétegbeli tevékenység, megtekintheti az érték a következő kifejezés használatával `@activity('<MyCustomActivity>').output.customOutput`.

## <a name="retrieve-securestring-outputs"></a>Kimenetek SecureString beolvasása

Bizalmas tulajdonságértékek típusúként megjelölt *SecureString*, ahogyan az ebben a cikkben szereplő példák némelyike maszkolva ki a figyelés lapon, a Data Factory felhasználói felületén.  A tényleges folyamat-végrehajtás, azonban egy *SecureString* tulajdonság szerializálva van belül JSON-fájlként a `activity.json` egyszerű szöveges fájlt. Példa:

```json
"extendedProperties": {
    "connectionString": {
        "type": "SecureString",
        "value": "aSampleSecureString"
    }
}
```

A szerializálás nem teljesen biztonságos, és nem tekinthető biztonságos. A célja, hogy a Data Factory maszkolandó az érték a Monitorozás lapon érhető el.

A típus tulajdonságai *SecureString* az egyéni tevékenységek, olvassa el a `activity.json` fájlt, amely az azonos mappába kerül a. Exe-fájl, a JSON deszerializálni, ezután pedig hozzáfér a JSON-tulajdonságot (extendedProperties = > [propertyName] = > érték).

## <a name="compare-v2-v1"></a> Hasonlítsa össze a v2 egyéni tevékenységei és verzió 1 (egyéni) DotNet tevékenységi

  Az Azure Data Factory 1. verziójának meg, hogy egy (egyéni) DotNet tevékenységi létrehoz egy .net osztálytár projektek egy olyan osztállyal, amely megvalósítja a `Execute` módszere a `IDotNetActivity` felületet. A társított szolgáltatások, adatkészletek és a egy (egyéni) DotNet tevékenységi JSON hasznos az Extended Properties továbbítódnak a végrehajtási módszer szigorú típusmegadású objektumként. Az 1. verziójának működéssel kapcsolatos részletekért lásd: [az 1. verzió (egyéni) DotNet](v1/data-factory-use-custom-activities.md). Ez a megvalósítás miatt az 1. verziójának DotNet tevékenységi kódokat rendelkezik, amelyekre a .net keretrendszer 4.5.2-es verzióját. Az 1. verziójának DotNet tevékenységi is rendelkezik Windows-alapú Azure Batch Pool-csomópontokon kell végrehajtani. 

  Az Azure Data Factory V2 egyéni tevékenységei a meg nem kell .net felületet megvalósítani. Most közvetlenül futtathat parancsokat, parancsprogramok és végrehajtható fájlként összeállított, saját egyéni kódot. Ez a megvalósítás konfigurálásához adja meg a `Command` tulajdonsággal együtt a `folderPath` tulajdonság. Az egyéni tevékenység feltölti a végrehajtható fájlt, és annak függőségeit, `folderpath` és végrehajtja a parancsot az Ön számára. 

  A társított szolgáltatások, a (referenceObjects meghatározott) adatkészletek és a egy Data Factory v2 egyéni tevékenységei JSON-fájlként is hozzáférhet a végrehajtható fájlt a JSON hasznos kiterjesztett tulajdonságok. A szükséges tulajdonságait egy JSON-szerializáló, ahogyan az előző SampleApp.exe munkafüzetkód-példát használatával is elérheti. 

  A Data Factory V2 egyéni tevékenység-ben bevezetett változások az egyéni kód logikára írja be a választott nyelven, és hajtsa végre, a Windows és az Azure Batch által támogatott Linux operációs rendszereken. 

  A következő táblázat ismerteti a Data Factory V2 egyéni tevékenységei és eltérései a Data Factory 1. verzió (egyéni) DotNet tevékenységi: 


|Különbségek      | Egyéni tevékenység      | verzió 1 (egyéni) DotNet tevékenységi      |
| ---- | ---- | ---- |
|Hogyan egyéni logikát van definiálva.      |Azáltal, hogy egy végrehajtható fájl      |Egy .net DLL-fájl végrehajtása      |
|Az egyéni logikát végrehajtási környezetbe      |Windows vagy Linux rendszeren      |Windows (.Net-keretrendszer 4.5.2-es verziója)      |
|Szkriptek végrehajtása      |Parancsfájlok közvetlenül (például "cmd /c echo hello world" Windows virtuális gépeken) végrehajtása támogatja      |Szükséges a .NET-keretrendszerben dll-fájl végrehajtása      |
|Adatkészlet szükséges      |Optional      |Összekapcsolja a tevékenységek információkezelési és -átadási szükséges      |
|A tevékenység át adatokat egyéni logikát      |ReferenceObjects (LinkedServices és adatkészletek) és ExtendedProperties (egyéni tulajdonságok)      |ExtendedProperties (egyéni tulajdonságokat), bemeneti és kimeneti adatkészletek      |
|Az egyéni logikát információk lekéréséhez      |Elemzi a activity.json linkedServices.json és a végrehajtható fájl ugyanabban a mappában tárolt datasets.json      |.Net SDK-t (a .net 4.5.2-es. képkocka) keresztül      |
|Naplózás      |Az STDOUT közvetlenül ír      |Végrehajtási naplózó .net DLL-ben      |


  Ha rendelkezik meglévő .net-kódot írni a verziók 1 (egyéni) DotNet tevékenységi, módosítania a kódot, hogy az egyéni tevékenység az aktuális verzióval működjön. A kód frissítése a következő általános irányelveket:  

   - A projekt módosítása a .net osztálytár, egy Konzolalkalmazást. 
   - Indítsa el az alkalmazás a `Main` metódust. A `Execute` módszere a `IDotNetActivity` felület már nem szükséges. 
   - Olvassa el, és elemezni a társított szolgáltatások, adatkészletek és a tevékenység a JSON szerializálót, valamint nem szigorú típusmegadású objektumok. Adja meg a szükséges tulajdonság értékét az fő egyéni kód logikára. Tekintse meg példaként a fenti SampleApp.exe kód. 
   - A naplózó objektum már nem támogatott. A konzol kimenete a végrehajtható fájl nyomtatható és stdout.txt menti. 
   - A Microsoft.Azure.Management.DataFactories NuGet-csomag már nem szükséges. 
   - A kód fordítása, a végrehajtható fájlt, és annak függőségeit feltöltése az Azure Storage és az elérési utat adja meg a `folderPath` tulajdonság. 

Teljes minta, hogyan a teljes körű DLL-t és a folyamat minta ismertetett az adat-előállító verziója 1. cikk [egyéni tevékenységek használata Azure Data Factory-folyamatot](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) is kell írni a Data Factory egyéni tevékenységként, Lásd:[ Data Factory egyéni tevékenység minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFv2CustomActivitySample). 

## <a name="auto-scaling-of-azure-batch"></a>Automatikus skálázás az Azure Batch
Az Azure Batch-készlet is létrehozhat **automatikus skálázási** funkció. Létrehozhat például egy azure batch-készletet 0 dedikált virtuális gépek és az automatikus skálázás képletét a függőben lévő feladatok száma alapján. 

A mintául szolgáló képlet itt éri el a következő viselkedés: Amikor először hozza létre a készletet, 1 virtuális gép kezdődik. $PendingTasks metrika határozza meg, hogy a feladatok száma futó + (sorban áll) aktív állapotban.  A képlet átlagos száma függőben lévő feladatokat megkeresi az elmúlt 180 másodperc alatt, és ennek megfelelően beállítja a TargetDedicated. Biztosítja, hogy TargetDedicated soha nem túllép 25 virtuális gépeket. Tehát új feladatokat az elküldésüket készlet automatikusan nő befejeződött feladatokat, mint a virtuális gépek ingyenes egyenként válnak és az automatikus skálázás zsugorítja ezeken a virtuális gépeken. igény szerinti startingNumberOfVMs és maxNumberofVMs kell beállítani.

Automatikus skálázási képletet:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Lásd: [automatikusan méretezni a számítási csomópontok az Azure Batch-készletben](../batch/batch-automatic-scaling.md) részleteiről.

Ha a készletet használja az alapértelmezett [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), a Batch szolgáltatás a virtuális gép előkészítése az egyéni tevékenység futtatása előtt 15 – 30 percet is igénybe vehet.  Ha a készlet egy másik autoScaleEvaluationInterval használ, a Batch szolgáltatás autoScaleEvaluationInterval + 10 percig is eltarthat.


## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkek, amelyek bemutatják, hogyan alakíthat át adatokat, egyéb módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce-tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)

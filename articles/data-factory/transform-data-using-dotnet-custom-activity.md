---
title: Egyéni tevékenységek használata folyamatban
description: Ismerje meg, hogyan hozhat létre egyéni tevékenységeket, és hogyan használhatja őket egy Azure Data Factory-folyamatban.
services: data-factory
ms.service: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: 4913152125b0fafd74db575f835d53fa992b075e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260578"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Egyéni tevékenységek használata Azure Data Factory-folyamatban

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-use-custom-activities.md)
> * [Aktuális verzió](transform-data-using-dotnet-custom-activity.md)

Az Azure Data Factory-folyamatban kétféle tevékenységet használhat.

- [Adatmozgatási tevékenységek](copy-activity-overview.md) az adatok [támogatott forrás- és fogadóadattárak közötti áthelyezéséhez.](copy-activity-overview.md#supported-data-stores-and-formats)
- [Adatátalakítási tevékenységek](transform-data.md) az adatok átalakításához számítási szolgáltatások, például az Azure HDInsight, az Azure Batch és az Azure Machine Learning használatával.

Ha olyan adattárba szeretne adatokat áthelyezni, amelyet a Data Factory nem támogat, vagy olyan módon szeretné átalakítani/feldolgozni az adatokat, amelyet a Data Factory nem támogat, létrehozhat egy **egyéni tevékenységet** a saját adatmozgatási vagy átalakítási logikájával, és használhatja a tevékenységet egy folyamatban. Az egyéni tevékenység a testreszabott kódlogikát futtatja a virtuális gépek **Azure Batch-készletén.**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tekintse meg a következő cikkeket, ha most jön az Azure Batch szolgáltatás:

* [Az Azure Batch alapjai](../batch/batch-technical-overview.md) az Azure Batch szolgáltatás áttekintéséhez.
* [New-AzBatchAccount-parancsmag](/powershell/module/az.batch/New-azBatchAccount) egy Azure Batch-fiók (vagy) [Azure Portal](../batch/batch-account-create-portal.md) létrehozásához az Azure Batch-fiók az Azure Portal használatával. A parancsmag használatával kapcsolatos részletes útmutatásért olvassa el a PowerShell használata az [Azure Batch-fiók](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) kalkulált cikkét.
* [New-AzBatchPool-parancsmag](/powershell/module/az.batch/New-AzBatchPool) az Azure Batch-készlet létrehozásához.

## <a name="azure-batch-linked-service"></a>Azure Batch-alapú szolgáltatás

A következő JSON egy azure-köteghez csatolt szolgáltatás mintát határoz meg. További részletek: [Az Azure Data Factory által támogatott számítási környezetek](compute-linked-services.md)

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

 Ha többet szeretne megtudni az Azure Batch-alapú szolgáltatásról, olvassa el [a Számítási kapcsolt szolgáltatásokról](compute-linked-services.md) szóló cikket.

## <a name="custom-activity"></a>Egyéni tevékenység

A következő JSON-kódrészlet egy egyszerű egyéni tevékenységet rendelkező folyamatot határoz meg. A tevékenységdefiníció az Azure Batch-alapú szolgáltatásra mutató hivatkozást tartalmaz.

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

Ebben a példában a helloworld.exe egy egyéni alkalmazás a resourceLinkedService-ben használt Azure Storage-fiók customactv2/helloworld mappájában tárolt egyéni alkalmazás. Az egyéni tevékenység elküldi ezt az egyéni alkalmazást az Azure Batch-en végrehajtandó. A parancs bármely előnyben részesített alkalmazásra cserélhető, amely az Azure Batch Pool-készlet csomópontjainak céloperációs rendszerén hajtható végre.

Az alábbi táblázat a tevékenységre jellemző tulajdonságok nevét és leírását ismerteti.

| Tulajdonság              | Leírás                              | Kötelező |
| :-------------------- | :--------------------------------------- | :------- |
| név                  | A folyamatban lévő tevékenység neve     | Igen      |
| leírás           | A tevékenység tevékenységét leíró szöveg.  | Nem       |
| type                  | Egyéni tevékenység esetén a tevékenység típusa **Egyéni.** | Igen      |
| linkedServiceName     | Csatolt szolgáltatás az Azure Batchhez. A csatolt szolgáltatásról a [Csatolt szolgáltatások számítási cikkében](compute-linked-services.md) olvashat.  | Igen      |
| command               | A végrehajtandó egyéni alkalmazás parancsa. Ha az alkalmazás már elérhető az Azure Batch-készlet csomópontján, a resourceLinkedService és folderPath kihagyható. Megadhatja például azt a `cmd /c dir`parancsot, amelyet a Windows batch készletcsomópont natív módon támogat. | Igen      |
| resourceLinkedService | Az Azure Storage-alapú szolgáltatás a Storage-fiók, ahol az egyéni alkalmazás tárolja | Nincs &#42;       |
| folderPath            | Az egyéni alkalmazás és az összes függőség mappájának elérési útja<br/><br/>Ha az almappákban – azaz a *FolderPath* alatt hierarchikus mappastruktúrában – tárolt függőségek vannak, a mappastruktúra jelenleg összeolvad, amikor a fájlokat az Azure Batchbe másolja. Ez azt illeti, az összes fájl egyetlen mappába másolva, almappák nélkül. A probléma kerülő megoldásához fontolja meg a fájlok tömörítését, a tömörített fájl másolását, majd a kívánt helyen lévő egyéni kóddal való kicsomagolását. | Nincs &#42;       |
| referenceObjects      | Meglévő csatolt szolgáltatások és adatkészletek tömbje. A hivatkozott csatolt szolgáltatások és adatkészletek json formátumban kerülnek átadásra az egyéni alkalmazásnak, így az egyéni kód hivatkozhat az adatgyár erőforrásaira | Nem       |
| extendedProperties (bővített tulajdonságok)    | Felhasználó által definiált tulajdonságok, amelyek JSON formátumban adhatók át az egyéni alkalmazásnak, így az egyéni kód további tulajdonságokra hivatkozhat | Nem       |
| retentionTimeInDays | Az egyéni tevékenységhez elküldött fájlok megőrzési ideje. Az alapértelmezett érték 30 nap. | Nem |

&#42; A `resourceLinkedService` `folderPath` tulajdonságokat, és mindkettőt meg kell adni, vagy mindkettőt ki kell hagyni.

> [!NOTE]
> Ha a csatolt szolgáltatásokat referenciaobjektumként adja át az egyéni tevékenységben, akkor jó biztonsági gyakorlat az Azure Key Vault-kompatibilis csatolt szolgáltatás átadása (mivel nem tartalmaz biztonságos karakterláncokat), és a hitelesítő adatokat a titkos név használatával közvetlenül a kulcsból kell lekérni Páncélterem a kódból. Itt talál egy [példát,](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) amely hivatkozik az AKV-kompatibilis csatolt szolgáltatásra, lekéri a hitelesítő adatokat a Key Vaultból, majd hozzáfér a kódban lévő tárolóhoz.

## <a name="custom-activity-permissions"></a>Egyéni tevékenységengedélyek

Az egyéni tevékenység beállítja az Azure Batch automatikus felhasználói fiók *nem rendszergazdai hozzáférés feladathatókörrel* (az alapértelmezett automatikus felhasználói specifikáció). Az automatikus felhasználói fiók jogosultsági szintje nem módosítható. További információ: [Feladatok futtatása a Felhasználói fiókok csoportban | Automatikus felhasználói fiókok](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Parancsok végrehajtása

Az Egyéni tevékenység használatával közvetlenül végrehajthat egy parancsot. A következő példa futtatja a "echo hello world" parancsot a cél Azure Batch Pool-készlet csomópontjain, és kinyomtatja a kimenetet stdout-ra.

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

Ez a minta bemutatja, hogyan használhatja a referenceObjects és extendedProperties adatfeldolgozó objektumok és a felhasználó által definiált tulajdonságok at az egyéni alkalmazás.

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

A tevékenység végrehajtásakor a referenceObjects és a extendedProperties a következő fájlokban tárolódik, amelyek a SampleApp.exe ugyanazon végrehajtási mappájába vannak telepítve:

- `activity.json`

  Tárolja a kiterjesztetttulajdonságokat és az egyéni tevékenység tulajdonságait.

- `linkedServices.json`

  A referenceObjects tulajdonságban definiált csatolt szolgáltatások tömbjét tárolja.

- `datasets.json`

  A referenceObjects tulajdonságban definiált adatkészletek tömbjét tárolja.

A következő mintakód bemutatja, hogy a SampleApp.exe hogyan férhet hozzá a JSON-fájlokból szükséges információkhoz:

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

## <a name="retrieve-execution-outputs"></a>Végrehajtási kimenetek lekérése

A folyamatfuttatást a következő PowerShell-paranccsal indíthatja el:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Amikor a folyamat fut, a végrehajtási kimenetet a következő parancsokkal ellenőrizheti:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

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

Az egyéni alkalmazás **stdout** és **stderr** menti az **adfjobs** tárolóaz Azure Storage-kapcsolt szolgáltatás, amely et az Azure Batch csatolt szolgáltatás létrehozásakor a feladat GUID-jével. A tevékenységfuttatás kimenetből a részletes elérési utat a következő kódrészletben látható módon kaphatja meg:

```
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

Ha az stdout.txt tartalmat szeretné felhasználni az alsóbb rétegbeli tevékenységekben, akkor a stdout.txt fájl elérési útját a "\@activity('SCustomActivity').output.outputs[0]" kifejezésben kaphatja meg.

> [!IMPORTANT]
> - A activity.json, a linkedServices.json és a datasets.json a Batch feladat futásidejű mappájában tárolódik. Ebben a példában a activity.json, linkedServices.json és datasets.json `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` elérési úton tárolódik. Szükség esetén külön kell megtisztítani őket.
> - A saját üzemeltetésű integrációs futásidejű csatolt szolgáltatások esetében a bizalmas adatokat, például a kulcsokat vagy a jelszavakat a saját üzemeltetésű integrációs futásidő titkosítja, hogy a hitelesítő adatok az ügyfél által meghatározott magánhálózati környezetben maradhassanak. Egyes bizalmas mezők hiányozhatnak, ha így hivatkoznak az egyéni alkalmazáskódra. Használja a SecureString-et a extendedProperties-ben a csatolt szolgáltatás hivatkozása helyett, ha szükséges.

## <a name="pass-outputs-to-another-activity"></a>Kimenetek áthárítása egy másik tevékenységbe

Egyéni értékeket küldhet a kódból egy egyéni tevékenységben az Azure Data Factory-ba. Ezt úgy teheti meg, `outputs.json` hogy beírja őket a jelentkezéséből. A Data Factory átmásolja a tartalom és `outputs.json` hozzáfűzi a `customOutput` tevékenység kimenet, mint a tulajdonság értéke. (A méretkorlát 2 MB.) Ha a későbbi tevékenységek `outputs.json` tartalmát szeretné felhasználni, az értéket a `@activity('<MyCustomActivity>').output.customOutput`kifejezés használatával kaphatja meg.

## <a name="retrieve-securestring-outputs"></a>SecureString kimenetek lekérése

A *SecureString*típusúként kijelölt bizalmas tulajdonságértékek , amint az ebben a cikkben szereplő példákban látható, a Data Factory felhasználói felületének Figyelés lapján maszkolt.  A tényleges folyamatvégrehajtás során azonban egy *SecureString* tulajdonság jSON-ként van szerializálva a `activity.json` fájlon belül egyszerű szövegként. Példa:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Ez a szerializálás nem igazán biztonságos, és nem célja, hogy biztonságos legyen. A szándék az, hogy tipp, hogy a Data Factory maszk az értéket a figyelés lapon.

Ha egyéni tevékenységből szeretné elérni a *SecureString* típusú tulajdonságokat, olvassa el a `activity.json` fájlt, amely ugyanabban a mappában van elhelyezve, mint a . EXE, deszerializálja a JSON-t, majd érje el a JSON tulajdonságot (extendedProperties => [propertyName] => érték).

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a>A v2 egyéni tevékenység és az 1-es verziójú (egyéni) DotNet-tevékenység összehasonlítása

Az Azure Data Factory 1-es verziójában egy (egyéni) DotNet-tevékenységet valósít meg `Execute` egy .NET osztálykönyvtár-projekt létrehozásával, amely a `IDotNetActivity` felület metódusát megvalósító osztállyal valósítja meg. A csatolt szolgáltatások, adatkészletek és kiterjesztett tulajdonságok a JSON hasznos adat egy (egyéni) DotNet tevékenység átadják a végrehajtási módszer erősen beírt objektumok. Az 1-es verzió viselkedésével kapcsolatos részletekért lásd [az (Egyéni) DotNet 1-es verzióban.](v1/data-factory-use-custom-activities.md) A megvalósítás miatt az 1-es verziójú DotNet-tevékenységkódnak a .NET Framework 4.5.2-t kell megcéloznia. Az 1-es verziójú DotNet-tevékenységet windows-alapú Azure Batch-készletcsomópontokon is végre kell hajtani.

Az Azure Data Factory V2 egyéni tevékenység, nem szükséges a .NET felület megvalósítása. Most már közvetlenül futtathatja a parancsokat, parancsfájlokat és a saját egyéni kódját, amelyet végrehajtható fájlként fordítanak le. A megvalósítás konfigurálásához `Command` adja meg `folderPath` a tulajdonságot a tulajdonsággal együtt. Az egyéni tevékenység feltölti a végrehajtható `folderpath` fájlt és annak függőségeit, és végrehajtja a parancsot.

A hivatkozott szolgáltatások, adatkészletek (a referenceObjects-ben definiálva) és a Data Factory v2 egyéni tevékenység JSON-tartalomban definiált kiterjesztett tulajdonságai json-fájlokként érhetők el. A szükséges tulajdonságokat json-szerializáló használatával érheti el, ahogy az az előző SampleApp.exe kódmintában látható.

A Data Factory V2 egyéni tevékenységben bevezetett módosításokkal az egyéni kódlogikát a kívánt nyelven írhatja, és végrehajthatja az Azure Batch által támogatott Windows és Linux operációs rendszereken.

Az alábbi táblázat a Data Factory V2 egyéni tevékenység és a Data Factory 1-es verziójú (egyéni) DotNet-tevékenység közötti különbségeket ismerteti:

|Eltérések      | Egyéni tevékenység      | 1.-es verzió (egyéni) DotNet-tevékenység      |
| ---- | ---- | ---- |
|Az egyéni logika definiálása      |Azáltal, hogy egy futtatható      |A .NET DLL megvalósításával      |
|Az egyéni logika végrehajtási környezete      |Windows vagy Linux      |Windows (.NET Framework 4.5.2)      |
|Parancsfájlok végrehajtása      |Támogatja a parancsfájlok közvetlen végrehajtásának (például "cmd /c echo hello world" Windows VM rendszeren)      |Megvalósítást igényel a .NET DLL-ben      |
|Adatkészlet szükséges      |Optional      |Tevékenységek láncolásához és információk adására      |
|Információk átadása a tevékenységből az egyéni logikának      |A ReferenceObjects (LinkedServices és adatkészletek) és az ExtendedProperties (egyéni tulajdonságok)      |ExtendedProperties (egyéni tulajdonságok), bemeneti és kimeneti adatkészletek      |
|Adatok beolvasása egyéni logikában      |Elemzési tevékenység.json, linkedServices.json és datasets.json ugyanabban a mappában a végrehajtható fájl      |A .NET SDK -n keresztül (.NET 4.5.2-es keret)      |
|Naplózás      |Közvetlenül az STDOUT-nak ír      |Naplózó implementálása a .NET DLL fájlban      |

Ha már létezik .NET kód az 1-es verziójú DotNet-tevékenységhez, módosítania kell a kódot, hogy az az egyéni tevékenység aktuális verziójával működjön. Frissítse a kódot az alábbi magas szintű irányelvek szerint:

  - Módosítsa a projektet .NET osztálykönyvtárból konzolalkalmazáslá.
  - Indítsa el az `Main` alkalmazást a módszerrel. A `Execute` felület `IDotNetActivity` metódusa már nincs szükség.
  - Olvassa el és elemezje a csatolt szolgáltatásokat, adatkészleteket és tevékenységet JSON-szerializálóval, és nem erősen beírt objektumokkal. Adja át a szükséges tulajdonságok értékeit a fő egyéni kód logikájának. Példaként tekintse meg az előző SampleApp.exe kódot.
  - A Naplózó objektum már nem támogatott. A végrehajtható fájl kimenete kinyomtatható a konzolra, és az stdout.txt fájlba menthető.
  - A Microsoft.Azure.Management.DataFactories NuGet csomagra már nincs szükség.
  - Fordítsa le a kódot, töltse fel a végrehajtható fájlt és annak `folderPath` függőségeit az Azure Storage-ba, és határozza meg az elérési utat a tulajdonságban.

Ha teljes mintát szeretne adni arról, hogy a Data Factory 1-es verziójában ismertetett végpontok között DLL- és folyamatminta [adatgyári](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) egyéni tevékenységként újraírható, olvassa el a [Data Factory egyéni tevékenység minta című témakört.](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample)

## <a name="auto-scaling-of-azure-batch"></a>Az Azure Batch automatikus méretezése

Az Automatikus **skálázási** funkcióval azure batch készletet is létrehozhat. Létrehozhat például egy azure-kötegkészletet 0 dedikált virtuális géptel és egy automatikus skálázási képlettel a függőben lévő feladatok száma alapján.

A mintaképlet itt a következő viselkedést éri el: Amikor a készlet először jön létre, 1 virtuális gépkel kezdődik. $PendingTasks metrika határozza meg a feladatok számát futó + aktív (várólistára helyezett) állapotban. A képlet megkeresi a függőben lévő feladatok átlagos számát az elmúlt 180 másodpercben, és ennek megfelelően állítja be a TargetDedicated függvényt. Ez biztosítja, hogy a TargetDedicated soha nem lépi túl a 25 virtuális gépet. Így az új feladatok elküldésével a készlet automatikusan növekszik, és a feladatok befejeződésével a virtuális gépek egyenként válnak ingyenessé, és az automatikus skálázás csökkenti a virtuális gépeket. az startingNumberOfVMs és a maxNumberofVMs az Ön igényeinek megfelelően módosítható.

Automatikus skálázási képlet:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

A részletekért tekintse meg [a számítási csomópontok automatikus méretezése az Azure Batch-készletben.](../batch/batch-automatic-scaling.md)

Ha a készlet az alapértelmezett [autoScaleEvaluationInterval ,](https://msdn.microsoft.com/library/azure/dn820173.aspx)a Batch szolgáltatás 15–30 percet is igénybe vehet a virtuális gép előkészítése az egyéni tevékenység futtatása előtt. Ha a készlet egy másik autoScaleEvaluationInterval- ot használ, a Batch szolgáltatás autoScaleEvaluationInterval + 10 percet vehet igénybe.

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben elmagyarázhatja, hogyan alakíthatja át más módon az adatokat:

* [U-SQL tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Sertésaktivitás](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming tevékenység](transform-data-using-hadoop-streaming.md)
* [Szikraaktivitás](transform-data-using-spark.md)
* [Gépi tanulási kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)

---
title: Stream Analytics-feladat létrehozása az Azure PowerShell használatával
description: Ez az útmutató azt mutatja be, hogy hogyan lehet egy Azure Stream Analytics-feladatot létrehozni és futtatni az Azure PowerShell-modul használatával.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 0be8cee9e6c7874282f4e8f43f75fa7f2490c14e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Útmutató: Stream Analytics-feladat létrehozása az Azure PowerShell használatával

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancsmagokkal vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató azt mutatja be, hogy hogyan lehet egy Azure Stream Analytics-feladatot létrehozni és futtatni az Azure PowerShell-modul használatával.

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)  

* Ehhez a rövid útmutatóhoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. Ahhoz, hogy megtudja, melyik verzió van telepítve a helyi gépen, futtassa a `Get-Module -ListAvailable AzureRM` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [Az Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) című cikket. A cikk azt mutatja be, hogy hogyan kell adatokat kiolvasni egy blobtárolóból, transzformálni az adatokat, majd visszaírni őket egy másik tárolóba ugyanezen a blobtárolón belül.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be Azure-előfizetésébe a `Connect-AzureRmAccount` parancs használatával, majd adja meg Azure hitelesítő adatait az előugró böngészőablakban. Bejelentkezés után, amennyiben több előfizetéssel is rendelkezik, a következő parancsmagok futtatásával választhatja ki az útmutató lépéseinek elvégzéséhez használni kívánt előfizetést. Ügyeljen rá, hogy a <your subscription> helyett saját előfizetésének a nevét adja meg:  

```powershell
# Log in to your Azure account
Connect-AzureRmAccount

# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```powershell
$resourceGroup = "ASAPSRG"
$location = "WestUS2"
New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location 
```

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics-feladat létrehozása előtt érdemes beállítani azokat az adatokat, amelyek majd a feladat bemenetét fogják képezni. Végezze el a következő lépéseket a feladat bemeneti adatainak előkészítéséhez: 

1. Töltse le az [érzékelői mintaadatokat](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) a GitHubról.  

2. Hozzon létre egy szabványos, általános célú tárfiókot LRS-replikációval a [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount) parancsmag használatával.  

3. Olvassa be a tárfiók környezetét, amely a használni kívánt tárfiókot határozza meg. Ha a tárfiókok használatával történő munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat. A példában egy mystorageaccount nevű tárfiókot hozunk létre helyileg redundáns tárolás (LRS) és blobtitkosítás (alapértelmezés szerint bekapcsolt) funkciókkal.  

4. Ezután hozzon létre egy tárolót a [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) parancsmag használatával, az engedélyeket állítsa „blob” értékre, hogy a fájlok nyilvánosan elérhetőek legyenek, majd töltse fel az [érzékelői mintaadatokat](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json), amelyeket korábban töltött le. 

Ezeket a lépéseket a következő PowerShell-szkript futtatásával végezheti el:

```powershell
$storageAccountName = "mystorageaccount"
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
$containerName = "myinputcontainer"

New-AzureStorageContainer `
  -Name $containerName `
  -Context $ctx `
  -Permission blob

Set-AzureStorageBlobContent `
  -File "C:\HelloWorldASA-InputStream.json" `
  -Container $containerName `
  -Context $ctx  

$storageAccountKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName).Value[0]
```

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Hozzon létre egy Stream Analytics-feladatot a [New-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0) parancsmag használatával. Ennél a parancsmagnál a feladat neve, az erőforráscsoport neve, és a feladat definíciója adható meg paraméterként. A feladat neve bármilyen olyan valódi név lehet, amellyel azonosítani tudja a feladatot. A Stream Analytics-feladat neve csak alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, és legalább 3, de legfeljebb 63 karakter hosszúságú lehet. A feladat definíciója egy olyan JSON-fájl, amely azokat a tulajdonságokat tartalmazza, amelyek a feladat létrehozásához szükségesek. Helyi gépén hozzon létre egy “JobDefinition.json” fájlt, és adja hozzá a következő JSON-adatokat:

```json
{    
   "location":"Central US",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"drop",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
}
}
```

Ezután futtassa a New-AzureRMStreamAnalyticsJob parancsmagot, és ügyeljen rá, hogy a jobDefinitionFile változónál azt az elérési utat adja meg, ahová a feladat definícióját tartalmazó JSON-fájlt mentette. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  –File $jobDefinitionFile `
  –Name $jobName -Force 
```

## <a name="configure-input-to-the-job"></a>A feladat bemenetének konfigurálása

Adja meg a feladat bemenetét a [New-AzureRMStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat bemenetének a neve, az erőforráscsoport neve, és a feladat bemenetének definíciója adható meg paraméterként. A feladat bemenetének definíciója egy olyan JSON-fájl, amely tartalmazza azokat a tulajdonságokat, amelyek a feladat bemenetének konfigurálásához szükségesek. A példában egy blobtárolót fogunk bemenetként megadni. Helyi gépén hozzon létre egy “JobInputDefinition.json” fájlt, és adja hozzá a következő JSON-adatokat, de ügyeljen rá, hogy az **accountKey** értékénél a tárfiókja elérési kulcsát adja meg, amelyet a $storageAccountKey érték tárol. 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<Your storage account key>"
                }],
                "container": "myinputcontainer",
                "pathPattern": "",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Ezután futtassa a New-AzureRMStreamAnalyticsInput parancsmagot, és ügyeljen rá, hogy a jobInputDefinitionFile változónál azt az elérési utat adja meg, ahová a feladat bemenetének definícióját tartalmazó JSON-fájlt mentette. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRMStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>A feladat kimenetének konfigurálása

Adja meg a feladat kimenetét a [New-AzureRMStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat kimenetének a neve, az erőforráscsoport neve, és a feladat kimenetének definíciója adható meg paraméterként. A feladat kimenetének definíciója egy olyan JSON-fájl, amely tartalmazza azokat a tulajdonságokat, amelyek a feladat kimenetének konfigurálásához szükségesek. A példában egy tárolóblobot fogunk kimenetként megadni. Helyi gépén hozzon létre egy “JobOutputDefinition.json” fájlt, és adja hozzá a következő JSON-adatokat, de ügyeljen rá, hogy az **accountKey** értékénél a tárfiókja elérési kulcsát adja meg, amelyet a $storageAccountKey érték tárol. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<Your storage account key>"
                    }],
                "container": "myoutputcontainer",
                "pathPattern": "",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Ezután futtassa a New-AzureRMStreamAnalyticsOutput parancsmagot, és ügyeljen rá, hogy a jobOutputDefinitionFile változónál azt az elérési utat adja meg, ahová a feladat kimenetének definícióját tartalmazó JSON-fájlt mentette. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRMStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobOutputDefinitionFile `
  –Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

Adjon hozzá transzformációt a feladathoz a [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat transzformációjának a neve, az erőforráscsoport neve, és a feladat transzformációjának definíciója adható meg paraméterként. Helyi gépén hozzon létre egy “JobTransformationDefinition.json” fájlt, és adja hozzá a következő JSON-adatokat. A JSON-fájl tartalmaz egy query (lekérdezés) paramétert, amellyel definiálható a transzformációs lekérdezés:

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

Ezután futtassa a New-AzureRMStreamAnalyticsTransformation parancsmagot, és ügyeljen rá, hogy a jobTransformationFile változónál azt az elérési utat adja meg, ahová a feladat transzformációjának definícióját tartalmazó JSON-fájlt mentette. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRMStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobTransformationDefinitionFile `
  –Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Indítsa el a feladatot a [Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) parancsmag használatával. Ennél a parancsmagnál a feladat neve, az erőforráscsoport neve, a feladat kimenetének indítási módja, és a kezdés ideje adható meg paraméterként. Az OutputStartMode (kimenet indítási módja) paraméter két értéket fogad el: JobStartTime (a feladat indításának ideje) értéket és CustomTime (egyéni időpont) vagy LastOutputEventTime (az utolsó kimeneti esemény időpontja) értéket. Ahhoz, hogy megtudja, mire vonatkoznak ezek az értékek, tekintse meg a PowerShell-dokumentáció [paraméterek](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) szakaszát. A példában az indítási módját CustomTime (egyéni időpont) értékkel határozzuk meg, és megadjuk az OutputStartTime (kimenet indítási ideje) értékét. 

Az időpont értékének válassza ki a fájl tárolóblobba való feltöltésének időpontját megelőző napot, hiszen a fájlt a jelen időponthoz képest már korábban feltöltötte. A következő parancsmag futtatása "True"értéket ad vissza, ha a feladat elindult. Egy “myoutputcontainer” nevű tároló jön létre a tárfiókban, amely a transzformált adatokat tartalmazza. 

```powershell
Start-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-03-11T14:45:12Z 
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a streamelési feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a feladatot a jövőben is szeretné használni, leállíthatja, és később újraindíthatja amikor ismét szükség van rá. Ha már nem használja a feladatot, akkor a következő parancsmag futtatásával az útmutatóban létrehozott összes erőforrást törölheti:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>További lépések

Az útmutató segítségével létrehozott és telepített egy egyszerű Stream Analytics-feladatot. A más típusú bemenetek beállításával és a valós idejű észleléssel kapcsolatban információt a következő cikkben talál:

> [!div class="nextstepaction"]
> [Valós idejű csalásészlelés az Azure Stream Analytics használatával](stream-analytics-real-time-fraud-detection.md)

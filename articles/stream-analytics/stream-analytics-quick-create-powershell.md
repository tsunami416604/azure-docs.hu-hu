---
title: Stream Analytics-feladat létrehozása az Azure PowerShell használatával
description: Ez az útmutató azt mutatja be, hogy hogyan lehet egy Azure Stream Analytics-feladatot létrehozni és futtatni az Azure PowerShell-modul használatával.
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 05/14/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 18903dfbe187de73a6edb14196fa29e02d35dbca
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185380"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Útmutató: Stream Analytics-feladat létrehozása az Azure PowerShell használatával

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancsmagokkal vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató azt mutatja be, hogy hogyan lehet egy Azure Stream Analytics-feladatot létrehozni és futtatni az Azure PowerShell-modul használatával. 
 
A példafeladat streamadatokat olvas az Azure Blob Storage egyik blobjából. Az ebben az útmutatóban használt bemeneti adatfájl statikus adatokat tartalmaz, illusztrációs céllal. Egy valós forgatókönyvben a Stream Analytics-feladathoz streamelt bemeneti adatokat használnánk. Ezután a feladat átalakítja az adatokat a Stream Analytics lekérdezési nyelv használatával, és kiszámolja a 100° feletti hőmérsékletek átlagát. Végül az eredményül kapott kimeneti eseményeket egy másik fájlba írja. 

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)  

* Ehhez a rövid útmutatóhoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. Ahhoz, hogy megtudja, melyik verzió van telepítve a helyi gépen, futtassa a `Get-Module -ListAvailable AzureRM` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [Az Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) című cikket. 


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésébe a `Connect-AzureRmAccount` paranccsal, majd adja meg Azure-beli hitelesítő adatait az előugró böngészőablakban:

```powershell
# Log in to your Azure account
Connect-AzureRmAccount
```

Bejelentkezés után, amennyiben több előfizetéssel is rendelkezik, a következő parancsmagok futtatásával választhatja ki az útmutató lépéseinek elvégzéséhez használni kívánt előfizetést. Ügyeljen rá, hogy a <your subscription name> helyett saját előfizetésének a nevét adja meg:  

```powershell
# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzureRmResourceGroup `
   -Name $resourceGroup `
   -Location $location 
```

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics-feladat létrehozása előtt készítse elő azokat az adatokat, amelyek a feladat bemenetét fogják képezni.

1. Töltse le az [érzékelői mintaadatokat](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) a GitHubról. Kattintson a jobb gombbal a hivatkozásra, majd válassza a **Hivatkozás mentése másként...** vagy a **Cél mentése másként** lehetőséget.

2. A következő PowerShell-kódblokk több parancsot is futtat a feladat szükséges bemeneti adatainak előkészítéséhez. Tekintse át az egyes szakaszokat a kód értelmezése céljából. 

   1. Hozzon létre egy szabványos, általános célú tárfiókot a [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount) parancsmaggal.  A példában egy mystorageaccount nevű tárfiókot hozunk létre helyileg redundáns tárolás (LRS) és blobtitkosítás (alapértelmezés szerint bekapcsolt) funkciókkal.  

   2. Kérje le a `$storageAccount.Context` nevű tárfiókkörnyezetet, amely meghatározza a használni kívánt tárfiókot. Ha a tárfiókok használatával történő munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat. 

   3. Hozzon létre egy Storage-tárolót a [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) paranccsal, és töltse fel a korábban letöltött [példa érzékelőadatokat](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json). 

   4. Másolja ki a kód által kimenetül adott tárkulcsot és illessze be a JSON-fájlokba, amelyek később a streamfeladat bemenetének és kimenetének létrehozásáért felelnek majd.

   ```powershell
   $storageAccountName = "mystorageaccount"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "streamanalytics"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   Set-AzureStorageBlobContent `
     -File "c:\HelloWorldASA-InputStream.json" `
     -Blob "input/HelloWorldASA-InputStream.json" `
     -Container $containerName `
     -Context $ctx  
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your input and output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Hozzon létre egy Stream Analytics-feladatot a [New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0) parancsmaggal. Ennél a parancsmagnál a feladat neve, az erőforráscsoport neve, és a feladat definíciója adható meg paraméterként. A feladat neve bármilyen, a feladatot azonosító rövid név lehet. Csak alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, és legalább 3, de legfeljebb 63 karakter hosszúságú lehet. A feladat definíciója egy JSON-fájl, amely a feladat létrehozásához szükséges tulajdonságokat tartalmazza. Hozzon létre a helyi gépén egy `JobDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat:

```json
{    
   "location":"WestUS2",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"adjust",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
   }
}
```

Ezután futtassa a `New-AzureRmStreamAnalyticsJob` parancsmagot. Ne felejtse el lecserélni a `jobDefinitionFile` változó értékét arra az elérési útra, ahol a feladatdefiníciós JSON-fájl található. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force 
```

## <a name="configure-input-to-the-job"></a>A feladat bemenetének konfigurálása

Adja meg a feladat bemenetét a [New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0) parancsmaggal. Ennél a parancsmagnál a feladat neve, a feladat bemenetének a neve, az erőforráscsoport neve, és a feladat bemenetének definíciója adható meg paraméterként. A feladat bemenetének definíciója egy JSON-fájl, amely a feladat bemenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ebben a példában egy Blob Storage-tárolót hozunk létre bemenetként. 

Hozzon létre a helyi gépén egy `JobInputDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. Ügyeljen arra, hogy az `accountKey` értékét lecserélje a tárfiókja hozzáférési kulcsára, amelyet a $storageAccountKey értéke ad meg. 

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
                   "accountKey":"<storage account key>"
                }],
                "container": "streamanalytics",
                "pathPattern": "input/",
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

Ezután futtassa a `New-AzureRmStreamAnalyticsInput` parancsmagot, és a `jobDefinitionFile` változó értékét cserélje le arra az elérési útra, ahol a feladat bemenetének definícióját tartalmazó JSON-fájl található. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRmStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>A feladat kimenetének konfigurálása

Adja meg a feladat kimenetét a [New-AzureRMStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat kimenetének a neve, az erőforráscsoport neve, és a feladat kimenetének definíciója adható meg paraméterként. A feladat kimenetének definíciója egy JSON-fájl, amely a feladat kimenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ez a példa egy Blob Storage-tárolót használ kimenetként. 

Hozzon létre a helyi gépén egy `JobOutputDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. Ügyeljen arra, hogy az `accountKey` értékét lecserélje a tárfiókja hozzáférési kulcsára, amelyet a $storageAccountKey értéke ad meg. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<storage account key>"
                    }],
                "container": "streamanalytics",
                "pathPattern": "output/",
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

Ezután futtassa a `New-AzureRmStreamAnalyticsOutput` parancsmagot. Ne felejtse el lecserélni a `jobOutputDefinitionFile` változó értékét arra az elérési útra, ahol a feladat kimenetét definiáló JSON-fájl található. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRmStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

Adjon hozzá transzformációt a feladathoz a [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat transzformációjának a neve, az erőforráscsoport neve, és a feladat transzformációjának definíciója adható meg paraméterként. Hozzon létre a helyi gépén egy `JobTransformationDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. A JSON-fájl tartalmaz egy query (lekérdezés) paramétert, amellyel definiálható a transzformációs lekérdezés:

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

Ezután futtassa a `New-AzureRmStreamAnalyticsTransformation` parancsmagot. Ne felejtse el lecserélni a `jobTransformationDefinitionFile` változó értékét arra az elérési útra, ahol a feladattranszformációs JSON-fájl található. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Indítsa el a feladatot a [Start-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) parancsmaggal. Ennél a parancsmagnál a feladat neve, az erőforráscsoport neve, a feladat kimenetének indítási módja, és a kezdés ideje adható meg paraméterként. Az `OutputStartMode` által elfogadott értékek: `JobStartTime`, `CustomTime` és `LastOutputEventTime`. A PowerShell-dokumentáció [paraméterekkel](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) foglalkozó szakaszában talál további információt arról, hogy ezek az értékek mit jelentenek. Ebben a példában válassza a `CustomTime` módot, és adja meg az `OutputStartTime` értékét. 

Az időt állítsa `2018-01-01` értékre. Azért választjuk ezt a kezdődátumot, mert megelőzi a mintaadatokban lévő esemény-időbélyeget. A következő parancsmag futtatása a `True` kimenetet adja vissza, ha a feladat elindult. A Storage-tárolóban létrejön egy kimeneti mappa, amely az átalakított adatokat tartalmazza. 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-01-01T00:00:00Z 
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a streamelési feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megelőzheti a feladat által használt streamelési egységek kiszámlázását. Ha később is szeretné használni a feladatot, akkor nem kell törölnie, hanem elég, ha leállítja. Ha már nem használja a feladatot, akkor a következő parancsmag futtatásával az útmutatóban létrehozott összes erőforrást törölheti:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics-feladatot helyezett üzembe. Az egyéb bemeneti források beállításával és a valós idejű észlelés végrehajtásával kapcsolatos információkért olvassa el az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Valós idejű csalásészlelés az Azure Stream Analytics használatával](stream-analytics-real-time-fraud-detection.md)

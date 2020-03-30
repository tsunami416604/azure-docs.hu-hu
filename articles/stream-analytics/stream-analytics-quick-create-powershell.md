---
title: Rövid útmutató – Stream Analytics-feladat létrehozása az Azure PowerShell használatával
description: Ez a rövid útmutató bemutatja, hogyan használhatja az Azure PowerShell-modult egy Azure Stream Analytics-feladat üzembe helyezéséhez és futtatásához.
author: mamccrea
ms.author: mamccrea
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 84c132c333e4d6ba052029350f275ebf499a906f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79536802"
---
# <a name="quickstart-create-a-stream-analytics-job-using-azure-powershell"></a>Rövid útmutató: Stream Analytics-feladat létrehozása az Azure PowerShell használatával

Az Azure PowerShell-modul segítségével azure-erőforrásokat hozhat létre és kezelhet PowerShell-parancsmagokkal vagy parancsfájlokkal. Ez az útmutató azt mutatja be, hogy hogyan lehet egy Azure Stream Analytics-feladatot létrehozni és futtatni az Azure PowerShell-modul használatával.

A példafeladat beolvassa az IoT Hub-eszközről a streamelési adatokat. A bemeneti adatokat egy Raspberry Pi online szimulátor hozza létre. Ezután a Stream Analytics-feladat átalakítja az adatokat a Stream Analytics lekérdezési nyelv használatával, hogy 27°-nál nagyobb hőmérsékletű üzeneteket szűrjön. Végül az eredményül kapott kimeneti eseményeket egy blob storage-ban lévő fájlba írja.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)

* Ehhez a rövid útmutatóhoz az Azure PowerShell-modul szükséges. Ahhoz, hogy megtudja, melyik verzió van telepítve a helyi gépen, futtassa a `Get-Module -ListAvailable Az` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-Az-ps) ismertető cikket.

* Néhány IoT Hub-műveletet az Azure PowerShell nem támogat, és az Azure CLI 2.0.70-es vagy újabb verziójával, valamint az Azure CLI IoT-bővítményével kell végrehajtani. [Telepítse az Azure CLI-t,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és használja `az extension add --name azure-iot` az IoT-bővítmény telepítéséhez.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és adja meg az Azure-hitelesítő adatait az előugró böngészőben:

```powershell
# Connect to your Azure account
Connect-AzAccount
```

Ha egynél több előfizetéssel rendelkezik, válassza ki a rövid útmutatóhoz használni kívánt előfizetést a következő parancsmagok futtatásával. Ügyeljen rá, hogy a `<your subscription name>` helyett saját előfizetésének a nevét adja meg:

```powershell
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup segítségével.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics-feladat létrehozása előtt készítse elő azokat az adatokat, amelyek a feladat bemenetét fogják képezni.

A következő Azure CLI kódblokk számos parancsot hajt végre a feladat által igényelt bemeneti adatok előkészítéséhez. Tekintse át az egyes szakaszokat a kód értelmezése céljából.

1. A PowerShell-ablakban futtassa az [az bejelentkezési](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) parancsot az Azure-fiókjába való bejelentkezéshez.

    Sikeres bejelentkezés után az Azure CLI visszaadja az előfizetések listáját. Másolja a rövid útmutatóhoz használt előfizetést, és futtassa az [az fiókkészlet parancsot](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription) az adott előfizetés kiválasztásához. Válassza ki ugyanazt az előfizetést, amelyet az előző szakaszban a PowerShell. Győződjön meg `<your subscription name>` róla, hogy lecseréli az előfizetés nevét.

    ```azurecli
    az login

    az account set --subscription "<your subscription>"
    ```

2. Hozzon létre egy IoT Hub az [az ioot hub create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) paranccsal. Ebben a példában létrehoz egy IoT Hub nevű **MyASAIoTHub.** Mivel az IoT Hub-nevek egyediek, saját IoT Hub-névvel kell előállnia. Állítsa be a termékváltozat ot F1-re az ingyenes szint használatára, ha az előfizetéssel elérhető. Ha nem, válassza ki a következő legalacsonyabb szintet.

    ```azurecli
    az iot hub create --name "<your IoT Hub name>" --resource-group $resourceGroup --sku S1
    ```

    Az IoT hub létrehozása után az IoT Hub kapcsolati karakterlánca az [az ioot hub show-connection-string](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest) paranccsal. Másolja a teljes kapcsolati karakterláncot, és mentse azt, amikor hozzáadja az IoT Hub-ot bemenetként a Stream Analytics-feladathoz.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

3. Adjon hozzá egy eszközt az IoT Hubhoz az [az iothub-eszközidentitás-létrehozási](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) paranccsal. Ebben a példában létrehoz egy **MyASAIoTDevice**nevű eszközt.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

4. Az eszköz kapcsolati karakterláncának beszereznie az [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) paranccsal. Másolja a teljes kapcsolati karakterláncot, és mentse el a Raspberry Pi szimulátor létrehozásakor.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Kimeneti példa:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-blob-storage"></a>Blob-tárterület létrehozása

A következő Azure PowerShell-kódblokk parancsokat használ a feladatkimenethez használt blobstorage létrehozásához. Tekintse át az egyes szakaszokat a kód értelmezése céljából.

1. Hozzon létre egy szabványos általános célú tárfiókot [a New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount) parancsmag használatával.  Ebben a példában létrehoz egy **myasaquickstartstorage** nevű tárfiókot helyileg redundáns tárral (LRS) és blob titkosítással (alapértelmezés szerint engedélyezve).

2. Kérje le a `$storageAccount.Context` nevű tárfiókkörnyezetet, amely meghatározza a használni kívánt tárfiókot. Ha a tárfiókok használatával történő munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat.

3. Hozzon létre egy tárolótárolót a [New-AzStorageContainer használatával.](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontainer)

4. Másolja a kód által kimenetelét tartalmazó tárolási kulcsot, és mentse azt a kulcsot a streamelési feladat kimenetének későbbi létrehozásához.

    ```powershell
    $storageAccountName = "myasaquickstartstorage"
    $storageAccount = New-AzStorageAccount `
      -ResourceGroupName $resourceGroup `
      -Name $storageAccountName `
      -Location $location `
      -SkuName Standard_LRS `
      -Kind Storage

    $ctx = $storageAccount.Context
    $containerName = "container1"

    New-AzStorageContainer `
      -Name $containerName `
      -Context $ctx

    $storageAccountKey = (Get-AzStorageAccountKey `
      -ResourceGroupName $resourceGroup `
      -Name $storageAccountName).Value[0]

    Write-Host "The <storage account key> placeholder needs to be replaced in your output json files with this key value:"
    Write-Host $storageAccountKey -ForegroundColor Cyan
    ```

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Hozzon létre egy Stream Analytics-feladatot a [New-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsjob) parancsmaggal. Ez a parancsmag a feladat nevét, az erőforráscsoport nevét és a feladatdefiníciót veszi fel paraméterekként. A feladat neve bármilyen, a feladatot azonosító rövid név lehet. Csak alfanumerikus karaktereket, kötőjeleket és aláhúzásokat tartalmazhat, és 3 és 63 karakter közötti nek kell lennie. A feladat definíciója egy JSON-fájl, amely a feladat létrehozásához szükséges tulajdonságokat tartalmazza. Hozzon létre a helyi gépén egy `JobDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat:

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

Ezután futtassa a `New-AzStreamAnalyticsJob` parancsmagot. Cserélje le `jobDefinitionFile` a változó értékét arra az elérési útra, amelyen a feladatdefiníciós JSON-fájlt tárolta.

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force
```

## <a name="configure-input-to-the-job"></a>A feladat bemenetének konfigurálása

Adjon hozzá egy bemenetet a feladathoz a [New-AzStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsinput) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat bemenetének a neve, az erőforráscsoport neve, és a feladat bemenetének definíciója adható meg paraméterként. A feladat bemenetének definíciója egy JSON-fájl, amely a feladat bemenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ebben a példában egy blob storage-t hoz létre bemenetként.

Hozzon létre a helyi gépén egy `JobInputDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. Győződjön meg arról, hogy cserélje le az értéket `accesspolicykey` az `SharedAccessKey` IoT Hub kapcsolati karakterlánc egy előző szakaszban mentett része.

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Devices/IotHubs",
            "properties": {
                "iotHubNamespace": "MyASAIoTHub",
                "sharedAccessPolicyName": "iothubowner",
                "sharedAccessPolicyKey": "accesspolicykey",
                "endpoint": "messages/events",
                "consumerGroupName": "$Default"
                }
        },
        "compression": {
            "type": "None"
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "IoTHubInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Ezután futtassa a `New-AzStreamAnalyticsInput` parancsmast, `jobDefinitionFile` győződjön meg arról, hogy lecseréli a változó értékét arra az elérési útra, ahol a feladatbemeneti definíciójának JSON-fájlját tárolta.

```powershell
$jobInputName = "IoTHubInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName
```

## <a name="configure-output-to-the-job"></a>A feladat kimenetének konfigurálása

Adjon hozzá egy kimenetet a feladathoz a [New-AzStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsoutput) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat kimenetének a neve, az erőforráscsoport neve, és a feladat kimenetének definíciója adható meg paraméterként. A feladat kimenetének definíciója egy JSON-fájl, amely a feladat kimenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ez a példa egy Blob Storage-tárolót használ kimenetként.

Hozzon létre a helyi gépén egy `JobOutputDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. Ügyeljen arra, hogy az `accountKey` értékét lecserélje a tárfiókja hozzáférési kulcsára, amelyet a $storageAccountKey értéke ad meg.

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "asaquickstartstorage",
                      "accountKey": "<storage account key>"
                    }
                ],
                "container": "container1",
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
    "name": "BlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Ezután futtassa a `New-AzStreamAnalyticsOutput` parancsmagot. Ne felejtse el lecserélni a `jobOutputDefinitionFile` változó értékét arra az elérési útra, ahol a feladat kimenetét definiáló JSON-fájl található.

```powershell
$jobOutputName = "BlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force
```

## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

Adjon hozzá egy átalakítást a feladathoz a [New-AzStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticstransformation) parancsmag használatával. Ennél a parancsmagnál a feladat neve, a feladat transzformációjának a neve, az erőforráscsoport neve, és a feladat transzformációjának definíciója adható meg paraméterként. Hozzon létre a helyi gépén egy `JobTransformationDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. A JSON-fájl tartalmaz egy query (lekérdezés) paramétert, amellyel definiálható a transzformációs lekérdezés:

```json
{
    "name":"MyTransformation",
    "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",
    "properties":{
        "streamingUnits":1,
        "script":null,
        "query":" SELECT * INTO BlobOutput FROM IoTHubInput HAVING Temperature > 27"
    }
}
```

Ezután futtassa a `New-AzStreamAnalyticsTransformation` parancsmagot. Győződjön meg arról, hogy lecseréli `jobTransformationDefinitionFile` a változó értékét arra az elérési útra, amelyen a feladatátalakítási definíciójának JSON-fájlját tárolta.

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="run-the-iot-simulator"></a>Az IoT-szimulátor futtatása

1. Nyissa meg a [Raspberry Pi Azure IoT Online Simulator t.](https://azure-samples.github.io/raspberry-pi-web-simulator/)

2. Cserélje le a helyőrzőt a 15- ös sorban az azure IoT Hub-eszköz teljes kapcsolati karakterláncára, amelyet egy előző szakaszban mentett.

3. Kattintson a **Futtatás gombra.** A kimenetnek meg kell jelennie az Érzékelő adatait és az IoT Hubnak küldött üzeneteket.

    ![Online Raspberry Pi Azure IoT-szimulátor](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Indítsa el a feladatot a [Start-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) parancsmag használatával. Ennél a parancsmagnál a feladat neve, az erőforráscsoport neve, a feladat kimenetének indítási módja, és a kezdés ideje adható meg paraméterként. Az `OutputStartMode` által elfogadott értékek: `JobStartTime`, `CustomTime` és `LastOutputEventTime`. A PowerShell-dokumentáció [paraméterekkel](https://docs.microsoft.com/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) foglalkozó szakaszában talál további információt arról, hogy ezek az értékek mit jelentenek.

A következő parancsmag futtatása a `True` kimenetet adja vissza, ha a feladat elindult. A Storage-tárolóban létrejön egy kimeneti mappa, amely az átalakított adatokat tartalmazza.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, törölheti az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha később is szeretné használni a feladatot, akkor nem kell törölnie, hanem elég, ha leállítja. Ha nem fogja tovább használni ezt a feladatot, törölje a rövid útmutató által létrehozott összes erőforrást a következő parancsmag futtatásával:

```powershell
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics-feladatot helyezett üzembe a PowerShell használatával. A Stream Analytics-feladatokat az [Azure Portallal](stream-analytics-quick-create-portal.md) és a [Visual Studióval](stream-analytics-quick-create-vs.md) is üzembe helyezheti.

Az egyéb bemeneti források beállításával és a valós idejű észlelés végrehajtásával kapcsolatos információkért olvassa el az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Valós idejű csalásészlelés az Azure Stream Analytics használatával](stream-analytics-real-time-fraud-detection.md)

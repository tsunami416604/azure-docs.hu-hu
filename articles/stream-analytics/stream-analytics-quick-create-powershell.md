---
title: Azure PowerShell-lel a Stream Analytics-feladat létrehozása
description: Ez a rövid útmutató azt ismerteti, hogyan használhatja az Azure PowerShell-modul üzembe helyezése és futtatása Azure Stream Analytics-feladat.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 83bcdfa1de3968fbb6fdf427879d84f917610c42
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339895"
---
# <a name="quickstart-create-a-stream-analytics-job-using-azure-powershell"></a>Gyors útmutató: Azure PowerShell-lel a Stream Analytics-feladat létrehozása

Az Azure PowerShell-modul létrehozása és kezelése az Azure-erőforrások PowerShell-parancsmagok és parancsfájlok segítségével. Ez az útmutató azt mutatja be, hogy hogyan lehet egy Azure Stream Analytics-feladatot létrehozni és futtatni az Azure PowerShell-modul használatával.

A példa feladat streamelési adatokat olvas be egy IoT Hub-eszköz. A bemeneti adatokat a Raspberry Pi online szimulátor hozza létre. Ezután a Stream Analytics-feladat alakítja át az adatokat egy nagyobb, mint 27° hőmérséklet-szűrő üzenetek a Stream Analytics lekérdezési nyelv használatával. Az eredményül kapott kimeneti események, a blob storage-ban egy fájlba ír.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)

* Ez a rövid útmutatóhoz az Azure PowerShell-modul. Ahhoz, hogy megtudja, melyik verzió van telepítve a helyi gépen, futtassa a `Get-Module -ListAvailable Az` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-Az-ps) ismertető cikket.

* Műveletek nem támogatottak az Azure PowerShell és a befejezett használatával az Azure CLI 2.0.24-es vagy újabb verzióját kell egyes IoT Hub és az IoT-bővítmény Azure CLI-hez. [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és `az extension add --name azure-cli-iot-ext` az IoT-bővítmény telepítése.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` parancsot, majd az előugró böngészőablakban adja meg Azure hitelesítő adatait:

```powershell
# Connect to your Azure account
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, válassza ki az útmutató lépéseinek elvégzéséhez a következő parancsmag futtatásával használni kívánt előfizetést. Ügyeljen rá, hogy a `<your subscription name>` helyett saját előfizetésének a nevét adja meg:

```powershell
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics-feladat létrehozása előtt készítse elő azokat az adatokat, amelyek a feladat bemenetét fogják képezni.

Az alábbi Azure CLI-kódblokkot hajtja végre a feladat bemeneti adatainak előkészítéséhez számos parancsokat. Tekintse át az egyes szakaszokat a kód értelmezése céljából.

1. A PowerShell-ablakban futtassa a [az bejelentkezési](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) paranccsal jelentkezzen be az Azure-fiókjával.

    Sikeresen jelentkezik be, amikor az Azure CLI-vel az előfizetések listáját adja vissza. Másolja ki az előfizetést, használja ezt a rövid útmutató, és futtassa a [fiók beállítása az](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription) parancsot előfizetés kiválasztásához. Válassza ki a PowerShell-lel az előző szakaszban kiválasztott ugyanahhoz az előfizetéshez. Cserélje le `<your subscription name>` az előfizetés nevét.

    ```azurecli
    az login

    az account set --subscription "<your subscription>"
    ```

2. Hozzon létre egy IoT Hub használata a [az iot hub létrehozása](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) parancsot. Ez a példa létrehoz egy IoT hubot nevű **MyASAIoTHub**. Az IoT Hub egyedinek kell lennie, mert szüksége kapja meg a saját IoT Hub nevét. Az ingyenes szint használandó, ha az előfizetéséhez elérhető F1 beállítása a Termékváltozat. Ha nem, válassza ki a következő legalacsonyabb szinttel.

    ```azurecli
    az iot hub create --name "<your IoT Hub name>" --resource-group $resourceGroup --sku S1
    ```

    Az IoT hub létrehozása után lekérése az IoT Hub kapcsolati karakterlánc használatával a [az iot hub show-connection-string](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest) parancsot. Másolja a teljes kapcsolati karakterláncot, és mentse a hozzáadásakor az IoT Hub a Stream Analytics-feladat bemeneteként.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

3. Hozzáad egy eszközt az IoT Hub használata a [az iothub-eszközidentitás létrehozása](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) parancsot. Ez a példa létrehoz egy nevű eszközt **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

4. Az eszköz kapcsolati karakterlánc használatával lekérése a [az iot hub eszközidentitás show-connection-string](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) parancsot. Másolja a teljes kapcsolati karakterláncot, és mentse a Raspberry Pi-szimulátort létrehozásakor.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Példa a kimenetre:**

    ```azurecli
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-blob-storage"></a>Blob-tároló létrehozása

Az alábbi Azure PowerShell-kódblokkot parancsokat használja a feladat kimenetének használt blob-tároló létrehozása. Tekintse át az egyes szakaszokat a kód értelmezése céljából.

1. Hozzon létre egy szabványos, általános célú storage fiókot [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount) parancsmagot.  Ez a példa létrehoz egy nevű tárfiókot **myasaquickstartstorage** titkosítással helyileg redundáns és blobtitkosítással (alapértelmezés szerint engedélyezve van).

2. Kérje le a `$storageAccount.Context` nevű tárfiókkörnyezetet, amely meghatározza a használni kívánt tárfiókot. Ha a tárfiókok használatával történő munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat.

3. Hozzon létre egy storage tárolót [New-AzStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-AzStoragecontainer).

4. A tárfiók hívóbetűjét, a kód által használt kimeneti adattípus másolja, majd mentse a kulcsot később létrehozása a streamelési feladat kimenetének a.

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

Hozzon létre egy Stream Analytics-feladatot a [New-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsjob?view=azurermps-5.4.0) parancsmagot. Ennél a parancsmagnál a feladat neve, az erőforráscsoport-nevet és a feladat definíciója adható meg paraméterként. A feladat neve bármilyen, a feladatot azonosító rövid név lehet. Ez lehet alfanumerikus karaktereket, kötőjeleket, és aláhúzásjelet tartalmazhat, és 3 és 63 karakter között kell lennie. A feladat definíciója egy JSON-fájl, amely a feladat létrehozásához szükséges tulajdonságokat tartalmazza. Hozzon létre a helyi gépén egy `JobDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat:

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

Ezután futtassa a `New-AzStreamAnalyticsJob` parancsmagot. Cserélje le a értékét `jobDefinitionFile` változó az elérési útját, amelyben megtalálható a feladat definícióját JSON-fájlt.

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

Vegye fel a feladat bemenete szerint a [New-AzStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsinput?view=azurermps-5.4.0) parancsmagot. Ennél a parancsmagnál a feladat neve, a feladat bemenetének a neve, az erőforráscsoport neve, és a feladat bemenetének definíciója adható meg paraméterként. A feladat bemenetének definíciója egy JSON-fájl, amely a feladat bemenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ebben a példában egy blob storage-bA bemenetként fog létrehozni.

Hozzon létre a helyi gépén egy `JobInputDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. Cserélje le az értéket `accesspolicykey` együtt a `SharedAccessKey` az IoT Hub kapcsolati karakterláncra az egyik előző szakaszban mentett része.

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

Ezután futtassa a `New-AzStreamAnalyticsInput` parancsmag értékét cserélje le `jobDefinitionFile` változó az elérési útját, amelyben megtalálható a feladat bemenetének definícióját JSON-fájlt.

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

Kimenet hozzáadása a feladathoz használatával a [New-AzStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsoutput?view=azurermps-5.4.0) parancsmagot. Ennél a parancsmagnál a feladat neve, a feladat kimenetének a neve, az erőforráscsoport neve, és a feladat kimenetének definíciója adható meg paraméterként. A feladat kimenetének definíciója egy JSON-fájl, amely a feladat kimenetének konfigurálásához szükséges tulajdonságokat tartalmazza. Ez a példa egy Blob Storage-tárolót használ kimenetként.

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

Adjon hozzá transzformációt a feladathoz használatával a [New-AzStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticstransformation?view=azurermps-5.4.0) parancsmagot. Ennél a parancsmagnál a feladat neve, a feladat transzformációjának a neve, az erőforráscsoport neve, és a feladat transzformációjának definíciója adható meg paraméterként. Hozzon létre a helyi gépén egy `JobTransformationDefinition.json` nevű fájlt, és adja hozzá a következő JSON-adatokat. A JSON-fájl tartalmaz egy query (lekérdezés) paramétert, amellyel definiálható a transzformációs lekérdezés:

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

Ezután futtassa a `New-AzStreamAnalyticsTransformation` parancsmagot. Győződjön meg arról, hogy `jobTransformationDefinitionFile` változó az elérési útját, amelyben megtalálható a feladat átalakítási definíció JSON-fájlt.

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```
## <a name="run-the-iot-simulator"></a>Az IoT-szimulátor futtatásához

1. Nyissa meg a [Raspberry Pi az Azure IoT Online szimulátor](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. A sor 15 a helyőrzőt cserélje le a teljes Azure IoT Hub-eszköz az egyik előző szakaszban mentett kapcsolati karakterláncot.

3. Kattintson a **Run** (Futtatás) parancsra. A kimenet az érzékelő adatokat és az IoT hubnak küldött üzenetek kell megjelennie.

    ![Raspberry Pi az Azure IoT Online szimulátor](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Indítsa el a feladat segítségével a [Start-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/start-azstreamanalyticsjob?view=azurermps-5.4.0) parancsmagot. Ennél a parancsmagnál a feladat neve, az erőforráscsoport neve, a feladat kimenetének indítási módja, és a kezdés ideje adható meg paraméterként. Az `OutputStartMode` által elfogadott értékek: `JobStartTime`, `CustomTime` és `LastOutputEventTime`. A PowerShell-dokumentáció [paraméterekkel](https://docs.microsoft.com/powershell/module/az.streamanalytics/start-azstreamanalyticsjob?view=azurermps-5.4.0) foglalkozó szakaszában talál további információt arról, hogy ezek az értékek mit jelentenek.

A következő parancsmag futtatása a `True` kimenetet adja vissza, ha a feladat elindult. A Storage-tárolóban létrejön egy kimeneti mappa, amely az átalakított adatokat tartalmazza.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a streamelési feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megelőzheti a feladat által használt streamelési egységek kiszámlázását. Ha később is szeretné használni a feladatot, akkor nem kell törölnie, hanem elég, ha leállítja. Ha nem fogja tovább használni ezt a feladatot, ha törli a következő parancsmag futtatásával a rövid útmutatóhoz létrehozott összes erőforrást:

```powershell
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics-feladatot helyezett üzembe a PowerShell használatával. A Stream Analytics-feladatokat az [Azure Portallal](stream-analytics-quick-create-portal.md) és a [Visual Studióval](stream-analytics-quick-create-vs.md) is üzembe helyezheti.

Az egyéb bemeneti források beállításával és a valós idejű észlelés végrehajtásával kapcsolatos információkért olvassa el az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Valós idejű csalásészlelés az Azure Stream Analytics használatával](stream-analytics-real-time-fraud-detection.md)

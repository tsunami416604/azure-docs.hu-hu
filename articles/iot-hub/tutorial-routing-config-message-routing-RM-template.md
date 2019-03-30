---
title: Azure IoT hubra az Azure Resource Manager-sablon használatával üzenet útválasztásának konfigurálása |} A Microsoft Docs
description: Azure IoT hubra az Azure Resource Manager-sablon használatával üzenet útválasztásának konfigurálása
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 58b0039980f448c9bd953e9d59289c511ce95517
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663026"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Oktatóanyag: IoT Hub üzenet-útválasztása konfigurálása Azure Resource Manager-sablon használatával

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Üzenetirányítás

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Töltse le a sablon és paraméterek

Ez az oktatóanyag második része, a töltse le és a egy Visual Studio-alkalmazást az üzenetek küldése az IoT Hub futtassa. A letöltés, amely tartalmazza az Azure Resource Manager-sablon és paraméterek fájl, valamint az Azure CLI és PowerShell-parancsfájlok egy mappában van.

Lépjen tovább, és töltse le a [Azure IoT C# minták](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) most. Bontsa ki a master.zip fájlt. A Resource Manager-sablon és a paramétereket tartalmazó fájlt is /iot-hub/Tutorials/Routing/SimulatedDevice/resources/mint **template_iothub.json** és **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Az erőforrások létrehozása

Egy Azure Resource Manager (RM) sablon használatával létrehozhat az összes erőforrás fog. Az Azure CLI és PowerShell parancsfájlokat egyszerre néhány sort lehet futtatni. Egy erőforrás-kezelő sablon helyezünk üzembe egy lépésben. Ez a cikk bemutatja a külön-külön segítenek megérteni az egyes szakaszokban. Akkor jelenik meg a sablon üzembe helyezéséhez és teszteléséhez a virtuális eszköz létrehozásához. A sablon telepítése után megtekintheti az üzenet-útválasztási konfigurációja a portálon.

Nincsenek több erőforrás nevét, például az IoT Hub nevét és a tárfiók nevének globálisan egyedinek kell lennie. Ahhoz, hogy az erőforrások egyszerűbb elnevezési, ezek erőforrásnevek egy véletlenszerű alfanumerikus érték az aktuális dátum/idő hozzáfűzni kívánt állíthatók be. 

Ha megtekinti a sablont, látni fogja, ahol változók beállítva ezeket az erőforrásokat, amelyek az átadott paraméter, és fűzze össze *randomValue* paraméteréhez. 

Az alábbi szakasz ismerteti a használt paramétereket.

### <a name="parameters"></a>Paraméterek

Ezek a paraméterek többsége az alapértelmezett értékek találhatók. A kapcsolatok végződő **_Foko** eredményeinek összefűzéséhez vannak *randomValue* , hogy globálisan egyedi. 

**randomValue**: Ez az érték az aktuális dátum/idő telepítésekor meg kell adnia a sablon alapján jön létre. Ebben a mezőben nem szerepel a paramétereket tartalmazó fájlt, mert maga a sablon hozza létre.

**subscriptionId**: Ez a mező értéke meg az előfizetést, amelybe a sablon telepítésekor. Ebben a mezőben nem szerepel a paramétereket tartalmazó fájlt, mert van állítva az Ön számára.

**IoTHubName_in**: A mező kitöltése a alapszintű IoT Hub nevét, amely globálisan egyedinek kell lennie a randomValue van kibővítve.

**Hely**: Ez a mező, amelybe helyez üzembe, mint például a "westus" az Azure-régióban.

**consumer_group**: Ezt a mezőt kötelező a fogyasztói csoportot beállítani a útválasztási végpont beérkező üzeneteket. Az Azure Steam elemzési eredmények szűréséhez használatos. Például van a teljes stream mindaz megtalálható, illetve ha consumer_group beállítása a beérkező adatokat **Contoso**, majd beállíthat egy Azure Stream Analytics stream (és a Power BI-jelentés) bejegyzésekre megjelenítéséhez. A 2. rész a jelen oktatóanyag használja ezt a mezőt.

**sku_name**: Ez a mező az IoT hub méretezés. Ennek az értéknek kell lennie az S1 vagy újabb; ingyenes nem működik Ebben az oktatóanyagban, mert nem teszi lehetővé több végpontot.

**sku_units**: Ebben a mezőben a kerül a **sku_name**, és használható az IoT Hub-egységek száma.

**d2c_partitions**: Ez a mező az eseménystream használt partíciók számát.

**storageAccountName_in**: A mező kitöltése létrehozni a tárfiók nevére. Üzenetek egy a storage-fiókban lévő tárolóba kerülnek. Ez a mező van kibővítve a randomValue globálisan egyedi legyen.

**storageContainerName**: Ez a mező, amelyben az üzeneteket irányítja a tárfiók tárolja a tároló neve.

**storage_endpoint**: Ebben a mezőben az a név, a storage-fiók az üzenet-útválasztása által használt végpont.

**service_bus_namespace_in**: A mező kitöltése létrehozni a Service Bus-névtér nevét. Ez az érték van kibővítve a randomValue globálisan egyedi legyen.

**service_bus_queue_in**: Ez a mező akkor használja az üzenetek Service Bus-üzenetsor neve. Ez az érték van kibővítve a randomValue globálisan egyedi legyen.

**AuthRules_sb_queue**: Ez a mező az engedélyezési szabályokat, a service bus-üzenetsorba, a kapcsolati karakterláncot a várólista olvashatók be a.

### <a name="variables"></a>Változók

Ezeket az értékeket a sablont használja, és többnyire származó paraméterek.

**queueAuthorizationRuleResourceId**: Ez a mező az erőforrás-azonosító, az engedélyezési szabály esetében a Service Bus-üzenetsorba. Erőforrás-azonosító le a kapcsolati karakterláncot a várólista viszont szolgál.

**iotHubName**: A mező az IoT Hub nevére akkor, ha összefűzött randomValue kellene. 

**storageAccountName**: A mező a tárfiók neve akkor, ha összefűzött randomValue kellene. 

**service_bus_namespace**: Ebben a mezőben az a névtér összefűzött randomValue kellene után.

**service_bus_queue**: Ez a mező összefűzött randomValue kellene után a Service Bus-üzenetsor neve.

**sbVersion**: A Service Bus API használandó verziója. Ebben az esetben célszerű a "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Erőforrások: Tárfiók és tároló

Az első létrehozott erőforrást, amelyre a rendszer üzeneteket irányítja a tároló és a tárfiók. A tároló egy olyan erőforrás, a storage-fiókban. Rendelkezik egy `dependsOn` a tárfiókhoz a tárfiók igénylő záradék előtt a tároló hozható létre.

Itt van ez a szakasz néz ki:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Erőforrások: A Service Bus-névtérhez és üzenetsorhoz

A második erőforrás létrehozása a Service Bus-névteret, a Service Bus-üzenetsorba, amelyhez üzenetek útválasztása együtt. A Termékváltozat beállítása standard lesz. Az API-verzió veszi át a változókat. A Service Bus-névtér aktiválása, ha ez a szakasz (állapot: aktív) helyez üzembe is van beállítva. 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

Ez a szakasz a Service Bus-üzenetsort hoz létre. Ez a szkript részén egy `dependsOn` záradékot, amely biztosítja, hogy a névtér jön létre a várólista előtt.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Erőforrások: IOT Hub és az üzenet-útválasztása

Most, hogy a storage-fiók és a Service Bus-üzenetsor létrehozása után hozzon létre az IoT Hub, amely üzeneteket irányítja őket. Az erőforrás-kezelő sablon által `dependsOn` szolgáló szerződéses klauzulák, így azt nem próbál létrehozná a központot, a Service Bus-erőforrások és a storage-fiók létrehozása előtt. 

Íme az IoT Hub szakasz első része. Ez a sablon részét állítja be a függőségeket, és elindítja a tulajdonságokkal.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

A következő szakaszban a szakasz az Iot Hub üzenet-útválasztási konfigurációt. Először is a szakasz a végpontok. Ez a sablon részét állítja be a Service Bus-üzenetsorba, és a tárfiókot, beleértve a kapcsolati karakterláncok útválasztási végpontjait.

A kapcsolati karakterláncot a várólista létrehozására van szüksége a queueAuthorizationRulesResourcedId azaz lekért beágyazott. A kapcsolati karakterlánc a tárfiók létrehozásához, kérje le az elsődleges tárolási kulcsot, majd a következő formátumban a kapcsolati karakterlánc.

A végpont-konfiguráció is, ha beállította a blob formátumát `AVRO` vagy `JSON`.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

Ez a következő szakasz az üzenet útvonalakat a végpontokhoz szól. Van egy másolatot minden végponton, így egy a Service Bus-üzenetsorba, és egyet a tárfiók tárolója.

Ne feledje, hogy az üzeneteket, hogy a tárolási lekérdezés feltételét `level="storage"`, és a lekérdezési feltétel esetében az üzeneteket, hogy a Service Bus-üzenetsorba `level="critical"`.

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Ez a json az IoT Hub szakaszban, ahol az alapértelmezett információkat és a Termékváltozat a hub tartalmaz a többi jeleníti meg.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Erőforrások: A Service Bus üzenetsor-engedélyezési szabályok

A Service Bus üzenetsor-engedélyezési szabály a Service Bus-üzenetsor kapcsolati karakterláncára lekérdezéséhez használatos. Használja a `dependsOn` záradékot úgy, hogy azt nem jön létre a Service Bus-névtér és a Service Bus-üzenetsorba előtt.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Erőforrások: Fogyasztói csoport

Ebben a szakaszban ez az oktatóanyag második részében az Azure Stream Analytics által használandó az IoT Hub adatait egy fogyasztói csoport létrehozása.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Erőforrások: Kimenetek

Ha szeretne értéket küldi vissza az üzembe helyezési parancsfájl megjeleníteni, használhat egy kimeneti szakaszban. Ez a sablon részét adja vissza a Service Bus-üzenetsor kapcsolati karakterláncára. Ad vissza értéket nem szükséges, azt rendelkezik-adatként tartalmazza az eredményeket a hívó parancsfájl egy példát.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Az erőforrás-kezelő sablon üzembe helyezése

A sablon üzembe helyezéséhez az Azure-ba, a sablon és paraméterek fájlt feltölteni az Azure Cloud Shell és hajthat végre egy szkriptet a sablon üzembe helyezéséhez. Nyissa meg az Azure Cloud Shellt, és jelentkezzen be. Ez a példa Powershellt használja.

Töltse fel a fájlokat, jelölje be a **fájlok feltöltése/letöltése** ikon a menüsávon, majd válassza a feltöltés lehetőséget.

![Cloud Shell menüsor feltöltési/letöltési fájlok vannak kiemelve](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

A Fájlkezelőben keresse meg a helyi lemezen lévő fájlokat, és válassza ki azokat, majd válassza a felugró **nyílt**.

A fájlok feltöltése után egy eredmények párbeszédpanel jeleníti meg a hiba a következő képhez hasonlóan.

![Cloud Shell menüsor feltöltési/letöltési fájlok vannak kiemelve](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

A fájlok feltöltése a megosztásba, a Cloud Shell-példány által használt. 

Futtassa a szkriptet a telepítés végrehajtásához. Ez a szkript utolsó sora kérdezi le a változót, amely be lett állítva a visszaadandó – a Service Bus-üzenetsor kapcsolati karakterláncot.

Ez a szkript meg ezeket a változókat.

**$RGName** van az erőforráscsoport neve, amelyhez a sablon üzembe helyezéséhez. Ez a mező jön létre a sablon üzembe helyezése előtt.

**$location** van az Azure-helyen lehet használni a sablont, például a "westus".

**deploymentname** név az adatszolgáltató változó értékét a vizualizációhoz a központi telepítés hozzárendelése.

Íme a PowerShell-parancsfájlt. Másolja ezt a PowerShell-parancsfájlt, majd illessze be a Cloud Shell-ablakról le az ENTER billentyűt a futtatáshoz.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Ha parancsprogram-hibák, szerkessze a szkriptet helyileg, töltse fel újra a Cloud shellbe, és futtassa a parancsprogramot. Miután a parancsfájl futtatásának sikeres befejezése, továbbra is a következő lépéssel.

## <a name="create-simulated-device"></a>Szimulált eszköz létrehozása

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Zobrazit zprávu útválasztás a portálon

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>További lépések

Most, hogy mind az erőforrásokat, állítsa be, és az üzenet útvonalak vannak konfigurálva, folytassa a következő oktatóanyaggal, megtudhatja, hogyan dolgozza fel, és az irányított üzenetek információit jeleníti meg.

> [!div class="nextstepaction"]
> [2. rész – az üzenet-útválasztási eredmények megtekintése](tutorial-routing-view-message-routing-results.md)
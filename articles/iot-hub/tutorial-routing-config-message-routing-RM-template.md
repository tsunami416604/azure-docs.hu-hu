---
title: Az Azure IoT Hub üzenet-útválasztásának konfigurálása Egy Azure Resource Manager-sablon használatával
description: Az Azure IoT Hub üzenet-útválasztásának konfigurálása Egy Azure Resource Manager-sablon használatával
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8f245653a8b84944e1e8a3f48a49992f0065be58
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084395"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Oktatóanyag: Az IoT Hub-üzenetútválasztás konfigurálásához használjon Azure Resource Manager-sablont

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Üzenetek útválasztása

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>A sablon- és paraméterfájl letöltése

Az oktatóanyag második részében letölt és futtat egy Visual Studio-alkalmazást az IoT Hubnak küldött üzenetek küldéséhez. Van egy mappa a letöltésben, amely tartalmazza az Azure Resource Manager-sablont és paraméterfájlt, valamint az Azure CLI és a PowerShell-parancsfájlokat.

Folytassa az [Azure IoT C# minták](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) letöltésével. Csomagolja ki a master.zip fájlt. A Resource Manager sablon és a paraméterek fájl ja: /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ mint **template_iothub.json** és **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Az erőforrások létrehozása

Az Azure Resource Manager (RM) sablonhasználatával hozza létre az összes erőforrást. Az Azure CLI és a PowerShell-parancsfájlok egyszerre néhány sort futtathatnak. Egy RM-sablon egy lépésben van telepítve. Ez a cikk külön-külön mutatja be a szakaszokat, hogy segítsen megérteni őket. Ezután megmutatja, hogyan telepítheti a sablont, és hozza létre a virtuális eszközt tesztelésre. A sablon telepítése után megtekintheti az üzenetútválasztási konfigurációt a portálon.

Számos erőforrásnév, amely globálisan egyedi, például az IoT Hub nevét és a tárfiók nevét. Az erőforrások elnevezésének megkönnyítése érdekében ezek az erőforrásnevek úgy vannak beállítva, hogy hozzáfűzjenek egy véletlenszerű alfanumerikus értéket, amelyet az aktuális dátumból/időből hoztak létre. 

Ha megnézi a sablont, láthatja, hogy hol vannak beállítva változók ezekhez az erőforrásokhoz, amelyek a paramétert átadják, és összefűzik a *randomValue-t* a paraméterrel. 

A következő szakasz ismerteti a használt paramétereket.

### <a name="parameters"></a>Paraméterek

A legtöbb paraméter alapértelmezett értékekkel rendelkezik. A **_in** végződőértékek *randomValue* értékkel vannak összefűzve, hogy globálisan egyedivé tegyék őket. 

**randomValue**: Ez az érték az aktuális dátumból/időből jön létre a sablon telepítésekor. Ez a mező nem szerepel a paraméterfájlban, mivel magában a sablonban jön létre.

**subscriptionId**: Ez a mező arra az előfizetésre van beállítva, amelybe a sablont telepíti. Ez a mező nincs a paraméterfájlban, mivel be van állítva.

**IoTHubName_in**: Ez a mező az IoT Hub alapneve, amely a randomValue globálisan egyediként összevan fűzve.

**hely**: Ez a mező az Az Azure-régió, amelybe telepíti, például a "westus".

**consumer_group**: Ez a mező a műveletterv végponton keresztül érkező üzenetek fogyasztói csoportja. Az Azure Stream Analytics eredmények szűrésére szolgál. Például ott van a teljes adatfolyam, ahol mindent beszerez, vagy ha a **Contoso consumer_group**beállított adatokkal érkeznek, akkor beállíthat egy Azure Stream Analytics-adatfolyamot (és a Power BI-jelentést), hogy csak ezeket a bejegyzéseket jelenítse meg. Ez a mező az oktatóanyag 2.

**sku_name**: Ez a mező az IoT Hub méretezése. Ennek az értéknek S1 vagy annál magasabb nak kell lennie; egy ingyenes szint nem működik ez a bemutató, mert nem teszi lehetővé több végpontok.

**sku_units**: Ez a mező a **sku_name,** és a használható IoT Hub-egységek száma.

**d2c_partitions**: Ez a mező az eseményfolyamhoz használt partíciók számát.

**storageAccountName_in**: Ez a mező a létrehozandó tárfiók neve. Az üzenetek a tárfiókban lévő tárolóba kerülnek. Ez a mező össze van fűzve a randomValue értékkel, hogy globálisan egyedi legyen.

**storageContainerName**: Ez a mező annak a tárolónak a neve, amelyben a tárfiókba irányított üzeneteket tárolják.

**storage_endpoint**: Ez a mező az üzenetművelet által használt tárfiók-végpont neve.

**service_bus_namespace_in**: Ez a mező a létrehozandó Service Bus névtér neve. Ez az érték összevan fűzve a randomValue értékkel, hogy globálisan egyedi legyen.

**service_bus_queue_in**: Ez a mező az útválasztási üzenetekhez használt Service Bus-várólista neve. Ez az érték összevan fűzve a randomValue értékkel, hogy globálisan egyedi legyen.

**AuthRules_sb_queue**: Ez a mező a szolgáltatásbusz-várólista engedélyezési szabályai, amelyek a várólista kapcsolati karakterláncának beolvasására szolgálnak.

### <a name="variables"></a>Változók

Ezek az értékek a sablonban használatosak, és többnyire paraméterekből származnak.

**queueAuthorizationRuleResourceId**: Ez a mező a Service Bus-várólista engedélyezési szabályának ResourceId azonosítója. A ResourceId viszont a várólista kapcsolati karakterláncának beolvasására szolgál.

**iotHubName**: Ez a mező az IoT Hub neve a randomValue összefűzése után. 

**storageAccountName**: Ez a mező a tárfiók neve a randomValue összefűzése után. 

**service_bus_namespace**: Ez a mező a névtér a randomValue összefűzése után.

**service_bus_queue**: Ez a mező a Service Bus várólista neve a randomValue összefűzése után.

**sbVersion**: A Service Bus API használni a verziót. Ebben az esetben ez a "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Erőforrások: Tárfiók és tároló

Az első létrehozott erőforrás a tárfiók, valamint a tároló, amelyhez az üzenetek irányítják. A tároló egy erőforrás a tárfiók alatt. Rendelkezik `dependsOn` egy záradékkal a tárfiókhoz, amely megköveteli a tárfiókot a tároló előtt létre kell hozni.

Így néz ki ez a szakasz:

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

### <a name="resources-service-bus-namespace-and-queue"></a>Erőforrások: Service Bus névtér és várólista

A második létrehozott erőforrás a Service Bus névtér, valamint a Service Bus-várólista, amelyhez az üzenetek et továbbítják. A termékváltozat alapfelszereltségre van állítva. Az API-verzió beolvasása a változókból. Azt is be van állítva, hogy aktiválja a Service Bus névtér, amikor telepíti ezt a szakaszt (status:Active). 

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

Ez a szakasz létrehozza a Service Bus várólistát. A parancsfájl ezen része `dependsOn` rendelkezik egy záradékkal, amely biztosítja, hogy a névtér a várólista előtt létrejön.

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

### <a name="resources-iot-hub-and-message-routing"></a>Erőforrások: Iot Hub és üzenetútválasztás

Most, hogy a tárfiók és a Service Bus-várólista létrejött, hozza létre az IoT Hub, amely üzeneteket irányítja őket. Az RM-sablon záradékokat használ, `dependsOn` így nem próbálja meg létrehozni a hubot a Service Bus-erőforrások és a tárfiók létrehozása előtt. 

Az IoT Hub szakasz első része. A sablon ezen része beállítja a függőségeket, és a tulajdonságokkal kezdődik.

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

A következő szakasz az Iot Hub üzenet-útválasztási konfigurációjának szakasza. Az első a végpontok szakasza. A sablon ezen része beállítja a Service Bus-várólista és a tárfiók útválasztási végpontjait, beleértve a kapcsolati karakterláncokat is.

A várólista kapcsolati karakterláncának létrehozásához szüksége van a queueAuthorizationRulesResourcedId azonosítóra, amely a szövegközi beolvasást kéri. A tárfiók kapcsolati karakterláncának létrehozásához kérje le az elsődleges tárolókulcsot, és használja azt a kapcsolati karakterlánc formátumában.

A végpont konfigurációja is, ahol beállíthatja a blob formátumot, hogy `AVRO` vagy. `JSON`

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

Ez a következő szakasz a végpontokhoz vezető üzenetútvonalakra szól. Minden végponthoz egy van beállítva, így van egy a Service Bus-várólistához és egy a tárfiók tárolóhoz.

Ne feledje, hogy a tárolóba irányítandó üzenetek lekérdezési feltétele a `level="storage"`, és a `level="critical"`Service Bus várólistába irányított üzenetek lekérdezési feltétele a.

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

Ez a json az IoT Hub szakasz többi részét jeleníti meg, amely az alapértelmezett információkat és a hub termékváltozatát tartalmazza.

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

### <a name="resources-service-bus-queue-authorization-rules"></a>Erőforrások: A Service Bus várólista engedélyezési szabályai

A Service Bus várólista engedélyezési szabálya a Service Bus-várólista kapcsolati karakterláncának lekéréséhez használatos. Egy `dependsOn` záradékot használ annak biztosítására, hogy nem jön létre a Service Bus névtér és a Service Bus-várólista előtt.

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

### <a name="resources-consumer-group"></a>Források: Fogyasztói csoport

Ebben a szakaszban hozzon létre egy fogyasztói csoportot az IoT Hub-adatok az Azure Stream Analytics által használandó az oktatóanyag második részében.

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

### <a name="resources-outputs"></a>Források: Outputok

Ha egy értéket szeretne visszaküldeni a központi telepítési parancsfájlnak, használjon kimeneti szakaszt. A sablon ezen része a Service Bus-várólista kapcsolati karakterláncát adja vissza. Egy érték visszaadása nem szükséges, ez példaként szolgál az eredmények visszaadása a hívó parancsfájlba.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Az RM-sablon telepítése

A sablon üzembe helyezéséhez az Azure-ba, töltse fel a sablont és a paraméterek et az Azure Cloud Shell, majd hajtson végre egy parancsfájlt a sablon üzembe helyezéséhez. Nyissa meg az Azure Cloud Shellt, és jelentkezzen be. Ez a példa a PowerShellt használja.

A fájlok feltöltéséhez válassza a **Fájlok feltöltése/letöltése** ikont a menüsorban, majd kattintson a Feltöltés gombra.

![Cloud Shell menüsor kiemelt fájlok feltöltésével/letöltésével](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

A felugró Fájlkezelőben keresse meg a helyi lemezen lévő fájlokat, és jelölje ki őket, majd válassza a **Megnyitás**gombot.

A fájlok feltöltése után az eredménypárbeszédpanelen az alábbihoz hasonló kép jelenik meg.

![Cloud Shell menüsor kiemelt fájlok feltöltésével/letöltésével](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

A fájlok feltöltésre kerülnek a Cloud Shell-példány által használt megosztásra. 

Futtassa a parancsfájlt a központi telepítés végrehajtásához. A parancsfájl utolsó sora lekéri a visszaadandó változót - a Service Bus várólista kapcsolati karakterláncát.

A parancsfájl beállítja és használja a következő változókat:

**$RGName** az az erőforráscsoport neve, amelyre a sablont telepíteni szeretné. Ez a mező a sablon telepítése előtt jön létre.

**$location** a sablonhoz használható Azure-hely, például a "westus".

**A központi telepítés neve** a központi telepítéshez rendelt név a visszatérő változó értékének beolvasásához.

Itt a PowerShell-parancsfájl. Másolja a PowerShell-parancsfájlt, és illessze be a Cloud Shell ablakába, majd nyomja meg az Enter billentyűt a futtatásához.

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

Ha parancsfájlhibákat észlel, helyileg szerkesztheti a parancsfájlt, újra feltöltheti a Cloud Shellbe, és újra futtathatja a parancsfájlt. Miután a parancsfájl sikeresen befejeződött, folytassa a következő lépéssel.

## <a name="create-simulated-device"></a>Szimulált eszköz létrehozása

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Üzenet-útválasztás megtekintése a portálon

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>További lépések

Most, hogy az összes erőforrás be van állítva, és az üzenetútvonalak konfigurálva vannak, haladjon tovább a következő oktatóanyagra, és ismerje meg, hogyan dolgozhatja fel és jelenítheti meg az útválasztással kapcsolatos üzenetek adatait.

> [!div class="nextstepaction"]
> [2. rész – Az üzenetút-tervezés eredményeinek megtekintése](tutorial-routing-view-message-routing-results.md)

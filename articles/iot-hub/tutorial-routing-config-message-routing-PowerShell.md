---
title: Üzenet-útválasztás konfigurálása az Azure IoT Hubhoz az Azure PowerShell használatával
description: Az Azure PowerShell használatával konfigurálhatja az Üzenetek útválasztását az Azure IoT Hubhoz. Az üzenet tulajdonságaitól függően irányítsa át egy tárfiókhoz vagy egy Service Bus-várólistához.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 68338c56419316e561bb072c1a0555e89d3de85b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084437"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Oktatóanyag: Az IoT Hub-üzenetútválasztás konfigurálásához használja az Azure PowerShellt

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>A szkript letöltése (nem kötelező)

Az oktatóanyag második részében letölt és futtat egy Visual Studio-alkalmazást az IoT Hubnak küldött üzenetek küldéséhez. Van egy mappa a letöltésben, amely tartalmazza az Azure Resource Manager-sablont és paraméterfájlt, valamint az Azure CLI és a PowerShell-parancsfájlokat. 

Ha meg szeretné tekinteni a kész parancsfájlt, töltse le az [Azure IoT C# mintákat.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Csomagolja ki a master.zip fájlt. Az Azure CLI-parancsfájl a /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ könyvtárban található, mint **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Az erőforrások létrehozása

Először hozza létre az erőforrásokat a PowerShell használatával.

### <a name="use-powershell-to-create-your-base-resources"></a>Az alaperőforrások létrehozása a PowerShell használatával

Másolja és illessze be az alábbi parancsfájlt a Cloud Shell be, és nyomja le az Enter billentyűt. A parancsfájlt soronként futtatja. A parancsfájl első szakasza létrehozza az oktatóanyag alaperőforrásait, beleértve a tárfiókot, az IoT Hubot, a Service Bus-névtér és a Service Bus-várólistát. Abemutató során másolja az egyes parancsfájlokat, és illessze be a Cloud Shellbe a futtatásához.

Számos erőforrásnév, amely globálisan egyedi, például az IoT Hub nevét és a tárfiók nevét. A könnyebb ikonra, ezek az erőforrásnevek egy random alfanumerikus értékkel, a *randomValue-értékkel*vannak elfűzve. A randomValue a parancsfájl tetején egyszer jön létre, és szükség szerint hozzáfűzi az erőforrásneveket a parancsfájlban. Ha nem szeretné, hogy véletlenszerű legyen, beállíthatja egy üres karakterláncra vagy egy adott értékre. 

> [!IMPORTANT]
> A kezdeti parancsfájlban beállított változókat az útválasztási parancsfájl is használja, ezért futtassa az összes parancsfájlt ugyanabban a Cloud Shell-munkamenetben. Ha új munkamenetet nyit meg a parancsfájl futtatásához az útválasztás beállításához, több változóból hiányoznak az értékek. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Szimulált eszköz létrehozása

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Most, hogy az alaperőforrások be vannak állítva, konfigurálhatja az üzenet-útválasztást.

## <a name="set-up-message-routing"></a>Üzenetek útválasztásának beállítása

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Útválasztási végpont létrehozásához használja [az Add-AzIotHubRoutingEndpoint .to create a routing endpoint, use Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). A végpont üzenetkezelési útvonalának létrehozásához használja az [Add-AzIotHubRoute .to](/powershell/module/az.iothub/Add-AzIoTHubRoute)create the messaging route for the endpoint, use Add-AzIotHubRoute .

### <a name="route-to-a-storage-account"></a>Átirányítás tárfiókba 

Először állítsa be a végpontot a tárfiókhoz, majd hozza létre az üzenetútvonalat.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Ezek azok a változók, amelyeket a parancsfájl használ, és amelyeket a Cloud Shell-munkamenetben kell beállítani:

**resourceGroup**: Ennek a mezőnek két előfordulása van - mindkettőt az erőforráscsoportra állítja.

**name**: Ez a mező annak az IoT-központnak a neve, amelyre a műveletterv vonatkozik.

**endpointName**: Ez a mező a végpontot azonosító név. 

**endpointType**: Ez a mező a végpont típusa. Ezt az értéket `azurestoragecontainer`a `eventhub` `servicebusqueue`, `servicebustopic`, , vagy . Az Ön céljaira állítsa `azurestoragecontainer`be a .

**subscriptionID:** Ez a mező az Azure-fiók előfizetési azonosítójára van beállítva.

**storageConnectionString**: Ezt az értéket az előző parancsfájlban beállított tárfiókból olvassa be a rendszer. Az útválasztási rendszer a tárfiók eléréséhez használja.

**containerName**: Ez a mező annak a tárolónak a neve, amelybe az adatokat írni fogja.

**Kódolás**: Állítsa ezt `AVRO` a `JSON`mezőt vagy a. Ez a tárolt adatok formátumát jelöli. Az alapértelmezett érték az AVRO.

**routeName**: Ez a mező a beállított útvonal neve. 

**feltétel**: Ez a mező az erre a végpontra küldött üzenetek szűrésére szolgáló lekérdezés. A tárolóba irányítandó üzenetek lekérdezési `level="storage"`feltétele a .

**engedélyezve**: Ez `true`a mező alapértelmezés szerint a , jelezve, hogy az üzenetútvonal at a létrehozás után engedélyezni kell.

Másolja a parancsfájlt, és illessze be a Cloud Shell ablakába.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

A következő lépés a tárfiók útválasztási végpontjának létrehozása. Azt a tárolót is megadhatja, amelyben az eredmények tárolásra kerülnek. A tároló a tárfiók létrehozásakor jött létre.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Ezután hozza létre az üzenetútvonalat a tárolási végponthoz. Az üzenet útvonala azt jelzi, hogy hová kell küldeni a lekérdezési specifikációnak megfelelő üzeneteket.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Útvonal a Service Bus várólistájára

Most állítsa be az útválasztást a Service Bus-üzenetsorhoz. A Service Bus-várólista kapcsolati karakterláncának beolvasásához létre kell hoznia egy engedélyezési szabályt, amely a megfelelő jogosultságokkal rendelkezik. A következő parancsfájl létrehoz egy engedélyezési szabályt `sbauthrule`a Service Bus `Listen Manage Send`várólistához, amelyet a rendszernek hív, és a jogosultságokat a számára állítja be. Az engedélyezési szabály beállítása után a várólistához való kapcsolati karakterlánc beolvasására használhatja.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Most használja az engedélyezési szabályt a Service Bus várólista kulcsának lekéréséhez. Ez az engedélyezési szabály a parancsfájl későbbi részében a kapcsolati karakterlánc beolvasására szolgál.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Most állítsa be az útválasztási végpontot és a Service Bus-várólista üzenetútvonalát. Ezek azok a változók, amelyeket a parancsfájl használ, és amelyeket a Cloud Shell-munkamenetben kell beállítani:

**endpointName**: Ez a mező a végpontot azonosító név. 

**endpointType**: Ez a mező a végpont típusa. Ezt az értéket `azurestoragecontainer`a `eventhub` `servicebusqueue`, `servicebustopic`, , vagy . Az Ön céljaira állítsa `servicebusqueue`be a .

**routeName**: Ez a mező a beállított útvonal neve. 

**feltétel**: Ez a mező az erre a végpontra küldött üzenetek szűrésére szolgáló lekérdezés. A Service Bus várólistába irányított üzenetek lekérdezési `level="critical"`feltétele a .

Itt van az Azure PowerShell az üzenet-útválasztás a Service Bus-várólistához.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Üzenet-útválasztás megtekintése a portálon

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>További lépések

Most, hogy beállította az erőforrásokat, és az üzenetútvonalak konfigurálva, előre a következő oktatóanyag, hogy megtanulják, hogyan küldhet üzeneteket az IoT hub, és látni őket a különböző célhelyekre. 

> [!div class="nextstepaction"]
> [2. rész – Az üzenetút-tervezés eredményeinek megtekintése](tutorial-routing-view-message-routing-results.md)
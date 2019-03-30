---
title: Azure PowerShell-lel az Azure IoT Hub üzenet útválasztásának konfigurálása |} A Microsoft Docs
description: Azure PowerShell-lel az Azure IoT Hub üzenet útválasztásának konfigurálása
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 51e9bc85c2ee843aa096674a25a1f634bd08b838
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660931"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Oktatóanyag: Az IoT Hub üzenet útválasztásának konfigurálása az Azure PowerShell használatával

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Töltse le a parancsfájl (nem kötelező)

Ez az oktatóanyag második része, a töltse le és a egy Visual Studio-alkalmazást az üzenetek küldése az IoT Hub futtassa. A letöltés, amely tartalmazza az Azure Resource Manager-sablon és paraméterek fájl, valamint az Azure CLI és PowerShell-parancsfájlok egy mappában van. 

Ha azt szeretné, a befejezett parancsfájl megtekintése, töltse le a [Azure IoT C# minták](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Bontsa ki a master.zip fájlt. Az Azure CLI-példaszkript /iot-hub/Tutorials/Routing/SimulatedDevice/resources/másként van **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Az erőforrások létrehozása

Először hozzon létre erőforrásokat a PowerShell használatával.

### <a name="use-powershell-to-create-your-base-resources"></a>Az alapvető erőforrások létrehozása a PowerShell használatával

Nincsenek több erőforrás nevét, például az IoT Hub nevét és a tárfiók nevének globálisan egyedinek kell lennie. Az eljárás egyszerűbbé tétele az erőforráscsoport nevére kiegészítve egy véletlenszerű alfanumerikus érték nevű *randomValue*. A randomValue egyszer jön létre, a szkript elején, és hozzáfűzi az erőforrások nevei a parancsfájl egész igény szerint. Ha nem szeretné, hogy a véletlenszerű, beállíthatja azt egy adott értékre vagy üres karakterlánc. 

> [!IMPORTANT]
> A kezdeti parancsfájlban állítsa be a változókat is használhatók az útválasztási parancsfájl, így a ugyanazon Cloud Shell-munkameneten belül futó minden, a parancsfájl. Futtassa a parancsfájlt az útválasztás beállítása új munkamenetet nyit meg, ha több, változó hiányozni fognak értékeket. 
>

Másolja és illessze be az alábbi parancsfájlt a Cloud shellbe, és nyomja le az Enter billentyűt. A szkript egy sor fut egyszerre. Ez a szkript első szakaszában létrehozza az alapvető erőforrások ebben az oktatóanyagban, beleértve a storage-fiók, az IoT Hub, a Service Bus-Namespace és a Service Bus-üzenetsorba. Az oktatóanyag lépéseinek követése közben minden parancsfájl-blokk másolja és illessze be a Cloud Shell a futtatáshoz.

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
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

Most, hogy az alapvető erőforrások beállítása, beállíthatja az üzenet-útválasztással.

## <a name="set-up-message-routing"></a>Üzenetek útválasztásának beállítása

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Hozzon létre egy útválasztási végpontot, használja a [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Az üzenetkezelési útvonal a végpont létrehozásához használja [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Átirányítása a tárfiókba 

Először állítsa be a storage-fiók végpontját, majd hozzon létre az üzenetek útvonalának.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Ezek a változók vannak beállítva:

**Erőforráscsoport**: Nincsenek ezt a mezőt két előfordulását – mindkettő értékre az erőforráscsoportban.

**Név**: Ez a mező pedig az IoT Hub, amelyekre alkalmazza az útválasztási neve.

**Végpontneve**: A mező kitöltése azonosítása a végpont nevét. 

**endpointType**: Ebben a mezőben az a végpont típusa. Ezt az értéket kell beállítani `azurestoragecontainer`, `eventhub`, `servicebusqueue`, vagy `servicebustopic`. A okokból Itt beállíthatja azt a `azurestoragecontainer`.

**subscriptionID**: Ez a mező értéke az előfizetés-azonosító az Azure-fiókjával.

**storageConnectionString**: Ezt az értéket veszi át az előző parancsfájlban állítsa be a tárfiók. Akkor használják az Útválasztás a tárfiók eléréséhez.

**containerName**: Ebben a mezőben az a név a tároló a tárfiókban, amelyre az adatok írva lesznek.

**Kódolás**: Adja meg a mezőben vagy `AVRO` vagy `JSON`. Ez azt jelzi, a tárolt adatok formátumát. Az alapértelmezett érték az avro-hoz.

**routeName**: Ebben a mezőben az a név, az útvonal állítja be. 

**A feltétel**: Ez a mező ennek a végpontnak küldött üzenetek szűréséhez használt lekérdezés. A lekérdezési feltétel, az üzenetek továbbítása a storage `level="storage"`.

**Engedélyezett**: Ez a mező az alapértelmezett `true`, amely azt jelzi, hogy az üzenetek útvonalának engedélyezni kell a létrehozása után.

Ez a szkript másolja és illessze be a Cloud Shell ablakába.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

A következő lépés, hogy a tárfiók útválasztási végpont létrehozásához. Emellett adja meg a tároló, amelyben az eredményeket fogja tárolni. A tároló létrejött, a storage-fiók létrehozásakor.

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

Ezután hozza létre az üzenetek útvonalának a storage-végponthoz. Az üzenet útvonalának jelöl ki, hova küldhetők az üzeneteket, amelyek megfelelnek a lekérdezés specifikációjában.

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

### <a name="route-to-a-service-bus-queue"></a>Irányíthatja a Service Bus-üzenetsorba

Most állítsa be az útválasztást a Service Bus-üzenetsorhoz. A Service Bus-üzenetsor kapcsolati karakterláncára lekéréséhez létre kell hoznia egy engedélyezési szabályt, amely rendelkezik definiált e megfelelő jogosultságokkal. A következő szkriptet a Service Bus-üzenetsorba nevű engedélyezési szabályt hoz létre `sbauthrule`, és a jogok `Listen Manage Send`. Ez az engedélyezési szabály beállítása után használhatja, hogy a várólista a kapcsolati karakterlánc lekéréséhez.

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

A Service Bus üzenetsor-kulcs lekérése mostantól használhatja az engedélyezési szabályt. Ez az engedélyezési szabály le a kapcsolati karakterláncot a szkript későbbi részében lesz használható.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Most állítsa be az útválasztási végpont és az üzenetek útvonalának for a Service Bus-üzenetsorba. Ezek a változók vannak beállítva:

**Végpontneve**: A mező kitöltése azonosítása a végpont nevét. 

**endpointType**: Ebben a mezőben az a végpont típusa. Ezt az értéket kell beállítani `azurestoragecontainer`, `eventhub`, `servicebusqueue`, vagy `servicebustopic`. A okokból Itt beállíthatja azt a `servicebusqueue`.

**routeName**: Ebben a mezőben az a név, az útvonal állítja be. 

**A feltétel**: Ez a mező ennek a végpontnak küldött üzenetek szűréséhez használt lekérdezés. A lekérdezési feltétel esetében az üzeneteket, hogy a Service Bus-üzenetsorba `level="critical"`.

Itt látható az Azure PowerShell, az üzenet útválasztás esetében a Service Bus-üzenetsorba.

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

### <a name="view-message-routing-in-the-portal"></a>Zobrazit zprávu útválasztás a portálon

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik az erőforrások beállítása és az üzenetútvonalak konfigurálva, folytassa a következő oktatóanyag üzenetek küldése az IoT hub és azok a másik helyre lesznek irányítva. 

> [!div class="nextstepaction"]
> [2. rész – az üzenet-útválasztási eredmények megtekintése](tutorial-routing-view-message-routing-results.md)
---
title: Üzenet-útválasztás konfigurálása az Azure IoT Hub hoz az Azure CLI használatával
description: Az Azure IoT Hub üzenet-útválasztáskonfigurálása az Azure CLI használatával. Az üzenet tulajdonságaitól függően irányítsa át egy tárfiókhoz vagy egy Service Bus-várólistához.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 056dac7977115f97892d8dbfde0710e00237804e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78674345"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Oktatóanyag: Az Azure CLI használatával konfigurálhatja az IoT Hub-üzenetútválasztást

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>A szkript letöltése (nem kötelező)

Az oktatóanyag második részében letölt és futtat egy Visual Studio-alkalmazást az IoT Hubnak küldött üzenetek küldéséhez. Van egy mappa a letöltésben, amely tartalmazza az Azure Resource Manager-sablont és paraméterfájlt, valamint az Azure CLI és a PowerShell-parancsfájlokat.

Ha meg szeretné tekinteni a kész parancsfájlt, töltse le az [Azure IoT C# mintákat.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Csomagolja ki a master.zip fájlt. Az Azure CLI-parancsfájl a /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ könyvtárban található, mint **iothub_routing_cli.azcli.**

## <a name="use-the-azure-cli-to-create-your-resources"></a>Az Azure CLI segítségével hozza létre az erőforrásokat

Másolja és illessze be az alábbi parancsfájlt a Cloud Shell be, és nyomja le az Enter billentyűt. A parancsfájlt soronként futtatja. A parancsfájl első szakasza létrehozza az oktatóanyag alaperőforrásait, beleértve a tárfiókot, az IoT Hubot, a Service Bus-névtér és a Service Bus-várólistát. Abemutató többi részének végighaladva másolja az egyes parancsfájlokat, és illessze be a Cloud Shellbe a futtatásához.

> [!TIP]
> Tipp a hibakeresésről: ez a parancsfájl a `\`folytatási szimbólumot (a fordított perjelet) használja a parancsfájl olvashatóbbá teszi. Ha probléma merül fel a parancsfájl futtatásakor, `bash` győződjön meg arról, hogy a Cloud Shell-munkamenet fut, és hogy nincsenek szóközök a fordított perjelek után.
> 

Számos erőforrásnév, amely globálisan egyedi, például az IoT Hub nevét és a tárfiók nevét. A könnyebb ikonra, ezek az erőforrásnevek egy random alfanumerikus értékkel, a *randomValue-értékkel*vannak elfűzve. A randomValue a parancsfájl tetején egyszer jön létre, és szükség szerint hozzáfűzi az erőforrásneveket a parancsfájlban. Ha nem szeretné, hogy véletlenszerű legyen, beállíthatja egy üres karakterláncra vagy egy adott értékre. 

> [!IMPORTANT]
> A kezdeti parancsfájlban beállított változókat az útválasztási parancsfájl is használja, ezért futtassa az összes parancsfájlt ugyanabban a Cloud Shell-munkamenetben. Ha új munkamenetet nyit meg a parancsfájl futtatásához az útválasztás beállításához, több változóból hiányoznak az értékek.
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Most, hogy az alaperőforrások be vannak állítva, konfigurálhatja az üzenet-útválasztást.

## <a name="set-up-message-routing"></a>Üzenetek útválasztásának beállítása

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Útválasztási végpont létrehozásához használja [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create). A végpont üzenetútvonalának létrehozásához használja az [iot hub route create](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Átirányítás tárfiókba

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Először állítsa be a végpontot a tárfiókhoz, majd állítsa be az útvonalat. 

Ezek azok a változók, amelyeket a parancsfájl használ, és amelyeket a Cloud Shell-munkamenetben kell beállítani:

**storageConnectionString**: Ezt az értéket az előző parancsfájlban beállított tárfiókból olvassa be a rendszer. Az üzenet-útválasztás a tárfiók eléréséhez használja.

  **resourceGroup**: Az erőforráscsoportnak két előfordulása van – állítsa be őket az erőforráscsoportra.

**végpont-előfizetési azonosító:** Ez a mező a végpont Azure-előfizetési azonosítója. 

**endpointType**: Ez a mező a végpont típusa. Ezt az értéket `azurestoragecontainer`a `eventhub` `servicebusqueue`, `servicebustopic`, , vagy . Az Ön céljaira állítsa `azurestoragecontainer`be a .

**iotHubName**: Ez a mező annak a hubnak a neve, amely a művelettervet fogja végezni.

**containerName**: Ez a mező annak a tárolónak a neve, amelybe az adatokat írni fogja.

**kódolás**: Ez a `avro` mező `json`vagy a . Ez a tárolt adatok formátumát jelöli.

**routeName**: Ez a mező a beállított útvonal neve. 

**endpointName**: Ez a mező a végpontot azonosító név. 

**engedélyezve**: Ez `true`a mező alapértelmezés szerint a , jelezve, hogy az üzenetútvonal at a létrehozás után engedélyezni kell.

**feltétel**: Ez a mező az erre a végpontra küldött üzenetek szűrésére szolgáló lekérdezés. A tárolóba irányítandó üzenetek lekérdezési `level="storage"`feltétele a .

Másolja a parancsfájlt, és illessze be a Cloud Shell ablakába, és futtassa azt.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

A következő lépés a tárfiók útválasztási végpontjának létrehozása. Azt a tárolót is megadhatja, amelyben az eredmények tárolásra kerülnek. A tároló korábban jött létre a tárfiók létrehozásakor.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Ezután hozza létre az útvonalat a tárolási végponthoz. Az üzenet útvonala azt jelzi, hogy hová kell küldeni a lekérdezési specifikációnak megfelelő üzeneteket. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Útvonal a Service Bus várólistájára

Most állítsa be az útválasztást a Service Bus-üzenetsorhoz. A Service Bus-várólista kapcsolati karakterláncának beolvasásához létre kell hoznia egy engedélyezési szabályt, amely a megfelelő jogosultságokkal rendelkezik. A következő parancsfájl létrehoz egy engedélyezési szabályt `sbauthrule`a Service Bus `Listen Manage Send`várólistához, amelyet a rendszernek hív, és a jogosultságokat a számára állítja be. Az engedélyezési szabály definiálása után a várólistához való kapcsolati karakterlánc lekéréséhez használhatja.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Most használja az engedélyezési szabályt a kapcsolati karakterlánc lekéréséhez a Service Bus várólistához.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Most állítsa be az útválasztási végpontot és a Service Bus-várólista üzenetútvonalát. Ezek azok a változók, amelyeket a parancsfájl használ, és amelyeket a Cloud Shell-munkamenetben kell beállítani:

**endpointName**: Ez a mező a végpontot azonosító név. 

**endpointType**: Ez a mező a végpont típusa. Ezt az értéket `azurestoragecontainer`a `eventhub` `servicebusqueue`, `servicebustopic`, , vagy . Az Ön céljaira állítsa `servicebusqueue`be a .

**routeName**: Ez a mező a beállított útvonal neve. 

**feltétel**: Ez a mező az erre a végpontra küldött üzenetek szűrésére szolgáló lekérdezés. A Service Bus várólistába irányított üzenetek lekérdezési `level="critical"`feltétele a .

Itt van az Azure CLI az útválasztási végpont és az üzenet útvonala a Service Bus-várólista.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Üzenet-útválasztás megtekintése a portálon

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>További lépések

Most, hogy beállította az erőforrásokat, és az üzenetútvonalak konfigurálva, előre a következő oktatóanyag, hogy megtanulják, hogyan küldhet üzeneteket az IoT hub, és látni őket a különböző célhelyekre. 

> [!div class="nextstepaction"]
> [2. rész – Az üzenetút-tervezés eredményeinek megtekintése](tutorial-routing-view-message-routing-results.md)

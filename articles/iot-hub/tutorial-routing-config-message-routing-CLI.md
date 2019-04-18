---
title: Az Azure CLI használatával az Azure IoT Hub üzenet útválasztásának konfigurálása |} A Microsoft Docs
description: Az Azure CLI használatával az Azure IoT Hub üzenet útválasztásának konfigurálása
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 6faa585f1ad38eb981e0bbffffef603c4aab0bc8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360285"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Oktatóanyag: Az IoT Hub üzenet útválasztásának konfigurálása az Azure CLI használatával

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Töltse le a parancsfájl (nem kötelező)

Ez az oktatóanyag második része, a töltse le és a egy Visual Studio-alkalmazást az üzenetek küldése az IoT Hub futtassa. A letöltés, amely tartalmazza az Azure Resource Manager-sablon és paraméterek fájl, valamint az Azure CLI és PowerShell-parancsfájlok egy mappában van.

Ha azt szeretné, a befejezett parancsfájl megtekintése, töltse le a [Azure IoT C# minták](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Bontsa ki a master.zip fájlt. Az Azure CLI-példaszkript /iot-hub/Tutorials/Routing/SimulatedDevice/resources/másként van **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Az erőforrások létrehozása az Azure CLI használatával

Nincsenek több erőforrás nevét, például az IoT Hub nevét és a tárfiók nevének globálisan egyedinek kell lennie. Az eljárás egyszerűbbé tétele az erőforráscsoport nevére kiegészítve egy véletlenszerű alfanumerikus érték nevű *randomValue*. A randomValue egyszer jön létre, a szkript elején, és hozzáfűzi az erőforrások nevei a parancsfájl egész igény szerint. Ha nem szeretné, hogy a véletlenszerű, beállíthatja azt egy adott értékre vagy üres karakterlánc. 

> [!IMPORTANT]
> A kezdeti parancsfájlban állítsa be a változókat is használhatók az útválasztási parancsfájl, így a ugyanazon Cloud Shell-munkameneten belül futó minden, a parancsfájl. Futtassa a parancsfájlt az útválasztás beállítása új munkamenetet nyit meg, ha több, változó hiányozni fognak értékeket.
>

Másolja és illessze be az alábbi parancsfájlt a Cloud shellbe, és nyomja le az Enter billentyűt. A szkript egy sor fut egyszerre. Ez a szkript első szakaszában létrehozza az alapvető erőforrások ebben az oktatóanyagban, beleértve a storage-fiók, az IoT Hub, a Service Bus-Namespace és a Service Bus-üzenetsorba. Az oktatóanyag további részeinek lépésein végighaladva minden parancsfájl-blokk másolja és illessze be a Cloud Shell a futtatáshoz.

> [!TIP]
> Tipp: hibakeresésről: Ez a szkript a folytatási szimbólum (fordított perjel `\`) olvashatóbbá a parancsfájlt. Ha a probléma, a szkript futtatása, győződjön meg arról, ne legyenek szóközök után bármely a perjelek szerepeljenek.
> 

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

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

Most, hogy az alapvető erőforrások beállítása, beállíthatja az üzenet-útválasztással.

## <a name="set-up-message-routing"></a>Üzenetek útválasztásának beállítása

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Hozzon létre egy útválasztási végpontot, használja a [az iot hub útválasztás-végpont létrehozása](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create). Az üzenet útvonalának a végpont létrehozásához használja [az iot hub útvonal létrehozása](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Átirányítása a tárfiókba

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Először állítsa be a storage-fiók végpontját, majd állítsa be az útvonalat. 

Ezek a változók vannak beállítva:

**storageConnectionString**: Ezt az értéket veszi át az előző parancsfájlban állítsa be a tárfiók. A tárfiók eléréséhez üzenetirányítás használják.

  **Erőforráscsoport**: Nincsenek erőforráscsoport két előfordulását – állítsa őket az erőforráscsoportban.

**végpont subscriptionID**: Ez a mező értéke a végpontot Azure előfizetés-azonosító. 

**endpointType**: Ebben a mezőben az a végpont típusa. Ezt az értéket kell beállítani `azurestoragecontainer`, `eventhub`, `servicebusqueue`, vagy `servicebustopic`. A okokból Itt beállíthatja azt a `azurestoragecontainer`.

**iotHubName**: A mező kitöltése, amelyeket a rendszer az Útválasztás a hub nevét.

**containerName**: Ebben a mezőben az a név a tároló a tárfiókban, amelyre az adatok írva lesznek.

**Kódolás**: Ez a mező lehet `avro` vagy `json`. Ez azt jelzi, hogy a tárolt adatok formátumát.

**routeName**: Ebben a mezőben az a név, az útvonal állítja be. 

**Végpontneve**: A mező kitöltése azonosítása a végpont nevét. 

**Engedélyezett**: Ez a mező az alapértelmezett `true`, amely azt jelzi, hogy az üzenetek útvonalának engedélyezni kell a létrehozása után.

**A feltétel**: Ez a mező ennek a végpontnak küldött üzenetek szűréséhez használt lekérdezés. A lekérdezési feltétel, az üzenetek továbbítása a storage `level="storage"`.

Ez a szkript másolja és illessze be a Cloud Shell ablakába, és futtathatja.

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

A következő lépés, hogy a tárfiók útválasztási végpont létrehozásához. Emellett adja meg a tároló, amelyben az eredményeket fogja tárolni. A tárolót a storage-fiók létrehozásakor korábban lett létrehozva.

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

Ezután hozza létre az útvonal a storage-végponthoz. Az üzenet útvonalának jelöl ki, hova küldhetők az üzeneteket, amelyek megfelelnek a lekérdezés specifikációjában. 

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

### <a name="route-to-a-service-bus-queue"></a>Irányíthatja a Service Bus-üzenetsorba

Most állítsa be az útválasztást a Service Bus-üzenetsorhoz. A Service Bus-üzenetsor kapcsolati karakterláncára lekéréséhez létre kell hoznia egy engedélyezési szabályt, amely rendelkezik definiált e megfelelő jogosultságokkal. A következő szkriptet a Service Bus-üzenetsorba nevű engedélyezési szabályt hoz létre `sbauthrule`, és a jogok `Listen Manage Send`. Ez az engedélyezési szabály meghatározása után a várólista a kapcsolati karakterlánc lekéréséhez használhatja.

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

Mostantól használhatja az engedélyezési szabályt le a kapcsolati karakterláncot, a Service Bus-üzenetsorba.

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

Most állítsa be az útválasztási végpont és az üzenetek útvonalának for a Service Bus-üzenetsorba. Ezek a változók vannak beállítva:

**Végpontneve**: A mező kitöltése azonosítása a végpont nevét. 

**endpointType**: Ebben a mezőben az a végpont típusa. Ezt az értéket kell beállítani `azurestoragecontainer`, `eventhub`, `servicebusqueue`, vagy `servicebustopic`. A okokból Itt beállíthatja azt a `servicebusqueue`.

**routeName**: Ebben a mezőben az a név, az útvonal állítja be. 

**A feltétel**: Ez a mező ennek a végpontnak küldött üzenetek szűréséhez használt lekérdezés. A lekérdezési feltétel esetében az üzeneteket, hogy a Service Bus-üzenetsorba `level="critical"`.

Íme, az Azure CLI az útválasztási végpont és az üzenetek útvonalának for a Service Bus-üzenetsorba.

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

### <a name="view-message-routing-in-the-portal"></a>Zobrazit zprávu útválasztás a portálon

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik az erőforrások beállítása és az üzenetútvonalak konfigurálva, folytassa a következő oktatóanyag üzenetek küldése az IoT hub és azok a másik helyre lesznek irányítva. 

> [!div class="nextstepaction"]
> [2. rész – az üzenet-útválasztási eredmények megtekintése](tutorial-routing-view-message-routing-results.md)
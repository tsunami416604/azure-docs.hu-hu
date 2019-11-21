---
title: Set up Azure IoT Hub Device Provisioning Service using Azure CLI
description: Quickstart - Set up the Azure IoT Hub Device Provisioning Service using Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 6406929c3abc3612da2c27edc45e10fd84883d73
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228545"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Quickstart: Set up the IoT Hub Device Provisioning Service with Azure CLI

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. This quickstart details using the Azure CLI to create an IoT hub and an IoT Hub Device Provisioning Service, and to link the two services together. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!IMPORTANT]
> Both the IoT hub and the provisioning service you create in this quickstart will be publicly discoverable as DNS endpoints. Ha úgy dönt, hogy megváltoztatja ezen erőforrások nevét, ügyeljen arra, hogy ne adjon meg bizalmas adatokat.
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *my-sample-resource-group* nevű erőforráscsoportot a *westus* helyen.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Ebben a példában az erőforráscsoport az USA nyugati régiójában jön létre. Az `az account list-locations -o table` parancs futtatásával megtekintheti az elérhető helyek listáját.
>
>

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Egy IoT Hubot az [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) paranccsal hozhat létre.

A következő példában létrehozunk egy *my-sample-hub* nevű IoT Hubot a *westus* helyen. An IoT hub name must be globally unique in Azure, so you may want to add a unique prefix or suffix to the example name, or choose a new name altogether. Make sure your name follows proper naming conventions for an IoT hub: it should be 3-50 characters in length, and can contain only upper or lower case alphanumeric characters or hyphens ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Create a Device Provisioning Service

Create a Device Provisioning Service with the [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) command. 

The following example creates a provisioning service named *my-sample-dps* in the *westus* location. You will also need to choose a globally unique name for your own provisioning service. Make sure it follows proper naming conventions for an IoT Hub Device Provisioning Service: it should be 3-64 characters in length and can contain only upper or lower case alphanumeric characters or hyphens ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Ebben a példában az eszközkiépítési szolgáltatás az USA nyugati régiójában jön létre. Az elérhető helyek listáját az `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` parancs futtatásával vagy az [Azure állapotlapjának](https://azure.microsoft.com/status/) megnyitásával, majd a „Device Provisioning Service” kifejezésre való kereséssel tekintheti meg. In commands, locations can be specified either in one word or multi-word format; for example: westus, West US, WEST US, etc. The value is not case sensitive. Ha többszavas formátumot használ a hely megadásához, tegye idézőjelek közé az értéket, például: `-- location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Az IoT Hub kapcsolati sztringjének lekérése

Szükség van az IoT Hub kapcsolati sztringjére a Device Provisioning Service-szel való összekapcsoláshoz. Az [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) használatával kérje le a kapcsolati sztringet, majd használja a kimenetét egy olyan változó megadásához, amelyet a két erőforrás összekötésére fog használni. 

The following example sets the *hubConnectionString* variable to the value of the connection string for the primary key of the hub's *iothubowner* policy (the `--policy-name` parameter can be used to specify a different policy). Trade out *my-sample-hub* for the unique IoT hub name you chose earlier. A parancs az Azure CLI [lekérdezés](/cli/azure/query-azure-cli) és [kimenet](/cli/azure/format-output-azure-cli#tsv-output-format) lehetőségeinek használatával nyeri ki a kapcsolati sztringet a parancskimenetből.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

A `echo` paranccsal megtekintheti a kapcsolati sztringet:

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Ez a két parancs a Bash alatt futó gazdagépek esetében érvényesek. Ha helyi Windows/CMD rendszerhéjat vagy PowerShell-gazdagépet használ, módosítania kell a parancsokat az adott környezetnek megfelelő szintaxis használatára.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Az IoT Hub és az eszközkiépítési szolgáltatás csatolása

Az IoT Hub és az eszközkiépítési szolgáltatás csatolása az [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create) paranccsal. 

The following example links an IoT hub named *my-sample-hub* in the *westus* location and a Device Provisioning Service named *my-sample-dps*. Trade out these names for the unique IoT hub and Device Provisioning Service names you chose earlier. The command uses the connection string for your IoT hub that was stored in the *hubConnectionString* variable in the previous step.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

The command may take a few minutes to complete.

## <a name="verify-the-provisioning-service"></a>Eszközkiépítési szolgáltatás ellenőrzése

Eszközkiépítési szolgáltatás adatainak lekérése az [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show) paranccsal.

Az alábbi példa lekéri a *my-sample-dps* nevű eszközkiépítési szolgáltatás adatait. Trade out this name for your own Device Provisioning Service name.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
A csatolt IoT Hub a *properties.iotHubs* gyűjteményben jelenik meg.

![Verify Provisioning Service](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, a következő parancsok használatával törölheti az eszközkiépítési szolgáltatást, az IoT Hubot, vagy az erőforráscsoportot az ahhoz tartozó összes erőforrással együtt. Replace the names of the resources written below with the names of your own resources.

Az eszközkiépítési szolgáltatás törléséhez futtassa az [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete) parancsot:

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Az IoT Hub törléséhez futtassa az [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete) parancsot:

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Erőforráscsoport és az ahhoz tartozó összes erőforrás törléséhez futtassa az [az group delete](/cli/azure/group#az-group-delete) parancsot:

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Következő lépések

In this quickstart, you’ve deployed an IoT hub and a Device Provisioning Service instance, and linked the two resources. To learn how to use this setup to provision a simulated device, continue to the quickstart for creating a simulated device.

> [!div class="nextstepaction"]
> [Quickstart to create a simulated device](./quick-create-simulated-device.md)

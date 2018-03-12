---
title: "A Device Provisioning Service üzembe helyezése Azure CLI használatával | Microsoft Docs"
description: "Azure rövid útmutató – Az Azure IoT Hub Device Provisioning Service üzembe helyezése az Azure CLI használatával"
services: iot-dps
keywords: 
author: JimacoMS2
ms.author: v-jamebr
ms.date: 02/26/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 09b212a5abe2ebb9c123f3adcbdfa59390d16c10
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Az IoT Hub Device Provisioning szolgáltatás beállítása az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató egy IoT Hub és IoT Hub Device Provisioning szolgáltatás Azure CLI használatával végzett létrehozását, valamint a két szolgáltatás csatolását ismerteti. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!IMPORTANT]
> Az ezzel a rövid útmutatóval létrehozott IoT Hub és az eszközkiépítési szolgáltatás is DNS-végpontként nyilvánosan észlelhető lesz. Ha úgy dönt, hogy megváltoztatja ezen erőforrások nevét, ügyeljen arra, hogy ne adjon meg bizalmas adatokat.
>


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *my-sample-resource-group* nevű erőforráscsoportot a *westus* helyen.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Ebben a példában az erőforráscsoport az USA nyugati régiójában jön létre. Az `az account list-locations -o table` parancs futtatásával megtekintheti az elérhető helyek listáját.
>
>

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

IoT Hub létrehozása az [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) parancs segítségével. 

A következő példában létrehozunk egy *my-sample-hub* nevű IoT Hubot a *westus* helyen.  

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-provisioning-service"></a>Eszközkiépítési szolgáltatás létrehozása

Eszközkiépítési szolgáltatás létrehozása az [az iot dps create](/cli/azure/iot/dps#az_iot_dps_create) paranccsal. 

A következő példában létrehozunk egy *my-sample-dps* nevű eszközkiépítési szolgáltatást a *westus* helyen.  

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Ebben a példában az eszközkiépítési szolgáltatás az USA nyugati régiójában jön létre. Az elérhető helyek listáját az `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` parancs futtatásával vagy az [Azure állapotlapjának](https://azure.microsoft.com/status/) megnyitásával, majd a „Device Provisioning Service” kifejezésre való kereséssel tekintheti meg. A parancsokban egyszavas vagy többszavas formátumban adhatók meg a helyek, például: westus, USA nyugati régiója, USA NYUGATI RÉGIÓJA stb. Az érték nem különbözteti meg a kis- és nagybetűket. Ha többszavas formátumot használ a hely megadásához, tegye idézőjelek közé az értéket, például: `-- location "West US"`.
>


## <a name="get-the-connection-string-for-the-iot-hub"></a>Az IoT Hub kapcsolati karakterláncának lekérése

Az IoT Hub kapcsolati karakterláncára az eszközkiépítési szolgáltatással való összekapcsoláshoz van szükség. Az [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string) használatával kérje le a kapcsolati karakterláncot, majd használja a kimenetét egy olyan változó megadásához, amelyet a két erőforrás összekötésére fog használni. 

Az alábbi példa a *hubConnectionString* változót a hub *iothubowner* szabályzatának elsődleges kulcsához tartozó kapcsolati karakterlánc értékére állítja. Megadhat egy másik szabályzatot is a `--policy-name` paraméterrel. A parancs az Azure CLI [lekérdezés](/cli/azure/query-azure-cli) és [kimenet](/cli/azure/format-output-azure-cli#tsv-output-format) lehetőségeinek használatával nyeri ki a kapcsolati karakterláncot a parancskimenetből.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

A `echo` paranccsal megtekintheti a kapcsolati karakterláncot:

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Ez a két parancs a Bash alatt futó gazdagépek esetében érvényesek. Ha helyi Windows/CMD rendszerhéjat vagy PowerShell-gazdagépet használ, módosítania kell a parancsokat az adott környezetnek megfelelő szintaxis használatára.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Az IoT Hub és az eszközkiépítési szolgáltatás csatolása

Az IoT Hub és az eszközkiépítési szolgáltatás csatolása az [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create) paranccsal. 

A következő példában egy, a *westus* helyen található, *my-sample-hub* nevű IoT Hubot csatolunk egy *my-sample-dps* nevű eszközkiépítési szolgáltatáshoz. A *my-sample-hub* kapcsolati karakterláncát használja, amelyet az előző lépésben szereplő *hubConnectionString* változó tárol.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

## <a name="verify-the-provisioning-service"></a>Eszközkiépítési szolgáltatás ellenőrzése

Eszközkiépítési szolgáltatás adatainak lekérése az [az iot dps show](/cli/azure/iot/dps#az_iot_dps_show) paranccsal.

Az alábbi példa lekéri a *my-sample-dps* nevű eszközkiépítési szolgáltatás adatait. A csatolt IoT Hub a *properties.iotHubs* gyűjteményben jelenik meg.

```azurecli-interactive
az iot dps show --name my-sample-dps
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, a következő parancsok használatával törölheti az eszközkiépítési szolgáltatást, az IoT Hubot, vagy az erőforráscsoportot az ahhoz tartozó összes erőforrással együtt.

Az eszközkiépítési szolgáltatás törléséhez futtassa az [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete) parancsot:

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Az IoT Hub törléséhez futtassa az [az iot hub delete](/cli/azure/iot/hub#az_iot_hub_delete) parancsot:

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Erőforráscsoport és az ahhoz tartozó összes erőforrás törléséhez futtassa az [az group delete](/cli/azure/group#az_group_delete) parancsot:

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban IoT Hubot és eszközkiépítési szolgáltatás példányt helyezett üzembe, és csatolta a két erőforrást. Ha szimulált eszköz kiépítéséhez szeretné használni ezt a beállítást, folytassa a szimulált eszköz létrehozásának rövid útmutatójával.

> [!div class="nextstepaction"]
> [Szimulált eszköz létrehozásának rövid útmutatója](./quick-create-simulated-device.md)


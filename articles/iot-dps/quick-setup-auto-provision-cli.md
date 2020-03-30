---
title: Az Azure IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure CLI használatával
description: Rövid útmutató – Az Azure IoT Hub-eszközkiépítési szolgáltatás (DPS) beállítása az Azure CLI használatával
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: ea1cae1f5a30d4cd76df39fec43f3818178fc213
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77484196"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Rövid útmutató: Az IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure CLI-vel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató az Azure CLI használatával hozzon létre egy IoT hub ot és egy IoT Hub-eszközkiépítési szolgáltatást, és kapcsolja össze a két szolgáltatást. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

> [!IMPORTANT]
> Az IoT hub és a kiépítési szolgáltatás létrehozása ebben a rövid útmutatóban nyilvánosan felderíthető DNS-végpontokként lesz nyilvánosan felderíthető. Ha úgy dönt, hogy megváltoztatja ezen erőforrások nevét, ügyeljen arra, hogy ne adjon meg bizalmas adatokat.
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

A következő példában létrehozunk egy *my-sample-hub* nevű IoT Hubot a *westus* helyen. Az IoT-központ nevének globálisan egyedinek kell lennie az Azure-ban, ezért érdemes lehet egy egyedi előtagot vagy utótagot hozzáadni a példanévhez, vagy teljesen új nevet választani. Győződjön meg arról, hogy a neve az IoT-központ megfelelő elnevezési konvencióit követi: 3–50 karakter hosszúnak kell lennie, és csak kis- vagy nagybetűket tartalmazhat alfanumerikus karaktereket vagy kötőjeleket ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Eszközkiépítési szolgáltatás létrehozása

Hozzon létre egy eszközkiépítési szolgáltatást az [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) paranccsal. 

A következő példa létrehoz egy kiépítési szolgáltatás neve *my-sample-dps* a *Westus* helyen. Emellett globálisan egyedi nevet kell választania a saját létesítési szolgáltatásához. Győződjön meg arról, hogy az IoT Hub-eszközlétesítési szolgáltatás megfelelő elnevezési konvencióit követi: 3–64 karakter hosszúnak kell lennie, és csak kis- vagy nagybetűket tartalmazhat alfanumerikus karaktereket vagy kötőjeleket ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Ebben a példában az eszközkiépítési szolgáltatás az USA nyugati régiójában jön létre. Az elérhető helyek listáját az `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` parancs futtatásával vagy az [Azure állapotlapjának](https://azure.microsoft.com/status/) megnyitásával, majd a „Device Provisioning Service” kifejezésre való kereséssel tekintheti meg. A parancsokban a helyek egy vagy többszavas formátumban is megadhatók; például: westus, WEST USA, WEST USA stb. Az érték nem a kis- és nagybetűket. Ha többszavas formátumot használ a hely megadásához, tegye idézőjelek közé az értéket, például: `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Az IoT Hub kapcsolati sztringjének lekérése

Szükség van az IoT Hub kapcsolati sztringjére a Device Provisioning Service-szel való összekapcsoláshoz. Az [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) használatával kérje le a kapcsolati sztringet, majd használja a kimenetét egy olyan változó megadásához, amelyet a két erőforrás összekötésére fog használni. 

A következő példa a *hubConnectionString* változót a hub *iothubowner* házirendjének elsődleges kulcsához `--policy-name` használt kapcsolati karakterlánc értékére állítja be (a paraméter egy másik házirend megadására használható). Cserélje ki *a minta-hubomat* a korábban kiválasztott egyedi IoT hubnévért. A parancs az Azure CLI [lekérdezés](/cli/azure/query-azure-cli) és [kimenet](/cli/azure/format-output-azure-cli#tsv-output-format) lehetőségeinek használatával nyeri ki a kapcsolati sztringet a parancskimenetből.

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

A következő példa egy *my-sample-hub* nevű IoT-hubot kapcsol össze a *Westus-helyen,* és egy eszközkiépítési *szolgáltatást, amelyet my-sample-dps*néven neveznek el. Ezeket a neveket az egyedi IoT hub és az eszközkiépítési szolgáltatás korábban kiválasztott neveket. A parancs az előző lépésben a *hubConnectionString* változóban tárolt IoT hub kapcsolati karakterláncát használja.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

A parancs kifejeződése eltarthat néhány percig.

## <a name="verify-the-provisioning-service"></a>Eszközkiépítési szolgáltatás ellenőrzése

Eszközkiépítési szolgáltatás adatainak lekérése az [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show) paranccsal.

Az alábbi példa lekéri a *my-sample-dps* nevű eszközkiépítési szolgáltatás adatait. Ezt a nevet a saját Eszközkiépítési szolgáltatás nevére cserélheti ki.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
A csatolt IoT Hub a *properties.iotHubs* gyűjteményben jelenik meg.

![Kiépítési szolgáltatás ellenőrzése](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, a következő parancsok használatával törölheti az eszközkiépítési szolgáltatást, az IoT Hubot, vagy az erőforráscsoportot az ahhoz tartozó összes erőforrással együtt. Cserélje le az alábbi erőforrások nevét a saját erőforrások nevére.

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

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy IoT-központot és egy eszközkiépítési szolgáltatáspéldányt, és összekapcsolta a két erőforrást. Ha meg szeretné tudni, hogyan használhatja ezt a beállítást egy szimulált eszköz kiépítési, folytassa a rövid útmutatót a szimulált eszköz létrehozásához.

> [!div class="nextstepaction"]
> [Gyorsútmutató szimulált eszköz létrehozásához](./quick-create-simulated-device.md)

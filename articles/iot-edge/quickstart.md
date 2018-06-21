---
title: Az Azure IoT Edge + Windows rövid útmutatója | Microsoft Docs
description: Az Azure IoT Edge kipróbálása elemzés futtatásával egy szimulált Edge-eszközön
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/03/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 2fd16ab4ade61b1a08f93294051f4246e47839b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631734"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Rövid útmutató: Az első IoT Edge-modul üzembe helyezése az Azure Portal segítségével egy Windows-eszközön – előzetes verzió

Ebben a rövid útmutatóban előre összeállított kódokat fog távolról üzembe helyezni egy IoT Edge-eszközön az Azure IoT Edge felhőalapú interfészével. A feladat teljesítése érdekében először a Windows-eszközzel szimulálni fogja az IoT Edge-eszközt, amelyen aztán üzembe fog helyezni egy modult.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot][lnk-account].

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy Windows rendszerű számítógépet vagy virtuális gépet használ az eszközök internetes hálózatához csatlakozó eszköz szimulálására. Ha a Windows virtuális gépen fut, engedélyezze a [beágyazott virtualizálást][lnk-nested], és foglaljon le legalább 2 GB memóriát. 

1. Győződjön meg arról, hogy a Windows támogatott verzióját használja:
   * Windows 10 
   * Windows Server
2. Telepítse a [Windowshoz készült Dockert][lnk-docker], és ellenőrizze, hogy fut-e.
3. Telepítse a [Pythont a Windowsra][lnk-python], és ellenőrizze, hogy tudja-e használni a pip parancsot. A rövid útmutató tesztelése a Python >=2.7.9-es és >=3.5.4-es verzióival történt.  
4. Az IoT Edge-vezérlőszkript letöltéséhez futtassa a következő parancsot.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Az Azure IoT Edge Windows- és Linux-tárolókat is tud futtatni. Windows-tárolók használatához a következő rendszerek valamelyikét kell futtatnia:
>    * Windows 10 Fall Creators Update vagy
>    * Windows Server 1709 (16299-es build) vagy
>    * Windows IoT Core (16299-es build) egy x64-alapú eszközön
>
> Windows IoT Core esetén kövesse az [IoT Edge-futtatókörnyezet Windows IoT Core-on történő telepítésével][lnk-install-iotcore] foglalkozó témakörben leírt utasításokat. A többi esetben egyszerűen [konfigurálja a Dockert Windows-tárolók használatához][lnk-docker-containers], és ha szükséges, ellenőrizze az előfeltételeket a következő PowerShell-paranccsal:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

## <a name="create-an-iot-hub-with-azure-cli"></a>IoT-központ létrehozása az Azure CLI-vel

Hozzon létre egy IoT-központot Azure-előfizetésében. Ehhez a rövid útmutatóhoz az IoT Hub ingyenes csomagja is elegendő. Ha korábban már használta az IoT Hubot, és már létrehozott egy ingyenes központot, kihagyhatja ezt a szakaszt, és továbbléphet az [IoT Edge-eszköz regisztrálása][anchor-register] részhez. Mindegyik előfizetés csak egy ingyenes IoT-központtal rendelkezhet. 

1. Jelentkezzen be az [Azure Portalra][lnk-portal]. 
1. Válassza ki a **Cloud Shell** gombot. 

   ![Cloud Shell gomb][1]

1. Hozzon létre egy erőforráscsoportot. A következő kód egy **IoTEdge** nevű erőforráscsoportot hoz létre az **USA nyugati régiójában**:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Hozzon létre egy IoT-központot az új erőforráscsoportban. A következő kód egy **MyIotHub** nevű ingyenes **F1** központot hoz létre az **IoTEdge** erőforráscsoportban:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet konfigurálása

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Két modulból áll. Az első, az IoT Edge-ügynök, modulok üzembe helyezését és monitorozását teszi lehetővé az IoT Edge-eszközön. A második, az IoT Edge-központ, az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli. 

Konfigurálja a futtatókörnyezetet IoT Edge-eszköz előző szakaszban megadott kapcsolati sztringjével.

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

Indítsa el a futtatókörnyezetet.

```cmd
iotedgectl start
```

Ellenőrizze a Dockerben, hogy az IoT Edge-ügynök modulként fut-e.

```cmd
docker ps
```

![Lásd a Docker edgeAgent paraméterét](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Modul üzembe helyezése

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezután az Azure Portal segítségével úgy futtatta az IoT Edge-modult az eszközön, hogy magát az eszközt nem kellett módosítania. Ebben az esetben az Ön által továbbított modul az oktatóanyagokhoz használható környezeti adatokat hoz létre. 

Nyissa meg újra a parancssort a szimulált eszközt futtató számítógépen. Győződjön meg arról, hogy a felhőből üzembe helyezett modul fut az IoT Edge-eszközön. 

```cmd
docker ps
```

![Három modul megtekintése az eszközön](./media/tutorial-simulate-device-windows/docker-ps2.png)

Tekintse meg a tempSensor modul által a felhőbe küldött üzeneteket. 

```cmd
docker logs -f tempSensor
```

![A modulból származó adatok megtekintése](./media/tutorial-simulate-device-windows/docker-logs.png)

Az eszköz által küldött telemetriát is megtekintheti az [IoT Hub Explorer eszközzel][lnk-iothub-explorer]. 
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a létrehozott szimulált eszközt a modulonként elindított Docker-tárolókkal együtt, használja a következő parancsot: 

```cmd
iotedgectl uninstall
```

Ha már nincs szüksége a létrehozott IoT-központra, az [az iot hub delete][lnk-delete] paranccsal eltávolíthatja az erőforrást és a vele társított eszközöket:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>További lépések

Ismertettük, hogyan helyezhet üzembe egy IoT Edge-modult egy IoT Edge-eszközön. Következő lépésként megpróbálkozhat különböző típusú Azure-szolgáltatások modulként történő üzembe helyezésével, hogy elemezhesse az adatokat a peremhálózaton. 

* [Az Azure Function üzembe helyezése modulként](tutorial-deploy-function.md)
* [Az Azure Stream Analytics üzembe helyezése modulként](tutorial-deploy-stream-analytics.md)
* [Saját kód üzembe helyezése modulként](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device

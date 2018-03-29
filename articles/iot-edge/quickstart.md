---
title: Gyors üzembe helyezés Azure IoT peremhálózati + Windows |} Microsoft Docs
description: Próbálja ki Azure IoT peremhálózati analytics egy szimulált peremhálózati eszköz futtatásával
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: f9ad01d3194ee0f8be4c3b4321c83c4bb15ea55c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Gyors üzembe helyezés: Az első IoT peremhálózati modul az Azure-portálon a Windows rendszerű eszközre központi telepítése – előzetes

A gyors üzembe helyezés, az előre elkészített kód távolról telepíteni az IoT-peremhálózati eszköz Azure IoT peremhálózati felhő felületet használ. Ennek a feladatnak először használja a Windows-eszköz szimulálása IoT peremhálózati eszköz, akkor a modul telepítene.

Ha nincs egy aktív Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot] [ lnk-account] megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy használ egy számítógépet vagy a Windows rendszerű virtuális gép az eszközök internetes hálózatát eszközök szimulálására. Ha a Windows virtuális gépként fut, engedélyezni [beágyazott virtualizálási] [ lnk-nested] és legalább 2 GB memóriát lefoglalni. 

1. Ellenőrizze, hogy egy támogatott Windows-verzió használata:
   * Windows 10 
   * Windows Server
2. Telepítés [Docker for Windows] [ lnk-docker] , és győződjön meg arról, hogy fut-e.
3. Telepítés [Python 2.7-es Windows] [ lnk-python] , és győződjön meg arról, hogy a pip paranccsal.
4. A következő parancsot a IoT peremhálózati vezérlő parancsprogram letöltése.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT peremhálózati futtathat Windows tárolók vagy a Linux-tárolók. Windows-tárolók használatára, akkor kell futtatnia:
>    * Windows 10 alá esik Creators frissítéséhez vagy
>    * Windows Server 1709 (Build 16299), vagy
>    * Windows IoT mag (Build 16299) x64-alapú eszköz
>
> A Windows az IoT-Core, kövesse az utasításokat a [az IoT-Edge futásidejű telepíthető Windows IoT Core][lnk-install-iotcore]. Ellenkező esetben egyszerűen [konfigurálása a Windows-tárolók használatára Docker][lnk-docker-containers], és opcionálisan ellenőrzi az előfeltételeket a következő powershell-paranccsal:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

## <a name="create-an-iot-hub-with-azure-cli"></a>IoT hub létrehozása az Azure parancssori felülettel

IoT hub létrehozása az Azure-előfizetésben. Az IoT-központ szabad szintjét a gyors üzembe helyezés esetén használható. Ha korábban már használt IoT-központot, és már rendelkezik egy szabad hub létrehozása, hagyja ki ezt a szakaszt, és lépjen a [IoT peremhálózati eszköz regisztrálása][anchor-register]. Minden előfizetés csak egy ingyenes IoT-központ van. 

1. Jelentkezzen be az [Azure Portalra][lnk-portal]. 
1. Válassza ki a **felhő rendszerhéj** gombra. 

   ![Felhő rendszerhéj gomb][1]

1. Hozzon létre egy erőforráscsoportot. Az alábbi kód létrehoz nevű erőforráscsoport **IoTEdge** a a **USA nyugati régiója** régió:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Az IoT-központ az új erőforráscsoport létrehozása. Az alábbi kód létrehoz egy szabad **F1** nevű hub **MyIotHub** erőforráscsoportban **IoTEdge**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Az IoT-peremhálózati eszköz regisztrálása

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Az IoT-Edge futásidejű konfigurálása

Az IoT-Edge futásidejű minden IoT peremhálózati eszközön van telepítve. Ez magában foglalja a két modulok. Először az IoT-Edge ügynök elősegíti a központi telepítési és figyelési modulokat az IoT-peremhálózati eszközön. Második a peremhálózati IoT hub kezeli a kommunikációt az IoT-peremhálózati eszközön modulokat, valamint az eszköz és az IoT-központ között. 

A futtatókörnyezet konfigurálása az IoT-peremhálózati eszköz kapcsolati karakterlánccal előző szakaszából.

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

Indítsa el a futtatókörnyezetben.

```cmd
iotedgectl start
```

Ellenőrizze, hogy az IoT-Edge-ügynök fut-e modulként Docker.

```cmd
docker ps
```

![A Docker edgeAgent lásd:](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>A modulok telepítése

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Adatok generált megtekintése

A gyors üzembe helyezés létrehozott egy új IoT peremhálózati eszköz, és telepítve van-e az IoT-Edge futásidejű. Az Azure-portálon, majd leküldéses egy IoT peremhálózati modul futtatható az eszközön anélkül, hogy a módosításokat az magához az eszközhöz használt. A modult, amely akkor leküldött ebben az esetben is használhatja az oktatóanyagok a környezeti adatokat hoz létre. 

Nyissa meg a parancssort a szimulált eszköz újra futtatni a számítógépen. Győződjön meg arról, hogy fut-e a modul telepítve a felhőben az IoT-peremhálózati eszközön. 

```cmd
docker ps
```

![Három modulok megtekintése az eszközön](./media/tutorial-simulate-device-windows/docker-ps2.png)

A tempSensor modulból a felhőbe küldött üzenetek megjelenítése. 

```cmd
docker logs -f tempSensor
```

![A modul az adatok megtekintése](./media/tutorial-simulate-device-windows/docker-logs.png)

A telemetriai adatokat küld az eszköz segítségével is megtekintheti a [IoT-központ explorer eszköz][lnk-iothub-explorer]. 
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a szimulált eszköz létrehozott, és minden modulhoz indított Docker tárolók használja a következő parancsot: 

```cmd
iotedgectl uninstall
```

Ha már nincs szüksége az IoT Hub létrehozott, használhatja a [az iot hub delete] [ lnk-delete] parancs beírásával távolítsa el az erőforrás és a vele társított eszközök:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>További lépések

Megtudta, hogyan IoT peremhálózati eszköz egy IoT-Edge-modul telepítéséhez. Próbálja meg különböző modulok, az Azure szolgáltatások üzembe helyezése, hogy elemezheti az adatokat a peremhálózaton. 

* [Azure-függvény modul telepítése](tutorial-deploy-function.md)
* [Az Azure Stream Analytics üzembe helyezése modulként](tutorial-deploy-stream-analytics.md)
* [Telepítse a saját kódját modulként](tutorial-csharp-module.md)


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

---
title: "Gyors üzembe helyezés Azure IoT peremhálózati + Linux |} Microsoft Docs"
description: "Próbálja ki Azure IoT peremhálózati analytics egy szimulált peremhálózati eszköz futtatásával"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb93efcf00cb7b165c497d7ef38685f80bce84c0
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-linux-device---preview"></a>Gyors üzembe helyezés: Az első IoT peremhálózati modul az Azure-portálon Linux eszközre központi telepítése – előzetes

Azure IoT peremhálózati felhő power áthelyezi az eszközök internetes hálózatát eszközét. Ebben a témakörben útmutató előre elkészített kód távolról telepítése IoT peremhálózati eszköz a felhő felhasználói felület használata.

Ha nincs egy aktív Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot] [ lnk-account] megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ennek a feladatnak használja a számítógép vagy virtuális gép egy az eszközök internetes hálózatát eszköz szimulálásához. A következő szolgáltatások sikeres telepítéséhez az IoT-peremhálózati eszköz van szükség:

- [Telepítse a Docker Linux] [ lnk-docker-ubuntu] , és győződjön meg arról, hogy fut-e. 
- A legtöbb Linux terjesztésekről, beleértve az Ubuntu, már telepített Python 2.7. A következő parancs használatával győződjön meg arról, hogy telepítve van-e a pip: `sudo apt-get install python-pip`.

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

Hozzon létre egy eszközidentitás a szimulált eszköz, így képes kommunikálni az IoT hub. Mivel IoT peremeszközök viselkednek, és működnek, mint a tipikus IoT-eszközök felügyelt, ez lesz az elejétől kezdve IoT peremhálózati eszköz deklarálhatja. 

1. Az Azure-portálon lépjen az IoT hub.
1. Válassza ki **IoT peremhálózati (előzetes verzió)**.
1. Válassza ki **hozzáadása IoT peremhálózati eszköz**.
1. A szimulált eszköz adjon egy egyedi eszköz.
1. Válassza ki **mentése** az eszköz hozzáadásához.
1. Válassza ki az új eszköz az eszközök a listából. 
1. Másolja a következő **kapcsolati karakterlánc – elsődleges kulcs** és mentse azt. Ezt az értéket az IoT-Edge futásidejű konfigurálása a következő szakaszban ismertetjük. 

## <a name="install-and-start-the-iot-edge-runtime"></a>Telepítse és indítsa el az IoT-Edge futásidejű

Az IoT-Edge futásidejű minden IoT peremhálózati eszközön van telepítve. Ez magában foglalja a két modulok. Először az IoT-Edge ügynök elősegíti a központi telepítési és figyelési modulokat az IoT-peremhálózati eszközön. Második a peremhálózati IoT hub kezeli a kommunikációt az IoT-peremhálózati eszközön modulokat, valamint az eszköz és az IoT-központ között. 

A számítógépen, ahol az IoT-peremhálózati eszköz fogja futtatni töltse le az IoT-Edge vezérlő parancsfájl:
```python
sudo pip install -U azure-iot-edge-runtime-ctl
```

A futtatókörnyezet származó az előző szakaszban az IoT peremhálózati eszköz kapcsolati karakterlánc konfigurálása:
```python
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Indítsa el a futtatókörnyezet:
```python
sudo iotedgectl start
```

Ellenőrizze, hogy az IoT-Edge-ügynök fut-e modulként Docker:
```python
sudo docker ps
```

## <a name="deploy-a-module"></a>A modulok telepítése

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Adatok generált megtekintése

A gyors üzembe helyezés létrehozott egy új IoT peremhálózati eszköz, és telepítve van-e az IoT-Edge futásidejű. Az Azure-portálon, majd leküldéses egy IoT peremhálózati modul futtatható az eszközön anélkül, hogy a módosításokat az magához az eszközhöz használt. A modult, amely akkor leküldött ebben az esetben is használhatja az oktatóanyagok a környezeti adatokat hoz létre. 

A tempSensor modulból küldött üzenetek megjelenítése:

```cmd/sh
sudo docker logs -f tempSensor
```

A telemetriai adatokat küld az eszköz segítségével is megtekintheti a [IoT-központ explorer eszköz][lnk-iothub-explorer]. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az IoT Hub létrehozott, használhatja a [az iot hub delete] [ lnk-delete] parancs beírásával távolítsa el az erőforrás és a vele társított eszközök:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Következő lépések

Megtudta, hogyan IoT peremhálózati eszköz egy IoT-Edge-modul telepítéséhez. Próbálja meg különböző modulok, az Azure szolgáltatások üzembe helyezése, hogy elemezheti az adatokat a peremhálózaton. 

* [Telepítse a saját kódját modulként](tutorial-csharp-module.md)
* [Azure-függvény modul telepítése](tutorial-deploy-function.md)
* [Azure Stream Analytics egy modul telepítése](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device

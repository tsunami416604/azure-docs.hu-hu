---
title: "Azure IoT Edge Windows szimulálása |} Microsoft Docs"
description: "A szimulált eszköz a Windows Azure IoT peremhálózati futásidejű telepítse, és az első modul központi telepítése"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 08c501b9132bb21f47f099725d1fad5556befb4c
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>A szimulált eszköz a Windows Azure IoT peremhálózati telepített – előzetes

Azure IoT peremhálózati felhő power áthelyezi az eszközök internetes hálózatát (IoT) eszközét. Ez az oktatóanyag bemutatja, hogyan létrehozása a szimulált IoT peremhálózati eszköz, amely az érzékelő adatokat állít elő. Az alábbiak végrehajtásának módját ismerheti meg:

![Útmutató architektúrája][2]

A szimulált eszköz, amely ebben az oktatóanyagban létrehozhat egy figyelőt a hőmérséklet, páratartalom és nyomás adatokat generáló határi turbina. Továbbra is érdekli ezeket az adatokat, mert a turbinák hatékonyságát, attól függően, hogy a időjárási feltételek különböző szintjein végrehajtani. Az egyéb Azure IoT peremhálózati oktatóanyagok végzett munkát itt elemezhetők az adatok az üzleti elemzések modulok üzembe helyezésével épül. 

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy használ egy számítógépet vagy a Windows rendszerű virtuális gép az eszközök internetes hálózatát eszközök szimulálására. 

>[!TIP]
>Ha a Windows virtuális gépként fut, engedélyezni [beágyazott virtualizálási] [ lnk-nested] és legalább 2 GB memóriát lefoglalni. 

1. Ellenőrizze, hogy egy támogatott Windows-verzió használata:
   * Windows 10 
   * Windows Server
2. Telepítés [Docker for Windows] [ lnk-docker] , és győződjön meg arról, hogy fut-e.
3. Telepítés [Python 2.7-es Windows] [ lnk-python] , és győződjön meg arról, hogy a pip paranccsal.
4. A következő parancsot a IoT peremhálózati vezérlő parancsprogram letöltése.

   ```
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT peremhálózati futtathat Windows tárolók vagy a Linux-tárolók. Windows-tárolók használatára, akkor kell futtatnia:
>    * Windows 10 alá esik Creators frissítéséhez vagy
>    * Windows Server 1709 (Build 16299), vagy
>    * Windows IoT mag (Build 16299) x64-alapú eszköz
>
> A Windows az IoT-Core, kövesse az utasításokat a [az IoT-Edge futásidejű telepíthető Windows IoT Core][lnk-install-iotcore]. Ellenkező esetben egyszerűen [konfigurálása a Windows-tárolók használatára Docker][lnk-docker-containers], és opcionálisan ellenőrzi az előfeltételeket a következő powershell-paranccsal:
>    ```
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Indítsa el az oktatóanyag az IoT Hub létrehozása.
![Az IoT Hub létrehozása][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Az IoT-peremhálózati eszköz regisztrálása

Az IoT-peremhálózati eszköz regisztrálása az újonnan létrehozott IoT Hub.
![Eszköz regisztrálása][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Az IoT-Edge futásidejű konfigurálása

Telepítse, és indítsa el az Azure IoT peremhálózati futásidejű az eszközön. 
![Eszköz regisztrálása][5]

Az IoT-Edge futásidejű minden IoT peremhálózati eszközön van telepítve. Ez magában foglalja a két modulok. Először az IoT-Edge ügynök elősegíti a központi telepítési és figyelési modulokat az IoT-peremhálózati eszközön. Második a peremhálózati IoT hub kezeli a kommunikációt az IoT-peremhálózati eszközön modulokat, valamint az eszköz és az IoT-központ között. 


Az alábbi lépések segítségével telepítheti, és indítsa el az IoT-Edge futásidejű:

1. A futtatókörnyezet konfigurálása az IoT-peremhálózati eszköz kapcsolati karakterlánccal előző szakaszából.

   ```
   iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Indítsa el a futtatókörnyezetben.

   ```
   iotedgectl start
   ```

1. Ellenőrizze, hogy az IoT-Edge-ügynök fut-e modulként Docker.

   ```
   docker ps
   ```

## <a name="deploy-a-module"></a>A modulok telepítése

Az Azure IoT peremhálózati eszköz felügyelete a felhőből, egy modult, amely telemetriai adatokat küld az IoT-központ telepítéséhez.
![Eszköz regisztrálása][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Adatok generált megtekintése

A gyors üzembe helyezés létrehozott egy új IoT peremhálózati eszköz, és telepítve van-e az IoT-Edge futásidejű. Az Azure-portálon, majd leküldéses egy IoT peremhálózati modul futtatható az eszközön anélkül, hogy a módosításokat az magához az eszközhöz használt. A modult, amely akkor leküldött ebben az esetben is használhatja az oktatóanyagok a környezeti adatokat hoz létre. 

A tempSensor modulból küldött üzenetek megjelenítése:

```cmd/sh
sudo docker logs -f tempSensor
```

A telemetriai adatokat küld az eszköz segítségével is megtekintheti a [IoT-központ explorer eszköz][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létre egy új IoT peremhálózati eszköz, és az Azure IoT peremhálózati felhő illesztőfelületet kódot az eszköz telepítésére szolgál. Most hogy a szimulált eszköz nyers adatok a környezetre vonatkozó. 

Ez az oktatóanyag az előfeltételként szükséges összes az IoT-Edge oktatóanyagok. Megtudhatja, hogyan Azure IoT peremhálózati segítségével kapcsolja be az adatok az üzleti elemzések készítése a peremhálózaton egyéb oktatóprogramok valamelyikét be tovább.

> [!div class="nextstepaction"]
> [Fejlesztésekor és telepítésekor C#-kódban modulként](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md
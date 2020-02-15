---
title: Modulok üzembe helyezése a Visual Studio Code-Azure IoT Edge
description: A Visual Studio Code és az Azure IoT Tools használatával leküldheti az IoT Edge modult a IoT Hubról a IoT Edge eszközre, amelyet a telepítési jegyzék konfigurál.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e1b2e2a80670cf0409f8f8477563b9a209cc8706
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209205"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>A Visual Studio Code-ból az Azure IoT Edge-modulok telepítése

Miután létrehozott egy IoT Edge modulok az üzleti logikával rendelkező, érdemes őket az eszközökre telepíteni kívánt megfelelően működjenek a peremhálózaton. Ha több modulokat, amelyek együttműködve gyűjtenek és dolgoznak fel adatokat, és egyszerre telepítheti őket, és deklarálja az útválasztási szabályokat, amelyek csatlakoztathatja őket.

Ez a cikk bemutatja, hogyan hozzon létre egy JSON-manifest nasazení, majd küldje le az üzembe helyezés IoT Edge-eszköz fájl használatával. További információ a megosztott címkék alapján több eszközt célzó központi telepítések létrehozásáról: [IoT Edge modulok méretezése a Visual Studio Code használatával](how-to-deploy-monitor-vscode.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében.
* [IoT Edge-eszköz](how-to-register-device.md#register-with-visual-studio-code) , amelyen telepítve van a IoT Edge futtatókörnyezet.
* [Visual Studio Code](https://code.visualstudio.com/).
* A Visual Studio Code-hoz készült [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) .

## <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. Az üzembe helyezési jegyzékek működésével és létrehozásával kapcsolatos további információkért lásd: [IoT Edge modulok használatának, konfigurálásának és](module-composition.md)újbóli használatának ismertetése.

A Visual Studio Code modulok üzembe helyezéséhez az üzembe helyezés a jegyzék mentése helyileg, egy. JSON-fájlt. A fájl elérési útja fogja használni a következő szakaszban, amikor futtatja a parancsot a alkalmazni a konfigurációt az eszközre.

Íme egy modult az alapszintű üzemelő példányhoz jegyzék példaként:

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

Használhatja a Visual Studio Code az Azure IoT-bővítmények az IoT hub-műveletek végrehajtásához. Ezeket a műveleteket működéséhez kell jelentkezzen be az Azure-fiókjával, és válassza ki az IoT hubot, amelyen dolgozik.

1. A Visual Studio Code-ban nyissa meg a **Explorer** nézetet.

1. Az Explorer alján bontsa ki az **Azure IoT hub** szakaszt.

   ![Az Azure IoT Hub szakasz kibontása](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Kattintson a **...** elemre az **Azure IoT hub** szakasz fejlécében. Ha nem látja a három pontra, a kurzort a fejléc.

1. Válassza a **IoT hub kiválasztása**lehetőséget.

1. Ha nem jelentkezett be az Azure-fiókját, kövesse az ehhez az utasításokat.

1. Válassza ki az Azure-előfizetését.

1. Válassza ki az IoT hubnak.

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

A modul információkkal konfigurált manifest nasazení alkalmazása modulok üzembe az eszközre.

1. A Visual Studio Code Explorer nézetében bontsa ki az **Azure IoT hub** szakaszt, majd bontsa ki az **eszközök** csomópontot.

1. Kattintson a jobb gombbal arra a IoT Edge eszközre, amelyet az üzembe helyezési jegyzékkel szeretne konfigurálni.

    > [!TIP]
    > Annak ellenőrzéséhez, hogy a kiválasztott eszköz egy IoT Edge eszköz-e, válassza ki a modulok listájának kibontásához és a **$edgeHub** és a **$edgeAgent**meglétének ellenőrzéséhez. Minden IoT Edge eszköz tartalmazza ezt a két modult.

1. Válassza **a központi telepítés létrehozása egyetlen eszközhöz**lehetőséget.

1. Navigáljon a használni kívánt üzembe helyezési jegyzékfájlhoz tartozó JSON-fájlhoz, majd kattintson a **peremhálózat-telepítési jegyzék kiválasztása**elemre.

   ![Válassza ki a peremhálózati Manifest Nasazení](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Az eredmények az üzembe helyezés a VS Code-kimenetben nyomtatott. A sikeres telepítések telepített néhány percen belül, ha a cél-eszközön fut, és csatlakozik az internethez.

## <a name="view-modules-on-your-device"></a>Modulok megjelenítése az eszközön

Miután telepítette a modulokat az eszközre, megtekintheti az összeset az **Azure IoT hub** szakaszban. Válassza ki az IoT Edge-eszköz annak kibontásához melletti nyílra. A jelenleg futó modulok jelennek meg.

Ha nemrég telepített új modulokat egy eszközre, vigye az egérmutatót az **Azure IoT hub-eszközök** szakasz fejlécére, és kattintson a frissítés ikonra a nézet frissítéséhez.

Kattintson a jobb gombbal megtekintheti és szerkesztheti az ikermodul modul nevét.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [helyezhet üzembe és figyelheti IoT Edge modulokat a Visual Studio Code használatával](how-to-deploy-monitor.md)

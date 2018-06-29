---
title: Azure IoT biztonsági modulok (Visual STUDIO Code) telepítése |} Microsoft Docs
description: Visual Studio Code segítségével IoT peremhálózati eszköz modulok telepítése
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7e75f2ff5e2df3189683d084a315ad6c8730be84
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036068"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>A Visual Studio Code Azure IoT Edge-modulok telepítése

Miután létrehozott egy IoT peremhálózati modulok az üzleti logikát, érdemes őket az eszközökre telepíteni kívánt működéséhez a peremhálózaton. Ha több modulokat, amelyek együttműködése gyűjti és feldolgozza, egyszerre telepítheti őket, és csatlakoztassa őket az útválasztási szabályokat deklarálható. 

Ez a cikk bemutatja, hogyan hozzon létre egy JSON-üzembe helyezési jegyzék, majd küldje le a központi telepítés IoT peremhálózati eszköz, hogy a fájl segítségével. A központi telepítés, amelynek célpontja a megosztott címkék alapján több eszközre létrehozásával kapcsolatos további információkért lásd: [telepítés és az IoT peremhálózati modulok léptékű figyelése](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésben. 
* Egy [IoT peremhálózati eszköz](how-to-register-device-portal.md) telepített IoT peremhálózati futtatási idő mellett. 
* [Visual Studio Code](https://code.visualstudio.com/).
* [Az Azure IoT peremhálózati bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) a Visual Studio Code. 

## <a name="configure-a-deployment-manifest"></a>Egy üzembe helyezési jegyzék konfigurálása

Egy üzembe helyezési jegyzék egy JSON-dokumentumában, hogy melyik modulokat telepíteni, hogyan közötti adatáramlás a modulok, és a modul twins kívánt tulajdonságait ismerteti. Hogyan telepítési módjától munkahelyi és hogyan hozza létre a címzetteket kapcsolatos további információkért lásd: [megérteni, hogyan IoT peremhálózati modulok használják, konfigurálhatók, és használja fel újra](module-composition.md).

Központi telepítése a Visual Studio Code modulokkal, mentse az üzembe helyezési jegyzék helyileg, egy. JSON-fájlt. A fájl elérési útját fogja használni a következő szakaszban, a konfiguráció alkalmazása az eszköz a parancs futtatásakor.

Íme egy egyszerű üzembe helyezési jegyzék egy modul példa:

   ```json
   {
     "moduleContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {
                 "registryName": {
                   "username": "",
                   "password": "",
                   "address": ""
                 }
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
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
               }
             }
           },
           "modules": {
             "tempSensor": {
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
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

Az IoT hubbal műveletek végrehajtásához használhatja a Visual Studio Code Azure IoT-bővítmények. Az ezekhez a műveletekhez működjön szükség jelentkezzen be az Azure-fiókjával, és válassza ki az IoT hub, amelyen dolgozik.

1. A Visual Studio Code, nyissa meg a **Explorer** nézet.

2. Bontsa ki a Explorer alján a **Azure IoT Hub-eszközöknek** szakasz. 

   ![Bontsa ki az Azure IoT Hub-eszközöknek](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

3. Kattintson a **...**  a a **Azure IoT Hub-eszközöknek** szakaszfejléc. Ha nem látja a három ponttal, mutat a fejlécben. 

4. Válasszon **válassza ki az IoT-központ**.

5. Ha nincs bejelentkezve Azure-fiókjába, kövesse a megjelenő utasításokat ehhez. 

6. Válassza ki az Azure-előfizetését. 

7. Válassza ki az IoT hub. 


## <a name="deploy-to-your-device"></a>Az eszköz telepítése

Modulok az eszközre, az üzembe helyezési jegyzék, a modul adatokkal konfigurált alkalmazásával telepítheti. 

1. A Visual Studio Code terület Intézőbeli nézetében bontsa ki a **Azure IoT Hub-eszközöknek** szakasz. 

2. Kattintson a jobb gombbal az eszközre, amelyet szeretne az üzembe helyezési jegyzék konfigurálása. 

3. Válassza ki **IoT peremhálózati eszköz a központi telepítés létrehozásához**. 

4. Keresse meg a központi telepítés JSON-alkalmazásjegyzéket használni kívánt, és kattintson **válasszon peremhálózati telepítési Manifest**. 

   ![Válassza ki a peremhálózati üzembe helyezési jegyzék](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)


A Visual STUDIO Code kimenet lista tartalmazza a telepítés eredményét. Sikeres központi telepítések néhány percen belül alkalmazza, ha a cél-eszközön fut, és csatlakozik az internethez. 

## <a name="view-modules-on-your-device"></a>Modulok megjelenítése az eszközön

Miután modulok telepítése után az eszközön, megtekintheti azokat a **Azure IoT Hub-eszközöknek** szakasz. Válassza ki a csomópontot a IoT peremhálózati eszköz melletti nyílra. A jelenleg futó modulok jelennek meg. 

Ha nemrégiben telepített új modulok eszközre, mutasson a **Azure IoT Hub-eszközöknek** fejléc szakaszt, és válassza ki a frissítési ikonra a nézet frissítéséhez. 

Kattintson a jobb gombbal a megtekintése és szerkesztése a modul iker egy modul nevét. 

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [telepítés és az IoT peremhálózati modulok léptékű figyelése](how-to-deploy-monitor.md)
---
title: Modulok üzembe helyezése a Visual Studio-kódból – Azure IoT Edge
description: A Visual Studio-kód az Azure IoT-eszközök segítségével leküldéses egy IoT Edge-modul az IoT Hub az IoT Edge-eszköz, egy központi telepítési jegyzékfájl által konfigurált.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e1b2e2a80670cf0409f8f8477563b9a209cc8706
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209205"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Azure IoT Edge-modulok üzembe helyezése a Visual Studio-kódból

Miután létrehozta az IoT Edge-modulokat az üzleti logikával, szeretné telepíteni őket az eszközökre, hogy a peremhálózaton működjenek. Ha több modullal rendelkezik, amelyek együtt dolgoznak az adatok gyűjtésében és feldolgozásában, egyszerre telepítheti őket, és deklarálhatja az őket összekötő útválasztási szabályokat.

Ez a cikk bemutatja, hogyan hozhat létre egy JSON-telepítési jegyzékfájl, majd használja ezt a fájlt, hogy leküldéses a központi telepítés egy IoT Edge-eszköz. Ha tudni szeretné, hogy miként hozhat létre olyan központi telepítést, amely több eszközt céloz meg a megosztott címkék alapján, olvassa [el az IoT Edge-modulok telepítése a Visual Studio-kód használatával.](how-to-deploy-monitor-vscode.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésben.
* IoT [Edge-eszköz](how-to-register-device.md#register-with-visual-studio-code) az IoT Edge futásidejű telepítve.
* [Visual Studio kód](https://code.visualstudio.com/).
* [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) a Visual Studio-kódhoz.

## <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzékfájl konfigurálása

A központi telepítési jegyzékfájl egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan adatfolyamok a modulok között, és a modul twins kívánt tulajdonságait. A központi telepítési jegyzékek működéséről és létrehozásáról további információt [az IoT Edge-modulok használatba és újrafelhasználtkezelésének ismertetése](module-composition.md)című témakörben talál.

Ha modulokat szeretne telepíteni a Visual Studio-kód használatával, mentse a központi telepítési jegyzéket helyileg . JSON fájl. A parancs futtatásakor a következő szakaszban lévő fájlelérési utat fogja használni a konfiguráció eszközre való alkalmazásához.

Íme egy egyszerű központi telepítési jegyzékfájl egy modullal példaként:

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

## <a name="sign-in-to-access-your-iot-hub"></a>Bejelentkezés az IoT-központ eléréséhez

Az Azure IoT-bővítmények a Visual Studio-kód műveletek et az IoT hub műveletek végrehajtásához. Ahhoz, hogy ezek a műveletek működjenek, be kell jelentkeznie az Azure-fiókjába, és ki kell választania azt az IoT-központot, amelyen dolgozik.

1. A Visual Studio-kódban nyissa meg az **Intéző** nézetet.

1. Az Intéző alján bontsa ki az **Azure IoT Hub** szakaszt.

   ![Az Azure IoT Hub szakasz bővítése](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Kattintson a **...** az **Azure IoT Hub** szakasz fejlécében. Ha nem látja a három pontot, vigye az egérmutatót a fejléc fölé.

1. Válassza **az IoT-központ kiválasztása**lehetőséget.

1. Ha nincs bejelentkezve az Azure-fiókjába, kövesse az utasításokat.

1. Válassza ki az Azure-előfizetését.

1. Válassza ki az IoT-központot.

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

A moduloküzembe helyezése az eszközre a moduladatokkal konfigurált központi telepítési jegyzék alkalmazásával.

1. A Visual Studio Kódkezelő nézetben bontsa ki az **Azure IoT Hub** szakaszt, majd bontsa ki az **Eszközök** csomópontot.

1. Kattintson a jobb gombbal az IoT Edge-eszközre, amelyet a központi telepítési jegyzékfájlval konfigurálni szeretne.

    > [!TIP]
    > Annak ellenőrzéséhez, hogy a kiválasztott eszköz egy IoT Edge-eszköz, jelölje ki, hogy bővítse a modulok listáját, és ellenőrizze a **$edgeHub** és **$edgeAgent**jelenlétét. Minden IoT Edge-eszköz tartalmazza ezt a két modult.

1. Válassza **a Központi telepítés létrehozása egyetlen eszközhöz**lehetőséget.

1. Keresse meg a használni kívánt telepítési jegyzékfájl JSON-fájlját, és kattintson **az Edge Deployment Manifest kiválasztása parancsra.**

   ![Szegélytelepítési jegyzékfájl kiválasztása](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

A központi telepítés eredményei a VS Code kimeneten jelennek meg. A sikeres telepítések néhány percen belül érvénybe lépnek, ha a céleszköz fut, és csatlakozik az internethez.

## <a name="view-modules-on-your-device"></a>Modulok megtekintése az eszközön

Miután üzembe helyezte a modulokat az eszközére, megtekintheti az összeset az **Azure IoT Hub** szakaszban. Az IoT Edge-eszköz melletti nyíllal bontsa ki. Az összes jelenleg futó modul megjelenik.

Ha a közelmúltban telepített új modulokat egy eszközre, vigye az egérmutatót az **Azure IoT Hub Devices** szakasz fejlécére, és válassza a frissítésikont a nézet frissítéséhez.

Kattintson a jobb gombbal egy modul nevére az ikermodul megtekintéséhez és szerkesztéséhez.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [telepítheti és figyelheti az IoT Edge-modulokat a Visual Studio-kód használatával](how-to-deploy-monitor.md)

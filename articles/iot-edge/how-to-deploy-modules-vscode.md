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
ms.openlocfilehash: d8c90c2a13d111d01a1e7a1bd9b63da180621ded
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772091"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Azure IoT Edge-modulok üzembe helyezése a Visual Studio Code-ból

Miután létrehozta IoT Edge modulokat az üzleti logikával, üzembe helyezheti azokat az eszközökön a peremhálózat működéséhez. Ha több modullal is együttműködik az adatok gyűjtéséhez és feldolgozásához, egyszerre telepítheti őket, és deklarálhatja az azokat összekötő útválasztási szabályokat.

Ez a cikk bemutatja, hogyan hozhat létre JSON központi telepítési jegyzéket, majd ezzel a fájllal leküldheti a központi telepítést egy IoT Edge eszközre. További információ a megosztott címkék alapján több eszközt célzó központi telepítések létrehozásáról: [IoT Edge modulok méretezése a Visual Studio Code használatával](how-to-deploy-monitor-vscode.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében.
* [IoT Edge-eszköz](how-to-register-device.md#register-with-visual-studio-code) , amelyen telepítve van a IoT Edge futtatókörnyezet.
* [Visual Studio Code](https://code.visualstudio.com/).
* A Visual Studio Code-hoz készült [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) .

## <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzék konfigurálása

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan zajlik az adatforgalom a modulok és a modul kívánt tulajdonságai között. Az üzembe helyezési jegyzékek működésével és létrehozásával kapcsolatos további információkért lásd: [IoT Edge modulok használatának, konfigurálásának és](module-composition.md)újbóli használatának ismertetése.

Ha Visual Studio Code-t használó modulokat kíván üzembe helyezni, mentse helyileg a telepítési jegyzéket. JSON-fájl. A fájl elérési útját a következő szakaszban fogja használni, amikor a parancs futtatásával alkalmazza a konfigurációt az eszközre.

Íme egy alapszintű üzembe helyezési jegyzék egy modullal, például:

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
                 "createOptions": "{}"
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

A Visual Studio Code-hoz készült Azure IoT Extensions használatával műveleteket hajthat végre az IoT hub segítségével. Ezeknek a műveleteknek a működéséhez be kell jelentkeznie az Azure-fiókjába, és ki kell választania azt az IoT hub-t, amelyen dolgozik.

1. A Visual Studio Code-ban nyissa meg a **Explorer** nézetet.

1. Az Explorer alján bontsa ki az **Azure IoT hub** szakaszt.

   ![Az Azure IoT Hub szakasz kibontása](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Kattintson a **...** elemre az **Azure IoT hub** szakasz fejlécében. Ha nem látja a három pontot, vigye a kurzort a fejléc fölé.

1. Válassza a **IoT hub kiválasztása**lehetőséget.

1. Ha nem jelentkezett be az Azure-fiókjába, kövesse az utasításokat.

1. Válassza ki az Azure-előfizetését.

1. Válassza ki az IoT hubot.

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

A modulok az eszközön való üzembe helyezéséhez alkalmazza a modul adataival konfigurált telepítési jegyzékfájlt.

1. A Visual Studio Code Explorer nézetében bontsa ki az **Azure IoT hub** szakaszt, majd bontsa ki az **eszközök** csomópontot.

1. Kattintson a jobb gombbal arra a IoT Edge eszközre, amelyet az üzembe helyezési jegyzékkel szeretne konfigurálni.

    > [!TIP]
    > Annak ellenőrzéséhez, hogy a kiválasztott eszköz egy IoT Edge eszköz-e, válassza ki a modulok listájának kibontásához és a **$edgeHub** és a **$edgeAgent**meglétének ellenőrzéséhez. Minden IoT Edge eszköz tartalmazza ezt a két modult.

1. Válassza **a központi telepítés létrehozása egyetlen eszközhöz**lehetőséget.

1. Navigáljon a használni kívánt üzembe helyezési jegyzékfájlhoz tartozó JSON-fájlhoz, majd kattintson a **peremhálózat-telepítési jegyzék kiválasztása**elemre.

   ![Edge központi telepítési jegyzékfájljának kiválasztása](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Az üzembe helyezés eredményét a VS Code kimenetében kell kinyomtatni. A sikeres telepítések néhány percen belül alkalmazhatók, ha a célként megadott eszköz fut, és az internethez csatlakozik.

## <a name="view-modules-on-your-device"></a>Az eszközön található modulok megtekintése

Miután telepítette a modulokat az eszközre, megtekintheti az összeset az **Azure IoT hub** szakaszban. Kattintson a IoT Edge eszköz melletti nyílra a kibontásához. A jelenleg futó modulok jelennek meg.

Ha nemrég telepített új modulokat egy eszközre, vigye az egérmutatót az **Azure IoT hub-eszközök** szakasz fejlécére, és kattintson a frissítés ikonra a nézet frissítéséhez.

Kattintson a jobb gombbal egy modul nevére a különálló modul megtekintéséhez és szerkesztéséhez.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [helyezhet üzembe és figyelheti IoT Edge modulokat a Visual Studio Code használatával](how-to-deploy-monitor.md)

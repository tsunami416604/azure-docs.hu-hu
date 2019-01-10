---
title: A Visual Studio Code - az Azure IoT Edge modulok üzembe helyezése |} A Microsoft Docs
description: Modulok IoT Edge-eszköz üzembe helyezése a Visual Studio Code használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 91a074cf98291b105864a69730314efff3482254
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54155014"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>A Visual Studio Code-ból az Azure IoT Edge-modulok telepítése

Miután létrehozott egy IoT Edge modulok az üzleti logikával rendelkező, érdemes őket az eszközökre telepíteni kívánt megfelelően működjenek a peremhálózaton. Ha több modulokat, amelyek együttműködve gyűjtenek és dolgoznak fel adatokat, és egyszerre telepítheti őket, és deklarálja az útválasztási szabályokat, amelyek csatlakoztathatja őket.

Ez a cikk bemutatja, hogyan hozzon létre egy JSON-manifest nasazení, majd küldje le az üzembe helyezés IoT Edge-eszköz fájl használatával. A megosztott címkék alapján több eszköz célzó központi telepítés létrehozásával kapcsolatos információkért lásd: [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [az IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében.
* Egy [IoT Edge-eszköz](how-to-register-device-portal.md) az telepítve van az IoT Edge-futtatókörnyezet.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Az Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) a Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. Hogyan alkalmazásjegyzékeket az üzembe helyezési a munkahelyi, és hogyan hozhat létre, azokat kapcsolatos további információkért lásd: [megismerheti, hogyan IoT Edge-modulok használják, konfigurálhatók, és újra felhasználható](module-composition.md).

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
                 "createOptions": "{}"
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

Használhatja a Visual Studio Code az Azure IoT-bővítmények az IoT hub-műveletek végrehajtásához. Ezeket a műveleteket működéséhez kell jelentkezzen be az Azure-fiókjával, és válassza ki az IoT hubot, amelyen dolgozik.

1. A Visual Studio Code-ban nyissa meg a **Explorer** megtekintése.

1. Az Explorer alján bontsa ki a **Azure IoT Hub-eszközök** szakaszban.

   ![Az Azure IoT Hub-eszközök a szakaszt kibontva](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Kattintson a **...**  a a **Azure IoT Hub-eszközök** szakaszcímben. Ha nem látja a három pontra, a kurzort a fejléc.

1. Válasszon **válassza ki az IoT Hub**.

1. Ha nem jelentkezett be az Azure-fiókját, kövesse az ehhez az utasításokat.

1. Válassza ki az Azure-előfizetését.

1. Válassza ki az IoT hubnak.

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

A modul információkkal konfigurált manifest nasazení alkalmazása modulok üzembe az eszközre.

1. A Visual Studio Code terület Intézőbeli nézetében bontsa ki a **Azure IoT Hub-eszközök** szakaszban.

1. Kattintson a jobb gombbal manifest nasazení a konfigurálni kívánt IoT Edge-eszközön.

    > [!TIP]
    > Győződjön meg arról, hogy úgy döntött, az eszköz IoT Edge-eszköz, válassza azt, bontsa ki a modulok listájához, és meglétének ellenőrzése **$edgeHub** és **$edgeAgent**. Minden IoT Edge-eszköz az alábbi két modulokat tartalmaz.

1. Válassza ki **hozzon létre telepítést az adott eszköz**.

1. Keresse meg a központi telepítési JSON-alkalmazásjegyzéket, amelyet használni szeretne, és kattintson a **kiválasztása peremhálózati üzembe helyezési Manifest**.

   ![Válassza ki a peremhálózati Manifest Nasazení](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Az eredmények az üzembe helyezés a VS Code-kimenetben nyomtatott. A sikeres telepítések telepített néhány percen belül, ha a cél-eszközön fut, és csatlakozik az internethez.

## <a name="view-modules-on-your-device"></a>Modulok megjelenítése az eszközön

Miután telepítette a modulokat az eszközön, megtekintheti azokat a **Azure IoT Hub-eszközök** szakaszban. Válassza ki az IoT Edge-eszköz annak kibontásához melletti nyílra. A jelenleg futó modulok jelennek meg.

Ha a közelmúltban helyezte üzembe új modulokat az eszközökre, mutasson a **Azure IoT Hub-eszközök** fejléc szakaszt, és válassza a frissítés ikont, a nézet frissítéséhez.

Kattintson a jobb gombbal megtekintheti és szerkesztheti az ikermodul modul nevét.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md)

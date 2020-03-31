---
title: Az MXChip IoT DevKit felhasználói LED vezérléséhez használja az Azure-eszközök ikreitjét | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan figyelheti a DevKit-állapotokat, és szabályozhatja a felhasználói LED-et az Azure IoT Hub-eszköz twins-i használatával.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: deb1ea8c7b41ad48bddebfbed1b15c667ee0071a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73483945"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Ebben a példában figyelheti az MXChip IoT DevKit WiFi-információkat és -érzékelő állapotokat, és szabályozhatja a felhasználói LED színét az Azure IoT Hub-eszköz twins használatával.

## <a name="what-you-learn"></a>Ismertetett témák

- Az MXChip IoT DevKit érzékelő állapotok figyelése.

- Az Azure-eszközök twins használata a DevKit RGB LED-jének színének szabályozásához.

## <a name="what-you-need"></a>Mi szükséges

- Állítsa be a fejlesztői környezetet az [Első lépések útmutató ban.](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)

- A GitBash terminálablakából (vagy más Git parancssori felületről) írja be a következő parancsokat:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Azure-szolgáltatások kiépítése

1. Kattintson a Visual Studio-kód **Feladatok** legördülő menüparancsára, és válassza a **Feladat futtatása...**  -  **felhő-ellátás**.

2. A folyamat az Üdvözlőpanel **TERMINÁL** lapján jelenik **meg.**

3. Amikor a rendszer a Következő előfizetést szeretné választani, válasszon ki egy előfizetést, amikor a rendszer a *Következő üzenetet szeretné kijelölni.*

4. Jelöljön ki vagy válasszon ki egy erőforráscsoportot. 
 
   > [!NOTE]
   > Ha már rendelkezik egy ingyenes IoT Hub, kihagyhatja ezt a lépést.

5. Amikor a rendszer megkérdezi, *hogy milyen IoT-központot szeretne választani,* válassza ki vagy hozzon létre egy IoT Hubot.

6. Valami hasonló a *függvény alkalmazáshoz: függvényalkalmazás neve: xxx*, jelenik meg. Írja le a függvényalkalmazás nevét; egy későbbi lépésben fogják használni.

7. Várja meg, amíg az Azure Resource Manager-sablon üzembe helyezése befejeződik, amely akkor jelenik meg, amikor az üzenet *Resource Manager sablon központi telepítése: Kész* jelenik meg.

## <a name="deploy-function-app"></a>Függvényalkalmazás telepítése

1. Kattintson a Visual Studio-kód **Feladatok** legördülő menüparancsára, és válassza a **Feladat futtatása...**  -  **felhőalapú telepítése**.

2. Várja meg, amíg a függvényalkalmazáskód feltöltési folyamata befejeződik; az *üzenetfüggvény-alkalmazás telepítése: Kész* jelenik meg.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>IoT Hub-eszköz kapcsolati karakterláncának konfigurálása a DevKit ben

1. Csatlakoztassa az MXChip IoT DevKit-et a számítógépéhez.

2. Kattintson a Visual Studio-kód **Feladatok** legördülő menüparancsára, és válassza a **Feladat futtatása...**  -  **konfigurációs eszköz-kapcsolat**

3. Az MXChip IoT DevKit en nyomja meg és tartsa lenyomva az **A**gombot, nyomja meg a **Reset** gombot, majd engedje el az **A** gombot, hogy a DekKit konfigurációs módba lépjen.

4. Várja meg, amíg a kapcsolati karakterlánc konfigurációs folyamata befejeződik.

## <a name="upload-arduino-code-to-devkit"></a>Arduino kód feltöltése a DevKit-hez

Az MXChip IoT DevKit számítógéphez csatlakoztatva:

1. Kattintson a Visual Studio-kód **Feladatok** legördülő menüparancsára, és válassza a **Build Task futtatása...** Az Arduino vázlatot összeállítják és feltöltik a DevKitbe.

2. Ha a vázlat feltöltése sikeres, megjelenik egy *Build & Upload Sketch: success* üzenet.

## <a name="monitor-devkit-state-in-browser"></a>DevKit állapotának figyelése a böngészőben

1. Webböngészőben nyissa meg `DevKitState\web\index.html` a fájlt , amely a Mire van szüksége lépés során jött létre.

2. A következő weblap jelenik meg:![Adja meg a függvényalkalmazás nevét.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Adja meg a korábban leírt függvényalkalmazás nevét.

4. Kattintson a **Csatlakozás** gombra

5. Néhány másodpercen belül a lap frissíti és megjeleníti a DevKit Wi-Fi-kapcsolat állapotát és az egyes alaplapi érzékelők állapotát.

## <a name="control-the-devkits-user-led"></a>A DevKit felhasználói LED-jének vezérlése

1. Kattintson a weblap illusztrációján a felhasználó LED-es képére.

2. Néhány másodpercen belül a képernyő frissül, és megjeleníti a felhasználó LED-jének aktuális színállapotát.

3. Próbálja meg módosítani az RGB LED színértékét az RGB csúszka vezérlőinek különböző pontjaira kattintva.

## <a name="example-operation"></a>Példa művelet

![Példa vizsgálati eljárás](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Az ikereszköz nyers adatait láthatja az Azure\> Portalon: IoT Hub - IoT-eszközök –\> * \<az eszköz\> *  - \> Device Twin.

## <a name="next-steps"></a>További lépések

Megtanultad, hogyan:
- Csatlakoztasson egy MXChip IoT DevKit-eszközt az Azure IoT Remote Monitoring megoldásgyorsítóhoz.
- Az Azure IoT-eszköz twins funkcióval érzékelheti és szabályozhatja a DevKit RGB LED-je színét.

A következő javasolt lépések a következők:

* [Az Azure IoT remote monitoring megoldásgyorsító – áttekintés](https://docs.microsoft.com/azure/iot-suite/)
* [MXChip IoT DevKit-eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](/azure/iot-central/core/howto-connect-devkit)

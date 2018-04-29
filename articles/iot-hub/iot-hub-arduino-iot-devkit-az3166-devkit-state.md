---
title: Azure eszköz twins segítségével szabályozhatja a felhasználó LED MXChip IoT DevKit |} Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan DevKit állapotok figyelésére, és szabályozhatja a felhasználó Azure IoT Hub eszköz twins LED-jét.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 33d8a36cc88bd1c263f2c4a38a59e04e1253357c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Ebben a példában is használhatja, figyelheti a Wi-Fi DevKit MXChip IoT információkat és érzékelő állapota, valamint szabályozhatja a felhasználó LED-jét használó Azure IoT Hub eszköz twins színét.

## <a name="what-you-learn"></a>Ismertetett témák

- Az IoT-DevKit MXChip érzékelő figyelése szerint.
- Hogyan lehet Azure eszköz twins segítségével szabályozhatja a DevKit RGB LED színét.

## <a name="what-you-need"></a>Mi szükséges

- A fejlesztési környezet beállítása a következő a [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
- Írja be a következő parancsokat a GitBash terminálablakot (vagy más Git parancssori felület):
    - `git clone https://github.com/DevKitExamples/DevKitState.git`
    - `cd DevKitState`
    - `code .`

## <a name="provision-azure-services"></a>Azure-szolgáltatások kiépítése

1. Kattintson a **feladatok** Visual Studio Code, és válassza a legördülő menü **feladat futtatása...**   -  **felhő-provision**.
2. A folyamat alatt jelenik meg a **TERMINÁLSZOLGÁLTATÁSOK** lapján a **üdvözlő** panel.
3. Amikor megjelenik az üzenet *milyen előfizetésre szeretne válasszon*, válasszon egy előfizetést.
4. Válassza ki, vagy válasszon egy erőforráscsoportot. 
 
    > [!NOTE]
    > Ha már van egy ingyenes IoT-központot, ez a lépés kimarad.

5. Amikor megjelenik az üzenet *milyen IoT-központ szeretne válasszon*, válassza ki, vagy létrehoz egy IoT-központot.
6. Valami hasonló *függvény app: függvény alkalmazásnév: a(z) xxx*, akkor jelenik meg. Jegyezze fel az alkalmazás függvénynév; egy későbbi lépésben használandó.
7. Várjon, amíg az Azure Resource Manager sablon üzembe helyezés befejeződik, amely jelzi, ha az üzenet *Resource Manager sablon-üzembehelyezés: kész* jelenik meg.

## <a name="deploy-function-app"></a>Függvény alkalmazás üzembe helyezése

1. Kattintson a **feladatok** Visual Studio Code, és válassza a legördülő menü **feladat futtatása...**   -  **felhő telepítése**.
2. Várjon, amíg függvény alkalmazáskódot feltöltése a folyamat befejezéséhez; az üzenet *függvény alkalmazást központilag telepíti: kész* jelenik meg.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>A DevKit IoT Hub eszköz kapcsolati karakterlánc konfigurálása

1. A MXChip IoT DevKit kapcsolódni a számítógéphez.
2. Kattintson a **feladatok** Visual Studio Code, és válassza a legördülő menü **feladat futtatása...**   -  **config-eszköz-kapcsolat**
3. MXChip IoT DevKit, nyomja le és tartsa lenyomva a gombot **A**, nyomja le az ENTER a **alaphelyzetbe** , és ezután kiadás gomb **A** a adja meg a konfigurációs mód DekKit végrehajtásához.
4. Várja meg a kapcsolódási karakterlánc konfigurációs folyamat végrehajtása.

## <a name="upload-arduino-code-to-devkit"></a>Töltse fel Arduino kód DevKit

Az a MXChip IoT DevKit csatlakoztatva a számítógéphez:
1. Kattintson a **feladatok** Visual Studio Code, és válassza a legördülő menü **Buildkészítő feladat futtatása...** A Arduino vázlat van lefordítva, és a DevKit fel van töltve.
2. Ha a Vázlat sikeresen fel lett töltve egy *Build & feltöltése vázlat: sikeres* üzenet jelenik meg.

## <a name="monitor-devkit-state-in-browser"></a>Figyelőállapot DevKit böngészőben

1. Egy böngészőben nyissa meg a `DevKitState\web\index.html` fájl--során létrehozott a [teendők](#whatyouneed) lépés.
2. A következő weblap jelenik meg:![Adja meg a függvény alkalmazás neve.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)
1. Adjon meg a korábban megírt le függvény alkalmazás neve.
2. Kattintson a **Connect** gomb
3. Néhány másodpercen belül az oldal frissül, és a DevKit Wi-Fi kapcsolat állapota és az alaplapi érzékelők állapotának jeleníti meg.

## <a name="control-the-devkits-user-led"></a>A DevKit felhasználói LED szabályozása

1. Kattintson a weblap ábra felhasználói LED képet.
2. Néhány másodpercen belül a képernyő frissül, és a felhasználó LED aktuális szín állapotát jeleníti meg.
3. Próbálja a RGB LED szín értékének módosítása gombra kattintva a RGB csúszkával különböző helyein.

## <a name="example-operation"></a>Példa művelet

![Példa tesztelési eljárás](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan:
- Csatlakoztasson egy MXChip IoT DevKit eszközt az Azure IoT Suite.
- Az Azure IoT eszköz twins függvény segítségével értelemben, és szabályozhatja a DevKit RGB LED színét.

A javasolt következő lépések a következők:

* [Az Azure IoT Suite – áttekintés](https://docs.microsoft.com/azure/iot-suite/)
* [Csatlakoztasson egy MXChip IoT DevKit eszközt a Microsoft IoT Central alkalmazás](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)

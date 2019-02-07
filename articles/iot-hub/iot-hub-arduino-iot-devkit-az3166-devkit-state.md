---
title: Azure-ikereszközökkel segítségével szabályozza a MXChip IoT DevKit felhasználói LED |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan DevKit állapotok monitorozását és ellenőrzését a felhasználó LED az Azure IoT Hub ikereszközökhöz.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: e955d21132dda6caa137ad3b5de9d00ccf7ed1b4
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811052"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Ebben a példában használhatja az MXChip IoT DevKit Wi-Fi információkat és érzékelő államok figyelése és szabályozhatja az Azure IoT Hub ikereszközök használata felhasználói LED színét.

## <a name="what-you-learn"></a>Ismertetett témák

- Az MXChip IoT DevKit érzékelő monitorozása az államokhoz.

- Hogyan lehet Azure-ikereszközökkel segítségével szabályozhatja a DevKit RGB LED színét.

## <a name="what-you-need"></a>Mi szükséges

- A fejlesztési környezet beállítása a következő a [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

- Írja be a következő parancsokat a GitBash terminálablakban (vagy más Git parancssori felület):

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Azure-szolgáltatások üzembe helyezése

1. Kattintson a **feladatok** a Visual Studio Code-ot, és válassza a legördülő menüből **feladat futtatása...**   -  **felhőalapú a kiépítés**.

2. A folyamat alatt jelenik meg a **TERMINÁLON** lapján a **üdvözlő** panel.

3. Amikor a rendszer kéri, az üzenet *milyen előfizetésre szeretne válasszon*, válasszon ki egy előfizetést.

4. Válassza ki, vagy válasszon ki egy erőforráscsoportot. 
 
   > [!NOTE]
   > Ha már rendelkezik egy ingyenes IoT hubot, kihagyhatja ezt a lépést.

5. Amikor a rendszer kéri, az üzenet *milyen IoT hub szeretne válassza*, válassza ki vagy hozzon létre egy IoT hubot.

6. Valami hasonló *függvényalkalmazás: függvényalkalmazás neve: a(z) xxx*, jelenik meg. Írja le a függvényalkalmazás neve; egy későbbi lépésben lesz.

7. Várjon, amíg az Azure Resource Manager sablon üzembe helyezés befejeződik, amely jelzi, ha az üzenet *Resource Manager-sablon üzembe helyezése: Kész* jelenik meg.

## <a name="deploy-function-app"></a>Függvényalkalmazás üzembe helyezése

1. Kattintson a **feladatok** a Visual Studio Code-ot, és válassza a legördülő menüből **feladat futtatása...**   -  **felhő üzembe helyezése**.

2. Várjon, amíg a függvény kódját folyamat befejezéséhez; feltöltése az üzenet *függvényalkalmazás helyez üzembe: Kész* jelenik meg.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Az IoT Hub eszköz kapcsolati karakterláncának konfigurálása a fejlesztői készlet

1. Az MXChip IoT DevKit csatlakoztatása a számítógéphez.

2. Kattintson a **feladatok** a Visual Studio Code-ot, és válassza a legördülő menüből **feladat futtatása...**   -  **config-device-kapcsolat**

3. Az MXChip IoT DevKit, nyomja le és tartsa lenyomva a gombot **A**, nyomja le a **alaphelyzetbe** gomb, valamint a kiadásban gomb **A** , hogy a DekKit adja meg a konfigurációs mód.

4. Várjon, amíg a kapcsolódási karakterlánc konfigurációs folyamat végrehajtani.

## <a name="upload-arduino-code-to-devkit"></a>Fejlesztői készlet Arduino kód feltöltése

Az MXChip IoT DevKit egymáshoz a számítógépére:

1. Kattintson a **feladatok** a Visual Studio Code-ot, és válassza a legördülő menüből **összeállítása feladat futtatása...** Az Arduino rajz összeállítani, és feltölti a fejlesztői készlet.

2. Ha a Vázlat sikeresen fel lett töltve egy *Build & feltöltése vázlat: sikeres* üzenet jelenik meg.

## <a name="monitor-devkit-state-in-browser"></a>Figyelőállapot DevKit böngészőben

1. Egy webböngészőben nyissa meg a `DevKitState\web\index.html` fájl--létrehozott során a mi lépés szükséges.

2. A következő weblap jelenik meg:![Adja meg a függvényalkalmazás nevét.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Adjon meg a függvényalkalmazás nevét korábban megírt le.

4. Kattintson a **Connect** gomb

5. Néhány másodpercen belül az oldal frissül, és a fejlesztői készlet Wi-Fi kapcsolat állapota és azok állapotát a fedélzeti érzékelők jeleníti meg.

## <a name="control-the-devkits-user-led"></a>A fejlesztői készlet felhasználói LED szabályozása

1. Kattintson a weblap ábra felhasználói LED grafika.

2. Néhány másodpercen belül a képernyő frissül, és a felhasználó LED szín aktuális állapotát jeleníti meg.

3. Próbálja meg módosítani a RGB LED szín értékét a RGB csúszka vezérlőkre különböző helyeken található gombra kattintva.

## <a name="example-operation"></a>A példában a művelet

![Példa tesztelési eljárás](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Nyers adatok az ikereszköz az Azure Portalon tekintheti meg: Az IoT Hub -\> IoT-eszközök –\> *\<az eszköz\>*  - \> Ikereszköz.

## <a name="next-steps"></a>További lépések

Megtanulhatta, hogyan lehet:
- Az MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT távoli figyelési megoldásgyorsító.
- Az Azure IoT device twins függvény segítségével szempontjából ésszerű helyen, és szabályozhatja a DevKit RGB LED színét.

Íme a javasolt következő lépések:

* [Az Azure IoT távoli figyelési megoldásgyorsító áttekintése](https://docs.microsoft.com/azure/iot-suite/)
* [Az MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)

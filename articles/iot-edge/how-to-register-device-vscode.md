---
title: Regisztráljon egy új Azure IoT Edge-eszköz (a VS Code) |} A Microsoft Docs
description: Hozzon létre egy új IoT Edge-eszköz az Azure IoT hub a Visual Studio Code használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cf9603c65454f076a494789e784c9352fb7bef33
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578705"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>A Visual Studio Code-ból egy új Azure IoT Edge-eszköz regisztrálása

Az IoT-eszközök Azure IoT Edge használata előtt kell regisztrálni őket az IoT hubbal. Miután regisztrált egy eszközt, kap egy kapcsolati karakterláncot, amely segítségével konfigurálja az eszközt, a peremhálózati számítási feladatokhoz. 

Ez a cikk bemutatja, hogyan regisztrálhat egy új IoT Edge-eszköz, a Visual Studio Code (a VS Code). A legtöbb műveletek végrehajtása a VS Code-ban többféle módon lehet. Ez a cikk a Explorert használja, de is használhatja a Parancskatalógus futtatásához a lépések többségét. 

## <a name="prerequisites"></a>Előfeltételek

* Egy [az IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésben
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Az Azure IoT Edge bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) a Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

Használhatja a Visual Studio Code az Azure IoT-bővítmények az IoT hub-műveletek végrehajtásához. Ezeket a műveleteket működéséhez kell jelentkezzen be az Azure-fiókjával, és válassza ki az IoT hubot, amelyen dolgozik.

1. A Visual Studio Code-ban nyissa meg a **Explorer** megtekintése.

2. Az Explorer alján bontsa ki a **Azure IoT Hub-eszközök** szakaszban. 

   ![Bontsa ki az Azure IoT Hub-eszközök](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. Kattintson a **...**  a a **Azure IoT Hub-eszközök** szakaszcímben. Ha nem látja a három pontra, kattintson a, vagy vigye a kurzort a fejléc fölé. 

4. Válasszon **válassza ki az IoT Hub**.

5. Ha nem jelentkezett be az Azure-fiókját, kövesse az ehhez az utasításokat. 

6. Válassza ki az Azure-előfizetését. 

7. Válassza ki az IoT hubnak. 

## <a name="create-a-device"></a>Eszköz létrehozása

1. A VS Code Explorerben bontsa ki a **Azure IoT Hub-eszközök** szakaszban. 

2. Kattintson a **...**  a a **Azure IoT Hub-eszközök** szakaszcímben. Ha nem látja a három pontra, kattintson a, vagy vigye a kurzort a fejléc fölé. 

3. Válassza ki **IoT Edge-eszköz létrehozása**. 

4. A megnyíló szövegbeviteli mezőben adjon az eszköz azonosítóval. 

A képernyőn a parancs eredménye látható. Az eszközinformáció nyomtatott, amely tartalmazza a **deviceId** megadott és a **connectionString** , hogy segítségével a fizikai eszköz csatlakoztatása az IoT hubnak. 

## <a name="view-all-devices"></a>Minden eszköz megjelenítése

Az IoT hubhoz csatlakozó eszközök szerepelnek az **Azure IoT Hub-eszközök** a Visual Studio Code Explorerben szakaszában. IoT Edge-eszközök különböznek egymástól, egy másik ikon és a tényt, hogy azok az adott eszközön üzembe helyezett modulok megjeleníthető bővíthetők nem peremhálózati eszközökön. 

   ![A VS Code-ban eszközök megtekintése](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>A kapcsolati karakterlánc

Amikor készen áll az eszköz beállításához, a kapcsolati karakterláncot, amely összekapcsolja a fizikai eszköz az IoT hub az identitással kell.

1. Kattintson a jobb gombbal az eszköz azonosítója az **Azure IoT Hub-eszközök** szakaszban. 
2. Válassza ki **eszköz kapcsolati karakterlánc másolása**.

   A kapcsolati karakterláncot a vágólapra másolja. 

Lehetőség kiválasztásával **eszközinformáció első** a helyi menüben megtekintheti az összes eszköz adatai, beleértve a kapcsolati karakterláncot, a kimeneti ablakban. 


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [modulok üzembe helyezése a Visual Studio Code egy eszközön](how-to-deploy-modules-vscode.md).

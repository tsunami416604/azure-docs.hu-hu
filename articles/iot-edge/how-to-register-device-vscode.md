---
title: A Visual Studio Code - az Azure IoT Edge egy új eszköz regisztrálása |} A Microsoft Docs
description: A Visual Studio Code használatával hozzon létre egy új IoT Edge-eszköz az Azure IoT hub és a kapcsolati karakterlánc
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 2b851e7f2ebdbff08fa09002765fbd7d7927deba
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051169"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>A Visual Studio Code-ból egy új Azure IoT Edge-eszköz regisztrálása

Az IoT-eszközök Azure IoT Edge használata előtt kell regisztrálni őket az IoT hubbal. Miután regisztrált egy eszközt, kap egy kapcsolati karakterláncot, amely segítségével konfigurálja az eszközt, a peremhálózati számítási feladatokhoz.

Ez a cikk bemutatja, hogyan regisztrálhat egy új IoT Edge-eszköz, a Visual Studio Code (a VS Code). A legtöbb műveletek végrehajtása a VS Code-ban többféle módon lehet. Ez a cikk a Explorert használja, de is használhatja a Parancskatalógus futtatásához a lépések többségét.

## <a name="prerequisites"></a>Előfeltételek

* Egy [az IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésben
* [Visual Studio Code](https://code.visualstudio.com/)
* [Az Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

Használhatja a Visual Studio Code az Azure IoT-bővítmények az IoT hub-műveletek végrehajtásához. Ezeket a műveleteket működéséhez kell jelentkezzen be az Azure-fiókjával, és válassza ki az IoT hubot, amelyen dolgozik.

1. A Visual Studio Code-ban nyissa meg a **Explorer** megtekintése.

1. Az Explorer alján bontsa ki a **Azure IoT Hub-eszközök** szakaszban.

   ![Az Azure IoT Hub-eszközök a szakaszt kibontva](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

1. Kattintson a **...**  a a **Azure IoT Hub-eszközök** szakaszcímben. Ha nem látja a három pontra, kattintson a, vagy vigye a kurzort a fejléc fölé.

1. Válasszon **válassza ki az IoT Hub**.

1. Ha nem jelentkezett be az Azure-fiókját, kövesse az ehhez az utasításokat.

1. Válassza ki az Azure-előfizetését.

1. Válassza ki az IoT hubnak.

## <a name="create-a-device"></a>Eszköz létrehozása

1. A VS Code Explorerben bontsa ki a **Azure IoT Hub-eszközök** szakaszban.

1. Kattintson a **...**  a a **Azure IoT Hub-eszközök** szakaszcímben. Ha nem látja a három pontra, kattintson a, vagy vigye a kurzort a fejléc fölé.

1. Válassza ki **IoT Edge-eszköz létrehozása**.

1. A megnyíló szövegbeviteli mezőben adjon az eszköz azonosítóval.

A képernyőn a parancs eredménye látható. Az eszközinformáció nyomtatott, amely tartalmazza a **deviceId** megadott és a **connectionString** , hogy segítségével a fizikai eszköz csatlakoztatása az IoT hubnak.

## <a name="view-all-devices"></a>Minden eszköz megjelenítése

Az IoT hubhoz csatlakozó eszközök szerepelnek az **Azure IoT Hub-eszközök** a Visual Studio Code Explorerben szakaszában. IoT Edge-eszközök különböznek egymástól, egy másik ikon és a tényt, hogy azok az adott eszközön üzembe helyezett modulok megjeleníthető bővíthetők nem peremhálózati eszközökön.

   ![Az IoT hub IoT Edge-eszközök megtekintése](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>A kapcsolati karakterlánc

Amikor készen áll az eszköz beállításához, a kapcsolati karakterláncot, amely összekapcsolja a fizikai eszköz az IoT hub az identitással kell.

1. Kattintson a jobb gombbal az eszköz azonosítója az **Azure IoT Hub-eszközök** szakaszban.

1. Válassza ki **eszköz kapcsolati karakterlánc másolása**.

   A kapcsolati karakterláncot a vágólapra másolja.

Lehetőség kiválasztásával **eszközinformáció első** a helyi menüben megtekintheti az összes eszköz adatai, beleértve a kapcsolati karakterláncot, a kimeneti ablakban.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [modulok üzembe helyezése a Visual Studio Code egy eszközön](how-to-deploy-modules-vscode.md).

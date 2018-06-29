---
title: Új Azure IoT peremhálózati eszköz (Visual STUDIO Code) regisztrálása |} Microsoft Docs
description: Visual Studio Code segítségével hozzon létre egy új IoT peremhálózati eszköz az Azure IoT hub
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7902461f58df1b4fe0c3ed3b577f668fe8be4cc2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036118"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>A Visual Studio Code új Azure IoT peremhálózati eszköz regisztrálása

Az IoT-eszközök Azure IoT oldala használata előtt kell regisztrálja őket az IoT hub. Eszköz regisztrálása után kapni egy kapcsolati karakterláncot, amely segítségével konfigurálja az eszközt, a peremhálózati munkaterhelésekhez. 

Ez a cikk bemutatja, hogyan kell regisztrálni egy új IoT peremhálózati eszköz, a Visual Studio (kód VS) használatával. Több módon is végzik a műveleteik VS-kódban. Ez a cikk a Explorer használja, de a lépéseket a legtöbb futtatásához a parancs paletta is használhatja. 

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetése
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Az Azure IoT peremhálózati bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) a Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

Az IoT hubbal műveletek végrehajtásához használhatja a Visual Studio Code Azure IoT-bővítmények. Az ezekhez a műveletekhez működjön szükség jelentkezzen be az Azure-fiókjával, és válassza ki az IoT hub, amelyen dolgozik.

1. A Visual Studio Code, nyissa meg a **Explorer** nézet.

2. Bontsa ki a Explorer alján a **Azure IoT Hub-eszközöknek** szakasz. 

   ![Bontsa ki az Azure IoT Hub-eszközöknek](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. Kattintson a **...**  a a **Azure IoT Hub-eszközöknek** szakaszfejléc. Ha nem látja a három ponttal, mutat a fejlécben. 

4. Válasszon **válassza ki az IoT-központ**.

5. Ha nincs bejelentkezve Azure-fiókjába, kövesse a megjelenő utasításokat ehhez. 

6. Válassza ki az Azure-előfizetését. 

7. Válassza ki az IoT hub. 

## <a name="create-a-device"></a>Eszköz létrehozása

1. A Visual STUDIO Code Explorer bontsa ki a **Azure IoT Hub-eszközöknek** szakasz. 

2. Kattintson a **...**  a a **Azure IoT Hub-eszközöknek** szakaszfejléc. Ha nem látja a három ponttal, mutat a fejlécben. 

3. Válassza ki **IoT peremhálózati eszköz létrehozása**. 

4. Megnyitja a szövegmezőben adjon az eszköz egy. 

A kimeneti képernyőn tekintse meg a parancs eredménye. Az eszközinformáció nyomtatva, mely tartalmazza a **deviceId** megadott és a **connectionString** , hogy segítségével a fizikai eszköz csatlakoztatása az IoT hub. 

## <a name="view-all-devices"></a>Minden eszközök megtekintése

Minden olyan eszközre, az IoT hub-hez felsorolt a **Azure IoT Hub-eszközöknek** a Visual Studio Code Explorer szakasza. IoT peremhálózati megkülönböztethető külön ikonja, és azt a tényt, hogy azok bővíthető megjelenítése az egyes eszközre telepített modulok nem peremhálózati eszközök tartoznak. 

   ![Visual STUDIO Code eszközök megtekintése](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>A kapcsolat-karakterlánc beolvasása

Amikor készen áll az eszköz beállításához, a kapcsolati karakterlánc, amely összekapcsolja a fizikai eszköz, az ad meg az IoT hub szüksége.

1. Kattintson a jobb gombbal az eszközt az azonosító a **Azure IoT Hub-eszközöknek** szakasz. 
2. Válassza ki **másolja eszköz kapcsolati karakterláncot**.

   A kapcsolati karakterláncot a vágólapra másolja. 

Igény szerint kiválaszthatja **eszköz információ** a helyi menüben a eszközinformáció megtekintéséhez, beleértve a kapcsolati karakterláncot a kimeneti ablakban. 


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [modulok telepítése egy eszközön keresztül a Visual Studio Code](how-to-deploy-modules-vscode.md).

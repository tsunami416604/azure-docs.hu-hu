---
title: Azure IoT-eszközkészlet használata a VS Code Azure IoT Hub létrehozása |} A Microsoft Docs
description: Hogyan hozzon létre egy IoT hubot a VS Code Azure IoT-eszközkészlet használatával.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: 71b188443ee48d742b14753cd5526edac493d9e3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369134"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>A Visual Studio Code az Azure IoT-eszközkészlet használatával IoT hub létrehozása

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Használhat [a Visual Studio Code az Azure IoT-eszközkészlet](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) létrehozása az Azure IoT hubs. Ez a cikk bemutatja, hogyan hozhat létre egy IoT hubra az Azure IoT-eszközkészlet.

Ez a cikk a következőkre lesz szüksége:

* Aktív Azure-fiók.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT-eszközkészlet](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

1. A Visual Studio Code-ban nyissa meg a **Explorer** megtekintése.

2. Az Explorer alján bontsa ki a **Azure IoT Hub-eszközök** szakaszban. 

   ![Bontsa ki az Azure IoT Hub-eszközök](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Kattintson a **...**  a a **Azure IoT Hub-eszközök** szakaszcímben. Ha nem látja a három pontra, a kurzort a fejléc. 

4. Válasszon **IoT Hub létrehozása**.

5. Egy előugró ablakban megjelenik a jobb alsó sarokban lehetővé teszi, hogy az Azure-ban először jelentkezzen be.

6. Azure-előfizetés kiválasztásához. 

7. Válasszon erőforráscsoportot.

8. Válassza ki azt a helyet.

9. Válassza ki a tarifacsomagot.

10. Adja meg az IoT Hub globálisan egyedi nevét.

11. Várjon néhány percet, amíg az IoT hubot létre kívánja hozni.

## <a name="next-steps"></a>További lépések

Most egy IoT hubot, a Visual Studio Code az Azure IoT-eszközkészlet használatával telepített, akkor érdemes vizsgálódáshoz:

* [Az eszközön, és az IoT Hub közötti üzenetek küldése és fogadása az Azure IoT-eszközkészlet bővítmény a Visual Studio Code használatával](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).
* [Wikioldal](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki) az Azure IoT-eszközkészlet.

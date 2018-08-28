---
title: Azure IoT-eszközkészlet használata a VS Code Azure IoT Hub létrehozása |} A Microsoft Docs
description: Hogyan hozzon létre egy IoT hubot a VS Code Azure IoT-eszközkészlet használatával.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: 5097d7e1f6a0b9e94919ccc8731702206c0a1568
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047233"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>A Visual Studio Code az Azure IoT-eszközkészlet használatával IoT hub létrehozása

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk bemutatja, hogyan használható a [a Visual Studio Code az Azure IoT-eszközkészlet](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) egy Azure IoT hub létrehozása. 

Ez a cikk a következőkre lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

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

Most már telepített egy IoT hubot, a Visual Studio Code az Azure IoT-eszközkészlet használatával. Vizsgálódáshoz, tekintse meg a következő cikkeket:

* [Az eszköz és a egy IoT Hub közötti üzenetek küldése és fogadása az Azure IoT-eszközkészlet bővítmény a Visual Studio Code használatával](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [A Visual Studio Code az Azure IoT-eszközkészlet bővítmény használata az Azure IoT Hub-Eszközfelügyelet](iot-hub-device-management-iot-toolkit.md)

* [Tekintse meg az Azure IoT-eszközkészlet wikioldal](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).

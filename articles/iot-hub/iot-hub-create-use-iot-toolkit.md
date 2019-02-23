---
title: Az Azure IoT Tools for VS Code Azure IoT Hub létrehozása |} A Microsoft Docs
description: Hogyan IoT hub létrehozása az Azure IoT-eszközök a VS Code használatával.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: c37eeec6429e8367ade12b58bb4e20022423edf6
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730359"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>A Visual Studio Code az Azure IoT-eszközök használatával IoT hub létrehozása

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk bemutatja, hogyan használható a [Azure IoT-eszközök a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) egy Azure IoT hub létrehozása. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a cikk a következőkre lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Az Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a Visual Studio Code.

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

Most már telepített egy IoT hubot, a Visual Studio Code az Azure IoT-eszközök használatával. Vizsgálódáshoz, tekintse meg a következő cikkeket:

* [Az eszköz és a egy IoT Hub közötti üzenetek küldése és fogadása az Azure IoT-eszközök a Visual Studio Code használatával](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Az Azure IoT-eszközök a Visual Studio Code használata az Azure IoT Hub-Eszközfelügyelet](iot-hub-device-management-iot-toolkit.md)

* [Tekintse meg az Azure IoT Hub-eszközkészlet wikioldal](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).

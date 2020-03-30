---
title: Hozzon létre egy Azure IoT Hubot az Azure IoT-eszközök vs-kódhoz használatával | Microsoft dokumentumok
description: Megtudhatja, hogyan használhatja az Azure IoT-eszközöket a Visual Studio-kódhoz egy Azure IoT-központ létrehozásához egy erőforráscsoportban.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912244"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>IoT-központ létrehozása az Azure IoT-eszközök a Visual Studio-kódhoz használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk bemutatja, hogyan használhatja az [Azure IoT-eszközök a Visual Studio-kód](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) hoz létre egy Azure IoT hub. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk befejezéséhez a következőkre van szükség:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

- [Visual Studio kód](https://code.visualstudio.com/)

- [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a Visual Studio-kódhoz.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

1. A Visual Studio-kódban nyissa meg az **Intéző** nézetet.

2. Az Intéző alján bontsa ki az **Azure IoT Hub-eszközök szakaszt.** 

   ![Az Azure IoT Hub-eszközök bővítése](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Kattintson a **...** az **Azure IoT Hub devices** szakasz fejlécében. Ha nem látja a három pontot, vigye az egérmutatót a fejléc fölé. 

4. Válassza **az IoT hub létrehozása**lehetőséget.

5. Egy előugró ablak jelenik meg a jobb alsó sarokban, amely lehetővé teszi, hogy először jelentkezzen be az Azure-ba.

6. Válassza az Azure-előfizetést. 

7. Erőforráscsoport kiválasztása.

8. Válassza ki a helyet.

9. Válassza ki a tarifacsomagot.

10. Adjon meg egy globálisan egyedi nevet az IoT Hub.

11. Várjon néhány percet, amíg létrejön az IoT Hub.

## <a name="next-steps"></a>További lépések

Most már üzembe helyezett egy IoT-központot az Azure IoT-eszközök a Visual Studio-kód használatával. A további felfedezéshez tekintse meg az alábbi cikkeket:

* [Az Azure IoT-eszközök a Visual Studio-kódhoz használatával üzeneteket küldhet és fogadhat az eszköz és az IoT Hub között.](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

* [Az Azure IoT-eszközök az Azure IoT Hub-hoz kód használata az Azure IoT Hub eszközkezeléséhez](iot-hub-device-management-iot-toolkit.md)

* [Tekintse meg az Azure IoT Hub for VS Code wikilapot.](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki)

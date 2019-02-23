---
title: Azure IoT Hub eszközök felhőalapú üzenetkezelése az Azure IoT-eszközök a Visual Studio Code |} A Microsoft Docs
description: Útmutató az Azure IoT-eszközök a Visual Studio Code használatával eszköz a felhőbe irányuló üzenetek figyeléséhez és a felhő küldeni az Azure IoT Hub eszköz üzeneteket.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 0455dafaa39f9f6bc19399fab31663d6b2e06e95
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726346"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Az eszközön, és az IoT Hub közötti üzenetek küldése és fogadása az Azure IoT-eszközök a Visual Studio Code használatával

![Végpontok közötti diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) egy hasznos, a Visual Studio Code-bővítmény, amely megkönnyíti az IoT Hub és IoT-alkalmazás fejlesztői. Ez a cikk foglalkozik, az eszköz és az IoT hub közötti üzenetek küldése és fogadása az Azure IoT-eszközök a Visual Studio Code használatával.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-will-learn"></a>Amiről tanulni fog

Megismerheti, hogyan használható az Azure IoT-eszközök a Visual Studio Code-eszközt a felhőbe irányuló üzenetek figyeléséhez és a felhőből az eszközre irányuló üzenetek küldéséhez. Eszköz – felhő üzeneteket lehet, hogy az eszköz gyűjt, és ezután küld az IoT hub érzékelőktől kapott adatok. Felhőből az eszközre irányuló üzenetek lehet, amely az IoT hub küld az eszközre, amely az eszköz csatlakozik LED kurzorvillogás parancsokat.

## <a name="what-you-will-do"></a>Mit fog

- Az Azure IoT-eszközök a Visual Studio Code használatával eszköz a felhőbe irányuló üzenetek figyeléséhez.
- Az Azure IoT-eszközök a Visual Studio Code használatával üzenetküldés a felhőből az eszközre.

## <a name="what-you-need"></a>Mi szükséges

- Aktív Azure-előfizetés.
- Az Azure IoT hub az előfizetéséhez.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Az Azure IoT-eszközök a VS Code alkalmazáshoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

1. A **Explorer** megtekintése a VS Code, bontsa ki a **Azure IoT Hub-eszközök** szakaszban a bal alsó sarokban.
1. Kattintson a **válassza ki az IoT Hub** helyi menüben.
1. Egy előugró ablakban megjelenik a jobb alsó sarokban lehetővé teszi, hogy az Azure-ban először jelentkezzen be.
1. Miután bejelentkezett, az Azure-előfizetési lista jelenik meg, majd válassza ki az Azure-előfizetés és az IoT Hub.
1. Az eszközlistában megjelenő **Azure IoT Hub-eszközök** lapon pár másodpercen belül.

   > [!Note]
   > A telepítést elvégezheti a **Set IoT Hub Connection String** (IoT Hub kapcsolati sztring beállítása) elemre kattintva is. Az IoT hub, amely az IoT-eszköz csatlakozik az előugró ablakban adja meg a kapcsolati karakterláncot.
   
## <a name="monitor-device-to-cloud-messages"></a>Eszköz a felhőbe irányuló üzenetek figyeléséhez

Az eszközről az IoT hubnak küldött üzenetek monitorozásához kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközt, és válassza ki **figyelési D2C üzenet Start**.
1. A figyelt üzenetek megjelenő **kimeneti** > **Azure IoT Hub-eszközkészlet** megtekintése.
1. Figyelés leállításához kattintson a jobb gombbal a **kimeneti** megtekintéséhez és kijelöléséhez **Stop Monitoring D2C üzenet**.

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Küldjön üzenetet az IoT hubhoz az eszközt, kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközt, és válassza ki **C2D-üzenet küldése eszközre**. 
1. Adja meg a beviteli mezőbe.
1. Eredmények megjelenő **kimeneti** > **Azure IoT Hub-eszközkészlet** megtekintése.

## <a name="next-steps"></a>További lépések

Bemutattuk, hogyan lehet az eszköz a felhőbe irányuló üzenetek figyeléséhez és a felhőből az eszközre irányuló üzenetküldés az IoT-eszköz és az Azure IoT Hub között.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

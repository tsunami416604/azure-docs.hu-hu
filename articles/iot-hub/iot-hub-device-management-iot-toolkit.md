---
title: Az Azure IoT-Eszközfelügyelet az Azure IoT-eszközök a Visual Studio Code |} A Microsoft Docs
description: Az Azure IoT-eszközök használata a Visual Studio Code az Azure IoT Hub eszközfelügyeleti közvetlen módszerek és lehetőségek az Ikereszköz kívánt tulajdonságait.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 03df2ceb2df4d857e48f1790703a1d87647e43d0
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445282"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Az Azure IoT-eszközök a Visual Studio Code használata az Azure IoT Hub-Eszközfelügyelet

![Végpontok közötti diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) egy hasznos, a Visual Studio Code-bővítmény, amely megkönnyíti az IoT Hub és IoT-alkalmazás fejlesztői. Felügyeleti lehetőségeket, amelyek segítségével különböző feladatok elvégzésére, tartalmaz.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Lehetőséget          | Tevékenység                    |
|----------------------------|--------------------------------|
| Közvetlen metódusok             | Hogy egy eszköz, például indítása vagy leállítása üzeneteket küldjenek vagy az eszköz újraindítása jár el.                                        |
| Olvasási ikereszköz           | Az eszköz a jelentett állapot beolvasása. Például az eszköz jelenti a LED most villogó-e.                                    |
| Ikereszköz frissítése         | Egy eszköz egyes állapotok, például a LED zöld vagy a telemetriai adatok küldési időköz – 30 percet kell helyezni.         |
| Felhőből az eszközre irányuló üzenetek   | Értesítések küldése egy eszközt. Például "nagyon valószínű, eső még ma. Ne felejtse el ahhoz, hogy egy szolgáltatáscsaládnak."              |

A különbségek ismertetése és útmutató az ezek a beállítások használatával, lásd: [eszközről a felhőbe való kommunikáció útmutatást](iot-hub-devguide-d2c-guidance.md) és [felhőből az eszközre irányuló kommunikáció útmutatást](iot-hub-devguide-c2d-guidance.md).

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. Az IoT Hub továbbra is fennáll, az eszközök ikereszköze a minden eszközön, amelyhez kapcsolódik hozzá. Ikereszközök kapcsolatos további információkért lásd: [ikereszközök – első lépések](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti az Azure IoT-eszközök a Visual Studio Code használata a fejlesztési számítógépén különböző felügyeleti lehetőségek.

## <a name="what-you-do"></a>TEENDŐ

Futtassa az Azure IoT-eszközök a Visual Studio Code különböző felügyeleti lehetőségek.

## <a name="what-you-need"></a>Mi szükséges

* Aktív Azure-előfizetés.
* Az Azure IoT hub az előfizetéséhez.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-eszközök a VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) vagy [a hivatkozás megnyitásához a Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

1. A **Explorer** megtekintése a VS Code, bontsa ki a **Azure IoT Hub-eszközök** szakaszban a bal alsó sarokban.

2. Kattintson a **válassza ki az IoT Hub** helyi menüben.

3. Egy előugró ablakban megjelenik a jobb alsó sarokban lehetővé teszi, hogy az Azure-ban először jelentkezzen be.

4. Miután bejelentkezett, az Azure-előfizetési lista jelenik meg, majd válassza ki az Azure-előfizetés és az IoT Hub.

5. Az eszközlistában megjelenő **Azure IoT Hub-eszközök** lapon pár másodpercen belül.

   > [!Note]
   > A telepítést elvégezheti a **Set IoT Hub Connection String** (IoT Hub kapcsolati sztring beállítása) elemre kattintva is. Az IoT hub, amely az IoT-eszköz csatlakozik az előugró ablakban adja meg a kapcsolati karakterláncot.

## <a name="direct-methods"></a>Közvetlen metódusok

1. Kattintson a jobb gombbal az eszközt, és válassza ki **közvetlen metódus meghívása**. 

2. Adja meg a metódus nevét és az adattartalom beviteli mezőbe.

3. Eredmények megjelenő **kimeneti** > **Azure IoT Hub-eszközkészlet** megtekintése.

## <a name="read-device-twin"></a>Olvasási ikereszköz

1. Kattintson a jobb gombbal az eszközt, és válassza ki **Ikereszköz szerkesztése**. 

2. Egy **azure-iot-device-twin.json** fájlt fogja megnyitni az ikereszköz tartalmát.

## <a name="update-device-twin"></a>Ikereszköz frissítése

1. Bizonyos szerkesztések, **címkék** vagy **properties.desired** mező.

2. Kattintson a jobb gombbal a **azure-iot-device-twin.json** fájlt.

3. Válassza ki **Ikereszköz frissítése** az ikereszköz frissíteni.

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Küldjön üzenetet az IoT hubhoz az eszközt, kövesse az alábbi lépéseket:
 
1. Kattintson a jobb gombbal az eszközt, és válassza ki **C2D-üzenet küldése eszközre**. 

2. Adja meg a beviteli mezőbe.

3. Eredmények megjelenő **kimeneti** > **Azure IoT Hub-eszközkészlet** megtekintése.

## <a name="next-steps"></a>További lépések

Bemutattuk, hogyan lehet Azure IoT Tools bővítmény a Visual Studio Code használata különböző felügyeleti lehetőségek.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

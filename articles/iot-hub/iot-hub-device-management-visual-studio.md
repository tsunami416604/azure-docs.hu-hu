---
title: Az Azure IoT-Eszközfelügyelet a Cloud Explorer a Visual Studióhoz |} A Microsoft Docs
description: A Cloud Explorer használata Visual Studióhoz készült Azure IoT Hub eszközfelügyeleti közvetlen módszerek és lehetőségek az Ikereszköz kívánt tulajdonságait.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: 87a0847f5d42e014f3b2691c96446892176b481b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269571"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>A Visual Studio Cloud Explorer használata az Azure IoT Hub-Eszközfelügyelet

![Végpontok közötti diagram](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[A cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) egy hasznos, a Visual Studio-bővítmény, amely lehetővé teszi az Azure-erőforrások megtekintése, azok tulajdonságait megvizsgálhatja és műveleteket kulcs Fejlesztőeszközök a Visual Studión belül. Felügyeleti lehetőségeket, amelyek segítségével különböző feladatok elvégzésére, tartalmaz.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Lehetőséget          | Tevékenység                    |
|----------------------------|--------------------------------|
| Közvetlen metódusok             | Hogy egy eszköz, például indítása vagy leállítása üzeneteket küldjenek vagy az eszköz újraindítása jár el.                                        |
| Olvasási ikereszköz           | Az eszköz a jelentett állapot beolvasása. Például az eszköz jelenti a LED most villogó-e.                                    |
| Ikereszköz frissítése         | Egy eszköz egyes állapotok, például a LED zöld vagy a telemetriai adatok küldési időköz – 30 percet kell helyezni.         |
| Felhőből az eszközre irányuló üzenetek   | Értesítések küldése egy eszközt. Például "nagyon valószínű, eső még ma. Ne felejtse el ahhoz, hogy egy szolgáltatáscsaládnak."              |

A különbségek ismertetése és útmutató az ezek a beállítások használatával, lásd: [eszközről a felhőbe való kommunikáció útmutatást](iot-hub-devguide-d2c-guidance.md) és [felhőből az eszközre irányuló kommunikáció útmutatást](iot-hub-devguide-c2d-guidance.md).

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. Az IoT Hub továbbra is fennáll, az eszközök ikereszköze a minden eszközön, amelyhez kapcsolódik hozzá. Ikereszközök kapcsolatos további információkért lásd: [ikereszközök – első lépések](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti a Cloud Explorer for Visual Studio használata a fejlesztési számítógépén különböző felügyeleti lehetőségek.

## <a name="what-you-do"></a>TEENDŐ

A Visual Studio Cloud Explorer futtatási különböző felügyeleti lehetőségekkel.

## <a name="what-you-need"></a>Mi szükséges

- Aktív Azure-előfizetéssel
- Az előfizetéshez tartozó Azure IoT Hub
- A Microsoft Visual Studio 2017 Update 8 vagy újabb
- Cloud Explorer összetevő a Visual Studio telepítőjének (az Azure számítási feladatok alapértelmezés szerint kiválasztva)

## <a name="update-cloud-explorer-to-latest-version"></a>Cloud Explorer legújabb verziójára való frissítéséhez

A Cloud Explorer az összetevőt a Visual Studio telepítőjének csak támogatja az eszközről a felhőbe és a felhőből az eszközre irányuló üzenetek monitorozását. Töltse le és telepítse a legújabb kell [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) a kezelési lehetőségek eléréséhez.

## <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT Hub eléréséhez

1. A Visual Studióban **Cloud Explorer** ablakban kattintson a felhasználóifiók-kezelés ikonra. Megnyithatja a Cloud Explorer ablakot **nézet** > **Cloud Explorer** menü.

    ![Kattintson a fiók kezelése](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Kattintson a **fiókok kezelése** a Cloud Explorerben.
1. Kattintson a **-fiók hozzáadása...**  az Azure-ban először jelentkezzen be az új ablakban.
1. Miután bejelentkezett, megjelenik az Azure-előfizetési lista. Válassza ki az Azure-előfizetések megtekintése, és kattintson a kívánt **alkalmaz**.
1. Bontsa ki a **az előfizetés** > **IoT-központok** > **az IoT Hub**, az IoT Hub csomópont alatt megjelenik az eszközlista. Kattintson a jobb gombbal a felügyeleti beállítások egy eszköz.

    ![Felügyeleti beállítások](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>Közvetlen metódusok

1. Kattintson a jobb gombbal az eszközt, és válassza ki **eszköz közvetlen metódus meghívása**.
1. Adja meg a metódus nevét és az adattartalom beviteli mezőbe.
1. Az eredmények megjelennek a **az IoT Hub** tesztkimenet ablaktáblán.

## <a name="read-device-twin"></a>Olvasási ikereszköz

1. Kattintson a jobb gombbal az eszközt, és válassza ki **Ikereszköz szerkesztése**.
1. Egy **azure-iot-device-twin.json** fájlt fogja megnyitni az ikereszköz tartalmát.

## <a name="update-device-twin"></a>Ikereszköz frissítése

1. Bizonyos szerkesztések, **címkék** vagy **properties.desired** mezőt a **azure-iot-device-twin.json** fájlt.
1. Nyomja meg **Ctrl + S** az ikereszköz frissíteni.
1. Az eredmények megjelennek a **az IoT Hub** tesztkimenet ablaktáblán.

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Küldjön üzenetet az IoT hubhoz az eszközt, kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközt, és válassza ki **C2D-üzenet küldése**.
1. Adja meg a beviteli mezőbe.
1. Az eredmények megjelennek a **az IoT Hub** tesztkimenet ablaktáblán.

## <a name="next-steps"></a>További lépések

Bemutattuk, hogyan lehet a Visual Studio Cloud Explorer használata különböző felügyeleti lehetőségek.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
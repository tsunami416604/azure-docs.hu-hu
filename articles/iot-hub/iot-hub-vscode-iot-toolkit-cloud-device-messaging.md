---
title: Az Azure IoT-eszközök vscode-hoz használata az INFORMATIKAI központ üzenetküldésének kezelőjéhez
description: Megtudhatja, hogy miként használhatja az Azure IoT-eszközöket a Visual Studio-kódhoz az eszköz felhőalapú üzenetekre történő figyelésére és felhőbe küldésére az Azure IoT Hubban.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 0b081229dcb382786fea03dff358b5cc47d77ee7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912019"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Az Azure IoT-eszközök a Visual Studio-kódhoz használatával üzeneteket küldhet és fogadhat az eszköz és az IoT Hub között

![Végpontok között diagram](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Az Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) egy hasznos Visual Studio-kódbővítmény, amely megkönnyíti az IoT Hub-kezelés és az IoT-alkalmazások fejlesztését. Ez a cikk arra összpontosít, hogyan használhatja az Azure IoT-eszközöket a Visual Studio-kódhoz az eszköz és az IoT hub közötti üzenetek küldésére és fogadására.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Mit fogsz tanulni

Megtudhatja, hogyan használhatja az Azure IoT-eszközöket a Visual Studio-kódhoz az eszközről a felhőbe irányuló üzenetek figyelésére és a felhőből az eszközre irányuló üzenetek küldéséhez. Az eszközről a felhőbe irányuló üzenetek lehetnek az eszköz által gyűjtött és az IoT hubba küldött érzékelőadatok. A felhőből az eszközre irányuló üzenetek olyan parancsok lehetnek, amelyeket az IoT hub küld az eszköznek, hogy villogjon egy, az eszközhöz csatlakoztatott LED-es.

## <a name="what-you-will-do"></a>Mit fog tenni?

* Az Azure IoT-eszközök a Visual Studio-kódhoz használatával figyelheti az eszközök ről a felhőbe irányuló üzeneteket.

* Az Azure IoT-eszközök a Visual Studio-kódhoz használatával felhőből az eszközre irányuló üzeneteket küldhet.

## <a name="what-you-need"></a>Mi szükséges

* Aktív Azure-előfizetés.

* Egy Azure IoT hub az előfizetés alatt.

* [Visual Studio kód](https://code.visualstudio.com/)

* [Az Azure IoT-eszközök vs-kódhoz,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) vagy [nyissa meg ezt a hivatkozást a Visual Studio-kódban.](vscode:extension/vsciot-vscode.azure-iot-tools)

## <a name="sign-in-to-access-your-iot-hub"></a>Bejelentkezés az IoT-központ eléréséhez

1. A VS-kód **Explorer** nézetében bontsa ki az **Azure IoT Hub-eszközök** szakaszt a bal alsó sarokban.

2. Kattintson **az IoT-központ kijelölése** parancsra a helyi menüben.

3. Egy előugró ablak jelenik meg a jobb alsó sarokban, amely lehetővé teszi, hogy először jelentkezzen be az Azure-ba.

4. Miután bejelentkezik, megjelenik az Azure-előfizetési lista, majd válassza az Azure Subscription és az IoT Hub lehetőséget.

5. Az eszközlista néhány másodpercen belül megjelenik az **Azure IoT Hub Devices** lapon.

   > [!Note]
   > A telepítést elvégezheti a **Set IoT Hub Connection String** (IoT Hub kapcsolati sztring beállítása) elemre kattintva is. Adja meg az IoT-központ **iothubowner** házirend-kapcsolati karakterláncát, amelyhez az IoT-eszköz csatlakozik az előugró ablakban.

## <a name="monitor-device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek figyelése

Az eszközről az IoT hubra küldött üzenetek figyeléséhez kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza **a Beépített eseményvégpont indítása parancsot.**

2. A figyelt üzenetek jelennek meg **a OUTPUT** > **Azure IoT Hub** nézetben.

3. A figyelés leállításához kattintson a jobb gombbal a **OUTPUT** nézetre, és válassza **a Beépített eseményvégpont figyelésének leállítása parancsot.**

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Ha üzenetet szeretne küldeni az IoT hubról az eszközére, kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza **a C2D-üzenet küldése az eszközre parancsot.**

2. Írja be az üzenetet a beviteli mezőbe.

3. Az eredmények az **AZURE** > **Azure IoT Hub** nézetben jelennek meg.

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan figyelheti az eszközről a felhőbe irányuló üzeneteket, és küldhet felhőből az eszközre üzeneteket az IoT-eszköz és az Azure IoT Hub között.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
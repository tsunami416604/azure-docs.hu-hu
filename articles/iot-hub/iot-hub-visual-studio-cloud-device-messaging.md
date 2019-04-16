---
title: Azure IoT Hub for Visual Studio Cloud Explorer üzenetküldés felhőből az eszközre kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan eszköz a felhőbe irányuló üzenetek figyeléséhez és a felhő küldhet üzeneteket az Azure IoT Hub for Visual Studio Cloud Explorer használatával.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571311"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>A Visual Studio Cloud Explorer használatával az eszközön, és az IoT Hub közötti üzenetek küldése és fogadása

![Végpontok közötti diagram](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[A cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) egy hasznos, a Visual Studio-bővítmény, amely lehetővé teszi az Azure-erőforrások megtekintése, azok tulajdonságait megvizsgálhatja és műveleteket kulcs Fejlesztőeszközök a Visual Studión belül. Ez a cikk a Cloud Explorer használata az eszköz és az IoT Hub közötti üzenetek küldése és fogadása az összpontosít.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Amiről tanulni fog

Megtudhatja, hogyan használható a Visual Studio Cloud Explorer eszköz a felhőbe irányuló üzenetek figyeléséhez és a felhőből az eszközre irányuló üzenetek küldéséhez. Eszköz – felhő üzeneteket lehet, hogy az eszköz gyűjt, és ezután küld az IoT Hub érzékelőktől kapott adatok. Felhőből az eszközre irányuló üzenetek lehet parancsokat, amelyek az eszközt az IoT Hub küldi. Ha például kurzorvillogás LED, amely az eszköz csatlakozik.

## <a name="what-you-will-do"></a>Mit fog

- Használja a Visual Studio Cloud Explorer eszköz a felhőbe irányuló üzenetek figyeléséhez.
- A Visual Studio Cloud Explorer használatával üzenetküldés a felhőből az eszközre.

## <a name="what-you-need"></a>Mi szükséges

- Aktív Azure-előfizetés.
- Az Azure IoT Hub az előfizetéséhez.
- A Microsoft Visual Studio 2017 Update 8 vagy újabb
- Cloud Explorer összetevő a Visual Studio telepítőjének (az Azure számítási feladatok alapértelmezés szerint kiválasztva)

## <a name="update-cloud-explorer-to-latest-version"></a>Cloud Explorer legújabb verziójára való frissítéséhez

A Cloud Explorer az összetevőt a Visual Studio telepítőjének csak támogatja az eszközről a felhőbe és a felhőből az eszközre irányuló üzenetek monitorozását. Annak érdekében, hogy üzeneteket küld az eszköz vagy a felhőben, töltse le és telepítse a legújabb [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT Hub eléréséhez

1. A Visual Studióban **Cloud Explorer** ablakban kattintson a felhasználóifiók-kezelés ikonra. Megnyithatja a Cloud Explorer ablakot **nézet** > **Cloud Explorer** menü.

    ![Kattintson a fiók kezelése](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)


2. Kattintson a **fiókok kezelése** a Cloud Explorerben.

3. Kattintson a **-fiók hozzáadása...**  az Azure-ban először jelentkezzen be az új ablakban.

4. Miután bejelentkezett, megjelenik az Azure-előfizetési lista. Válassza ki az Azure-előfizetések megtekintése, és kattintson a kívánt **alkalmaz**.

5. Bontsa ki a **az előfizetés** > **IoT-központok** > **az IoT Hub**, az IoT Hub csomópont alatt megjelenik az eszközlista.

    ![Eszközök listája](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Eszköz a felhőbe irányuló üzenetek figyeléséhez

Az eszközről az IoT hubnak küldött üzenetek monitorozásához kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az IoT Hub vagy az eszköz, és válassza ki **figyelési D2C üzenet Start**.

    ![D2C üzenet Monitorozásának megkezdése](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

2. A figyelt üzenetek megjelennek a **az IoT Hub** tesztkimenet ablaktáblán.

    ![Figyelési D2C állapotüzenetének eredménye](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

3. Leállíthatja a figyelést, kattintson a jobb gombbal az IoT Hub-vagy eszközt, és válassza ki **Stop Monitoring D2C üzenet**.

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Küldjön üzenetet az IoT hubhoz az eszközt, kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközt, és válassza ki **C2D-üzenet küldése**.

    ![C2D üzenet küldése](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

2. Adja meg a beviteli mezőbe.

3. Az eredmények megjelennek a **az IoT Hub** tesztkimenet ablaktáblán.

    ![Küldés C2D állapotüzenetének eredménye](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>További lépések

Bemutattuk, hogyan lehet az eszköz a felhőbe irányuló üzenetek figyeléséhez és a felhőből az eszközre irányuló üzenetküldés az IoT-eszköz és az Azure IoT Hub között.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
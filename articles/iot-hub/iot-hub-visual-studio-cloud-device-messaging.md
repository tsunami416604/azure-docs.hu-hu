---
title: Az Azure IoT Hub eszközüzenet-kezelésének kezelése a VS Cloud Explorer használatával
description: Megtudhatja, hogy miként figyelheti az eszközt a felhőbe küldött eszközök, és hogyan küldhet felhőt az eszközre az Azure IoT Hubban.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079480"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Üzenetek küldése és fogadása a Visual Studio felhőkezelőjében az eszköz és az IoT Hub között

![Végpontok között diagram](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[A Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) egy hasznos Visual Studio-bővítmény, amely lehetővé teszi az Azure-erőforrások megtekintését, tulajdonságaik vizsgálatát és a Visual Studio kulcsfontosságú fejlesztői műveletek elvégzését. Ez a cikk arra összpontosít, hogy a Cloud Explorer használatával hogyan küldhet és fogadhat üzeneteket az eszköz és a központ között.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Ebből a cikkből megtudhatja, hogy miként figyelheti az eszközről a felhőbe irányuló üzeneteket a Cloud Explorer for Visual Studio alkalmazásával, és hogyan küldhet felhőből az eszközre irányuló üzeneteket. Az eszközről a felhőbe irányuló üzenetek lehetnek az eszköz által gyűjtött és az IoT Hubba küldött érzékelőadatok. A felhőből az eszközre irányuló üzenetek lehetnek az IoT Hub által az eszközre küldött parancsok. Például villogjon egy LED, amely csatlakozik a készülékhez.

## <a name="what-you-do"></a>Mit csinálsz

Ebben a cikkben a következő feladatokat kell elvégeznie:

- A Cloud Explorer for Visual Studio segítségével figyelheti az eszközök közötti üzeneteket.

- A Cloud Explorer for Visual Studio segítségével küldhet felhőből eszközre irányuló üzeneteket.

## <a name="what-you-need"></a>Mi szükséges

A következő előfeltételekre van szüksége:

- Aktív Azure-előfizetés.

- Egy Azure IoT Hub az előfizetés alatt.

- Microsoft Visual Studio 2017 Update 9 vagy újabb verzió. Ez a cikk a [Visual Studio 2019-et](https://www.visualstudio.com/vs/)használja.

- A Visual Studio Installer Felhőkezelő-összetevője, amely alapértelmezés szerint az Azure Workload szolgáltatással van kiválasztva.

## <a name="update-cloud-explorer-to-latest-version"></a>A Cloud Explorer frissítése a legújabb verzióra

A Visual Studio Installer for Visual Studio 2017 Cloud Explorer összetevője csak az eszközről a felhőbe és a felhőből az eszközre irányuló üzenetek figyelését támogatja. A Visual Studio 2017 használatához töltse le és telepítse a legújabb [Cloud Explorert.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Bejelentkezés a hub eléréséhez

A hub eléréséhez kövesse az alábbi lépéseket:

1. A Visual Studio alkalmazásban válassza a**Felhőkezelő** **megtekintése** > lehetőséget a Cloud Explorer megnyitásához.

1. Az előfizetések megjelenítéséhez válassza a Fiókkezelés ikont.

    ![Fiókkezelés ikon](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Ha be van jelentkezve az Azure-ba, a fiókok jelennek meg. Ha először szeretne bejelentkezni az Azure-ba, válassza a **Fiók hozzáadása lehetőséget.**

1. Válassza ki a használni kívánt Azure-előfizetéseket, és válassza **az Alkalmaz**lehetőséget.

1. Bontsa ki az előfizetést, majd **bontsa ki az IoT Hubs csomópontot.**  Az egyes hubok alatt láthatja az adott hubeszközeit.

    ![Eszközlista](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek figyelése

Az eszközről az IoT Hubba küldött üzenetek figyeléséhez kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az IoT Hubra vagy az eszközre, és válassza **a D2C-üzenet figyelésének indítása parancsot.**

    ![D2C-üzenet figyelésének megkezdése](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. A figyelt üzenetek a **Kimenet csoportban**jelennek meg.

    ![D2C üzenet eredményének figyelése](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. A figyelés leállításához kattintson a jobb gombbal bármelyik IoT Hubra vagy eszközre, és válassza **a D2C-üzenet figyelésének leállítása parancsot.**

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Ha üzenetet szeretne küldeni az IoT Hubról az eszközére, kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza **a C2D-üzenet küldése parancsot.**

1. Írja be az üzenetet a beviteli mezőbe.

    ![C2D-üzenet küldése](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Az eredmények a **Kimenet**csoportban jelennek meg.

    ![C2D-üzenet eredménynek küldése](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan figyelheti az eszközről a felhőbe irányuló üzeneteket, és küldhet felhőből az eszközre üzeneteket az IoT-eszköz és az Azure IoT Hub között.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
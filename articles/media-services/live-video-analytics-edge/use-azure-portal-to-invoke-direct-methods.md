---
title: A Azure Portal használata közvetlen metódusok meghívásához
description: Ez a cikk áttekintést nyújt a Azure Portal használatával közvetlen metódusok meghívásához.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: 763dd82c8263a5e180468f9fbd7f86526295a80d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87279287"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>A Azure Portal használata közvetlen metódusok meghívásához

IoT Hub lehetővé teszi [közvetlen metódusok](/azure/iot-hub/iot-hub-devguide-direct-methods#method-invocation-for-iot-edge-modules) meghívását a felhőből származó peremhálózati eszközökön. Az élő videó elemzése IoT Edge (LVA) modul számos [közvetlen módszert](/azure/media-services/live-video-analytics-edge/direct-methods) tesz elérhetővé, amelyek segítségével meghatározhatja, telepítheti és létrehozhatja az élő videók elemzésére szolgáló különböző munkafolyamatokat.

Ebből a cikkből megtudhatja, hogyan hívhat meg közvetlen metódusú hívásokat az élő videók elemzéséhez IoT Edge modulhoz a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

* A Microsoft Edge-eszközén futó IoT Edge-modul élő videó-elemzését a gyors útmutató [: élő videó analytics IoT Edge](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart) vagy a portál használatával végezheti el [.](/azure/media-services/live-video-analytics-edge/deploy-iot-edge-device)

* Megismerheti az [élő videók elemzését](/azure/media-services/live-video-analytics-edge/overview) és [a Media Graph koncepcióját](/azure/media-services/live-video-analytics-edge/media-graph-concept).

## <a name="invoking-direct-methods-via-azure-portal"></a>Közvetlen metódusok meghívása Azure Portal használatával

A LVA modul által elérhető [közvetlen metódusok](/azure/media-services/live-video-analytics-edge/direct-methods) mindegyike Azure Portal használatával hívható meg. Az alábbi lépések egy közvetlen metódus részleteit tartalmazzák. Más közvetlen metódusokat hasonló lépések használatával hívhat meg. Az egyes közvetlen metódusoknak azonban egy adott JSON-törzsre van szükségük.

A `GraphTopologyList` metódus hívásával lekérheti az aktuálisan telepített gráf-topológiák listáját IoT Edge modulon. A következő lépésekkel hívhatja meg ezt a közvetlen metódust:

1. Bejelentkezés Azure Portal
1. Keresse meg a megfelelő erőforráscsoportot a portál kezdőlapján a IoT Hub megkereséséhez, vagy ha tudja, hogy IoT Hub, válassza ki.
    ![erőforráscsoport a portál kezdőlapján](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. A IoT Hub lapon a különböző eszközök azonosítóinak listázásához válassza a IoT Edge lehetőséget az automatikus eszközkezelés területen. Válassza ki a megfelelő eszköz-azonosítót az eszközön futó modulok listázásához.
    ![IOT hub lapja](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. Válassza ki az élő videó-elemzést IoT Edge modulban a konfigurációs oldalának megjelenítéséhez.<br><br>
    ![Válassza ki az élő videó-elemzést IoT Edge modulban a konfigurációs oldalának megjelenítéséhez](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. Válassza a közvetlen metódus menüpontot. <br><br>
    ![Kattintson a közvetlen metódus menüpontra](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > Előfordulhat, hogy az aktuális oldalon látható módon hozzá kell adnia egy értéket a kapcsolódási karakterlánc szakaszban. A beállítás neve szakaszban nem kell elrejtenie vagy módosítania a semmit. Az OK, hogy nyilvánosak legyenek.

1. Írja *GraphTopologyList* be a GraphTopologyList **nevet a metódus neve** mezőbe.
1. Másolja és illessze be az alábbi JSON-t a **hasznos** adatok mezőben.
    ```json
    {
    "@apiVersion":
    }
    ```
1. Kattintson a **metódus meghívása** gombra az oldal tetején.<br><br>
    ![metódus meghívása gomb](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. Az **eredmény** területén az 200-es állapotú üzenetnek kell megjelennie.<br><br>
    ![kapcsolat időtúllépése](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>Válaszok

| Feltétel             | Állapotkód | Részletes hibakód |
|-----------------------|-------------|---------------------|
| Success               | 200         | n.a.                 |
| Általános felhasználói hibák   | 400 tartomány   |                     |
| Általános kiszolgálói hibák | 500 tartomány   |                     |

## <a name="next-steps"></a>További lépések

A [közvetlen metódusok](/azure/media-services/live-video-analytics-edge/direct-methods) oldalon további közvetlen módszerek is megtalálhatók.

> [!NOTE]
> Egy gráf-példány egy adott topológiát hoz létre, ezért győződjön meg arról, hogy rendelkezik a megfelelő topológiával a Graph-példány létrehozása előtt.

Rövid útmutató [: a mozgásérzékelők kibocsátási eseményeinek észlelése](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart) jól használható a közvetlen metódusok által kezdeményezett hívások pontos sorrendjének megismeréséhez.

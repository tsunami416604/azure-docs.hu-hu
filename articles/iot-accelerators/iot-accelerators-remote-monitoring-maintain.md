---
title: Riasztások használata és eszközökkel kapcsolatos problémák megoldása a távoli monitorozási megoldásban – Azure | Microsoft Docs
description: Ez az oktatóanyag azt mutatja be, hogyan használhatja a riasztásokat a távoli monitorozási megoldásgyorsítóhoz csatlakoztatott eszközökkel kapcsolatos problémák azonosítására és megoldására.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/18/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 9607705220450b30d2ffaf0f2be9fa2a5664b879
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081788"
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Eszközökkel kapcsolatos problémák észlelése és elhárítása

Ebben az oktatóanyagban a távoli monitorozási megoldásgyorsítóval fogja azonosítani és orvosolni a csatlakoztatott IoT-eszközökkel kapcsolatos problémákat. Riasztásokat fog használni a megoldásgyorsító irányítópultján a problémák azonosításához, amelyeket távoli feladatok futtatásával fog megoldani.

A Contoso egy új **Prototype** (Prototípus) eszközt tesztel. Ön Contoso-üzemeltetőként a tesztelés során észreveszi, hogy a **Prototype** eszköz váratlanul hőmérsékleti riasztást aktivál az irányítópulton. Meg kell vizsgálnia a hibás **Prototype** eszköz viselkedését, és meg kell oldania a problémát.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Eszköz által kiváltott riasztás vizsgálata
> * Az eszközzel kapcsolatos probléma megoldása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag követéséhez rendelkeznie kell a távoli monitorozási megoldásgyorsító üzembe helyezett példányával az Azure-előfizetésében.

Ha még nem helyezte üzembe a távoli monitorozási megoldásgyorsítót, végezze el [Távoli felhőalapú monitorozási megoldás üzembe helyezése](quickstart-remote-monitoring-deploy.md) rövid útmutatóban leírtakat.

## <a name="investigate-an-alert"></a>Riasztás vizsgálata

Ön észreveszi, hogy a **Dashboard** (Irányítópult) lapon a **Prototype** eszközzel társított szabály által kiváltott, nem várt hőmérsékleti riasztások jelennek meg:

[![Az irányítópulton megjelenő riasztások](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

A probléma további vizsgálatához válassza a riasztás mellett található, **riasztás vizsgálatára** szolgáló gombot:

[![Riasztás vizsgálata az irányítópulton](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

A riasztás részletes nézetében a következők láthatók:

* A riasztás kiváltásának ideje
* A riasztáshoz társított eszközök állapotinformációi
* A riasztáshoz társított eszközökről származó telemetria

[![Riasztás részletei](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

A riasztás nyugtázásához jelölje ki az összes jelölőnégyzetet az **Alert occurrences** (Riasztás előfordulásai) alatt, majd válassza az **Acknowledge** (Nyugtázás) lehetőséget. Ezzel a művelettel tudatja a többi üzemeltetővel, hogy látta a riasztást, és dolgozik rajta.

[![Riasztások nyugtázása](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

A riasztás nyugtázása után az előfordulás állapota **Acknowledged** (Nyugtázva) állapotúra módosul.

A listában látható a hőmérsékleti riasztást aktiváló **Prototype** eszköz:

[![A riasztás kiváltó eszközök listája](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>A probléma megoldása

A **Prototype** eszközzel kapcsolatos probléma megoldásához meg kell hívnia a **DecreaseTemperature** metódust az eszközön.

Az eszközök listájából válassza ki az eszközt, amelyen művelet szeretne végrehajtani, majd válassza a **Jobs** (Feladatok) lehetőséget. A **Prototype** eszközmodell hat metódust határoz meg, amelyeket az eszköznek támogatnia kell:

[![Az eszköz által támogatott metódusok megtekintése](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

Válassza a **DecreaseTemperature** elemet, majd a feladat nevét állítsa **DecreaseTemperature** értékre. Ezután kattintson az **Apply** (Alkalmaz) gombra:

[![A feladat létrehozása a hőmérséklet csökkentéséhez](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

A feladat állapotának nyomon követéséhez kattintson a **View job status** (Feladat állapotának megtekintése) elemre. A **Jobs** (Feladatok) nézetben nyomon követheti a megoldás összes feladatát és metódushívását:

[![A hőmérsékletet csökkentő feladat monitorozása](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

Az eszköz hőmérsékletének csökkenését a telemetria megtekintésével ellenőrizheti a **Dashboard** (Irányítópult) lapon:

[![A hőmérséklet csökkenésének megtekintése](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan azonosíthatja az eszközökkel kapcsolatos problémákat a riasztások alapján, és hogyan hajthat végre műveleteket ezeken az eszközökön a probléma megoldása érdekében. Ha tudni szeretné, hogyan csatlakoztatható egy fizikai eszköz a megoldásgyorsítóhoz, lépjen tovább az ezzel kapcsolatos útmutatókhoz.

Most, hogy már tisztában van vele, hogyan kezelheti az eszközökkel kapcsolatos problémákat, a javasolt következő lépés annak megismerése, hogyan [csatlakoztatható az eszköz a távoli monitorozási megoldásgyorsítóhoz](iot-accelerators-connecting-devices.md).

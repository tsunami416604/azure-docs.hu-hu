---
title: Eszközök távoli felügyeleti megoldásba - Azure hibáinak elhárítása |} Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hibaelhárítása, és javíthatja a távoli felügyeleti megoldásba eszközökkel kapcsolatos problémákat.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 1e6c74b5c952d5a387dbdac4d6e81d691758fb36
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Hibaelhárítás és szervizelheti azokat a eszközökkel kapcsolatos problémákat

Az oktatóanyag bemutatja, hogyan használható a **karbantartási** lap hibaelhárítása, és javíthatja a eszközökkel kapcsolatos problémákat a megoldásban. Ezek a képességek szemléltetésére az oktatóprogram egy olyan forgatókönyvet a Contoso IoT-alkalmazásban.

A Contoso egy új teszteli **prototípus** eszköz a mezőben. Contoso-felelősként, megfigyelheti, hogy a tesztelés során a **prototípus** eszköz váratlanul van időt. az irányítópult egy hőmérséklet riasztás. Most vizsgálja ki a hibás viselkedését **prototípus** eszköz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * Használja a **karbantartási** vizsgálja meg a riasztás lap
> * A probléma megoldásáról eszköz metódusának hívása

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag van szükség a távoli felügyeleti megoldás telepített példányát az Azure-előfizetésben.

Ha még nem telepítette a távoli figyelési megoldást igényelnek, még el kell végeznie a [telepíteni a távoli felügyeleti megoldásgyorsító](iot-accelerators-remote-monitoring-deploy.md) oktatóanyag.

## <a name="use-the-maintenance-dashboard"></a>A karbantartási irányítópult

A a **irányítópult** lap bizonyára észrevette, hogy nincsenek a társított szabály váratlan hőmérséklet riasztásait a **prototípus** eszközöket:

![Értesítések megjelenítése az irányítópulton](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm.png)

Vizsgálja meg a probléma, válassza ki a **felfedezés riasztás** beállítás mellett a riasztás:

![Riasztás megismerkedhet az irányítópultról](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm.png)

A riasztás részletes nézete látható:

* Ha a-riasztás működésbe lépett
* Állapotinformációk kapcsolatban a riasztáshoz tartozó eszközök
* A riasztáshoz tartozó eszközökről telemetriai adat

![Riasztás részletei](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail.png)

Megerősíti a riasztásra, válassza ki a **előfordulások riasztási** válassza **nyugtázási**. Ez a művelet lehetővé teszi, hogy más operátorokkal, hogy a riasztás láthatta, és dolgozunk a probléma.

![Az értesítések nyugtázása](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge.png)

Megerősíti a riasztást, amikor a bekövetkezésének állapota **Visszaigazolva**.

A listában megtekintheti a **prototípus** felelős a hőmérséklet riasztást kiváltó eszköz:

![Az eszközök, amely a riasztás felsorolása](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>A probléma megoldásáról

Kijavítani a problémát a **prototípus** eszköz, meg kell hívnia a **DecreaseTemperature** metódus az eszközön.

Segítségével az eszközön, az eszközök listájában válassza ki, és válassza a **feladatok**. A **prototípus** eszközmodell eszköz támogatnia kell a hat módszer megadása:

![Az eszköz támogatja a módszerek megtekintése](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods.png)

Válasszon **DecreaseTemperature** , és a feladat neve **DecreaseTemperature**. Válassza a **alkalmaz**:

![A hőmérséklet csökkenti a feladat létrehozása](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob.png)

A feladat állapotának nyomon követheti a **karbantartási** lapon, válassza ki **feladatok**. Használja a **feladatok** nyomon követéséhez a feladatok megtekintése és a megoldás metódushívások:

![A hőmérséklet csökkenti a feladat figyeléséhez](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob.png)

Egy adott feladat vagy a metódus hívása részleteinek megtekintéséhez jelölje ki azt a listát a **feladatok** megtekintése:

![Feladatok részleteinek megjelenítése](./media/iot-accelerators-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Használja a **karbantartási** vizsgálja meg a riasztás lap
> * A probléma megoldásáról eszköz metódusának hívása

Most már rendelkezik megtudta, hogyan kezelheti az eszközökkel kapcsolatos problémákat, a javasolt következő lépésre megtudhatja, hogyan [a megoldás tesztelése szimulált eszközökkel](iot-accelerators-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->
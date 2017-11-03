---
title: "Eszközök távoli felügyeleti megoldásba - Azure hibáinak elhárítása |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan hibaelhárítása, és javíthatja a távoli felügyeleti megoldásba eszközökkel kapcsolatos problémákat."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/16/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: ac284126c2c940caf97fb95bd46234de49b1f678
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
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

Ha még nem telepítette a távoli figyelési megoldást igényelnek, még el kell végeznie a [a távoli felügyeleti előkonfigurált megoldás üzembe helyezéséhez](iot-suite-remote-monitoring-deploy.md) oktatóanyag.

## <a name="use-the-maintenance-dashboard"></a>A karbantartási irányítópult

Az a **irányítópult** bizonyára észrevette, hogy nincsenek a társított szabály érkező váratlan hőmérséklet riasztások lap a **prototípus** eszközöket:

![Riasztások megjelenítése az irányítópulton](media/iot-suite-remote-monitoring-maintain/dashboardalarm.png)

Vizsgálja meg a probléma, válassza ki a **felfedezés riasztás** beállítás mellett a riasztás:

![Riasztás megismerkedhet az irányítópultról](media/iot-suite-remote-monitoring-maintain/dashboardexplorealarm.png)

Most már megtekintheti a riasztások listáját a **karbantartási** lap:

![A karbantartási oldalon riasztások listája](media/iot-suite-remote-monitoring-maintain/maintenancealarms.png)

A riasztás részleteinek megjelenítéséhez kattintson a riasztás a **riasztások** listája. A Részletek nézetben látható:

* Ha lett elindítva. a riasztás
* A riasztás társított eszköz állapotinformációról
* A riasztás társított eszközökről telemetriai adat

![Riasztás részletei](media/iot-suite-remote-monitoring-maintain/maintenancealarmdetail.png)

A riasztás megerősíti, hogy válassza ki a **előfordulások riasztás** válassza **nyugtázási**. Ez a művelet lehetővé teszi, hogy más operátorokkal, hogy a riasztás láthatta, és dolgozunk a probléma.

![Megerősíti a riasztások](media/iot-suite-remote-monitoring-maintain/maintenanceacknowledge.png)

A listában megtekintheti a **prototípus** felelős a hőmérséklet riasztás kiváltó eszköz:

![Az eszközök, amely a riasztás felsorolása](media/iot-suite-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>A probléma megoldásáról

Kijavítani a problémát a **prototípus** eszköz, meg kell hívnia a **DecreaseTemperature** metódus az eszközön.

Segítségével az eszközön, az eszközök listájában válassza ki, és válassza a **ütemezés**. A **motor** eszközmodell eszköz támogatnia kell a három módszer megadása:

![Az eszköz támogatja a módszerek megtekintése](media/iot-suite-remote-monitoring-maintain/maintenancemethods.png)

Válasszon **DecreaseTemperature** , és a feladat neve **DecreaseTemperature**. Válassza a **alkalmaz**:

![A hőmérséklet csökkenti a feladat létrehozása](media/iot-suite-remote-monitoring-maintain/maintenancecreatejob.png)

A feladat állapotának nyomon követheti a **karbantartási** lapon, válassza ki **rendszerállapot**. Használja a **rendszerállapot** nyomon követéséhez a feladatok megtekintése és a megoldás metódushívások:

![A hőmérséklet csökkenti a feladat figyeléséhez](media/iot-suite-remote-monitoring-maintain/maintenancerunningjob.png)

Egy adott feladat vagy a metódus hívása részleteinek megtekintéséhez jelölje ki azt a listát a **rendszerállapot** megtekintése:

![Feladatok részleteinek megjelenítése](media/iot-suite-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag azt bemutatta, hogyan számára:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Használja a **karbantartási** vizsgálja meg a riasztás lap
> * A probléma megoldásáról eszköz metódusának hívása

Most már rendelkezik megtudta, hogyan kezelheti az eszközökkel kapcsolatos problémákat, a javasolt következő lépésre megtudhatja, hogyan [a megoldás tesztelése szimulált eszközökkel](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->
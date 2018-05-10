---
title: A távoli felügyeleti megoldás - Azure kezelés |} Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan csatlakozik a távoli felügyeleti megoldás eszközök kezelésére.
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
ms.openlocfilehash: d05b7ca2ab1d5b2f3d3fd3973eefe1b3ec5a1c04
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="manage-and-configure-your-devices"></a>Kezelése és az eszközök konfigurálása

Ez az oktatóanyag bemutatja az eszköz felügyeleti funkcióit biztosítja a távoli figyelési megoldást igényelnek. Ezek a képességek szemléltetésére az oktatóprogram egy olyan forgatókönyvet a Contoso IoT-alkalmazásban.

Contoso bontsa ki a kimeneti növeléséhez létesítményekben egyik új gépek van rendezve. Várja meg az új gépek kézbesíti, amíg a megoldás működésének ellenőrzéséhez a szimuláció futtatni kívánt. Kezelőként kívánt kezelése és konfigurálása az eszközök távoli felügyeleti megoldásba.

Eszközök konfigurálása és kezelése bővíthető megoldás biztosításához, a távoli felügyeleti megoldás az IoT-központ funkciókat használ, mint [feladatok](../iot-hub/iot-hub-devguide-jobs.md) és [módszerek közvetlen](../iot-hub/iot-hub-devguide-direct-methods.md). Egy eszköz fejlesztői hogyan módszerek megvalósítja a fizikai eszköz kapcsolatban lásd: [testre szabhatja a távoli felügyeleti megoldásgyorsító](iot-suite-remote-monitoring-customize.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * A szimulált eszköz kiépítése.
> * A szimulált eszköz tesztelése.
> * Eszköz metódushívások a megoldásból.
> * Konfigurálja újra az eszköz.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag van szükség a távoli felügyeleti megoldás telepített példányát az Azure-előfizetésben.

Ha még nem telepítette a távoli figyelési megoldást igényelnek, még el kell végeznie a [telepíteni a távoli felügyeleti megoldásgyorsító](iot-suite-remote-monitoring-deploy.md) oktatóanyag.

## <a name="add-a-simulated-device"></a>A szimulált eszköz hozzáadása

Keresse meg a **eszközök** a megoldás lapját, és válassza a **+ új eszköz**. Az a **új eszköz** panelen, kattintson a **szimulált**:

![A szimulált eszköz kiépítése](media/iot-suite-remote-monitoring-manage/devicesprovision.png)

Az eszközök számát hagyja beállítása rendelkezésre **1**. Válassza ki a **hibás motor** eszköz modell, és válassza a **alkalmaz** a szimulált eszköz létrehozásához:

![A szimulált motor eszköz kiépítése](media/iot-suite-remote-monitoring-manage/devicesprovisionengine.png)

Megtudhatja, hogyan lehet kiépíteni egy *fizikai* eszköz, lásd: [csatlakoztassa az eszközt a távoli felügyeleti megoldásgyorsító](iot-suite-connecting-devices-node.md).

## <a name="test-the-simulated-device"></a>A szimulált eszköz tesztelése

Az új szimulált eszköz részletes adatainak megtekintéséhez válassza ki a listából az eszközök a a **eszközök** lap. Az eszközzel kapcsolatos információkat jeleníti meg a **eszköz részletei** panel:

![Az új szimulált motor eszköz megtekintése](media/iot-suite-remote-monitoring-manage/devicesviewnew.png)

A **eszköz részletei**, ellenőrizze az új eszköz telemetriai adatokat küld. Az eszközről a különféle telemetriai adatfolyamok megtekintéséhez válassza a telemetriai adatok neve, mint **vibráció**:

![Válassza ki a telemetriai adatok adatfolyam megtekintése](media/iot-suite-remote-monitoring-manage/devicesvibration.png)

A **eszköz részletei** panel az eszköz, például előfizetéscímkék értékeit, a támogatott módszerek és a tulajdonságok a eszköz által jelzett más információkat jelenít meg.

Részletes diagnosztikai megtekintéséhez görgessen le a nézet **diagnosztika**.

## <a name="act-on-a-device"></a>Az eszközön működésre

Segítségével egy vagy több eszközön, az eszközök listájában jelölje ki őket, és válassza a **feladatok**. A **motor** eszközmodell eszköz támogatnia kell a három módszer megadása:

![Motor módszerek](media/iot-suite-remote-monitoring-manage/devicesmethods.png)

Válasszon **FillTank**, a feladat neve **FillEngineTank**, és válassza a **alkalmaz**:

![A restart metódust ütemezése](media/iot-suite-remote-monitoring-manage/devicesrestartengine.png)

A feladat állapotának nyomon követheti a **karbantartási** lapon, válassza ki **feladatok**:

![A figyelő az ütemezések feladat](media/iot-suite-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>Más eszközökön módszerek

Során a különböző szimulált eszköz típusa, láthatja, hogy más eszköztípusok esetében támogatja a különböző módszereket. A fizikai eszközök üzembe helyezése esetén az eszköz típusa határozza meg a módszerek támogatniuk kell az eszközt. Az eszköz fejlesztői általában a kódot, amely lehetővé teszi az eszköz működésének metódushívások válaszul fejlesztése felelős.

Ütemezés módszerét több eszközön futtassa, jelölje ki a listában több eszközre a a **eszközök** lap. A **feladatok** panelen látható metódus típusú gyakori megoldás, hogy a kiválasztott eszközök.

## <a name="reconfigure-a-device"></a>Konfigurálja újra az eszköz

Az eszköz a konfiguráció módosításához jelölje ki az eszközök listája a a **eszközök** lapon, majd kattintson a **feladatok**, és válassza a **konfigurálja újra a**. A feladatok panelen látható a kijelölt eszközt, amely módosíthatja a tulajdonságértékek:

![Konfigurálja újra az eszköz](media/iot-suite-remote-monitoring-manage/devicesreconfigure.png)

Olyan módosítást, vegye fel a feladatnak a nevét, a tulajdonságértékek frissítése, és válassza **alkalmaz**:

![Frissítés eszköz tulajdonság értéke](media/iot-suite-remote-monitoring-manage/devicesreconfigurephysical.png)

A feladat állapotának nyomon követheti a **karbantartási** lapon, válassza ki **feladatok**.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan számára:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * A szimulált eszköz kiépítése.
> * A szimulált eszköz tesztelése.
> * Eszköz metódushívások a megoldásból.
> * Konfigurálja újra az eszköz.

Most, hogy rendelkezik megismerte az eszközök kezeléséhez, a javasolt lépések megtudhatja, hogyan:

* [Hibaelhárítás és szervizelheti azokat a eszközökkel kapcsolatos problémákat](iot-suite-remote-monitoring-maintain.md).
* [A megoldás tesztelése szimulált eszközökkel](iot-suite-remote-monitoring-test.md).
* [Csatlakoztassa az eszközt a távoli felügyeleti megoldásgyorsító](iot-suite-connecting-devices-node.md).

<!-- Next tutorials in the sequence -->
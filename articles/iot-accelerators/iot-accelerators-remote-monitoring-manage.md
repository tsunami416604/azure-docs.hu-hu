---
title: A távoli felügyeleti megoldás - Azure kezelés |} Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan csatlakozik a távoli felügyeleti megoldás eszközök kezelésére.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 0f177c3a8746f801e52cdac6cb2189e9cc28e1e8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627279"
---
# <a name="manage-and-configure-your-devices"></a>Kezelése és az eszközök konfigurálása

Ez az oktatóanyag bemutatja az eszköz a távoli figyelésére szolgáló megoldás felügyeleti funkcióit. Ezek a képességek szemléltetésére az oktatóprogram egy olyan forgatókönyvet a Contoso IoT-alkalmazásban.

Contoso bontsa ki a kimeneti növeléséhez létesítményekben egyik új gépek van rendezve. Várja meg az új gépek kézbesíti, amíg a megoldás működésének ellenőrzéséhez a szimuláció futtatni kívánt. Kezelőként szeretné kezelni, és az eszközök konfigurálása a távoli figyelésére szolgáló megoldás.

Eszközök konfigurálása és kezelése bővíthető megoldás biztosításához, a távoli figyelésére szolgáló megoldás az IoT-központ funkciókat használ, mint [feladatok](../iot-hub/iot-hub-devguide-jobs.md) és [módszerek közvetlen](../iot-hub/iot-hub-devguide-direct-methods.md). Egy eszköz fejlesztői hogyan módszerek megvalósítja a fizikai eszköz kapcsolatban lásd: [testre szabhatja a távoli megfigyelési megoldásgyorsító](iot-accelerators-remote-monitoring-customize.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * A szimulált eszköz kiépítése.
> * A szimulált eszköz tesztelése.
> * Eszköz metódushívások a megoldásból.
> * Konfigurálja újra az eszköz.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag van szükség a távoli figyelésére szolgáló megoldás telepített példányát az Azure-előfizetésben.

Ha a távoli figyelésére szolgáló megoldás még nem telepítette még, el kell végeznie a [telepíteni a távoli megfigyelési megoldásgyorsító](iot-accelerators-remote-monitoring-deploy.md) oktatóanyag.

## <a name="add-a-simulated-device"></a>A szimulált eszköz hozzáadása

Keresse meg a **eszközök** a megoldás lapját, és válassza a **+ új eszköz**. Az a **új eszköz** panelen, kattintson a **szimulált**:

![A szimulált eszköz kiépítése](./media/iot-accelerators-remote-monitoring-manage/devicesprovision.png)

Az eszközök számát hagyja beállítása rendelkezésre **1**. Válassza ki a **hibás motor** eszköz modell, és válassza a **alkalmaz** a szimulált eszköz létrehozásához:

![A szimulált motor eszköz kiépítése](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine.png)

Megtudhatja, hogyan lehet kiépíteni egy *fizikai* eszköz, lásd: [csatlakoztassa az eszközt a távoli megfigyelési megoldásgyorsító](iot-accelerators-connecting-devices-node.md).

## <a name="test-the-simulated-device"></a>A szimulált eszköz tesztelése

Az új szimulált eszköz részletes adatainak megtekintéséhez válassza ki a listából az eszközök a a **eszközök** lap. Az eszközzel kapcsolatos információkat jeleníti meg a **eszköz részletei** panel:

![Az új szimulált motor eszköz megtekintése](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew.png)

A **eszköz részletei**, ellenőrizze az új eszköz telemetriai adatokat küld. Az eszközről a különféle telemetriai adatfolyamok megtekintéséhez válassza a telemetriai adatok neve, mint **vibráció**:

![Válassza ki a telemetriai adatok adatfolyam megtekintése](./media/iot-accelerators-remote-monitoring-manage/devicesvibration.png)

A **eszköz részletei** panel az eszköz, például előfizetéscímkék értékeit, a támogatott módszerek és a tulajdonságok a eszköz által jelzett más információkat jelenít meg.

Részletes diagnosztikai megtekintéséhez görgessen le a nézet **diagnosztika**.

## <a name="act-on-a-device"></a>Az eszközön működésre

Segítségével egy vagy több eszközön, az eszközök listájában jelölje ki őket, és válassza a **feladatok**. A **motor** eszközmodell eszköz támogatnia kell a három módszer megadása:

![Motor módszerek](./media/iot-accelerators-remote-monitoring-manage/devicesmethods.png)

Válasszon **FillTank**, a feladat neve **FillEngineTank**, és válassza a **alkalmaz**:

![A restart metódust ütemezése](./media/iot-accelerators-remote-monitoring-manage/devicesrestartengine.png)

A feladat állapotának nyomon követheti a **karbantartási** lapon, válassza ki **feladatok**:

![A figyelő az ütemezések feladat](./media/iot-accelerators-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>Más eszközökön módszerek

Során a különböző szimulált eszköz típusa, láthatja, hogy más eszköztípusok esetében támogatja a különböző módszereket. A fizikai eszközök üzembe helyezése esetén az eszköz típusa határozza meg a módszerek támogatniuk kell az eszközt. Az eszköz fejlesztői általában a kódot, amely lehetővé teszi az eszköz működésének metódushívások válaszul fejlesztése felelős.

Ütemezés módszerét több eszközön futtassa, jelölje ki a listában több eszközre a a **eszközök** lap. A **feladatok** panelen látható metódus típusú gyakori megoldás, hogy a kiválasztott eszközök.

## <a name="reconfigure-a-device"></a>Konfigurálja újra az eszköz

Az eszköz a konfiguráció módosításához jelölje ki az eszközök listája a a **eszközök** lapon, majd kattintson a **feladatok**, és válassza a **konfigurálja újra a**. A feladatok panelen látható a kijelölt eszközt, amely módosíthatja a tulajdonságértékek:

![Konfigurálja újra az eszköz](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure.png)

Olyan módosítást, vegye fel a feladatnak a nevét, a tulajdonságértékek frissítése, és válassza **alkalmaz**:

![Frissítés eszköz tulajdonság értéke](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical.png)

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

* [Hibaelhárítás és szervizelheti azokat a eszközökkel kapcsolatos problémákat](iot-accelerators-remote-monitoring-maintain.md).
* [A megoldás tesztelése szimulált eszközökkel](iot-accelerators-remote-monitoring-test.md).
* [Csatlakoztassa az eszközt a távoli megfigyelési megoldásgyorsító](iot-accelerators-connecting-devices-node.md).

<!-- Next tutorials in the sequence -->
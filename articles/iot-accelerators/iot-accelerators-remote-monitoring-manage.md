---
title: Eszközök konfigurálása a távoli figyelési megoldásban – Azure | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhatja a távoli figyelési megoldáshoz csatlakoztatott eszközöket.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: e4236952bd41c4955e337813ff6d706263b8ef47
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "73890907"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>Oktatóanyag: a figyelési megoldáshoz csatlakoztatott eszközök konfigurálása

Ebben az oktatóanyagban a távoli monitorozási megoldásgyorsítóval konfigurálhatja és kezelheti a csatlakoztatott IoT-eszközöket. Vegyen fel egy új eszközt a megoldás-gyorsító eszközbe, és konfigurálja az eszközt.

A Contoso új gépet rendelt, amellyel az egyik létesítményét szeretné bővíteni. Amíg az új gép kézbesítésére vár, Ön lefuttat egy szimulációt a megoldás működésének tesztelése érdekében. A szimuláció futtatásához vegyen fel egy új szimulált motort a távoli figyelési megoldás-gyorsító eszközbe, és ellenőrizze, hogy a szimulált eszköz helyesen válaszol-e a konfigurációs frissítésekre. Habár ez az oktatóanyag szimulált eszközöket használ, az eszközök fejlesztői a [távoli figyelési megoldáshoz csatlakoztatott valódi eszközön](iot-accelerators-connecting-devices.md)is alkalmazhatnak közvetlen metódusokat.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Szimulált eszköz üzembe helyezése.
> * Szimulált eszköz tesztelése.
> * Eszköz újrakonfigurálása.
> * Eszközök rendszerezése.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Szimulált eszköz hozzáadása

Navigáljon a megoldás **Device Explorer** lapjára, majd kattintson az **+ új eszköz**elemre:

[![Szimulált eszköz kiépítése](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

Az **új eszköz** paneljén válassza a **Simulated** (Szimulált) lehetőséget, az üzembe helyezendő eszközök száma maradjon **1**, válassza ki a **Faulty Engine** (Hibás motor) eszközmodellt, majd kattintson az **Apply** (Alkalmaz) gombra a szimulált eszköz létrehozásához:

[![Szimulált motor eszköz kiépítése](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>A szimulált eszköz tesztelése

Ha tesztelni szeretné, hogy a szimulált motor eszköz telemetria és jelentéskészítési tulajdonságokat küldjön, válassza ki azt az eszközök listájában a **Device Explorer** oldalon. A motor valós idejű információi a **Device Details** (Eszköz részletei) panelen jelennek meg:

[![Az új szimulált motor eszköz megtekintése](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

A **Device Details** (Eszközök részletei) résznél ellenőrizze, hogy az új eszköz elküldi-e a telemetriai adatokat. Az eszköz által küldött rezgési telemetriastreamek megtekintéséhez kattintson a **Vibration** (Rezgés) gombra:

[![Válassza ki a megtekinteni kívánt telemetria-adatfolyamot](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

Az **eszközt részletező** panel további információkat is megjelenít (például címkeértékeket, támogatott metódusokat, az eszköz által támogatott tulajdonságokat).

A részletes diagnosztikai adatok megtekintéséhez görgessen lefelé a **Device Details** (Eszköz részletei) panelen a**Diagnostics** (Diagnosztika) szakasz megtekintéséhez.

## <a name="reconfigure-a-device"></a>Eszköz újrakonfigurálása

Annak ellenőrzéséhez, hogy frissíthető-e a motor konfigurációs tulajdonságai, válassza ki azt az eszközök listájában a **Device Explorer** oldalon. Ezután kattintson a **feladatok**elemre, majd válassza a **Tulajdonságok**lehetőséget. A feladatok paneljén a kiválasztott eszköz frissíthető tulajdonságértékei láthatók:

[![Eszköz újrakonfigurálása](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

A motor földrajzi helyének frissítéséhez adja meg az **UpdateEngineLocation** nevet, állítsa be a **-122,15** hosszúsági és a **47,62** szélességi fokot, a helyszín legyen **Factory 2**, végül kattintson az **Apply** (Alkalmaz) gombra:

[![Eszköz tulajdonság értékének frissítése](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

A feladatok állapotának nyomon követéséhez kattintson a **feladatok állapotának megtekintése**elemre:

[![Eszköz tulajdonság értékének frissítése](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Ha a feladat befejeződött, lépjen vissza az **irányítópultra**. A motoreszköz az új helyén jelenik meg a térképen:

[![Keresőmotor helyének megtekintése](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Eszközök rendszerezése

Operátorként az eszközök rendszerezésének és kezelésének megkönnyítése érdekében érdemes őket ellátni csapatnevet tartalmazó címkével. A helyszíni szolgáltatási tevékenységek elvégzéséhez a Contoso két különböző csapatot állított fel:

* A Smart Vehicle csapat a tehergépkocsikat és a prototípusokat felügyeli.
* A Smart Building csapat a hűtőeszközökért, a liftekért és a motorokért felelős.

Az összes eszköz megjelenítéséhez navigáljon a **Device Explorer** lapra, és válassza a **minden eszköz** szűrőt:

[![Az összes eszköz megjelenítése](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Címkék hozzáadása

Válassza ki az összes **Trucks** (Tehergépkocsi) és **Prototyping** (Prototípus) eszközt. Ezután kattintson a **Jobs** (Feladatok) gombra.

A **Jobs** (Feladatok) panelen válassza a **Tag** (Címke) lehetőséget, a feladat neveként adja meg az**AddConnectedVehicleTag** nevet, majd adja hozzá a **FieldService** nevű szöveges címkét, amelynek értéke legyen **ConnectedVehicle**. Ezután kattintson az **Apply** (Alkalmaz) gombra:

[![Címke hozzáadása a prototípusokhoz és a tehergépkocsi-eszközökhöz](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Az eszköz lapon válassza ki az összes **Chiller** (Hűtőeszköz), **Elevator** (Lift) és **Engine** (Motor) eszközt. Ezután kattintson a **Jobs** (Feladatok) gombra.

A **Jobs** (Feladatok) panelen válassza a **Tag** (Címke) lehetőséget, a feladat neveként adja meg az **AddSmartBuildingTag** nevet, majd adja hozzá a**FieldService** nevű szöveges címkét, amelynek értéke legyen **SmartBuilding**. Ezután kattintson az **Apply** (Alkalmaz) gombra:

[![Címke hozzáadása a hűtőhöz, a lifthez és a motor eszközeihez](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Szűrők létrehozása

A címkeértékek alapján létrehozhatja a szűrőket. A **Device Explorer** lapon kattintson az **erőforráscsoportok kezelése**lehetőségre:

[![Erőforráscsoportok kezelése](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Hozzon létre egy szöveges szűrőt, amely a **FieldService** címkenevet és a **SmartBuilding** értéket használja feltételként. Mentse a szűrőt **Smart Building** néven:

[![Intelligens építési szűrő létrehozása](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Hozzon létre egy szöveges szűrőt, amely a **FieldService** címkenevet és a **ConnectedVehicle** értéket használja feltételként. Mentse a szűrőt **Connected Vehicle** néven.

[![Csatlakoztatott jármű szűrő létrehozása](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

A Contoso operátora mostantól lekérdezheti az eszközöket az üzemeltetési csapat alapján:

[![Csatlakoztatott jármű szűrő létrehozása](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan konfigurálhatja és kezelheti a távoli monitorozási megoldásgyorsítóhoz csatlakoztatott eszközöket. A következő oktatóanyagból megtudhatja, hogyan használhatja a megoldásgyorsítót egy váratlan riasztás kiváltó okának elemzésére.

> [!div class="nextstepaction"]
> [Riasztás kiváltó okainak elemzése](iot-accelerators-remote-monitoring-root-cause-analysis.md)

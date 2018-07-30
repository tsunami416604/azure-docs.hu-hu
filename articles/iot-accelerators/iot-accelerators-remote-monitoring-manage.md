---
title: Eszközök kezelése egy Azure-alapú távoli monitorozási megoldásban – oktatóanyag | Microsoft Docs
description: Ez az oktatóanyag azt mutatja be, hogyan kezelheti a távoli monitorozási megoldásgyorsítóhoz csatlakoztatott eszközöket.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/19/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: cd8e8c1fe1b77113968b7af635f45f9e0e077b7c
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159197"
---
# <a name="tutorial-configure-and-manage-devices-connected-to-your-monitoring-solution"></a>Oktatóanyag: A monitorozási megoldáshoz csatlakoztatott eszközök konfigurálása és kezelése

Ebben az oktatóanyagban a távoli monitorozási megoldásgyorsítóval konfigurálhatja és kezelheti a csatlakoztatott IoT-eszközöket. Új eszközt fog hozzáadni a megoldásgyorsítóhoz, amelyet konfigurálni fog, és frissíti a belső vezérlőprogramját.

A Contoso új gépet rendelt, amellyel az egyik létesítményét szeretné bővíteni. Amíg az új gép kézbesítésére vár, Ön lefuttat egy szimulációt a megoldás működésének tesztelése érdekében. A szimuláció futtatásához hozzá kell adnia új szimulált motoreszközt a távoli monitorozási megoldásgyorsítóhoz, és tesztelnie kell, hogy a szimulált eszköz megfelelő választ ad-e a műveletekre és a konfiguráció frissítéseire.

Az eszközök rugalmas konfigurálásához és kezeléséhez a távoli monitorozási megoldásgyorsító olyan IoT Hub-funkciókat használ, amilyenek például a [feladatok](../iot-hub/iot-hub-devguide-jobs.md) és a [közvetlen metódusok](../iot-hub/iot-hub-devguide-direct-methods.md). Bár ebben az oktatóanyagban szimulált eszközök szerepelnek, az eszközfejlesztők a közvetlen metódusokat olyan [fizikai eszközökön is implementálhatják, amelyek a távoli monitorozási megoldásgyorsítóhoz vannak csatlakoztatva](iot-accelerators-connecting-devices.md).

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Szimulált eszköz üzembe helyezése.
> * Szimulált eszköz tesztelése.
> * Eszköz belső vezérlőprogramjának frissítése.
> * Eszköz újrakonfigurálása.
> * Eszközök rendszerezése.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [iot-iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Szimulált eszköz hozzáadása

Navigáljon a megoldás **Devices** (Eszközök) lapjára, majd kattintson a **+ New device** (Új eszköz) gombra:

[![Szimulált eszköz üzembe helyezése](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

Az **új eszköz** paneljén válassza a **Simulated** (Szimulált) lehetőséget, az üzembe helyezendő eszközök száma maradjon **1**, válassza ki a **Faulty Engine** (Hibás motor) eszközmodellt, majd kattintson az **Apply** (Alkalmaz) gombra a szimulált eszköz létrehozásához:

[![Szimulált motoreszköz üzembe helyezése](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>A szimulált eszköz tesztelése

Ha tesztelni szeretné, hogy a szimulált motoreszköz elküldi-e a telemetriai adatokat és a tulajdonságértékek jelentéseit, válassza ki az **eszközök** lapján található eszközlistából. A motor valós idejű információi a **Device Details** (Eszköz részletei) panelen jelennek meg:

[![Az új szimulált motoreszköz megtekintése](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

A **Device Details** (Eszközök részletei) résznél ellenőrizze, hogy az új eszköz elküldi-e a telemetriai adatokat. Az eszköz által küldött rezgési telemetriastreamek megtekintéséhez kattintson a **Vibration** (Rezgés) gombra:

[![A megtekinteni kívánt telemetriastream kiválasztása](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

Az **eszközt részletező** panel további információkat is megjelenít (például címkeértékeket, támogatott metódusokat, az eszköz által támogatott tulajdonságokat).

A részletes diagnosztikai adatok megtekintéséhez görgessen lefelé a **Device Details** (Eszköz részletei) panelen a**Diagnostics** (Diagnosztika) szakasz megtekintéséhez.

## <a name="act-on-a-device"></a>Műveletek elvégzése egy eszközön

Ha tesztelni szeretné, hogy a szimulált motoreszköz megfelelő választ ad-e az irányítópult által kezdeményezett műveletekre, futtassa a **FirmwareUpdate** metódust. Ha egy műveletet szeretne végrehajtani egy eszközön egy metódus futtatásával, válassza ki az eszközt az eszközök listájából, majd válassza a **Jobs** (Feladatok) lehetőséget. Ha több eszközön is szeretne műveleteket végezni, egyszerre több eszközt is kiválaszthat. A **feladatok** paneljén válassza a **Run method** (Metódus futtatása) lehetőséget. Az **Engine** (Motor) eszközmodell három metódust határoz meg: **FirmwareUpdate**, **FillTank** és **EmptyTank**:

[![Motorokhoz kapcsolódó metódusok](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-expanded.png#lightbox)

Válassza ki a **FirmwareUpdate** metódust, a feladat neveként adja meg az **UpdateEngineFirmware** nevet, a belső vezérlőprogram verziója legyen **2.0.0**, a belső vezérlőprogram URI-ja a **http://contoso.com/engine.bin**, majd kattintson az **Apply** (Alkalmaz) gombra:

[![A belső vezérlőprogram frissítési metódusának ütemezése](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-expanded.png#lightbox)

A feladat állapotának nyomon követéséhez kattintson a **View job status** (Feladat állapotának megtekintése) elemre:

[![Ütemezett belsővezérlőprogram-frissítési feladat monitorozása](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-expanded.png#lightbox)

Ha a feladat befejeződött, lépjen vissza az **eszközök** lapjára. Megjelenik a motoreszköz belső vezérlőprogramjának új verziója.

Ha az **eszközök** lapján több különböző típusú eszközt választ ki, létrehozhat egy feladatot, amellyel egy metódust az összes kiválasztott eszközön futtathat. A **feladatok** panelje a kiválasztott eszközöknek csak a közös metódusait jeleníti meg.

## <a name="reconfigure-a-device"></a>Eszköz újrakonfigurálása

Ha tesztelni szeretné, hogy a motor konfigurációs tulajdonságai frissíthetők-e, válassza ki a motort az **eszközök** lapján található eszközlistából. Ezután kattintson a **Jobs** (Feladatok) gombra, majd válassza a **Reconfigure** (Újrakonfigurálás) lehetőséget. A feladatok paneljén a kiválasztott eszköz frissíthető tulajdonságértékei láthatók:

[![Eszköz újrakonfigurálása](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

A motor földrajzi helyének frissítéséhez adja meg az **UpdateEngineLocation** nevet, állítsa be a **-122,15** hosszúsági és a **47,62** szélességi fokot, a helyszín legyen **Factory 2**, végül kattintson az **Apply** (Alkalmaz) gombra:

[![Eszköztulajdonság értékének frissítése](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

A feladat állapotának nyomon követéséhez kattintson a **View job status** (Feladat állapotának megtekintése) elemre:

[![Eszköztulajdonság értékének frissítése](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Ha a feladat befejeződött, lépjen vissza az **irányítópultra**. A motoreszköz az új helyén jelenik meg a térképen:

[![Motor földrajzi helyének megtekintése](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Eszközök rendszerezése

Operátorként az eszközök rendszerezésének és kezelésének megkönnyítése érdekében érdemes őket ellátni csapatnevet tartalmazó címkével. A helyszíni szolgáltatási tevékenységek elvégzéséhez a Contoso két különböző csapatot állított fel:

* A Smart Vehicle csapat a tehergépkocsikat és a prototípusokat felügyeli.
* A Smart Building csapat a hűtőeszközökért, a liftekért és a motorokért felelős.

Az összes eszköz megjelenítéséhez lépjen az **eszközök** lapjára, és válassza az **All devices** (Minden eszköz) szűrőt:

[![Minden eszköz megjelenítése](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Címkék hozzáadása

Válassza ki az összes **Trucks** (Tehergépkocsi) és **Prototyping** (Prototípus) eszközt. Ezután kattintson a **Jobs** (Feladatok) gombra.

A **Jobs** (Feladatok) panelen válassza a **Tag** (Címke) lehetőséget, a feladat neveként adja meg az**AddConnectedVehicleTag** nevet, majd adja hozzá a **FieldService** nevű szöveges címkét, amelynek értéke legyen **ConnectedVehicle**. Ezután kattintson az **Apply** (Alkalmaz) gombra:

[![Címke hozzáadása a prototípus- és teherautó-eszközökhöz](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Az eszköz lapon válassza ki az összes **Chiller** (Hűtőeszköz), **Elevator** (Lift) és **Engine** (Motor) eszközt. Ezután kattintson a **Jobs** (Feladatok) gombra.

A **Jobs** (Feladatok) panelen válassza a **Tag** (Címke) lehetőséget, a feladat neveként adja meg az **AddSmartBuildingTag** nevet, majd adja hozzá a**FieldService** nevű szöveges címkét, amelynek értéke legyen **SmartBuilding**. Ezután kattintson az **Apply** (Alkalmaz) gombra:

[![Címke hozzáadása a hűtőeszközökhöz, liftekhez és motorokhoz](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Szűrők létrehozása

A címkeértékek alapján létrehozhatja a szűrőket. Az **eszközök** lapján kattintson a **Manage device groups** (Eszközcsoportok kezelése) gombra:

[![Eszközcsoportok kezelése](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Hozzon létre egy szöveges szűrőt, amely a **FieldService** címkenevet és a **SmartBuilding** értéket használja feltételként. Mentse a szűrőt **Smart Building** néven:

[![A Smart Building szűrő létrehozása](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Hozzon létre egy szöveges szűrőt, amely a **FieldService** címkenevet és a **ConnectedVehicle** értéket használja feltételként. Mentse a szűrőt **Connected Vehicle** néven.

[![A Connected Vehicle szűrő létrehozása](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

A Contoso operátora mostantól lekérdezheti az eszközöket az üzemeltetési csapat alapján:

[![A Connected Vehicle szűrő létrehozása](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan konfigurálhatja és kezelheti a távoli monitorozási megoldásgyorsítóhoz csatlakoztatott eszközöket. A következő oktatóanyagból azt tudhatja meg, hogyan használhatja a megoldásgyorsítót a csatlakoztatott eszközök problémáinak azonosítására és javítására.

> [!div class="nextstepaction"]
> [Eszközriasztások használata a monitorozási megoldáshoz csatlakoztatott eszközök problémáinak azonosítására és javítására](iot-accelerators-remote-monitoring-maintain.md)

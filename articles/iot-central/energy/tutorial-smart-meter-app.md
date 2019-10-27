---
title: Smart Meter Analytics-alkalmazás létrehozása IoT Central használatával | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre intelligens mérőműszer-figyelő alkalmazást az Azure IoT Central alkalmazás-sablonok használatával.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: fd6062250b288f9c1f04b7e7853e7fdd84d612c0
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965832"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Oktatóanyag: az intelligens fogyasztásmérő monitorozási alkalmazás sablonjának létrehozása és végigvezeti 

Ez az oktatóanyag végigvezeti az intelligens mérőműszer-figyelő alkalmazás létrehozásának folyamatán, amely egy szimulált adattal rendelkező minta-eszköz modellt tartalmaz. Ezen oktatóanyag segítségével elsajátíthatja a következőket:

> [!div class="checklist"]
> * Hozzon létre ingyenes Smart Meter alkalmazást
> * Alkalmazás végigvezeti
> * Az erőforrások eltávolítása


Ha nincs előfizetése, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Előfeltételek
- None
- Az Azure-előfizetés használata ajánlott, de nem szükséges a kipróbáláshoz

## <a name="create-a-smart-meter-monitoring-app"></a>Intelligens mérőműszer-figyelő alkalmazás létrehozása 

Ezt az alkalmazást három egyszerű lépésben hozhatja létre:

1. Nyissa meg az [Azure IoT Central kezdőlapját](https://apps.azureiotcentral.com) , és kattintson a **Build** (létrehozás) gombra egy új alkalmazás létrehozásához. 

2. Válassza **az energia** fület, majd kattintson az alkalmazás **létrehozása** elemre az **intelligens fogyasztásmérő figyelése** alkalmazás csempén.

    > [!div class="mx-imgBorder"]
    > ![az alkalmazás létrehozása](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

3. Az alkalmazás **létrehozása** megnyitja az **új alkalmazás** űrlapot. Adja meg a kért adatokat az alábbi ábrán látható módon:
    * **Alkalmazás neve**: válasszon egy nevet a IoT Central alkalmazás számára. 
    * **URL**: válasszon egy IoT Central URL-címet, a platform ellenőrzi annak egyediségét.
    * **7 napos ingyenes próbaverzió**: Ha már rendelkezik Azure-előfizetéssel, az alapértelmezett beállítás ajánlott. Ha nem rendelkezik Azure-előfizetéssel, kezdje az ingyenes próbaverzióval.
    * **Számlázási információ**: maga az alkalmazás ingyenes. Az alkalmazás erőforrásainak kiépítéséhez a címtár, az Azure-előfizetés és a régió részletei szükségesek.
    * Kattintson a lap alján található **Létrehozás** gombra, és az alkalmazás egy percen belül létrejön.     
        > [!div class="mx-imgBorder"]
        > ![új alkalmazás űrlapja](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)


### <a name="verify-the-application-and-simulated-data"></a>Az alkalmazás és a szimulált adatértékek ellenőrzése

Az újonnan létrehozott intelligens fogyasztásmérő alkalmazás az alkalmazás, és bármikor módosítható. Győződjön meg arról, hogy az alkalmazás telepítve van, és a módosítás előtt a várt módon működik.

Az alkalmazás létrehozásának és adatszimulációjának ellenőrzéséhez nyissa meg az **irányítópultot**. Ha a csempék adatai megtekinthetők, az alkalmazás üzembe helyezése sikeres volt. Az adat-szimuláció eltarthat néhány percig az adat létrehozásakor, így 1-2 percet is igénybe vehet. 

## <a name="application-walk-through"></a>Alkalmazás végigvezeti
Az alkalmazás sikeres üzembe helyezése után a minta intelligens fogyasztásmérő eszköz, az eszköz modellje és az irányítópult is megtalálható. 

A adatum egy kitalált energetikai vállalat, amely figyeli és felügyeli az intelligens fogyasztásmérőket. Az intelligens fogyasztásmérő monitorozási irányítópultján megjelenik az intelligens fogyasztásmérő tulajdonságai, az adathalmazok és a minták parancsai. Lehetővé teszi a kezelők és a támogatási csapatok számára, hogy proaktív módon végezzék el a következő tevékenységeket, mielőtt a támogatási incidensekre váltanak: 
* Tekintse át a legújabb mérési adatokat és a telepítési helyét a térképen
* Proaktív módon ellenőrizhető a mérési hálózat és a kapcsolatok állapota 
* A hálózat állapotának minimális és maximális feszültségének figyelése 
* Tekintse át az energia, a teljesítmény és a feszültség tendenciáit, hogy bármilyen rendellenes mintázatot kapjon 
* A teljes energiafogyasztás nyomon követése tervezési és számlázási célokra
* Parancs-és vezérlési műveletek, például újrakapcsolási mérő és a belső vezérlőprogram frissítése. A sablonban a parancsgombok a lehetséges funkciókat mutatják, és nem küldenek valós parancsokat. 

> [!div class="mx-imgBorder"]
> ![intelligens fogyasztásmérő monitorozási irányítópultja](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Eszközök
Az alkalmazás egy minta intelligens mérőszám-eszközt tartalmaz. Az eszköz adatait a **Devices (eszközök** ) lapra kattintva tekintheti meg.

> [!div class="mx-imgBorder"]
> ![Smart Meter-eszközök](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Az eszköz részleteinek megtekintéséhez kattintson a minta eszköz **SM0123456789** hivatkozására. Frissítheti az eszköz írható tulajdonságait a **frissítés tulajdonságai** lapon, és megjelenítheti a frissített értékeket az irányítópulton.

> [!div class="mx-imgBorder"]
> ![az intelligens fogyasztásmérő tulajdonságai](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Eszköz sablonja
Az intelligenskártya-modell megjelenítéséhez kattintson az **eszköz sablonok** lapfülre. A modell előre definiált felületet tartalmaz az adattípushoz, a tulajdonsághoz, a parancsokhoz és a nézetekhez.

> [!div class="mx-imgBorder"]
> ![Smart Meter-eszközök sablonjai](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha úgy dönt, hogy nem folytatja az alkalmazás használatát, törölje az alkalmazást a következő lépésekkel:

1. A bal oldali menüben nyissa meg az adminisztráció lapot.
2. Válassza az Alkalmazásbeállítások lehetőséget, majd kattintson a lap alján található Törlés gombra. 

    > [!div class="mx-imgBorder"]
    > ![alkalmazás törlése](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)


## <a name="next-steps"></a>Következő lépések
* További információ az App Architecture architektúrával kapcsolatban [.](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* Smart Meter-alkalmazás sablonjainak létrehozása ingyen: [intelligens fogyasztásmérő](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring) alkalmazás
* További információ a IoT Centralről: [IoT Central áttekintése](https://docs.microsoft.com/azure/iot-central/)

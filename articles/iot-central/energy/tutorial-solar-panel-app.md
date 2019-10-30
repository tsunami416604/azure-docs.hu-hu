---
title: Solar büntetőjogi monitorozási alkalmazás létrehozása IoT Centralsal | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre napelem-alkalmazást az Azure IoT Central alkalmazás-sablonok használatával.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: b408d2cc0754cc1f2dd8671d037d4f9d348e883b
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027636"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Oktatóanyag: létrehozás és útmutató a napelemek figyelésére szolgáló alkalmazás sablonja 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez az oktatóanyag végigvezeti Önt a napelemes figyelő alkalmazás létrehozásának folyamatán, amely egy szimulált adattal rendelkező minta-eszköz modellt tartalmaz. Ezen oktatóanyag segítségével elsajátíthatja a következőket:


> [!div class="checklist"]
> * A Solar panel alkalmazás ingyenes létrehozása
> * Alkalmazás végigvezeti
> * Az erőforrások eltávolítása


Ha nincs előfizetése, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Előfeltételek
- None
- Az Azure-előfizetés használata ajánlott, de nem szükséges a kipróbáláshoz


## <a name="create-a-solar-panel-monitoring-app"></a>Napelemes figyelő alkalmazás létrehozása 

Ezt az alkalmazást három egyszerű lépésben hozhatja létre:

1. Nyissa meg az [Azure IoT Central kezdőlapját](https://apps.azureiotcentral.com) , és kattintson a **Build** (létrehozás) gombra egy új alkalmazás létrehozásához. 

2. Válassza **az energia** fület, majd kattintson az alkalmazás **létrehozása** elemre a **napelem figyelése** alkalmazás csempén. 

    [!div class="mx-imgBorder"]
    ![az alkalmazás létrehozása](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. Az alkalmazás **létrehozása** megnyitja az **új alkalmazás** űrlapot. Adja meg a kért adatokat az alábbi ábrán látható módon:
    * **Alkalmazás neve**: válasszon egy nevet a IoT Central alkalmazás számára. 
    * **URL**: válasszon egy IoT Central URL-címet, a platform ellenőrzi annak egyediségét.
    * **7 napos ingyenes próbaverzió**: Ha már rendelkezik Azure-előfizetéssel, az alapértelmezett beállítás ajánlott. Ha nem rendelkezik Azure-előfizetéssel, kezdje az ingyenes próbaverzióval.
    * **Számlázási információ**: maga az alkalmazás ingyenes. Az alkalmazás erőforrásainak kiépítéséhez a címtár, az Azure-előfizetés és a régió részletei szükségesek.
    * Kattintson a lap alján található **Létrehozás** gombra, és az alkalmazás egy percen belül létrejön.
        > [!div class="mx-imgBorder"]
        > ![új alkalmazás űrlapja](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)


### <a name="verify-the-application-and-simulated-data"></a>Az alkalmazás és a szimulált adatértékek ellenőrzése

Az újonnan létrehozott napelemes alkalmazás az Ön alkalmazása, és bármikor módosítható. Győződjön meg arról, hogy az alkalmazás telepítve van, és a módosítás előtt a várt módon működik.

Az alkalmazás létrehozásának és adatszimulációjának ellenőrzéséhez nyissa meg az **irányítópultot**. Ha a csempék adatai megtekinthetők, az alkalmazás üzembe helyezése sikeres volt. Az adat-szimuláció eltarthat néhány percig az adat létrehozásakor, így 1-2 percet is igénybe vehet. 

## <a name="application-walk-through"></a>Alkalmazás végigvezeti
Az alkalmazás sikeres üzembe helyezése után a minta intelligens fogyasztásmérő eszköz, az eszköz modellje és az irányítópult is megtalálható.

A adatum egy fiktív energetikai cég, aki figyeli és kezeli a napelemeket. A napelem-figyelési irányítópulton a napelemek tulajdonságai, az adathalmazok és a minták parancsai láthatók. Lehetővé teszi a kezelők és a támogatási csapatok számára, hogy proaktív módon végezzék el a következő tevékenységeket, mielőtt a támogatási incidensekre váltanak:
* Tekintse át a panel legújabb információit és a telepített helyét a térképen
* Proaktív módon ellenőrizhető a panel állapota és a kapcsolatok állapota
* Tekintse át az energia-generálási és hőmérsékleti trendeket, hogy bármilyen rendellenes mintázatot kapj
* Az energia teljes generációjának nyomon követése tervezési és számlázási célokra
* Parancs-és vezérlési műveletek, például aktiválási panel és a belső vezérlőprogram verziójának frissítése. A sablonban a parancsgombok a lehetséges funkciókat mutatják, és nem küldenek valós parancsokat.

[!div class="mx-imgBorder"]
![a Solar panel monitorozási irányítópultja](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Eszközök
Az alkalmazás egy minta napelemes eszközt tartalmaz. Az eszköz adatait a **Devices (eszközök** ) lapra kattintva tekintheti meg.

[!div class="mx-imgBorder"]
![napelemes eszközök](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Az eszköz részleteinek megtekintéséhez kattintson a minta eszköz **SP0123456789** hivatkozására. A **frissítés tulajdonságai** lapon frissítheti az eszköz írható tulajdonságait, és megjelenítheti a frissített értékeket az irányítópulton. 

[!div class="mx-imgBorder"]
![napelemek tulajdonságai](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Eszköz sablonja
A napelemes eszköz modell megjelenítéséhez kattintson az **eszköz sablonok** lapfülre. A modell előre definiált felületet tartalmaz az adattípushoz, a tulajdonsághoz, a parancsokhoz és a nézetekhez.

[!div class="mx-imgBorder"]
![napelemes eszközök sablonja](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha úgy dönt, hogy nem folytatja az alkalmazás használatát, törölje az alkalmazást a következő lépésekkel:

1. A bal oldali ablaktáblában nyissa meg az adminisztráció lapot.
2. Válassza az Alkalmazásbeállítások lehetőséget, majd kattintson a lap alján található Törlés gombra. 

    [!div class="mx-imgBorder"]
    ![alkalmazás törlése](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>Következő lépések
* A napelemes alkalmazások architektúrájának megismerése tekintse meg [a koncepciót ismertető cikket.](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Solar panel-alkalmazások sablonjainak létrehozása ingyen: [napelemes](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring) alkalmazás
* További információ a IoT Centralről: [IoT Central áttekintése](https://docs.microsoft.com/azure/iot-central/)


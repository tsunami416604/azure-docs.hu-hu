---
title: 'Oktatóanyag: Hozzon létre egy napelemes felügyeleti alkalmazást az IoT Central segítségével'
description: 'Oktatóanyag: Ismerje meg, hogyan hozhat létre napelemalkalmazást az Azure IoT Central alkalmazássablonjaihasználatával.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d5ea3d3420cb598693ccaede7ee10d2f8c4fd839
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77025775"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Bemutató: Létrehozása és séta a napelem felügyeleti alkalmazás sablon 



Ez az oktatóanyag végigvezeti a napelem-figyelő alkalmazás létrehozásának folyamatán, amely szimulált adatokkal rendelkező mintaeszköz-modellt tartalmaz. Ezen oktatóanyag segítségével elsajátíthatja a következőket:


> [!div class="checklist"]
> * Hozza létre a napelem app ingyen
> * Alkalmazás-átjárás
> * Az erőforrások eltávolítása


Ha nem rendelkezik előfizetéssel, [hozzon létre egy ingyenes próbafiókot](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Előfeltételek
- None
- Az Azure-előfizetés ajánlott, de nem szükséges kipróbálni


## <a name="create-a-solar-panel-monitoring-app"></a>Napelempanel monitorozására szolgáló alkalmazás létrehozása 

Ezt az alkalmazást három egyszerű lépésben hozhatja létre:

1. Nyissa meg [az Azure IoT Central kezdőlapját,](https://apps.azureiotcentral.com) és új alkalmazás létrehozásához kattintson a **Build gombra.** 

2. Válassza az **Energia** lapot, és kattintson **az Alkalmazás létrehozása** elemre a **Napelem figyelési** alkalmazás csempéje alatt. 

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás létrehozása](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. **Az Alkalmazás létrehozása** megnyílik **Az Új alkalmazásűrlap.** Töltse ki a kért adatokat az alábbi ábra szerint:
    * **Alkalmazás neve**: Válasszon nevet az IoT Central alkalmazás. 
    * **URL:** Válasszon egy IoT-központi URL-címet, a platform ellenőrzi annak egyediségét.
    * **7 napos ingyenes próbaverzió:** Ha már rendelkezik Azure-előfizetéssel, az alapértelmezett beállítás ajánlott. Ha nem rendelkezik Azure-előfizetéssel, kezdje az ingyenes próbaverzióval.
    * **Számlázási információk**: Maga az alkalmazás ingyenes. A címtár, az Azure-előfizetés és a régió adatait az alkalmazás erőforrásainak kiépítése szükséges.
    * Kattintson a lap alján található **Létrehozás** gombra, és az alkalmazás egy percen belül létrejön.
        ![Új jelentkezési lap](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Új alkalmazásűrlap számlázási adatai](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Az alkalmazás és a szimulált adatok ellenőrzése

Az újonnan létrehozott napelem alkalmazás az alkalmazás, és bármikor módosíthatja. Győződjünk meg arról, hogy az alkalmazás telepítése és a várt módon működik, mielőtt módosítaná.

Az alkalmazás létrehozásának és az adatszimulációnak az ellenőrzéséhez nyissa meg az **Irányítópult ot.** Ha látja a csempéket néhány adatot, akkor az alkalmazás telepítése sikeres volt. Az adatszimuláció néhány percet is igénybe vehet az adatok létrehozása, ezért adjon 1-2 percet. 

## <a name="application-walk-through"></a>Alkalmazás-átjárás
Miután sikeresen telepítette az alkalmazássablont, az intelligens mérőeszköz, az eszközmodell és az irányítópult mintahasználatával érkezik.

Adatum egy fiktív energetikai vállalat, aki figyeli és kezeli napelemek. A napelem monitoring műszerfalon napelem tulajdonságok, adatok és mintaparancsok láthatók. Lehetővé teszi az operátorok és a támogató csapatok számára, hogy proaktív módon hajtsák végre a következő tevékenységeket, mielőtt támogatási incidensekké alakulnának:
* Tekintse át a panel legfrissebb adatait és a térképen telepített tartózkodási helyét
* Proaktív módon ellenőrizze a panel állapotát és a kapcsolat állapotát
* Tekintse át az energiatermelési és hőmérsékleti trendeket, hogy elkapjon bármilyen rendellenes mintát
* A teljes energiatermelés nyomon követése tervezési és számlázási célokra
* Parancs- és vezérlési műveletek, például a panel aktiválása és a firmware verziójának frissítése. A sablonban a parancsgombok a lehetséges funkciókat jelenítik meg, és nem küldenek valódi parancsokat.

> [!div class="mx-imgBorder"]
> ![Napelem felügyeleti műszerfal](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Eszközök
Az alkalmazás hoz egy minta napelem eszköz. Az eszköz adatait az **Eszközök** fülre kattintva láthatja.

> [!div class="mx-imgBorder"]
> ![Napelem eszközök](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Kattintson a mintaeszköz **SP0123456789** linkre az eszköz részleteinek megtekintéséhez. A **Tulajdonságok frissítése** lapon frissítheti az eszköz írható tulajdonságait, és megjelenítheti a frissített értékeket az irányítópulton. 

> [!div class="mx-imgBorder"]
> ![Napelem tulajdonságai](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Eszközsablon
Kattintson a **Készülék sablonok** fülre, hogy a napelem eszköz modell. A modell előre definiált felülettel rendelkezik az adatokhoz, a tulajdonsághoz, a parancsokhoz és a nézetekhez.

> [!div class="mx-imgBorder"]
> ![Napelem eszközök sablon](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha úgy dönt, hogy nem használja tovább ezt az alkalmazást, törölje az alkalmazást az alábbi lépésekkel:

1. A bal oldali ablaktáblából nyissa meg a Felügyelet lapot
2. Válassza az Alkalmazásbeállítások lehetőséget, majd kattintson a lap alján található Törlés gombra. 

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás törlése](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>További lépések
* Tudjon meg többet a napelem app architektúra olvassa el [a koncepció cikk](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Hozzon létre napelem alkalmazás sablonok at free: [napelem app](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* További információ az IoT Centralról: [IoT Central overview](https://docs.microsoft.com/azure/iot-central/)


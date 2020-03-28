---
title: 'Oktatóanyag: Intelligens mérőműszer-elemző alkalmazás létrehozása az IoT Central segítségével'
description: 'Oktatóanyag: Ismerje meg, hogyan hozhat létre egy intelligens mérőműszer-figyelő alkalmazást az Azure IoT Central alkalmazássablonjaihasználatával.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 9e954e9c1a7efa43a19849b1c5b40284ec84eeed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77016000"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Oktatóanyag: Az intelligens fogyasztásmérő felügyeleti alkalmazássablon létrehozása és végigjárása 



Ez az oktatóanyag végigvezeti az intelligens mérőfigyelő alkalmazás létrehozásának folyamatán, amely szimulált adatokat tartalmazó mintaeszköz-modellt tartalmaz. Ezen oktatóanyag segítségével elsajátíthatja a következőket:

> [!div class="checklist"]
> * Hozza létre a Smart Meter App ingyen
> * Alkalmazás-átjárás
> * Az erőforrások eltávolítása


Ha nem rendelkezik előfizetéssel, [hozzon létre egy ingyenes próbafiókot](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Előfeltételek
- None
- Az Azure-előfizetés ajánlott, de nem szükséges kipróbálni

## <a name="create-a-smart-meter-monitoring-app"></a>Intelligens fogyasztásmérő monitorozására szolgáló alkalmazás létrehozása 

Ezt az alkalmazást három egyszerű lépésben hozhatja létre:

1. Nyissa meg [az Azure IoT Central kezdőlapját,](https://apps.azureiotcentral.com) és új alkalmazás létrehozásához kattintson a **Build gombra.** 

2. Válassza **az Energia** lapot, és kattintson az Alkalmazás **létrehozása** elemre az Intelligens **mérő figyelési** alkalmazáscsempéje csoportban.

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás létrehozása](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

3. **A Create app** megnyitja az **Új jelentkezési** lapot. Töltse ki a kért adatokat az alábbi ábra szerint:
    * **Alkalmazás neve**: Válasszon nevet az IoT Central alkalmazás. 
    * **URL:** Válasszon egy IoT-központi URL-címet, a platform ellenőrzi annak egyediségét.
    * **7 napos ingyenes próbaverzió:** Ha már rendelkezik Azure-előfizetéssel, az alapértelmezett beállítás ajánlott. Ha nem rendelkezik Azure-előfizetéssel, kezdje az ingyenes próbaverzióval.
    * **Számlázási információk**: Maga az alkalmazás ingyenes. A címtár, az Azure-előfizetés és a régió adatait az alkalmazás erőforrásainak kiépítése szükséges.
    * Kattintson a lap alján található **Létrehozás** gombra, és az alkalmazás egy percen belül létrejön.

        ![Új jelentkezési lap](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Új alkalmazásűrlap számlázási adatai](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Az alkalmazás és a szimulált adatok ellenőrzése

Az újonnan létrehozott intelligens mérőalkalmazás az alkalmazás, amelybármikor módosíthatja. Győződjünk meg arról, hogy az alkalmazás telepítése és a várt módon működik, mielőtt módosítaná.

Az alkalmazás létrehozásának és az adatszimulációnak az ellenőrzéséhez nyissa meg az **Irányítópult ot.** Ha látja a csempéket néhány adatot, akkor az alkalmazás telepítése sikeres volt. Az adatszimuláció néhány percet is igénybe vehet az adatok létrehozása, ezért adjon 1-2 percet. 

## <a name="application-walk-through"></a>Alkalmazás-átjárás
Miután sikeresen telepítette az alkalmazássablont, az intelligens mérőeszköz minta, az eszközmodell és egy irányítópult ot is mellékelt. 

Adatum egy fiktív energetikai vállalat, aki figyeli és kezeli az intelligens mérők. Az intelligens mérőfigyelő irányítópultján intelligens mérőtulajdonságok, adatok és mintaparancsok jelennek meg. Lehetővé teszi az operátorok és a támogató csapatok számára, hogy proaktív módon hajtsák végre a következő tevékenységeket, mielőtt támogatási incidensekké alakulnának: 
* Tekintse át a legfrissebb mérőadatokat és a térképen telepített tartózkodási helyét
* Proaktív módon ellenőrizze a mérő hálózat és a kapcsolat állapotát 
* Monitor Min és Max feszültség mért hálózati egészségügyi 
* Tekintse át az energia-, teljesítmény- és feszültségirányzatot, hogy elkapjon bármilyen rendellenes mintát 
* A teljes energiafogyasztás nyomon követése tervezési és számlázási célokra
* Parancs- és vezérlési műveletek, például a mérőújracsatlakoztatás és a firmware-verzió frissítése. A sablonban a parancsgombok a lehetséges funkciókat jelenítik meg, és nem küldenek valódi parancsokat. 

> [!div class="mx-imgBorder"]
> ![Intelligens fogyasztásmérő-figyelő műszerfal](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Eszközök
Az alkalmazás hoz egy minta intelligens mérő eszköz. Az eszköz adatait az **Eszközök** fülre kattintva láthatja.

> [!div class="mx-imgBorder"]
> ![Intelligens fogyasztásmérő eszközök](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Kattintson a mintaeszköz **SM0123456789** linkre az eszköz részleteinek megtekintéséhez. Az eszköz írható tulajdonságait frissítheti a **Tulajdonságok frissítése** lapon, és megjelenítheti a frissített értékeket az irányítópulton.

> [!div class="mx-imgBorder"]
> ![Intelligens fogyasztásmérő tulajdonságai](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Eszközsablon
Kattintson az **Eszközsablonok** fülre az intelligens mérőeszköz modelljének megtekintéséhez. A modell előre definiált felülettel rendelkezik az adatokhoz, a tulajdonsághoz, a parancsokhoz és a nézetekhez.

> [!div class="mx-imgBorder"]
> ![Intelligens mérőeszköz-sablonok](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha úgy dönt, hogy nem használja tovább ezt az alkalmazást, törölje az alkalmazást az alábbi lépésekkel:

1. A bal oldali ablaktáblából nyissa meg a Felügyelet lapot
2. Válassza az Alkalmazásbeállítások lehetőséget, majd kattintson a lap alján található Törlés gombra. 

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás törlése](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)


## <a name="next-steps"></a>További lépések
* Az intelligens mérőalkalmazás-architektúráról a [koncepciócikkben olvashat.](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* Hozzon létre intelligens mérő alkalmazás sablonokat ingyen: [intelligens mérő alkalmazás](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* További információ az IoT Centralról: [IoT Central overview](https://docs.microsoft.com/azure/iot-central/)

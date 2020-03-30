---
title: Energiamegoldások építése az IoT Central segítségével | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre energiamegoldást az Azure IoT Central alkalmazássablonjaival.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 13b626945743c17bad8df58fba77e1c960e90ef3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77016562"
---
# <a name="build-energy-solutions-with-iot-central"></a>Energiaipari megoldások létrehozása az IoT Centrallal 




Az intelligens fogyasztásmérők és napelemek fontos szerepet játszanak az energiaipar átalakulásában. Az intelligens mérők több ellenőrzést és valós idejű betekintést nyújtanak az energiafogyasztásról és a napelemek növekedéséről, ami áttörést jelent a megújuló energia termelésében. Az intelligens fogyasztásmérő és a napelem figyelő alkalmazások mintasablonok a különböző képességek megjelenítéséhez. A partnerek kihasználhatják ezeket a sablonokat, hogy energiamegoldásokat építsenek az IoT Central segítségével az egyedi igényeiknek megfelelően. Nincs szükség új kódolásra és további költségekre ezen alkalmazások üzembe helyezéséhez és használatához. További információ az energiaalkalmazások sablonjairól és képességeiről.


## <a name="what-is-the-smart-meter-monitoring-application"></a>Mi az intelligens mérő felügyeleti alkalmazás?
 Az intelligens fogyasztásmérők nem csak az automatikus számlázást teszik lehetővé, hanem a fejlett mérési használati eseteket is, például a valós idejű leolvasásokat és a kétirányú kommunikációt. Az intelligens mérőalkalmazás-sablon lehetővé teszi a segédprogramok és partnerek számára az intelligens fogyasztásmérők állapotának és adatainak figyelését, riasztások és értesítések meghatározását. Mintaparancsokat biztosít, például a kapcsolatbontásmérőt és a szoftverfrissítését. A mérőadatok beállíthatók más üzleti alkalmazásokba való kilépéshez és egyéni megoldások kifejlesztéséhez. 

Az alkalmazás legfontosabb funkciói: 

* Mérőminta eszköz modellje 
* Mérő info és élő állapot 
* Mérő értékek, mint például az energia, a teljesítmény és a feszültségek
* Mérőparancs-minták 
* Beépített képi megjelenítés és irányítópultok
* Bővíthetőség az egyéni megoldások fejlesztéséhez

Kipróbálhatja az [intelligens mérőműszer-figyelő alkalmazást ingyenesen](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring) Azure-előfizetés és kötelezettségvállalások nélkül.


Az alkalmazás üzembe helyezése után megjelennek a szimulált mérőadatok az irányítópulton, az alábbi ábrán látható módon. Ez a sablon egy mintaalkalmazás, amelyet könnyedén kiterjeszthet és testreszabhat az adott használati esetekhez.

> [!div class="mx-imgBorder"]
> ![Intelligens mérőalkalmazás irányítópultja](media/overview-iot-central-energy/smart-meter-app-dashboard.png)


## <a name="what-is-the-solar-panel-monitoring-application"></a>Mi a napelem felügyeleti alkalmazás?
A napelem monitoring alkalmazás lehetővé teszi a közművek és a partnerek számára, hogy figyelemmel kíséri a napelemek, mint például az energia-termelés és a kapcsolat állapotát közel valós időben. Értesítéseket küldhet meghatározott küszöbértékek alapján. Mintaparancsokat biztosít, például a belső vezérlőprogram frissítését és egyéb tulajdonságokat. A napelem adatok beállíthatók más üzleti alkalmazásokba való kijutáshoz és egyéni megoldások kifejlesztéséhez. 

Az alkalmazás legfontosabb funkciói: 

* Napelem minta eszköz modell 
* Napelem info és élő állapot
* Napenergia-termelés és egyéb értékek
* Parancsnoki és ellenőrző minták
* Beépített képi megjelenítés és irányítópultok
* Bővíthetőség az egyéni megoldások fejlesztéséhez

Kipróbálhatja a [napelem figyelési alkalmazást ingyenesen,](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring) Azure-előfizetés és kötelezettségvállalások nélkül.

Az alkalmazás telepítése után 1-2 percen belül láthatja a szimulált napelemadatokat, ahogy az az alábbi irányítópulton látható. Ez a sablon egy mintaalkalmazás, amelyet könnyedén kiterjeszthet és testreszabhat az adott használati esetekhez. 

> [!div class="mx-imgBorder"]
> ![Napelem App Műszerfal](media/overview-iot-central-energy/solar-panel-app-dashboard.png)


## <a name="next-steps"></a>További lépések
Az energiamegoldás kiépítésének megkezdéséhez:
* Hozzon létre alkalmazás sablonokat ingyen: [intelligens mérő alkalmazás](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring), [napelem app](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* További információ az [intelligens mérőműszer-figyelő alkalmazás fogalmairól](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* Tudjon meg többet a [napelem felügyeleti alkalmazás fogalmairól](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* További információ az [IoT Central platformról](https://docs.microsoft.com/azure/iot-central/)

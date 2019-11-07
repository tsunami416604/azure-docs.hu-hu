---
title: 'Rövid útmutató: a Azure Time Series Insights Preview bemutató környezetének megismerése | Microsoft Docs'
description: Rövid útmutató a Azure Time Series Insights előzetes verziójának bemutató környezetének megismeréséhez.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 10/24/2019
ms.openlocfilehash: 25d6d1aa1cf7db0f448ec22ed7b5c84d840af3f5
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579647"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Rövid útmutató: Ismerkedés a Azure Time Series Insights előzetes verziójának bemutató környezetével

Ez a rövid útmutató elsajátítja a Azure Time Series Insights előzetes verziójának környezetét. Az ingyenes bemutatóban megtekintheti az Time Series Insights előzetes verzióhoz hozzáadott főbb funkciókat.

A Time Series Insights Preview bemutató környezet a contoso vállalatot, amely két szélturbina-farmot üzemeltet. Minden Farm 10 turbinával rendelkezik. Minden turbina 20 érzékelővel rendelkezik, amely percenként jelentést készít az Azure IoT Hub. Az érzékelők információt gyűjtenek az időjárási feltételekről, a panel szurok és a kitérés pozícióról. A rendszer rögzíti a generátorok teljesítményéről, a sebességváltó működéséről és a biztonsági figyelőről szóló információkat is.

Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Time Series Insightst a contoso-információk gyakorlati elemzésének megkereséséhez. Emellett rövid kiváltó okokat is elvégezhet, hogy jobban megjósolja a kritikus hibákat, és hogy elvégezze a karbantartást.

> [!IMPORTANT]
> Ha még nem rendelkezik ilyennel, hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Ismerkedés a Time Series Insights Explorerrel bemutató környezetben

A Time Series Insights Preview Explorer az előzmények és a kiváltó okok elemzését mutatja be. Első lépések:

1. Nyissa meg a [contoso szélerőműpark bemutató](https://insights.timeseries.azure.com/preview/samples) környezetét.  

1. Ha a rendszer kéri, jelentkezzen be a Time Series Insights Explorerbe az Azure-fiókja hitelesítő adataival.

## <a name="work-with-historical-data"></a>Korábbi adatmennyiségek használata

1. A **contoso-üzem 1**. részében tekintse meg a Wind gázturbinás **W7**című részt.  

   1. Módosítsa a nézet tartományát **1/1/17 20:00:00.00 – 3/10/17 20:00:00.00 (UTC) értékre**.
   1. Az érzékelő kiválasztásához válassza a **contoso növény 1** > **W7** > **Generator System** > **GeneratorSpeed**elemet. Ezután tekintse át a megjelenített értékeket.

      [![W7 a contoso-üzemben 1](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Az utóbbi időben a contoso tüzet észlelt a Wind Turbine **W7**-ben. A vélemények eltérőek lehetnek a tüzet kiváltó okok miatt. Time Series Insights láthatjuk, hogy a Fire Alert-érzékelő aktiválva lett a tűz során.

   1. Módosítsa a nézet tartományát **3/9/17 20:00:00.00 – 3/10/17 20:00:00.00 (UTC) értékre**.
   1. Válassza a **biztonsági rendszerek** > **FireAlert**elemet.

      [![contoso tüzet észlelt a Wind Turbine W7 szolgáltatásban](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Tekintse át a további eseményeket a tűz idején, hogy megértse, mi történt. Az olajnyomás és az aktív figyelmeztetések közvetlenül a tűz előtt vannak megszegve.

   1. Válassza a **pitch System** > **HydraulicOilPressure**elemet.
   1. Válassza a **pitch System** > **ActiveWarning**elemet.

      [![tekintse át a többi eseményt egy időben](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. Az olajnyomás és az aktív figyelmeztetési érzékelők közvetlenül a tűz előtt vannak megszegve. A megjelenített idősorozat kibontásával megtekintheti a tüzet eredményező egyéb jeleket. Mindkét érzékelő következetesen ingadozik az idő múlásával. Az ingadozások állandó és aggasztó mintát jeleznek.

    * Módosítsa a nézet tartományát **2/24/17 20:00:00.00 – 3/10/17 20:00:00.00 (UTC) értékre**.

      [![olajnyomás és aktív figyelmeztetési érzékelők is Tüskén](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. A két éves korábbi adatmennyiség vizsgálatával egy másik, az érzékelővel megegyező állapotú esemény is kiderül.

    * Módosítsa a nézet tartományát **1/1/16-12/31/17 értékre** (az összes adatértékre).

      [![a korábbi minták megkeresése](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

A Time Series Insights és az érzékelő telemetria használatával felderítjük a régi adatokat tartalmazó hosszú távú trendet. Az új ismeretekkel a következőket teheti:

* Magyarázza el, mi történt valójában.
* Javítsa ki a problémát.
* Helyezzen üzembe jobb riasztási értesítési rendszereket.

## <a name="root-cause-analysis"></a>Kiváltó okok elemzése

1. Egyes forgatókönyvek kifinomult elemzést igényelnek az adatokban található nyomok feltárásához. Válassza ki a szélmalom **W6** a **6/25**. időpontban.

    1. Módosítsa a nézet tartományát **6/1/17 20:00:00.00 – 7/1/17 20:00:00.00 (UTC) értékre**.
    1. Válassza a **contoso növény 1** > **W6** > **biztonsági** > **VoltageActuatorSwitchWarning**lehetőséget.

       [![a nézet tartományának módosítása és a W6 kiválasztása](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. A figyelmeztetés a generátortól kapott feszültséggel kapcsolatos hibát jelez. A generátor teljes teljesítménye az aktuális intervallumban található normál paramétereken belül van. Az intervallum növelésével egy másik mintázat jelenik meg. A legördülő lista egyértelmű.

    1. Távolítsa el a **VoltageActuatorSwitchWarning** -érzékelőt.
    1. Válassza a **Generator System** > **ActivePower**elemet.
    1. Módosítsa az intervallumot a **3D**értékre.

       [![az intervallum módosítása a 3D értékre](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Az időtartomány kibővítésével meghatározhatja, hogy a probléma leállt-e, vagy folytatja-e a problémát.

    * Az időtartam meghosszabbítása 60 napra.

      [![az időtartam meghosszabbítása 60 napra](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Más szenzor-adatpontok is hozzáadhatók a nagyobb kontextus érdekében. Minél több érzékelők is megtekinthetők, a probléma megismertetését. A tényleges értékek megjelenítéséhez dobjunk egy jelölőt. 

    1. Válassza a **Generátorrendszer**lehetőséget, majd válassza ki a három érzékelőt: **GridVoltagePhase1**, **GridVoltagePhase2**és **GridVoltagePhase3**.
    1. Dobjon egy jelölőt a látható terület utolsó adatpontjára.

       [jelölő ![eldobása](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    A feszültség-érzékelők közül kettő egyformán működik, a normál paramétereken belül. Úgy tűnik, a **GridVoltagePhase3** -érzékelő a bűnös.

1. A fokozottan környezetfüggő adatbevitelsel a 3. fázis kidobása még ennél a problémánál is megjelenik. Most már jó a figyelmeztetés oka. Készen áll arra, hogy a problémát a karbantartási csapatunk is felhasználja.  

    * Módosítsa a megjelenítést úgy, hogy az összes **generátor** rendszerérzékelője ugyanazon a diagramon legyen.

      [![a Megjelenítés módosításával mindent tartalmaz](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy elvégezte a gyors üzembe helyezést, törölje a létrehozott erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden erőforrás**lehetőséget, és keresse meg a Azure Time Series Insights erőforráscsoportot.
1. Törölje a teljes erőforráscsoportot (és az abban található összes erőforrást) úgy, hogy kiválasztja az egyes erőforrások **törlését** vagy eltávolítását.

## <a name="next-steps"></a>További lépések

Készen áll saját Time Series Insights előnézeti környezet létrehozására. Kezdés:

> [!div class="nextstepaction"]
> [A Time Series Insights előzetes verzió környezetének megtervezése](time-series-insights-update-plan.md)

Ismerje meg a bemutató és funkcióinak használatát:

> [!div class="nextstepaction"]
> [A Time Series Insights Preview Explorer](time-series-insights-update-explorer.md)
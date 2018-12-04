---
title: Ismerkedés az Azure Time Series Insights (előzetes verzió) bemutató környezetben |} A Microsoft Docs
description: Megismerheti az Azure Time Series Insights (előzetes verzió) bemutató környezetben
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: 872969b0e72d32913e4528c5c6446827ae42691b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853133"
---
# <a name="explore-the-azure-time-series-insights-preview-demo-environment"></a>Ismerkedés az Azure Time Series Insights (előzetes verzió) bemutató környezetben

Ez a rövid útmutató bemutatja, hogyan kezdheti el az Azure Time Series Insights (előzetes verzió) Explorer egy ingyenes bemutató környezetben. Megismerheti, hogyan használhatja a webböngészőt jeleníthet meg nagy mennyiségű ipari IoT előzményadatok és a Time Series Insights (előzetes verzió) Explorer főbb jellemzőivel.

Az Azure Time Series Insights egy teljes körű Platform A – szolgáltatásként nyújtott betöltési, feldolgozásához, tárolásához és lekérdezése az ad hoc adatfeltárás, valamint a működési elemzés magas contextualized, time series-optimalizált IoT-méretű adatokra ajánlat biztosít. A Time Series Insights egy egyedi ajánlat, ipari IoT üzemelő példányok egyedi igényeinek megfelelően.

A bemutató környezetben egy elektromos áram generációs fiktív Contoso tárjon fel döntéstámogató információkat az adataikat, és a egy rövid alapvető okok elemzése végezni a Time Series Insights használatával jeleníti meg. Contoso működik két szél turbina-farmok mindegyike tíz turbina, és minden egyes turbina adatokról szóló jelentéseket percenként Azure IoT Hub 20 érzékelők rendelkezik. Érzékelők adatainak összegyűjtése az időjárási viszonyok, panel felébresztve & kitérése pozícióját, generátor teljesítmény, sebességváltó viselkedés és biztonsági figyelők.

A Time Series Insights frissítés elemezheti az utolsó két év – jelenleg, 40GB – jobb megismerésében és a kritikus hibák és a karbantartási problémák lassan mozgó előrejelzése és a folyamatosan növekvő adatkészlet szolgál.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Ismerkedés a Time Series Insights Explorerrel egy bemutató környezetben

1. Navigáljon a böngészőben, [insights.timeseries.azure.com/preview/samples](https://insights.timeseries.azure.com/preview/samples).  

1. Ha a rendszer kéri, jelentkezzen be a Time Series Insights Explorer az Azure-fiókja hitelesítő adataival.

### <a name="demo-step-one"></a>Bemutató első lépés

1. Vessünk egy pillantást **szélturbina sürgősségi #7 farm #1**. Ez azt már jól teljesít.  

    * Művelet: Frissítse a tartomány a `1/1/17 20:00 – 3/10/17 20:00 (UTC)` , és adja hozzá a `Farm 1 > W7 > Generator > GeneratorSpeed` érzékelő.

       ![Az egyik Gyorsútmutató][1]

1. Nemrég **Contoso található egy turbina #7**. Nézzük részletes itt. Láthatjuk, hogy az aktív időszaka alatt a fire fire riasztási érzékelőt.

    * Művelet: Frissítse a tartomány a `3/9/17 20:00 – 3/10/17 20:00 (UTC)` , és adja hozzá a `Safety > FireAlert` érzékelő.

      ![Két a rövid útmutató][2]

1. Lássuk, mi a tűz időpontjának közelében történt. Mindkét olaj nyomás és számolt a fire előtt, hanem a pont, a probléma elkerülése érdekében késő volt aktív figyelmeztetés.

    * Művelet: Adja hozzá a `Pitch > HydraulicOilPressure` érzékelő és a `Pitch > ActiveWarning` érzékelő.

      ![Három a rövid útmutató][3]

1. Ha tudjuk kicsinyítéshez láthatjuk voltak a fire vezető jelentkezik. Mindkét érzékelők ingadozott. Tehát ez történt előtt?

    * Művelet: Frissítse a tartomány a `2/24/17 20:00 – 3/10/17 20:00 (UTC)`.

      ![Négy a rövid útmutató][4]

1. A teljes két évre visszamenőleg vizsgáljuk meg, ha egy előző fire esemény ugyanezen táblákat láthatjuk. Ezeket az adatokat hogy hozhat létre olyan rendszerek ehhez hasonló problémák korai olvasásra.

    * Művelet: Frissítse a tartomány a `1/1/16 – 12/31/17` (az összes adat).

       ![Öt rövid útmutató][5]

### <a name="demo-step-two"></a>Bemutató második lépés

1. Egyéb problémák a változás is, és diagnosztizálhatja a nehezebb. A Time Series Insights segítségével nyomon követheti a problémákat, számos biztosít. Itt látható egy figyelmeztetés érzékelő szolgáltatáskimaradás a `turbine #6` a `6/25`. De tulajdonképpen mi történik?

    * Művelet: Távolítsa el a jelenlegi érzékelőket. Frissítse a tartomány a `6/1/17 20:00 – 7/1/17 20:00 (UTC)` , és adja hozzá a `Farm 1 > W6 > Safety > VoltageActuatorSwitchWarning`.

       ![Hat a rövid útmutató][6]

1. A figyelmeztetés a kimenet a generátor által éppen feszültség-hibát jelez. De mi az az oka? Az általános teljesítmény kimeneti a generátor úgy tűnik, részletes részletes időközönként. Azonban az adatok összesítésével láthatjuk végleges legördülő.

    * Művelet: Távoli a `VoltageActuatorSwitchWarning` , és adja hozzá `Generator > ActivePower` és a frissítési időköz `3d`.

       ![Hét a rövid útmutató][7]

1. Ha előre az adatkészletben, láthatjuk, ez nem egy átmeneti probléma. A probléma folytatja a műveletet.

    * Művelet: Nyomja meg a jobbra mutató nyílra kattintva folytassa a nézetet.

       ![Nyolc a rövid útmutató][8]

1. Most további részletes. Egyéb érzékelő adatpont fázis feszültség megtekintésére is. Azonban az összes megtekintése összehasonlítható. Nézzük, dobja el egy jelölő a tényleges értékek megtekintéséhez. Úgy tűnik, a 3. fázis kimeneti probléma három.

    * Művelet: `Add Generator > GridVoltagePhase1, 2, & 3`. A látható terület utolsó esetén a datapoint egy jelölő eldobása

       ![Nyolc a rövid útmutató][8]

1. Ha azt megtekinteni, mind a három ugyanazon a skálán, áttelepíteni a fázis 3 eldobási ki még nagyobb nyilvánvaló. Ezen a ponton készen állunk tekintse meg a probléma okát a figyelmeztetést, a helyes érdeklődő a karbantartási csapatunk.  

    * Művelet: Frissítse a átfedi az ugyanazon diagramléptéknek az összes érzékelő adatai jelennek meg.

       ![Kilenc a rövid útmutató][9]

## <a name="next-steps"></a>További lépések

Készen áll a saját Azure Time Series Insights (előzetes verzió) környezetet hozhat létre:

> [!div class="nextstepaction"]
> [Az Azure Time Series Insights (előzetes verzió) környezet megtervezése](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png
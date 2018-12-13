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
ms.date: 12/03/2018
ms.openlocfilehash: 76bdc122cab5b4ee87717c9c4df9774ecc3bd8f7
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888776"
---
# <a name="explore-the-azure-time-series-insights-preview-demo-environment"></a>Ismerkedés az Azure Time Series Insights (előzetes verzió) bemutató környezetben

Ez a rövid útmutató bemutatja, hogyan Ismerkedés az Azure Time Series Insight (TSI) előzetes explorer egy ingyenes bemutató környezetben. Megismerheti, hogyan jeleníthet meg nagy mennyiségű ipari IoT előzményadatokat, és az Azure Time Series Insights (előzetes verzió) explorer főbb jellemzőivel a webböngésző használatával.

Az Azure TSI biztosít egy teljes körű Platform A-szolgáltatás-ajánlat a betöltési, feldolgozásához, tárolásához és az ad hoc adatfeltárás, valamint a működési elemzés magas contextualized, time series-optimalizált IoT-méretű adatokat kérdezhet le. A Time Series Insights egy egyedi ajánlat, ipari IoT üzemelő példányok egyedi igényeinek megfelelően.

A bemutató környezetben egy elektromos áram generációs fiktív Contoso tárjon fel döntéstámogató információkat az adataikat, és a egy rövid alapvető okok elemzése végezni a TSI használatával jeleníti meg. Contoso működik két szél turbina-farmok mindegyike 10 turbina, és minden egyes turbina adatokról szóló jelentéseket percenként Azure IoT Hub 20 érzékelők rendelkezik. Érzékelők adatainak összegyűjtése az időjárási viszonyok, panel felébresztve & kitérése pozícióját, generátor teljesítmény, sebességváltó viselkedés és biztonsági figyelők.

A TSI (előzetes verzió) segítségével elemezheti az utolsó két év – jelenleg, 40 GB – jobb megismerésében és a kritikus hibák és a karbantartási problémák lassan mozgó előrejelzése és a folyamatosan növekvő adatkészlet.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Ismerkedés a Time Series Insights Explorerrel egy bemutató környezetben

1. Navigáljon a böngészőben, [insights.timeseries.azure.com/preview/samples](https://insights.timeseries.azure.com/preview/samples).  

1. Ha a rendszer kéri, jelentkezzen be a TSI Explorerben engedélyezett az Azure-fiókja hitelesítő adataival.

### <a name="demo-step-one"></a>Bemutató első lépés

1. Vessünk egy pillantást **szélturbina sürgősségi #7 szél farm #1**.  

    * **A művelet**: frissítse a tartomány a `1/1/17 20:00 – 3/10/17 20:00 (UTC)` , és adja hozzá a `Farm 1 > W7 > Generator > GeneratorSpeed` érzékelő. Ezután megjeleníti az eredményül kapott értékeket.

       ![Az egyik Gyorsútmutató][1]

1. Nemrég **Contoso található egy szélturbina sürgősségi #7**. Nézzük részletes itt. Láthatjuk, hogy az aktív időszaka alatt a fire fire riasztási érzékelőt.

    * **A művelet**: frissítse a tartomány a `3/9/17 20:00 – 3/10/17 20:00 (UTC)` , és adja hozzá a `Safety > FireAlert` érzékelő.

      ![Két a rövid útmutató][2]

1. Lássuk, mi a tűz időpontjának közelében történt. Mindkét olaj nyomás és számolt a fire előtt, hanem a pont, a probléma elkerülése érdekében késő volt aktív figyelmeztetés.

    * **A művelet**: Adja hozzá a `Pitch > HydraulicOilPressure` érzékelő és a `Pitch > ActiveWarning` érzékelő.

      ![Három a rövid útmutató][3]

1. Ha tudjuk kicsinyítéshez láthatjuk voltak a fire vezető jelentkezik. Mindkét érzékelők ingadozott. Tehát ez történt előtt?

    * **A művelet**: frissítse a tartomány a `2/24/17 20:00 – 3/10/17 20:00 (UTC)`.

      ![Négy a rövid útmutató][4]

1. A teljes két évre visszamenőleg vizsgáljuk meg, ha egy előző fire esemény ugyanezen táblákat láthatjuk. Ezeket az adatokat hogy hozhat létre olyan rendszerek ehhez hasonló problémák korai olvasásra.

    * **A művelet**: frissítse a tartomány a `1/1/16 – 12/31/17` (az összes adat).

       ![Öt rövid útmutató][5]

### <a name="demo-step-two"></a>Bemutató második lépés

1. Egyéb problémák a változás is, és diagnosztizálhatja a nehezebb. A Time Series Insights segítségével nyomon követheti a problémákat, számos biztosít. Itt látható egy figyelmeztetés érzékelő szolgáltatáskimaradás a `turbine #6` a `6/25`. De tulajdonképpen mi történik?

    * **A művelet**: távolítsa el a jelenlegi érzékelőket. Frissítse a tartomány a `6/1/17 20:00 – 7/1/17 20:00 (UTC)` , és adja hozzá a `Farm 1 > W6 > Safety > VoltageActuatorSwitchWarning`.

       ![Hat a rövid útmutató][6]

1. A figyelmeztetés a kimenet a generátor által éppen feszültség-hibát jelez. De mi az az oka? Az általános teljesítmény kimeneti a generátor úgy tűnik, részletes részletes időközönként. Azonban az adatok összesítésével láthatjuk végleges legördülő.

    * **A művelet**: távoli a `VoltageActuatorSwitchWarning` , és adja hozzá `Generator > ActivePower` és a frissítési időköz `3d`.

       ![Hét a rövid útmutató][7]

1. Ha előre az adatkészletben, láthatjuk, ez nem egy átmeneti probléma. A probléma folytatja a műveletet.

    * **A művelet**: jobb időtartomány kiterjesztése.

       ![Nyolc a rövid útmutató][8]

1. Most további részletes. Egyéb érzékelő adatpont fázis feszültség megtekintésére is. Azonban az összes megtekintése összehasonlítható. Nézzük, dobja el egy jelölő a tényleges értékek megtekintéséhez. Úgy tűnik, a 3. fázis kimeneti probléma három.

    * **A művelet**: `Add Generator > GridVoltagePhase1, 2, & 3`. A látható terület utolsó esetén a datapoint egy jelölő eldobása

       ![Nyolc a rövid útmutató][8]

1. Ha azt megtekinteni, mind a három ugyanazon a skálán, áttelepíteni a fázis 3 eldobási ki még nagyobb nyilvánvaló. Ezen a ponton készen állunk tekintse meg a probléma okát a figyelmeztetést, a helyes érdeklődő a karbantartási csapatunk.  

    * **A művelet**: átfedi az ugyanazon diagramléptéknek az összes érzékelő, megjelenítheti a képernyőn.

       ![Kilenc a rövid útmutató][9]

## <a name="next-steps"></a>További lépések

Készen áll a saját Azure TSI (előzetes verzió) környezetet hozhat létre:

> [!div class="nextstepaction"]
> [Az Azure TSI (előzetes verzió) környezet megtervezése](time-series-insights-update-plan.md)

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
---
title: 'Gyors útmutató: Ismerkedés az Azure Time Series Insights – előzetes bemutató környezetben |} A Microsoft Docs'
description: Ismerje meg az Azure Time Series Insights – előzetes bemutató környezetben.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 12/03/2018
ms.openlocfilehash: de5e853db6c6a0e98dea9251cc07b526288574e1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276832"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Gyors útmutató: Ismerkedés az Azure Time Series Insights – előzetes bemutató környezetben

Ez a rövid útmutató bemutatja, hogyan használhatja az Azure Time Series Insight előzetes explorer egy ingyenes bemutató környezetben. A Time Series Insights előzetes verziója explorer kulcsfunkciói megismerheti, hogyan jeleníthet meg nagy mennyiségű ipari IoT előzményadatokat, és azt a bemutató a webböngésző használatával.

A Time Series Insights egy teljes körű platform (PaaS) szolgáltatásajánlat biztosít. Betöltési, feldolgozhatók, tárolására, és lekérdezése rendkívül contextualized, a time series-optimalizált IoT-méretű adatokra veszélyes adatok feltárásához. Működési elemzés is tartalmazza. A Time Series Insights egy egyedi ajánlat, amely ipari IoT üzemelő példányok egyedi igényeinek megfelelő.

A bemutató környezetben egy elektromos áram generációs fiktív Contoso jeleníti meg. A környezetben a Time Series Insights tárjon fel döntéstámogató információkat a Contoso-adatokat, és a egy rövid alapvető okok elemzése végezni használhatja. Contoso két szél turbina-farmok mindegyike 10 turbina működik. Minden egyes turbina 20 percenként adatok csökkentik a az Azure IoT Hub érzékelők rendelkezik. Az érzékelők időjárási viszonyok panel felébresztve információt gyűjteni, és a pozíció, generátor teljesítmény, sebességváltó viselkedését és biztonsági figyelők yaw.

Time Series Insights előzetes verziója használatával elemezheti a Contoso – folyamatosan bővülő adatkészlethez az az előző két éven jelenleg 40 GB. Megkönnyíti, hogy jobb megismerésében és a kritikus hibák és a karbantartási problémák lassan mozgó előre jelezni.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Ismerkedés a Time Series Insights explorer egy bemutató környezetben

1. A böngészőben nyissa meg a [Contoso szél Farm környezetbe](https://insights.timeseries.azure.com/preview/samples).  

1. Ha az kéri, jelentkezzen be a Time Series Insights explorer, az Azure-fiók hitelesítő adataival.

### <a name="demo-step-1"></a>Bemutató: 1. lépés

1. Vessünk egy pillantást szélturbina sürgősségi **W7** a **Contoso üzem 1**.  

    * **A művelet**: Frissítse a tartomány a **1/1/17 20:00 – 3/10/17 20:00 (UTC)**, adja hozzá a **Contoso üzem 1** > **W7** > **generátor rendszer**   >  **GeneratorSpeed** érzékelő és a létrejövő majd megjelenített értékeket.

       ![Az egyik Gyorsútmutató][1]

1. Nemrég Contoso található egy szélturbina sürgősségi **W7**. Nézzük részletes itt. Láthatjuk, hogy a riasztás aktiválásának-érzékelő a fire során lett aktiválva.

    * **A művelet**: Frissítse a tartomány a **3/9/17 20:00 – 3/10/17 20:00 (UTC)**, és adja hozzá a **biztonsági rendszer** > **FireAlert** érzékelő.

      ![Két a rövid útmutató][2]

1. Lássuk, mi a tűz időpontjának közelében történt. Olaj nyomás és a probléma elkerülése érdekében késő volt aktív figyelmeztetés számolt a fire előtt, de idő szerint.

    * **A művelet**: Adja hozzá a **Felébresztve rendszer** > **HydraulicOilPressure** érzékelő és a **Felébresztve rendszer** > **ActiveWarning**érzékelő.

      ![Három a rövid útmutató][3]

1. Ha tudjuk kicsinyítéshez láthatjuk voltak a fire vezető jelentkezik. Mindkét érzékelők ingadozott. Tehát fordult elő előtt?

    * **A művelet**: Frissítse a tartomány a **2/24/17 20:00 – 3/10/17 20:00 (UTC)**.

      ![Négy a rövid útmutató][4]

1. A teljes két évre visszamenőleg vizsgáljuk meg, ha egy előző fire esemény ugyanezen táblákat láthatjuk. Ezeket az adatokat hogy hozhat létre olyan rendszerek, hasonló problémák egy korai.

    * **A művelet**: Frissítse a tartomány a **1/1/16 – 12/31/17** (az összes adat).

       ![Öt rövid útmutató][5]

### <a name="demo-step-2"></a>Bemutató 2. lépés.

1. Egyéb problémák a változás is, és diagnosztizálhatja a nehezebb. A Time Series Insights nyújt számos módon megfontolunk, nyomon követheti a problémákat. Itt látható egy figyelmeztetés érzékelő szolgáltatáskimaradás a **W6** a **6/25**. De tulajdonképpen mi történik?

    * **A művelet**: Távolítsa el a jelenlegi érzékelőket, frissítse a tartomány a **6/1/17 20:00-7/1/17 20:00 (UTC)**, majd adja hozzá a **Contoso üzem 1** > **W6**  >  **Biztonsági rendszer** > **VoltageActuatorSwitchWarning** érzékelő.

       ![Hat a rövid útmutató][6]

1. A figyelmeztetés a kimenet a generátor által éppen feszültség-hibát jelez. De mi az az oka? Az általános teljesítmény kimeneti a generátor úgy tűnik, részletes részletes időközönként. Azonban az adatok összesítésével végleges Gyűjtőtár látható.

    * **A művelet**: Távolítsa el a **VoltageActuatorSwitchWarning** érzékelő, adja hozzá a **generátor rendszer** > **ActivePower** érzékelő és a frissítés időközét **3d**.

       ![Hét a rövid útmutató][7]

1. Ha előre az adatkészletben, láthatjuk, hogy a probléma nem átmeneti. Folytatódik.

    * **A művelet**: A jobb oldali időtartomány kiterjesztése.

       ![Nyolc a rövid útmutató][8]

1. Most további részletes. Egyéb érzékelő adatpont fázis feszültség megtekintésére is hozzáadunk. De az összes adatponttal összehasonlítható. Nézzük, dobja el egy jelölő a tényleges értékek megtekintéséhez. Úgy tűnik, probléma és a 3. fázis kimenete van.

    * **A művelet**: Adjon hozzá **generátor rendszer** > **GridVoltagePhase1**, **GridVoltagePhase2**, és **GridVoltagePhase3** érzékelők. Az utolsó adatpont láthatóvá területén egy jelölő eldobása

       ![Nyolc a rövid útmutató][8]

1. Ha ugyanazon a skálán három adatpontok azt megtekinteni, a 3. fázis Gyűjtőtár kézenfekvő még megjelenik. Ezen a ponton készen állunk tekintse meg a probléma okát a figyelmeztetést, a helyes érdeklődő a karbantartási csapatunk.  

    * **A művelet**: Frissítse a átfedi az ugyanazon diagramléptéknek az összes érzékelő adatai jelennek meg.

       ![Kilenc a rövid útmutató][9]

## <a name="next-steps"></a>További lépések

Készen áll a saját Time Series Insights előzetes környezet létrehozásához:

> [!div class="nextstepaction"]
> [A Time Series Insights előzetes környezet megtervezése](time-series-insights-update-plan.md)

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

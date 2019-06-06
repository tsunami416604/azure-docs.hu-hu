---
title: 'Gyors útmutató: Ismerkedés az Azure Time Series Insights – előzetes bemutató környezetben |} A Microsoft Docs'
description: Ismerje meg az Azure Time Series Insights – előzetes bemutató környezetben.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 0bbab2acc9bf9e22e1d3c36336aa9dad04b0b73a
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688501"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Gyors útmutató: Ismerkedés az Azure Time Series Insights – előzetes bemutató környezetben

Ez a rövid útmutató megismerteti az Azure Time Series Insights – előzetes környezet. Az ingyenes bemutató, a, megismerkedhet a legfontosabb funkcióit, amelyek a Time Series Insights előzetes verziója lettek hozzáadva.

A Time Series Insights előzetes verziója bemutató környezetben tartalmaz egy forgatókönyv fiktív Contoso, hogy két szelet turbina farmok működik. A farm minden 10 turbina rendelkezik. Minden egyes turbina 20 percenként adatok csökkentik a az Azure IoT Hub érzékelők rendelkezik. Az érzékelők időjárási viszonyok panel lényegét, információkat gyűjthet, és a pozíció yaw. Generátor teljesítmény, sebességváltó viselkedését és biztonsági figyelők kapcsolatos információkat is rögzíti.

Ez a rövid útmutatóban megismerheti, hogyan tárjon fel döntéstámogató információkat a Contoso-adatok a Time Series Insights használatával. Is végezhet egy rövid kiváltó okok elemzése segítségével jobban megkönnyíti a kritikus hibák és a karbantartás céljából.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Ismerkedés a Time Series Insights explorer egy bemutató környezetben

A Time Series Insights előzetes verziója explorer korábbi adatok alapján mutatja be, és az alapvető okok elemzése. Első lépések:

1. Hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ha nem rendelkezik ilyennel.

1. Nyissa meg a [Contoso szél Farm bemutató](https://insights.timeseries.azure.com/preview/samples) környezetben.  

1. Ha az kéri, jelentkezzen be az Azure-fiók hitelesítő adataival a Time Series Insights explorer.

## <a name="work-with-historical-data"></a>A régebbi adatok használata

1. A **Contoso üzem 1**, tekintse meg szélturbina sürgősségi **W7**.  

   1. Módosítsa a tartomány a **1/1/17 20:00 – 3/10/17 20:00 (UTC)** .
   1. Érzékelő kiválasztásához jelölje be **Contoso üzem 1** > **W7** > **generátor rendszer** > **GeneratorSpeed** . Ezután tekintse meg az értékeket, amelyeket jelennek meg.

      [![Contoso üzemben 1 W7](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Nemrég Contoso található egy szélturbina sürgősségi **W7**. Vélemények arról, hogy mi okozta a fire eltérőek lehetnek. A Time Series Insightsban láthatjuk, hogy a riasztás aktiválásának-érzékelő a fire során lett aktiválva.

   1. Módosítsa a tartomány a **3/9/17 20:00 – 3/10/17 20:00 (UTC)** .
   1. Válassza ki **biztonsági rendszer** > **FireAlert**.

      [![A Contoso egy szélturbina sürgősségi W7 található](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Tekintse át a tűz végbement időpontja környékén rögzített eseményeket. Olaj nyomás és aktív figyelmeztetés számolt a fire előtt.

   1. Válassza ki **pályázati bemutató rendszer** > **HydraulicOilPressure**.
   1. Válassza ki **pályázati bemutató rendszer** > **ActiveWarning**.

      [![Körülbelül egy időben egyéb események áttekintése](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. Az olaj nyomás és aktív figyelmeztetés érzékelők számolt közvetlenül a fire előtt. Bontsa ki a megjelenített időn sorozat egyéb jeleket, amelyek korábban egyértelmű vezető a fire megtekintéséhez. Mindkét érzékelők ingadozott következetesen idővel. A ingadozások által megkövetelt azt jelzik, hogy egy állandó és aggasztó minta.

    * Módosítsa a tartomány a **2/24/17 20:00 – 3/10/17 20:00 (UTC)** .

      [![Olaj nyomás és aktív figyelmeztetés érzékelők is számolt](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. Egy másik fire eseményt, amely ugyanazt az érzékelő ingadozások által megkövetelt rendelkezett vizsgálata két évre visszamenőleg korábbi tárja fel.

    * Módosítsa a tartomány a **1/1/16 – 12/31/17** (az összes adat).

      [![Korábbi mintákat keressen](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

A Time Series Insights és az érzékelő telemetriát használja, hogy megismert rejtve az előzményadatokat a hosszú távú és problémás a tendencia. Ezen új elemzési azt is:

> [!div class="checklist"]
> * Azt ismerteti, mi történt.
> * Javítsa a problémát.
> * Kiváló riasztásértesítési rendszerek üzembe helyezéséhez.

## <a name="root-cause-analysis"></a>Kiváltó okok elemzése

1. Bizonyos forgatókönyvek finom keresőmotorok adatok elemzése révén kifinomult elemzést igényelnek. Válassza ki a Szélmalom **W6** dátumon **6/25**.

    1. Módosítsa a tartomány a **6/1/17 20:00-7/1/17 20:00 (UTC)** .
    1. Válassza ki **Contoso üzem 1** > **W6** > **biztonsági rendszer** > **VoltageActuatorSwitchWarning**.

       [![Módosítsa a tartomány, és válassza ki a W6](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. A figyelmeztetés a kimenet a generátor által éppen feszültség-hibát jelez. Általános teljesítménye a generátor az aktuális intervallum normál paraméterek belül működik. Az időköz növelésével egy másik áttekintéséből. Egy meghatározott Gyűjtőtár összefüggés nyilvánvaló.

    1. Távolítsa el a **VoltageActuatorSwitchWarning** érzékelő.
    1. Válassza ki **generátor rendszer** > **ActivePower**.
    1. Az az időköz módosítása **3d**.

       [![3D az időköz módosítása](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Az időtartomány kiterjesztése alapján határozható meg e a probléma leállt, vagy hogy továbbra is.

    * Terjessze ki az időtartam 60 nap.

      [![60 napig az időtartomány kiterjesztése](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Egyéb érzékelő adatpont nagyobb kontextusba lehet hozzáadni. A további érzékelők azt megtekinteni, minél teljesebb meg a probléma megértését. Nézzük, dobja el egy jelölő a tényleges értékek megtekintéséhez. 

    1. Válassza ki **generátor rendszer**, majd válassza a három érzékelők: **GridVoltagePhase1**, **GridVoltagePhase2**, és **GridVoltagePhase3**.
    1. Az utolsó adatpont láthatóvá területén egy jelölő eldobása

       [![Mutató közvetlen](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    A feszültség-érzékelő két működnek egyformán és normál paraméterek belül. Úgy tűnik, mint például a **GridVoltagePhase3** érzékelő nem sokkal.

1. Magas környezetfüggő adatokat hozzáadni a 3. fázis Gyűjtőtár még több, hogy a probléma jelenik meg. Most van egy jó érdeklődő, a figyelmeztetés oka a. Tekintse meg a probléma karbantartási csapatunk készen vagyunk.  

    * Az összes átfedő megjelenítésének módosítása **generátor rendszer** ugyanazon érzékelők diagram – méretezési csoport.

      [![Minden szükséges megjelenítésének módosítása](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="next-steps"></a>További lépések

Készen áll a saját Time Series Insights előzetes verziója környezetet hozhat létre. Indítása:

> [!div class="nextstepaction"]
> [A Time Series Insights előzetes környezet megtervezése](time-series-insights-update-plan.md)

Ismerje meg, nyissa meg a bemutatót, és funkcióihoz:

> [!div class="nextstepaction"]
> [A Time Series Insights előzetes verziója explorer](time-series-insights-update-explorer.md)

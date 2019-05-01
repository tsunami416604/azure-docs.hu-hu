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
ms.date: 04/22/2019
ms.openlocfilehash: 604603a145ab360af18ce74748707da9f5f93427
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726398"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Gyors útmutató: Ismerkedés az Azure Time Series Insights – előzetes bemutató környezetben

Ez a rövid útmutató megismerteti az Azure Time Series Insights előzetes verziója. Az ingyenes bemutató keresztül, fog ismerkedhet meg lettek hozzáadva a Time Series Insights előzetes verziója a fő funkcióját.

Az előzetes verzió bemutató környezetben egy forgatókönyv fiktív Contoso, hogy két szelet turbina farmok működik, egyenként 10 turbina tartalmazza. Minden egyes turbina 20 percenként adatok csökkentik a az Azure IoT Hub érzékelők rendelkezik. Az érzékelők időjárási viszonyok panel lényegét, információkat gyűjthet, és a pozíció yaw. Ezenkívül generátor teljesítmény, sebességváltó viselkedését és biztonsági figyeli.

 Azt ismerteti, hogyan használhatók a Time Series Insights gyakorlatban hasznosítható elemzéseket nyújt a Contoso adatok kereséséhez. Egy rövid alapvető okok elemzése jobban megkönnyíti a kritikus hibák és karbantartásával járó is fogja végezni.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Ismerkedés a Time Series Insights explorer egy bemutató környezetben

A Time Series Insights előzetes verziója explorer korábbi adatok alapján mutatja be, és az alapvető okok elemzése. Első lépések:

1. Hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ha egyik még nem hozták létre.

1. Keresse meg a [Contoso szél Farm bemutató](https://insights.timeseries.azure.com/preview/samples) környezetben.  

1. Ha az kéri, jelentkezzen be a Time Series Insights explorer, az Azure-fiókja hitelesítő adataival.

## <a name="work-with-historical-data"></a>A régebbi adatok használata

1. Tekintse meg szélturbina sürgősségi **W7** a **Contoso üzem 1**.  

    * Frissítse a tartomány a **1/1/17 20:00 – 3/10/17 20:00 (UTC)**.
    * Válassza ki a **Contoso üzem 1** > **W7** > **generátor rendszer** > **GeneratorSpeed** érzékelő. Tekintse át az eredményül kapott értékeket.

      [![Az egyik Gyorsútmutató](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Nemrég Contoso található egy szélturbina sürgősségi **W7**. Vélemények arról, hogy mi volt a fire fájlküldésre okának eltérőek lehetnek. Közelebbi vizsgálat után láthatjuk, hogy a riasztás aktiválásának-érzékelő a fire során lett aktiválva.

    * Frissítse a tartomány a **3/9/17 20:00 – 3/10/17 20:00 (UTC)**.
    * Válassza ki a **biztonsági rendszer** > **FireAlert** érzékelő.

      [![Két a rövid útmutató](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Tekintse át a tűz végbement időpontja környékén rögzített eseményeket. Olaj nyomás és aktív figyelmeztetés számolt a fire előtt.

    * Válassza ki a **Felébresztve rendszer** > **HydraulicOilPressure** érzékelő.
    * Válassza ki a **Felébresztve rendszer** > **ActiveWarning** érzékelő.

      [![Három a rövid útmutató](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. Az olaj nyomás és aktív figyelmeztetés érzékelők számolt közvetlenül a fire előtt. Bontsa ki a megjelenített idősorozatban egyéb jelzéseket jelen vezető a aktiválásának megjelenítéséhez. Mindkét érzékelők ingadozott egységesen egy állandó és aggasztó minta jelző idővel.

    * Frissítse a tartomány a **2/24/17 20:00 – 3/10/17 20:00 (UTC)**.

      [![Négy a rövid útmutató](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. Az azonos érzékelő ingadozások által megkövetelt egy másik tűz-esemény vizsgálata két év korábbi tárja fel.

    * Frissítse a tartomány a **1/1/16 – 12/31/17** (az összes adat).

      [![Öt rövid útmutató](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Azure Time Series Insights és az érzékelő telemetriai adatok segítségével, hogy megismert rejtve a korábbi adatok hosszú távú és problémás a tendencia. Ezen új elemzési el tudjuk magyarázni:

> [!div class="checklist"]
> * Mi történt
> * A probléma elhárításához
> * Kiváló riasztásértesítési rendszerek üzembe helyezéséhez.

## <a name="root-cause-analysis"></a>Kiváltó okok elemzése

1. Bizonyos forgatókönyvek finom keresőmotorok adatok elemzése révén kifinomult elemzést igényelnek. Válassza ki a Szélmalom **W6** dátumon **6/25-ös**

    * Frissítse a tartomány a **6/1/17 20:00-7/1/17 20:00 (UTC)**
    * Válassza ki a **Contoso üzem 1** > **W6** > **biztonsági rendszer** > **VoltageActuatorSwitchWarning**  érzékelő.

      [![Hat a rövid útmutató](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. A figyelmeztetés a kimenet a generátor által éppen feszültség-hibát jelez. Általános teljesítménye a generátor normál paraméterek megadva az aktuális intervallum belül működik. Az időköz növelésével egy másik áttekintéséből: nincs egyértelmű Gyűjtőtár.

    * Távolítsa el a **VoltageActuatorSwitchWarning** érzékelő.
    * Válassza ki a **generátor rendszer** > **ActivePower** érzékelő.
    * Frissítse az időtartam, amennyit **3d**.

      [![Hét a rövid útmutató](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Az időtartomány kiterjesztése alapján határozható meg e a probléma leállt, vagy hogy továbbra is.

    * Terjessze ki az időtartam 60 nap.

      [![Nyolc a rövid útmutató](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Egyéb érzékelő adatpont kiváló környezetet nyújtanak lehet hozzáadni. Megtekinthetők a további érzékelőket, minél teljesebb meg a probléma megértését. Nézzük, dobja el egy jelölő a tényleges értékek megtekintéséhez. 

    * Válassza ki a **generátor rendszer** > **GridVoltagePhase1**, **GridVoltagePhase2**, és **GridVoltagePhase3** érzékelők .
    * Az utolsó adatpont láthatóvá területén egy jelölő eldobása

      [![Kilenc a rövid útmutató](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    A három feszültség-érzékelő egyformán és normál paramétereket lévő működnek. Úgy tűnik, mint például a **GridVoltagePhase3** érzékelő nem sokkal.

1. Magas környezetfüggő adatokat hozzáadni a 3. fázis Gyűjtőtár még több, mint a hibás jelenik meg. Mi most már készen tekintse meg a probléma okát a figyelmeztetést, a helyes érdeklődő a karbantartási csapatunk.  

    * Megjelenítheti a képernyőn az összes átfedő **generátor rendszer** ugyanazon érzékelők diagram – méretezési csoport.

       [![Tíz a rövid útmutató](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="next-steps"></a>További lépések

Készen áll a saját Time Series Insights előzetes környezet létrehozásához:

> [!div class="nextstepaction"]
> [A Time Series Insights előzetes környezet megtervezése](time-series-insights-update-plan.md)
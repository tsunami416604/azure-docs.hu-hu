---
title: 'Rövid útmutató: Az előzetes bemutató környezet felfedezése – Azure Time Series Insights | Microsoft dokumentumok'
description: Fedezze fel az Azure Time Series Insights Preview bemutatókörnyezet ének főbb funkcióit.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 02/07/2020
ms.openlocfilehash: dc4a8da69b0398c6487008c106a9f5bcdb8a885e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77110235"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Rövid útmutató: Fedezze fel az Azure Time Series Insights előzetes bemutatókörnyezetét

Ez a rövid útmutató az Azure Time Series Insights előzetes verziójával kapcsolatos kezdést biztosít. Az ingyenes bemutatóban betekinthet a Time Series Insights előzetes verziójához hozzáadott legfontosabb funkciókba.

A Time Series Insights preview bemutató környezet tartalmaz egy forgatókönyv cég, Contoso, amely két szélturbina farmok működik. Minden farmon 10 turbina van. Minden turbina 20 érzékelővel rendelkezik, amelyek percenként jelentik az adatokat az Azure IoT Hubnak. Az érzékelők információkat gyűjtenek az időjárási viszonyokról, a pengeszurokról és a yaw pozícióról. A generátor teljesítményére, a sebességváltó viselkedésére és a biztonsági monitorokra vonatkozó információkat is rögzítik.

Ebben a rövid útmutatóban megtudhatja, hogyan használhatja a Time Series Insights segítségével a Contoso-adatok ban használható elemzési adatokat. Rövid kiváltó okelemzést is végezhet a kritikus hibák jobb előrejelzése és a karbantartás érdekében.

> [!IMPORTANT]
> Hozzon létre egy [ingyenes Azure-fiókot,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) ha nem rendelkezik ilyen.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Fedezze fel a Time Series Insights felfedezőt bemutatókörnyezetben

A Time Series Insights előzetes verziójú kezelője bemutatja a korábbi adatokat és a kiváltó ok elemzését. Első lépések:

1. Nyissa meg a [Contoso Wind Farm bemutató](https://insights.timeseries.azure.com/preview/samples) környezetét.  

1. Ha a rendszer kéri, jelentkezzen be a Time Series Insights-kezelőbe az Azure-fiók hitelesítő adataival.

## <a name="work-with-historical-data"></a>Korábbi adatok kal való kapcsolat

1. A **Contoso Plant 1**, nézd meg a szélturbina **W7**.  

   1. Módosítsa a nézettartományt **1/1/1/17 20:00:00.00-ra 3/10/17 20:00:00.00 (UTC) értékre.**
   1. Érzékelő kiválasztásához válassza **a Contoso Plant 1** > **W7** > Generator**System** > **GeneratorSpeed**lehetőséget. Ezután tekintse át a megjelenített értékeket.

      [![W7 a Contoso Plant 1-ben](media/v2-update-quickstart/quick-start-generator-speed.png)](media/v2-update-quickstart/quick-start-generator-speed.png#lightbox)

1. A közelmúltban, Contoso talált tüzet szélturbina **W7**. A vélemények változnak arról, hogy mi okozta a tüzet. A Time Series Insights ban megjelenik a tűz során aktivált tűzjelző érzékelő.

   1. Módosítsa a nézettartományt **3/9/17 20:00:00.00 értékre 3/10/17 20:00:00.00 (UTC) értékre.**
   1. Válassza **a Biztonsági rendszer** > **FireAlert**lehetőséget.

      [![Contoso talált egy tüzet a w7-es szélturbinában](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Tekintse át a tűz idején történt egyéb eseményeket, hogy megértse, mi történt. Az olajnyomás és az aktív figyelmeztetések a tűz előtt ugrottak meg.

   1. Válassza pitch **rendszer** > **hydraulicoilpressure**.
   1. Válassza **a Pitch System** > **ActiveWarning lehetőséget.**

      [![Más események áttekintése körülbelül ugyanabban az időben](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. Az olajnyomás és az aktív figyelmeztető érzékelők a tűz előtt megugrottak. Bontsa ki a megjelenített idősort, hogy áttekintse a tűzhöz vezető egyéb jeleket. Mindkét érzékelő folyamatosan ingadozott az idő múlásával. Az ingadozások tartós és aggasztó mintát jeleznek.

    * Módosítsa a nézettartományt **2/24/17 20:00:00.00-ra 3/10/17 20:00:00.00 (UTC) értékre.**

      [![Az olajnyomás és az aktív figyelmeztető érzékelők is tüskések](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Két évnyi történelmi adat vizsgálata egy másik tűzeseményt tár fel, aminek ugyanaz az érzékelőingadozása volt.

    * Módosítsa a nézettartományt **1/1/16-ra 12/31/17-re** (minden adat).

      [![Keresse meg a történelmi minták](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

A Time Series Insights és az érzékelő telemetriai adatok használatával felfedeztünk egy hosszú távú trendet, amely rejtve van az előzményadatokban. Ezekkel az új meglátásokkal:

* Magyarázza el, mi történt valójában.
* Javítsa ki a problémát.
* Jobb riasztási értesítési rendszerek et kell bevezetni.

## <a name="root-cause-analysis"></a>Kiváltó okok elemzése

1. Egyes forgatókönyvek kifinomult elemzést igényelnek az adatokban lévő nyomok feltárásához. Válassza ki a **W6** szélmalom **6/25**dátumát.

    1. Módosítsa a nézettartományt **6/1/17 20:00:00.00-ra 7/1/17 20:00:00.00 (UTC) értékre.**
    1. Válassza **a Contoso Plant 1** > **W6** > Biztonsági**rendszer** > **feszültsége**

       [![A nézettartomány módosítása és a W6 kiválasztása](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. A figyelmeztetés a generátor feszültségével kapcsolatos problémát jelez. A generátor teljes teljesítménye az aktuális intervallumban a normál paramétereken belül van. Az intervallum növelésével egy másik minta jelenik meg. A lemorzsolódás nyilvánvaló.

    1. Távolítsa el a **VoltageActuatorSwitchWarning** érzékelőt.
    1. Válassza **ki a Generátor rendszer** > **ActivePower**.
    1. Módosítsa az intervallumot **3d-re.**

       [![Az időköz módosítása 3d-re](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Az időtartomány bővítésével meghatározhatjuk, hogy a probléma leállt-e, vagy folytatódik-e.

    * Hosszabbítsa meg az időtartamot 60 napra.

      [![Az időtartam meghosszabbítása 60 napra](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Más érzékelő adatpontok is hozzáadhatók, hogy nagyobb környezetet biztosítsanak. Minél több érzékelőt látunk, annál jobban megértjük a problémát. Dobjunk le egy jelölőt a tényleges értékek megjelenítéséhez. 

    1. Válassza **a Generátorrendszer**lehetőséget, majd válasszon három érzékelőt: **GridVoltagePhase1**, **GridVoltagePhase2**és **GridVoltagePhase3**.
    1. Jelölő eldobása a látható terület utolsó adatpontjára.

       [![Jelölő eldobása](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    A feszültségérzékelők közül kettő összehasonlíthatóan és normál paramétereken belül működik. Úgy tűnik, a **GridVoltagePhase3** érzékelő a tettes.

1. A rendkívül kontextuális adatok hozzáadásával a fázis 3 lemorzsolódása még inkább problémát jelent. Nos, van egy jó nyomunk a figyelmeztetés okához. Készen állunk arra, hogy a problémát a karbantartó csapatunkhoz utaljuk.  

    * Módosítsa a kijelzőt úgy, hogy az összes **generátorrendszer-érzékelőt** átfedje ugyanazon a diagramskálán.

      [![A kijelző módosítása úgy, hogy minden](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy befejezte a rövid útmutatót, törölje a létrehozott erőforrásokat:

1. Az [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza az **Összes erőforrás**lehetőséget, és keresse meg az Azure Time Series Insights erőforráscsoportot.
1. Törölje a teljes erőforráscsoportot (és a benne lévő összes erőforrást) az egyes erőforrások **törlése** vagy eltávolítása külön-külön lehetőség kiválasztásával.

## <a name="next-steps"></a>További lépések

Készen áll saját Time Series Insights előzetes környezet létrehozására. Kezdés:

> [!div class="nextstepaction"]
> [A Time Series Insights előzetes környezetének megtervezése](time-series-insights-update-plan.md)

Ismerje meg a demót és annak funkcióit:

> [!div class="nextstepaction"]
> [A Time Series Insights előzetes verziójának felfedezője](time-series-insights-update-explorer.md)
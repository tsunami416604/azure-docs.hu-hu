---
title: Az előzetes verziójú környezet diagnosztizálása és megoldása – Azure Time Series Insights | Microsoft Docs
description: Ismerje meg, hogyan diagnosztizálhatja és elháríthatja a Azure Time Series Insights előzetes verziójának környezetét.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: df8300e84309a874faa4b1c06891a4c5b549fce6
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014774"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Előzetes verziójú környezet diagnosztizálása és megoldása

Ez a cikk számos gyakori problémát foglal össze, amelyek a Azure Time Series Insights előnézeti környezettel való együttműködés során merülhetnek fel. A cikk az egyes problémák lehetséges okait és megoldásait is ismerteti.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Probléma: nem találom a környezetet az előnézeti Explorerben

Ez a probléma akkor fordulhat elő, ha nem rendelkezik a Time Series Insights-környezet eléréséhez szükséges engedélyekkel. A felhasználóknak olvasó szintű hozzáférési szerepkörre van szükségük Time Series Insights környezetük megtekintéséhez. Az aktuális hozzáférési szintek ellenőrzéséhez és további hozzáférés biztosításához lépjen a [Azure Portal](https://portal.azure.com/)Time Series Insights erőforrásának **adatelérési házirendek** szakaszára.

  [![környezet](media/v2-update-diagnose-and-troubleshoot/environment.png)](media/v2-update-diagnose-and-troubleshoot/environment.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Probléma: az előnézeti Explorerben nem láthatók az adathalmazok

Számos gyakori oka lehet annak, hogy miért nem látja az adatait a [Azure Time Series Insights Preview Explorerben](https://insights.timeseries.azure.com/preview).

- Előfordulhat, hogy az eseményforrás nem fogadja az adatgyűjtést.

    Győződjön meg arról, hogy az eseményforrás, amely egy Event hub vagy egy IoT hub, fogadja a címkékből vagy példányokból származó adatok fogadását. Az ellenőrzéshez nyissa meg az erőforrás áttekintés lapját a Azure Portal.

    [![irányítópult – áttekintés](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png)](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png#lightbox)

- Az eseményforrás-adatforrás nem JSON formátumú.

    A Time Series Insights csak a JSON-fájlokat támogatja. JSON-minták, lásd: [támogatott JSON-alakzatok](./how-to-shape-query-json.md).

- Az eseményforrás kulcsa hiányzik egy szükséges engedély.

  * Egy IoT hub esetében meg kell adnia a **Service kapcsolódási** engedéllyel rendelkező kulcsot.

    [![Konfiguráció](media/v2-update-diagnose-and-troubleshoot/configuration.png)](media/v2-update-diagnose-and-troubleshoot/configuration.png#lightbox)

  * Ahogy az előző ábrán is látható, a **iothubowner** és a **szolgáltatás** mindkét szabályzata működik, mert a **szolgáltatás csatlakozási** engedéllyel rendelkeznek.
  * Az Event hub esetében meg kell adnia a **figyelés** engedéllyel rendelkező kulcsot.
  
    [Engedélyek ![](media/v2-update-diagnose-and-troubleshoot/permissions.png)](media/v2-update-diagnose-and-troubleshoot/permissions.png#lightbox)

  * Ahogy az előző ábrán is látható, mindkét **olvasási** és **kezelési** házirend működik, mert a **figyelés** engedéllyel rendelkezik.

- A megadott fogyasztói csoport nem kizárólag Time Series Insights.

    Az IoT hub vagy az Event hub regisztrálása során meg kell adnia az adatolvasáshoz használt fogyasztói csoportot. Ennek a fogyasztói csoportnak egyedinek kell lennie egy adott környezetben. Ha a fogyasztói csoport meg van osztva, a mögöttes Event hub automatikusan leválasztja az egyik olvasót véletlenszerűen. Adjon meg egy egyedi fogyasztói csoportot, amelyből a Time Series Insights olvasható.

- A kiépítés idején megadott idősorozat-azonosító tulajdonság helytelen, hiányzó vagy NULL értékű.

    Ez a probléma akkor fordulhat elő, ha az idősorozat-azonosító tulajdonságot helytelenül konfigurálták a környezet kiépítés időpontjában. További információ: [ajánlott eljárások az idősorozat-azonosító kiválasztásához](./time-series-insights-update-how-to-id.md). Jelenleg nem frissíthet meglévő Time Series Insights környezetet más idősorozat-azonosító használatára.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Probléma: egyes adatértékek, de hiányoznak

Előfordulhat, hogy az idősorozat-azonosító nélkül küld adatokat.

- Ez a probléma akkor fordulhat elő, ha a hasznos adatok idősorozat-azonosítója mező nélkül küld eseményeket. További információ: [támogatott JSON-alakzatok](./how-to-shape-query-json.md).
- Ez a probléma akkor fordulhat elő, ha a környezetét szabályozzák.

    > [!NOTE]
    > A Time Series Insights jelenleg legfeljebb 6 Mbps mennyiségű betöltési sebességet támogat.

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Probléma: az esemény forrásának időbélyeg-tulajdonságának neve nem működik

Győződjön meg arról, hogy a nevét és értékét megfelelnek-e a következő szabályok:

* Az időbélyeg-tulajdonság neve megkülönbözteti a kis-és nagybetűket.
* Az eseményforrás által a JSON-karakterláncként kapott időbélyeg-tulajdonság értéke `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Az ilyen típusú például `“2008-04-12T12:53Z”`.

Az időbélyeg-tulajdonságnév rögzítésének és megfelelő működésének legegyszerűbb módja a Time Series Insights Preview Explorer használata. A Time Series Insights Preview Explorerben a diagramon kiválaszthat egy időszakot, miután megadták az időbélyeg-tulajdonság nevét. Kattintson a jobb gombbal a kijelölésre, és válassza az **események feltárása** lehetőséget. Az első oszlop fejléce az időbélyeg-tulajdonság neve. `($ts)` a Word `Timestamp`mellett kell lennie a következő helyett:

* `(abc)`, amely azt jelzi, hogy a Time Series Insights sztringként beolvassa az adatértékeket.
* A **Naptár** ikon, amely azt jelzi, hogy Time Series Insights beolvassa az adatértéket datetime formátumban.
* `#`, amely azt jelzi, hogy Time Series Insights egész számként olvassa az adatértékeket.

Ha az időbélyegző tulajdonság nincs explicit módon megadva, az esemény IoT hub-vagy Event hub-várólistán lévő az alapértelmezett időbélyegző lesz használva.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Probléma: nem tudom megtekinteni a meleg áruházból származó adatok az Explorerben

- Lehet, hogy a közelmúltban kiépített egy meleg áruházat, és az adatai továbbra is áramlanak.
- Lehet, hogy törölte a meleg áruházat, amely esetben elveszett volna az adatai.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Probléma: nem tudom megtekinteni vagy szerkeszteni az idősorozat-modellt

- Lehet, hogy egy Time Series Insights S1 vagy S2 környezethez fér hozzá.

   Az idősorozat-modellek csak utólagos elszámolású környezetekben támogatottak. Az S1 vagy S2 környezet Time Series Insights Preview Explorerben való elérésével kapcsolatos további információkért lásd: [adatok megjelenítése az Explorerben](./time-series-insights-update-explorer.md).

   [![hozzáférés](media/v2-update-diagnose-and-troubleshoot/access.png)](media/v2-update-diagnose-and-troubleshoot/access.png#lightbox)

- Előfordulhat, hogy nincs engedélye a modell megtekintésére és szerkesztésére.

   A felhasználóknak közreműködői szintű hozzáférésre van szükségük az idősorozat-modell szerkesztéséhez és megtekintéséhez. Az aktuális hozzáférési szintek ellenőrzéséhez és további hozzáférés biztosításához lépjen a Azure Portal Time Series Insights erőforrásának **adatelérési házirendek** szakaszára.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Probléma: az előnézeti tallózóban lévő összes példány szülője hiányzik

Ez a probléma akkor fordulhat elő, ha a környezet nem rendelkezik meghatározott idősorozat-modell hierarchiával. További információ: [Time Series-modellek használata](./time-series-insights-update-how-to-tsm.md).

  [idősorozat-modellek ![](media/v2-update-diagnose-and-troubleshoot/tsm.png)](media/v2-update-diagnose-and-troubleshoot/tsm.png#lightbox)

## <a name="next-steps"></a>Következő lépések

- Olvasási [munka az Idősorozat-modellekkel](./time-series-insights-update-how-to-tsm.md).
- További információ a [támogatott JSON-alakzatokról](./how-to-shape-query-json.md).
- Tekintse át a [tervezést és a korlátozásokat](./time-series-insights-update-plan.md) Azure Time Series Insights előzetes verzióban.

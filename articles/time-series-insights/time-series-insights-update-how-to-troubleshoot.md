---
title: Hogyan diagnosztizálása és hibaelhárítása az Azure Time Series Insights (előzetes verzió) |} A Microsoft Docs
description: Megértse, hogyan diagnosztizálása és hibaelhárítása az Azure Time Series insights (előzetes verzió)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: d13d373169287a0ec5931d5437b0a3bc70ecd79a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856040"
---
# <a name="how-to-diagnose-and-troubleshoot"></a>Hogyan diagnosztika és hibaelhárítás

Ez a cikk számos gyakori problémák merülhetnek fel az Azure Time Series Insights (TSI) környezetében működő foglalja össze. A cikk ismerteti az egyes lehetséges okait és megoldásait is.

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Probléma: Adatok nem látható a Time Series Insights (előzetes verzió) Explorer

Miért nem láthatja az adatokat az Azure TSI Explorer több gyakori oka is van:

1. Előfordulhat, hogy nem fogad adatokat az eseményből.

    Győződjön meg arról, hogy az eseményforrás (Event Hub vagy az IoT Hub) adatokat fogad a címkék / példányok. Ezt az erőforrást az Azure Portal áttekintés oldalán navigálva teheti meg.

    ![Irányítópult – elemzések][1]

1. Az eseményadatok forrása nem a JSON formátumban van.

    Az Azure TSI támogatja a csak a JSON-adatokat. JSON-minták, lásd: [támogatott JSON-alakzatok](./how-to-shape-query-json.md).

1. Az esemény forrása kulcs hiányzik egy szükséges engedély

    ![konfiguráció][2]

    * IoT hub meg kell adnia a kulcsot, amelynek a connect engedély szolgáltatás.
    * A szabályzatok közül választhat az előző képen látható módon *iothubowner* és a szolgáltatás akkor működik, mert mindkét szolgáltatás a connect engedély.
    * Az eseményközpontok felé meg kell adnia a kulcsot, amely figyelési engedéllyel rendelkezik.
    * Amint az előző képen látható, vagy a házirendek és kezelheti a akkor működik, mert is rendelkezik figyelési engedéllyel.

    ![engedélyek][3]

1. A megadott fogyasztói csoportot nem kizárólagos, a TSI

    Során, de az IoT Hub eszközregisztrációs vagy egy eseményközpontba adja meg a fogyasztói csoportot, amely az adatok olvasása kell használni. Ezt a fogyasztói csoportot nem kell megosztani. A fogyasztói csoportot meg van osztva, ha az alapul szolgáló eseményközpont automatikusan bontja a kapcsolatot az olvasók egy véletlenszerűen. Adjon meg egy egyedi felhasználói csoport a TSI olvasni.

## <a name="problem-some-data-is-shown-but-some-is-missing"></a>Probléma: Néhány adat jelenik meg, de néhány hiányzik

Ez akkor fordulhat elő, mert a környezet szabályozás alatt áll.

> [!NOTE]
> Jelenleg az Azure TSI 6 MB/s maximális betöltési sebesség támogatja.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Probléma: A forrás timestamp tulajdonság neve beállítás nem működik

Győződjön meg arról, hogy a nevét és értékét megfelelnek-e a következő szabályok:

* A **időbélyeg** tulajdonság neve megkülönbözteti a kis-és nagybetűket.
* A **időbélyeg** tulajdonság értéke, amely egy JSON-karakterlánc, mint az eseményforrás származó kell rendelkeznie a formátum `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Az ilyen típusú például `“2008-04-12T12:53Z”`.

Győződjön meg arról, hogy az időbélyegző-tulajdonság neveként rögzített, és megfelelően működik a TSI Explorerben engedélyezett használandó legegyszerűbb módja. A TSI Explorerben használja a diagram egy bizonyos idő után az időbélyegző-tulajdonság neveként a megadott kiválasztásához. Kattintson a jobb gombbal a kijelölt, és válassza ki a **események tallózása** lehetőséget. Az első oszlop fejlécére kell lennie a **időbélyeg** tulajdonság nevét, és rendelkeznie kell egy `($ts)` mellett a word `Timestamp`, helyett:

* `(abc)`, azt jelzi, amely a TSI az adatértékek karakterláncként van olvasása
* Naptár ikonra, amely azt jelzi a TSI éppen olvas az adatérték, mint a dátum és idő
* `#`, amely azt jelzi a TSI éppen olvas az adatértékek egész számként

## <a name="problem-my-time-series-id-property-is-incorrect-missing-or-null"></a>Probléma: A saját Time Series azonosító tulajdonság értéke helytelen, hiányzik vagy null értékű

Ez akkor fordulhat elő, ha a **Time Series azonosító** tulajdonság helytelenül van konfigurálva a környezet kiépítési időpontjában. Tekintse meg a [ajánlott eljárások a Time Series ID kiválasztására vonatkozó](./time-series-insights-update-how-to-id.md) kiválasztásakor annak egy **Time Series azonosító**. Jelenleg egy másik meglévő TSI (előzetes verzió) környezet nem frissíthető **Time Series azonosító**.

## <a name="problem-all-my-instances-in-time-series-insights-preview-explorer-dont-have-a-parent"></a>Probléma: A Time Series Insights (előzetes verzió) Explorer szereplő összes példányt nem rendelkezik a szülő

Ez akkor fordulhat elő, ha a környezet nem rendelkezik egy **Idősorozat-modell** definiált hierarchiában. Ez a cikk további információt talál a [Time Series modellek használata](./time-series-insights-update-how-to-tsm.md).

## <a name="next-steps"></a>További lépések

* Olvasási [Time Series modellek használata](./time-series-insights-update-how-to-tsm.md).

* Olvasási [támogatott JSON-alakzatok](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[2]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[3]: media/v2-update-diagnose-and-troubleshoot/permissions.png
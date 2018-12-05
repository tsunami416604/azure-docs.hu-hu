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
ms.date: 12/03/2018
ms.openlocfilehash: 71ff435accc2c9c50533a31e3196905816cc3600
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868242"
---
# <a name="how-to-diagnose-and-troubleshoot"></a>Hogyan diagnosztika és hibaelhárítás

Ez a cikk számos gyakori problémák merülhetnek fel az Azure Time Series Insights (TSI) környezetében működő foglalja össze. A cikk ismerteti az egyes lehetséges okait és megoldásait is.

## <a name="problem-i-cant-find-my-environment-in-the-time-series-insights-preview-explorer"></a>Probléma: nem található a környezetben a Time Series Insights (előzetes verzió) Explorerben

Ez akkor fordulhat elő, ha nem rendelkezik engedélyekkel a TSI-környezet eléréséhez. A felhasználóknak kell "olvasó" szintű hozzáféréssel szerepkör TSI környezetükben megtekintéséhez. Előfordulhat, hogy ellenőrizze a jelenlegi hozzáférési szintet, és további hozzáférést biztosít az adathozzáférési házirendek a szakasz a TSI-erőforráson felkeresésével [az Azure Portal](https://portal.azure.com/).

  ![environment][1]

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Probléma: Adatok nem látható a Time Series Insights (előzetes verzió) Explorerben

Miért nem láthatja az adatokat számos gyakori oka lehet a [(előzetes verzió) az Azure TSI explorer](https://insights.timeseries.azure.com/preview):

1. Előfordulhat, hogy nem fogad adatokat az eseményből.

    Győződjön meg arról, hogy az eseményforrás (Event Hub vagy az IoT Hub) adatokat fogad a címkék / példányok. Ezt az erőforrást az Azure Portal áttekintés oldalán navigálva teheti meg.

    ![Irányítópult – elemzések][2]

1. Az eseményadatok forrása nem a JSON formátumban van.

    Az Azure TSI támogatja a csak a JSON-adatokat. JSON-minták, lásd: [támogatott JSON-alakzatok](./how-to-shape-query-json.md).

1. Az esemény forrása kulcs hiányzik egy szükséges engedély

    * IoT hub meg kell adnia a kulcsot, amelynek a connect engedély szolgáltatás.

    ![konfiguráció][3]

    * A szabályzatok közül választhat az előző képen látható módon *iothubowner* és a szolgáltatás akkor működik, mert mindkét szolgáltatás a connect engedély.
    * Az eseményközpontok felé meg kell adnia a kulcsot, amely figyelési engedéllyel rendelkezik.
  
    ![engedélyek][4]

    * A szabályzatok közül választhat az előző képen látható módon **olvasása** és **kezelése** akkor működik, mert egyaránt rendelkezik **figyelésére** engedéllyel.

1. A megadott fogyasztói csoportot nem kizárólagos, a TSI

    Az IoT Hub- vagy Event Hub-regisztráció során adja meg a fogyasztói csoportot, amely az adatok olvasása kell használni. Adott felhasználói csoportban nem kell megosztani. A fogyasztói csoportot meg van osztva, ha az alapul szolgáló Eseményközpont automatikusan bontja a kapcsolatot az olvasók egy véletlenszerűen. Adjon meg egy egyedi felhasználói csoport a TSI olvasni.

1. A Time Series azonosító a megadott kiépítési időpontjában tulajdonság helytelen, hiányzik vagy null értékű

    Ez akkor fordulhat elő, ha a **Time Series azonosító** tulajdonság helytelenül van konfigurálva a környezet kiépítési időpontjában. Tekintse át a [ajánlott eljárások a Time Series ID kiválasztására vonatkozó](./time-series-insights-update-how-to-id.md). Jelenleg nem frissíthető, egy meglévő Time Series Insights frissítési környezet egy másik **Time Series azonosító**.

## <a name="problem-some-data-is-shown-but-some-is-missing"></a>Probléma: Néhány adat jelenik meg, de néhány hiányzik

1. A Time Series azonosító nélkül adatok küldése

    Ez a hiba akkor fordulhat elő, ha a Time Series azonosító mező az adattartalomban szereplő nélkül események küldése folyamatban van. Lásd: [támogatott JSON-alakzatok](./how-to-shape-query-json.md) további információt.

1. Ez akkor fordulhat elő, mert a környezet szabályozás alatt áll.

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

Ha a **időbélyeg** tulajdonság nincs explicit módon beállítva, hogy használja egy eseményt az IoT Hub- vagy Event Hub **sorba helyezésekor** a alapértelmezett időbélyegzőként.

## <a name="problem-i-cant-edit-or-view-my-time-series-model"></a>Probléma: nem szerkesztheti vagy saját Idősorozat-modell megtekintése

1. A Time Series Insights S1 vagy S2 környezetet, előfordulhat, hogy elérése

   Csak a Time Series modellek támogatottak **PAYG** környezetekben. Az S1/S2 környezet eléréséhez a Time Series Insights frissítés Explorer jelenik meg ebben a cikkben talál további információt.

   ![hozzáférés][5]

1. Előfordulhat, hogy nincs engedélye megtekintése és szerkesztése a modell

   Felhasználók "közreműködő" szintű szerkeszthet, és megtekintheti az Idősorozat-modell hozzá kell férniük. Előfordulhat, hogy ellenőrizze a jelenlegi hozzáférési szintet, és az adathozzáférési házirendek szakaszban ellátogat az Azure Portalon a Time Series Insights-erőforrás a további hozzáférés biztosítása.

## <a name="problem-all-my-instances-in-time-series-insights-preview-explorer-dont-have-a-parent"></a>Probléma: A Time Series Insights (előzetes verzió) explorer szereplő összes példányt nem rendelkezik a szülő

Ez akkor fordulhat elő, ha a környezet nem rendelkezik egy **Idősorozat-modell** definiált hierarchiában. Ez a cikk további információt talál a [Time Series modellek használata](./time-series-insights-update-how-to-tsm.md).

  ![TSM][6]

## <a name="next-steps"></a>További lépések

Olvasási [Time Series modellek használata](./time-series-insights-update-how-to-tsm.md).

Olvasási [támogatott JSON-alakzatok](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/environment.png
[2]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[3]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[4]: media/v2-update-diagnose-and-troubleshoot/permissions.png
[5]: media/v2-update-diagnose-and-troubleshoot/access.png
[6]: media/v2-update-diagnose-and-troubleshoot/tsm.png
---
title: Diagnosztika és hibaelhárítás az Azure Time Series Insights előzetes verziója |} A Microsoft Docs
description: Megtudhatja, hogyan diagnosztizálása és hibaelhárítása az Azure Time Series Insights előzetes verziójával.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: eb1c302bf1954492ba2a7a78d16fc697fdf4b687
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080437"
---
# <a name="diagnose-and-troubleshoot"></a>Diagnosztika és hibaelhárítás

Ez a cikk összefoglalja számos gyakori problémák merülhetnek fel az Azure Time Series Insights – előzetes környezettel való munka során. A cikk is ismerteti a lehetséges okait és megoldásait minden probléma.

## <a name="problem-i-cant-find-my-environment-in-the-time-series-insights-preview-explorer"></a>Probléma: A Time Series Insights előzetes verziója Explorer nem találom a saját környezetben

Ez a probléma akkor fordulhat elő, ha nem rendelkezik engedélyekkel a Time Series Insights-környezet eléréséhez. Felhasználók megtekintése a Time Series Insights-környezet olvasó szintű hozzáférés szerepkörre van szüksége. Ellenőrizze a jelenlegi hozzáférési szintekkel, és további hozzáférést biztosítani, keresse fel az adathozzáférési házirendek a szakasz a Time Series Insights-erőforrás a a [az Azure portal](https://portal.azure.com/).

  ![Környezet][1]

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Probléma: Adatot nem látható a Time Series Insights előzetes verziója Explorerben

Miért nem láthatja az adatokat számos gyakori oka lehet a [Azure Time Series Insights – előzetes explorer](https://insights.timeseries.azure.com/preview).

- Előfordulhat, hogy nem fogad adatokat az eseményből.

    Győződjön meg arról, hogy az eseményforrás, amely egy event hubot vagy az IoT hub, a címkék vagy a példányok kapja az adatokat. Győződjön meg arról, hogy nyissa meg az erőforrást az Azure Portal áttekintés oldalán.

    ![Irányítópult – elemzések][2]

- Az eseményadatok forrása nem JSON formátumú.

    A Time Series Insights támogatja a csak a JSON-adatokat. JSON-minták, lásd: [támogatott JSON-alakzatok](./how-to-shape-query-json.md).

- Az esemény forrása kulcs hiányzik egy szükséges engedéllyel.

  * Egy IoT hubot, meg kell adnia a kulcsot, amelynek **szolgáltatás csatlakozása** engedéllyel.

    ![Konfiguráció][3]

  * A házirendek mindkét az előző képen látható módon **iothubowner** és **szolgáltatás** működik, mert az **szolgáltatás csatlakozása** engedéllyel.
  * Az eseményközpontok felé, meg kell adnia a kulcsot, amelynek **figyelésére** engedéllyel.
  
    ![Engedélyek][4]

  * Mindkét az előző képen látható módon a **olvasási** és **kezelése** házirendek működik, mert az **figyelésére** engedéllyel.

- A megadott fogyasztói csoportot nem kizárólagos, a Time Series Insights.

    Az IoT hub- vagy event hub-regisztráció során adja meg a fogyasztói csoportot, amellyel beolvashatja az adatokat. Ne ossza meg adott felhasználói csoportban. A fogyasztói csoportot meg van osztva, ha az alapul szolgáló eseményközpont automatikusan bontja a kapcsolatot az olvasók egy véletlenszerűen kiválasztott. Adjon meg egy egyedi felhasználói csoport számára a Time Series Insights olvasni.

- A Time Series azonosító a megadott kiépítési időpontjában tulajdonság helytelen, hiányzik vagy null értékű.

    Ez a probléma akkor fordulhat elő, ha a Time Series azonosító tulajdonság helytelenül van konfigurálva a környezet kiépítési időpontjában. További információkért lásd: [ajánlott eljárások a Time Series ID kiválasztására vonatkozó](./time-series-insights-update-how-to-id.md). Jelenleg nem frissíthető, egy meglévő Time Series Insights-környezet használatára egy különböző Time Series azonosítóját.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Probléma: Bizonyos adatokat jeleníti meg, de néhány hiányzik

Előfordulhat, hogy anélkül, hogy a Time Series azonosító. adatokat küldő

- A probléma akkor fordulhat elő, amikor eseményeket küldeni a Time Series azonosító mező, a hasznos adatok nélkül. További információkért lásd: [támogatott JSON-alakzatok](./how-to-shape-query-json.md).

- Ez a probléma akkor fordulhat elő, mert a környezet szabályozás alatt áll.

    > [!NOTE]
    > Jelenleg a Time Series Insights támogatja a maximális feldolgozási sebessége 6 MB/s.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Probléma: A forrás Timestamp tulajdonság neve beállítás nem működik

Győződjön meg arról, hogy a nevét és értékét megfelelnek-e a következő szabályok:

* A időbélyeg-tulajdonság neve megkülönbözteti a kis-és nagybetűket.
* A Timestamp tulajdonság értéke, amely egy JSON-karakterlánc, mint az eseményforrás származik formátuma a `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Az ilyen típusú például `“2008-04-12T12:53Z”`.

Győződjön meg arról, hogy az időbélyegző-tulajdonság neveként rögzített, és megfelelően működik a Time Series Insights előzetes verziója kezelővel legegyszerűbb módja. A Time Series Insights előzetes verziója Explorerben használja a diagram egy bizonyos idő után az időbélyegző-tulajdonság neveként a megadott kiválasztásához. Kattintson a jobb gombbal a kijelölt, és válassza ki a **események tallózása** lehetőséget. Az első oszlop fejlécére a időbélyegző-tulajdonság neve. Rendelkeznie kell `($ts)` mellett a word `Timestamp`, helyett:

* `(abc)`, ami azt jelenti, hogy a Time Series Insights olvassa be az adatértékek karakterláncként.
* Naptár ikonra, amely azt jelzi, hogy a Time Series Insights olvassa be az értéket, datetime.
* `#`, ami azt jelenti, hogy ha a Time Series Insights az adatértékek olvassa be a egész számként.

Az időbélyeg-tulajdonság nincs explicit módon meghatározva, ha egy eseményt az IoT hub- vagy event hub sorba helyezésekor használatos, az alapértelmezett időbélyeg.

## <a name="problem-i-cant-edit-or-view-my-time-series-model"></a>Probléma: Nem szerkeszthetők vagy saját Idősorozat-modell megtekintése

- Lehetséges, hogy a Time Series Insights S1 vagy S2 környezetet használja.

   Time Series modellek támogatottak, csak a Használatalapú környezetekben. A Time Series Insights előzetes verziója Explorer az S1/S2 környezet elérésével további információkért lásd: [megjelenítheti az Intéző adatait](./time-series-insights-update-explorer.md).

   ![Hozzáférés][5]

- Előfordulhat, hogy nincs engedélyek megtekintése és szerkesztése a modellt.

   A felhasználóknak kell szerkesztheti és megtekintheti az Idősorozat-modell közreműködője szintű hozzáféréssel. Ellenőrizze a jelenlegi hozzáférési szintekkel, és további hozzáférést biztosítani, keresse fel az adathozzáférési házirendek a szakasz a Time Series Insights-erőforrást az Azure Portalon.

## <a name="problem-all-my-instances-in-the-time-series-insights-preview-explorer-dont-have-a-parent"></a>Probléma: A Time Series Insights előzetes verziója Explorer összes saját példány nem rendelkezik a szülő

Ez a probléma akkor fordulhat elő, ha a környezet nem rendelkezik meghatározott Idősorozat-modell hierarchiába. További információkért lásd: [Time Series modellek](./time-series-insights-update-how-to-tsm.md).

  ![Time Series modellek][6]

## <a name="next-steps"></a>További lépések

- Olvasási [Time Series modellek](./time-series-insights-update-how-to-tsm.md).
- Olvasási [támogatott JSON-alakzatok](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/environment.png
[2]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[3]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[4]: media/v2-update-diagnose-and-troubleshoot/permissions.png
[5]: media/v2-update-diagnose-and-troubleshoot/access.png
[6]: media/v2-update-diagnose-and-troubleshoot/tsm.png
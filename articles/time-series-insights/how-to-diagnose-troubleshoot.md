---
title: Gen2-környezet diagnosztizálása és megoldása – Azure Time Series Insights | Microsoft Docs
description: Ismerje meg, hogyan diagnosztizálhatja és elháríthatja Azure Time Series Insights Gen2-környezetét.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: 106600b608586175cbab1098cf0eb7ac6fad94fa
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540302"
---
# <a name="diagnose-and-troubleshoot-an-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2-környezet diagnosztizálása és megoldása

Ez a cikk a Azure Time Series Insights Gen2-környezettel való együttműködés során felmerülő gyakori problémákat foglalja össze. A cikk az egyes problémák lehetséges okait és megoldásait is ismerteti.

## <a name="problem-i-cant-find-my-environment-in-the-gen2-explorer"></a>Probléma: nem találom a környezetet a Gen2 Explorerben

Ez a probléma akkor fordulhat elő, ha nem rendelkezik a Time Series Insights-környezet eléréséhez szükséges engedélyekkel. A felhasználóknak olvasó szintű hozzáférési szerepkörre van szükségük Time Series Insights környezetük megtekintéséhez. Az aktuális hozzáférési szintek ellenőrzéséhez és további hozzáférés biztosításához lépjen a [Azure Portal](https://portal.azure.com/)Time Series Insights erőforrásának **adatelérési házirendek** szakaszára.

  [![Ellenőrizze az adatelérési házirendeket.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-gen2-explorer"></a>Probléma: a Gen2 Explorerben nem láthatók az adattárolók

Számos gyakori oka lehet annak, hogy az adatai nem jelennek meg az [Azure Time Series Insights Gen2 Explorerben](https://insights.timeseries.azure.com/preview).

- Előfordulhat, hogy az eseményforrás nem fogadja az adatgyűjtést.

    Győződjön meg arról, hogy az eseményforrás, amely egy Event hub vagy egy IoT hub, fogadja a címkékből vagy példányokból származó adatok fogadását. Az ellenőrzéshez nyissa meg az erőforrás áttekintés lapját a Azure Portal.

    [![Tekintse át az irányítópult metrikáinak áttekintése című témakört.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Az eseményforrás-adatforrás nem JSON formátumú.

    A Time Series Insights csak a JSON-fájlokat támogatja. JSON-minták esetén olvassa el a [támogatott JSON-alakzatokat](./how-to-shape-query-json.md).

- Az eseményforrás kulcsa hiányzik egy szükséges engedély.

  * Egy IoT hub esetében meg kell adnia a **Service kapcsolódási** engedéllyel rendelkező kulcsot.

    [![Ellenőrizze az IoT hub engedélyeit.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Mind a szabályzatok **iothubowner** , mind a **szolgáltatás** működik, mert a **szolgáltatás csatlakozási** engedéllyel rendelkeznek.

  * Az Event hub esetében meg kell adnia a **figyelés** engedéllyel rendelkező kulcsot.
  
    [![Tekintse át az Event hub engedélyeit.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Az **olvasási** és a **kezelési** szabályzat is működik, mert a **figyelés** engedéllyel rendelkezik.

- A megadott fogyasztói csoport nem kizárólag Time Series Insights.

    Az IoT hub vagy az Event hub regisztrálása során meg kell adnia az adatolvasáshoz használt fogyasztói csoportot. Ennek a fogyasztói csoportnak egyedinek kell lennie egy adott környezetben. Ha a fogyasztói csoport meg van osztva, a mögöttes Event hub automatikusan leválasztja az egyik olvasót véletlenszerűen. Adjon meg egy egyedi fogyasztói csoportot, amelyből a Time Series Insights olvasható.

- A kiépítés idején megadott idősorozat-azonosító tulajdonság helytelen, hiányzó vagy NULL értékű.

    Ez a probléma akkor fordulhat elő, ha az idősorozat-azonosító tulajdonságot helytelenül konfigurálták a környezet kiépítés időpontjában. További információ: [ajánlott eljárások az idősorozat-azonosító kiválasztásához](./time-series-insights-update-how-to-id.md). Jelenleg nem frissíthet meglévő Time Series Insights környezetet más idősorozat-azonosító használatára.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Probléma: egyes adatértékek, de hiányoznak

Előfordulhat, hogy az idősorozat-azonosító nélkül küld adatokat.

- Ez a probléma akkor fordulhat elő, ha a hasznos adatok idősorozat-azonosítója mező nélkül küld eseményeket. További információért olvassa el a [támogatott JSON-alakzatok](./how-to-shape-query-json.md)című témakört.
- Ez a probléma akkor fordulhat elő, ha a környezetét szabályozzák.

    > [!NOTE]
    > A Time Series Insights jelenleg legfeljebb 6 Mbps mennyiségű betöltési sebességet támogat.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Probléma: az adatmegjelenítés már megtörtént, de most már leállt a betöltés

- Lehetséges, hogy az eseményforrás kulcsa újra lett létrehozva, és a Gen2-környezetnek szüksége van az új eseményforrás-kulcsra.

Ez a probléma akkor fordul elő, ha az eseményforrás létrehozásakor megadott kulcs már nem érvényes. Látni fogja a telemetria a központban, de nem érkezett bejövő üzenetek a Time Series Insightsban. Ha nem biztos abban, hogy a kulcsot újragenerálta-e, kereshet a Event Hubs "tevékenység naplójában a" névtér-engedélyezési szabályok létrehozása vagy frissítése "vagy a" IotHub-erőforrás létrehozása vagy frissítése "kifejezésre a IoT hub számára. 

Ha az új kulccsal szeretné frissíteni a Time Series Insights Gen2-környezetét, nyissa meg a hub-erőforrást a Azure Portal, és másolja az új kulcsot. Navigáljon az ÁME-erőforráshoz, és kattintson az események forrásai elemre. 

   [![Képernyőfelvétel: a T S I erőforrás az eseményforrás menüpont kinevezett eleme.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Válassza ki azokat az eseményforrás (ka) t, amelyekről leállt a betöltés, illessze be az új kulcsot, és kattintson a Save (Mentés) gombra.

   [![Képernyőfelvétel: a T S I erőforrás, amelyet a megadott I/o hub-szabályzat kulcsával mutat.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Probléma: az esemény forrásának időbélyeg-tulajdonságának neve nem működik

Győződjön meg arról, hogy a név és az érték megfelel a következő szabályoknak:

* Az időbélyeg-tulajdonság neve megkülönbözteti a kis-és nagybetűket.
* Az eseményforrás által a JSON-karakterláncként kapott timestamp tulajdonság értéke formátuma `yyyy-MM-ddTHH:mm:ss.FFFFFFFK` . Ilyen karakterlánc például: `"2008-04-12T12:53Z"` .

A legegyszerűbb módszer annak biztosítására, hogy az időbélyeg-tulajdonság neve rögzítve legyen, és megfelelően működjön a Time Series Insights Gen2 Explorer használata. A Time Series Insights Gen2 Explorerben a diagramon kiválaszthat egy időszakot, miután megadták az időbélyeg-tulajdonság nevét. Kattintson a jobb gombbal a kijelölésre, és válassza az **események feltárása** lehetőséget. Az első oszlop fejléce az időbélyeg-tulajdonság neve. `($ts)`A szó mellett kell lennie a következő `Timestamp` helyett:

* `(abc)`, amely azt jelzi, hogy Time Series Insights beolvassa az adatértékeket karakterláncként.
* A **Naptár** ikon, amely azt jelzi, hogy Time Series Insights beolvassa az adatértéket datetime formátumban.
* `#`, amely azt jelzi, hogy Time Series Insights egész számként olvassa az adatértékeket.

Ha az időbélyegző tulajdonság nincs explicit módon megadva, az esemény IoT hub-vagy Event hub-várólistán lévő az alapértelmezett időbélyegző lesz használva.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Probléma: nem tudom megtekinteni a meleg áruházból származó adatok az Explorerben

- Lehet, hogy a közelmúltban kiépített egy meleg áruházat, és az adatai továbbra is áramlanak.
- Lehet, hogy törölte a meleg áruházat, amely esetben elveszett volna az adatai.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Probléma: nem tudom megtekinteni vagy szerkeszteni az idősorozat-modellt

- Lehet, hogy egy Time Series Insights S1 vagy S2 környezethez fér hozzá.

   Az idősorozat-modellek csak utólagos elszámolású környezetekben támogatottak. Az S1 vagy S2 környezet Time Series Insights Gen2 Explorerben való elérésével kapcsolatos további információkért olvassa el [az adatok megjelenítése az Explorerben című részt](./time-series-insights-update-explorer.md).

   [![Nincsenek események a környezetben.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Előfordulhat, hogy nincs engedélye a modell megtekintésére és szerkesztésére.

   A felhasználóknak közreműködői szintű hozzáférésre van szükségük az idősorozat-modell szerkesztéséhez és megtekintéséhez. Az aktuális hozzáférési szintek ellenőrzéséhez és további hozzáférés biztosításához lépjen a Azure Portal Time Series Insights erőforrásának **adatelérési házirendek** szakaszára.

## <a name="problem-all-my-instances-in-the-gen2-explorer-lack-a-parent"></a>Probléma: a Gen2 Explorerben a saját példányok nem rendelkeznek szülővel

Ez a probléma akkor fordulhat elő, ha a környezet nem rendelkezik meghatározott idősorozat-modell hierarchiával. További információért olvassa el a [Time Series-modellek használata](/azure/time-series-insights/time-series-insights-overview)című témakört.

  [![A nem szülő példányok figyelmeztetést jelenítenek meg.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="problem-power-bi-connector-shows-unable-to-connect"></a>Probléma: Power BI-összekötő "nem lehet csatlakozni"

Ez a probléma akkor fordulhat elő, ha nem a Power BI-összekötő legújabb verzióját használja Power BI Desktop.

[![A képernyőfelvételen a nem lehet csatlakozni párbeszédpanel jelenik meg.](media/preview-troubleshoot/power-bi-unable-to-connect.png)](media/preview-troubleshoot/power-bi-unable-to-connect.png#lightbox)

* Ellenőrizze a Power BI Desktop verzióját, és győződjön meg arról, hogy a júliusi 2020-es verziót használja. Ha nem, frissítse a Power BI Desktop, majd futtassa újra az összekötőt. 

## <a name="next-steps"></a>További lépések

- Olvassa el, hogyan [dolgozhat a Time Series-modellekkel](/azure/time-series-insights/time-series-insights-overview).

- További információ a [támogatott JSON-alakzatokról](./how-to-shape-query-json.md).

- Tekintse át Azure Time Series Insights Gen2 [megtervezését és korlátozásait](./time-series-insights-update-plan.md) .

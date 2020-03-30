---
title: Előzetes verziójú környezet diagnosztizálása és hibaelhárítása – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg, hogyan diagnosztizálhatja és háríthatja el az Azure Time Series Insights előzetes verziójú környezetét.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 667dee6365f38ae058e91c61c24838d8912df26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152652"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Előzetes verziós környezet diagnosztizálása és hibaelhárítása

Ez a cikk számos gyakori problémát foglal össze, amelyek az Azure Time Series Insights előzetes környezetével való munka során találkozhatnak. A cikk ismerteti az egyes problémák lehetséges okait és megoldásait is.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Probléma: Nem találom a környezetet az előnézeti kezelőben

Ez a probléma akkor fordulhat elő, ha nincs engedélye a Time Series Insights környezet eléréséhez. A felhasználóknak olvasószintű hozzáférési szerepkörre van szükségük a Time Series Insights-környezet megtekintéséhez. Az aktuális hozzáférési szintek ellenőrzéséhez és további hozzáférés biztosításához nyissa meg az [Azure Portal](https://portal.azure.com/)Time Series Insights erőforrás **adatelérési szabályzata** szakaszát.

  [![Ellenőrizze az adatelérési házirendeket.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Probléma: Nem látható adat az előnézeti kezelőben

Számos gyakori oka lehet annak, hogy az adatok nem jelennek meg az [Azure Time Series Insights előzetes verziójának kezelőjében.](https://insights.timeseries.azure.com/preview)

- Lehet, hogy az eseményforrás nem fogad adatokat.

    Ellenőrizze, hogy az eseményforrás, amely egy eseményközpont vagy egy IoT hub, adatokat fogad a címkékvagy példányok. Az ellenőrzéshez nyissa meg az erőforrás áttekintő lapját az Azure Portalon.

    [![Tekintse át az irányítópult-mérőszámok áttekintését.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Az eseményforrás-adatok nem JSON formátumúak.

    A Time Series Insights csak a JSON-adatokat támogatja. JSON-minták esetén olvassa el [a Támogatott JSON-alakzatok című szöveget.](./how-to-shape-query-json.md)

- Az eseményforráskulcsból hiányzik egy szükséges engedély.

  * Egy IoT-központ, meg kell adnia a kulcsot, amely **rendelkezik a szolgáltatás csatlakozási** engedéllyel.

    [![Ellenőrizze az IoT-központ engedélyeit.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Mind a **szabályzatok iothubowner** és **a szolgáltatás** működik, mert **rendelkeznek a szolgáltatás connect** engedélyt.

  * Egy eseményközpont esetén meg kell adnia a **listen engedéllyel** rendelkező kulcsot.
  
    [![Tekintse át az eseményközpont engedélyeit.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Az **Olvasás** és a **Kezelés** házirend ek ként is működik, mert **rendelkeznek Listen** engedéllyel.

- A megadott fogyasztói csoport nem kizárólag a Time Series Insights.

    Az IoT hub vagy eseményközpont regisztrálása során megadhatja az adatok olvasásához használt fogyasztói csoportot. Ennek a fogyasztói csoportnak környezetenként egyedinek kell lennie. Ha a fogyasztói csoport meg van osztva, az alapul szolgáló eseményközpont automatikusan véletlenszerűen bontja az egyik olvasót. Adjon meg egy egyedi fogyasztói csoportot a Time Series Insights számára, amelyből olvasni lehet.

- A kiépítéskor megadott Time Series ID tulajdonság helytelen, hiányzik vagy null.

    Ez a probléma akkor fordulhat elő, ha a Time Series ID tulajdonság helytelenül van konfigurálva a környezet kiépítésekor. További információt az [Idősorozat-azonosító kiválasztására vonatkozó gyakorlati tanácsok](./time-series-insights-update-how-to-id.md)című ban talál. Jelenleg nem frissíthet egy meglévő Time Series Insights-környezetet egy másik Idősorozat-azonosító használatához.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Probléma: Néhány adat azt mutatja, de néhány hiányzik

Előfordulhat, hogy az adatokat az idősorozat-azonosító nélkül küldi.

- Ez a probléma akkor fordulhat elő, ha olyan eseményeket küld, amelyek nem a Time Series ID mező t a hasznos adatban. További információért olvassa el [a Támogatott JSON-alakzatok című szöveget.](./how-to-shape-query-json.md)
- Ez a probléma azért fordulhat elő, mert a környezet szabályozásalatt áll.

    > [!NOTE]
    > Ebben az időben a Time Series Insights támogatja a maximális betöltési sebesség 6 Mb/s.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Probléma: Az adatok jelennek meg, de most a betöltés leállt

- Előfordulhat, hogy az eseményforráskulcs újragenerálódott, és az előzetes verziójú környezetnek szüksége van az új eseményforráskulcsra.

Ez a probléma akkor fordul elő, ha az eseményforrás létrehozásakor megadott kulcs már nem érvényes. Telemetriai adatokat a központban láthat, de a Time Series Insights ban nem érkezik meg bejövő üzenetek. Ha nem biztos abban, hogy a kulcs újralett-e létrehozva, kereshet az Event Hubs tevékenységnaplójában a "Névtér engedélyezési szabályainak létrehozása vagy frissítése" kifejezésre, vagy az IoT hub "IotHub-erőforrás létrehozása vagy frissítése" kifejezésre keresve. 

A Time Series Insights előzetes verziójának környezetének frissítése az új kulccsal nyissa meg a központi erőforrást az Azure Portalon, és másolja az új kulcsot. Keresse meg az 1SI-erőforrást, és kattintson az Eseményforrások elemre. 

   [![Frissítési kulcs.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Válassza ki azokat az eseményforrás(oka)t, amelyekből a betöltés leállt, illessze be az új kulcsot, és kattintson a Mentés gombra.

   [![Frissítési kulcs.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Probléma: Az eseményforrás időbélyeg-tulajdonságának neve nem működik

Győződjön meg arról, hogy a név és az érték megfelel a következő szabályoknak:

* Az Időbélyeg tulajdonság neve a kis- és nagybetűket.
* Az eseményforrásból JSON-karakterláncként érkező Timestamp tulajdonságérték `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`formátuma a . Egy ilyen karakterlánc például a `“2008-04-12T12:53Z”`.

A timestamp tulajdonság nevének rögzítése és megfelelő működése a legegyszerűbben a Time Series Insights előzetes verziókezelőjének használata. A Time Series Insights előzetes verziójának kezelője segítségével a diagram segítségével válasszon ki egy időszakot, miután megadta az Időbélyeg tulajdonság nevét. Kattintson a jobb gombbal a kijelölésre, és válassza az **Események feltárása** lehetőséget. Az első oszlopfejléc a Timestamp tulajdonság neve. Meg kell `($ts)` mellett a `Timestamp`szó , nem pedig:

* `(abc)`, amely azt jelzi, hogy a Time Series Insights karakterláncként olvassa be az adatértékeket.
* A **naptár** ikonja, amely azt jelzi, hogy a Time Series Insights datetime-ként olvassa be az adatértéket.
* `#`, amely azt jelzi, hogy a Time Series Insights egész számként olvassa be az adatértékeket.

Ha a Timestamp tulajdonság nincs explicit módon megadva, az esemény IoT hubja vagy az eseményközpont enqueued time lesz az alapértelmezett időbélyegző.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Probléma: Nem tudom megtekinteni a böngészőben található meleg bolt adatait

- Előfordulhat, hogy a melegbolt a közelmúltban, és az adatok továbbra is áramlik be.
- Lehet, hogy törölte a melegboltot, amely esetben adatokat veszített volna.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Probléma: Nem tudom megtekinteni vagy szerkeszteni az Idősorozat-modellemet

- Előfordulhat, hogy egy Time Series Insights S1 vagy S2 környezetben fér hozzá.

   A Time Series modellek csak felosztó-ki-fel-fel-modellek ben támogatottak. Ha többet szeretne tudni arról, hogy miként érheti el az S1 vagy S2 környezetet a Time Series Insights előzetes verziójának kezelőjéből, olvassa el [a Visualize-adatok at a felfedező ben című dokumentumban.](./time-series-insights-update-explorer.md)

   [![Nincsenek események a környezetben.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Lehet, hogy nincs engedélye a modell megtekintésére és szerkesztésére.

   A felhasználóknak közreműködőszintű hozzáférésre van szükségük a Time Series modell szerkesztéséhez és megtekintéséhez. Az aktuális hozzáférési szintek ellenőrzéséhez és további hozzáférés biztosításához nyissa meg a Time Series Insights-erőforrás **Adatelérési szabályzatok** szakaszát az Azure Portalon.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Probléma: Az előnézeti felfedezőben minden példánynak nincs szülője

Ez a probléma akkor fordulhat elő, ha a környezetben nincs definiálva idősorozat-modell hierarchia. További információ: [Munka idősorozat-modellekkel.](./time-series-insights-update-how-to-tsm.md)

  [![A nem szülő példányok figyelmeztetést jelenítek meg.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>További lépések

- Olvasás [A time sorozatmodellekkel való munka](./time-series-insights-update-how-to-tsm.md).

- További információ a [támogatott JSON-alakzatokról.](./how-to-shape-query-json.md)

- Tekintse át [a tervezést és a korlátokat](./time-series-insights-update-plan.md) az Azure Time Series Insights előzetes verziójában.

---
title: A szabályozás figyelésének és csökkentésének leszűkítése – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg, hogyan figyelheti, diagnosztizálhatja és mérsékelheti a késést és szabályozást okozó teljesítményproblémákat az Azure Time Series Insightsban.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 2812b535c7aef7987db7106bfa6b07e15a1b61c7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263386"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>A sávszélesség-szabályozás figyelése és csökkentése a késés csökkentése érdekében az Azure Time Series Insightsban

Ha a bejövő adatok mennyisége meghaladja a környezet konfigurációját, késést vagy szabályozást tapasztalhat az Azure Time Series Insightsban.

Elkerülheti a késést és a szabályozást, ha megfelelően konfigurálja a környezetet az elemezni kívánt adatok mennyiségére.

A legvalószínűbb, hogy a következők esetén tapasztal késleltetést és szabályozást:

- Adjon hozzá egy eseményforrást, amely olyan régi adatokat tartalmaz, amelyek meghaladhatják a kiosztott be- és adatátviteli arányt (a Time Series Insights-nak fel kell zárkóznia).
- Adjon hozzá további eseményforrásokat egy környezethez, ami további események csúcsát eredményez (ami meghaladhatja a környezet kapacitását).
- Nagy mennyiségű történelmi esemény leküldése egy eseményforrásra, ami késést eredményez (a Time Series Insights-nak fel kell zárkóznia).
- A referenciaadatok telemetriai adatokkal való összeillesztése, ami nagyobb eseményméretet eredményez. A csomag maximálisan megengedett mérete 32 KB; a 32 KB-nál nagyobb adatcsomagok csonkulnak.

## <a name="video"></a>Videó

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Ismerje meg a Time Series Insights adatok be- és információkezelési viselkedését, és hogyan tervezze meg.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>A késés és a szabályozás figyelése riasztásokkal

Riasztások segítségével diagnosztizálhatja és enyhítheti a környezetben előforduló késési problémák.

1. Az Azure Portalon válassza ki a Time Series Insights-környezetben. Ezután válassza **a Riasztások**lehetőséget.

   [![Riasztás hozzáadása a Time Series Insights környezethez](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Válassza az **+ Új riasztási szabály** lehetőséget. Ekkor megjelenik **a Szabály létrehozása** panel. Válassza **a Hozzáadás** lehetőséget a **FELTÉTEL csoportban.**

   [![Figyelmeztetés hozzáadása ablaktábla](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Ezután állítsa be a pontos feltételeket a jellogika.

   [![Jellogika konfigurálása](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Itt az alábbi feltételek kel konfigurálhatja a riasztásokat:

   |Metrika  |Leírás  |
   |---------|---------|
   |**Fogadott bájtok beérkezése**     | Az eseményforrásokból beolvasott nyers bájtok száma. A nyers szám általában tartalmazza a tulajdonság nevét és értékét.  |  
   |**Bejövő üzenetek bejövő üzenetek**     | Az összes Azure Event Hubs vagy Az Azure IoT Hub eseményforrásból beolvasott érvénytelen üzenetek száma.      |
   |**Fogadott üzenetek be- és beérkezések**   | Az összes Event Hubs vagy IoT Hubs eseményforrásból olvasott üzenetek száma.        |
   |**Tárolt be- és betolatás**     | A tárolt és lekérdezésre elérhető események teljes mérete. A méretet csak a tulajdonság értéke számítja ki.        |
   |**Be- és betolatott események**    |   A tárolt és lekérdezésre elérhető összeolvasztott események száma.      |
   |**Be- és be- és visszaküldött üzenetek időeltolódása**   |  Az üzenet eseményforrásban való várólistán töltött ideje és a bejövő forgalomban történő feldolgozása közötti különbség másodpercben.      |
   |**Be- és be- és visszaküldött üzenetek száma lag**   |  Az eseményforrás-partíción az utolsó várólistára helyezett üzenetek sorszáma és a bejövő forgalomban feldolgozott üzenetek sorszáma közötti különbség.      |

   Válassza a **Done** (Kész) lehetőséget.

1. A kívánt jellogika konfigurálása után tekintse át vizuálisan a kiválasztott riasztási szabályt.

   [![Késésnézet és diagramkészítés](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Szabályozás és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és

* Ha szabályozás alatt áll, a *bejövő üzenet időbeli eltérés* értéke regisztrálva lesz, amely tájékoztatja Önt arról, hogy hány másodperccel van lemaradva a TIme Series Insights környezettől attól az időponttól, amikor az üzenet eléri az eseményforrást (kivéve az appx indexelési idejét. 30-60 másodperc).  

  *A fogadott üzenetek száma és késleltetése* is értékkel rendelkezik, amely lehetővé teszi annak meghatározását, hogy hány üzenet van mögötted.  A legegyszerűbb módja annak, hogy elkapjanak, hogy növelje a környezet kapacitását, hogy a méret, amely lehetővé teszi, hogy felszámolja a különbséget.  

  Ha például az S1-környezet 5 000 000 üzenetkkel jár le, a környezet méretét körülbelül egy napra hat egységre növelheti, hogy felzárkózzon.  Még tovább növekedhet, hogy gyorsabban felzárkózzon. A felzárkózási időszak gyakori esemény, amikor először egy környezetet létesít, különösen akkor, ha olyan eseményforráshoz csatlakozik, amely már rendelkezik eseményekkel, vagy ha tömegesen tölt fel sok előzményadatot.

* Egy másik technika, hogy állítsa be a **be- éstárolási tárolt események** riasztási >= egy küszöbértéket valamivel a teljes környezeti kapacitás alatt egy 2 órás időtartamra.  Ez a riasztás segít megérteni, ha folyamatosan kapacitás, ami azt jelzi, nagy a valószínűsége a késés. 

  Ha például három S1 egység van kiépítve (vagy 2100 esemény percenkénti be- és kapacitás), beállíthatja a >= 1900 események 2 órára **vonatkozó, a forgalomban lévő tárolt események** riasztást. Ha folyamatosan túllépi ezt a küszöbértéket, és ezért a riasztás aktiválása, akkor valószínűleg alul-kiépített.  

* Ha azt gyanítja, hogy a készüléket megfojtják, összehasonlíthatja a **beérkező üzeneteket** az eseményforrás kimenő üzeneteivel.  Ha az Eseményközpontba való be- ésbetöltés nagyobb, mint a **beérkező üzenetek,** akkor a Time Series Insights valószínűleg szabályozás alatt áll.

## <a name="improving-performance"></a>A teljesítmény javítása

A sávszélesség-szabályozás csökkentése vagy a késés csökkentése érdekében a legjobb módja annak, hogy javítsa ki, hogy növelje a környezet kapacitását.

Elkerülheti a késést és a szabályozást, ha megfelelően konfigurálja a környezetet az elemezni kívánt adatok mennyiségére. A környezet kapacitásának hozzáadásáról a [Környezet méretezése](time-series-insights-how-to-scale-your-environment.md)című olvasni tudó további információt talál.

## <a name="next-steps"></a>További lépések

- További információ [A Problémák diagnosztizálásáról és megoldásáról a Time Series Insights környezetben.](time-series-insights-diagnose-and-solve-problems.md)

- [Ismerje meg, hogyan méretezheti a Time Series Insights-környezetet.](time-series-insights-how-to-scale-your-environment.md)
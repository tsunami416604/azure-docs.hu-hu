---
title: Adatok feltárása az explorer használatával - Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az Azure Time Series Insights explorer t az IoT-adatok megtekintéséhez.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: b564373780da5bba71bc46ddbac3dc69a0c94a4a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382785"
---
# <a name="azure-time-series-insights-explorer"></a>Az Azure Time Series Insights kezelője

Ez a cikk az Azure Time Series Insights [explorer webalkalmazás](https://insights.timeseries.azure.com/)általános elérhetőségi funkcióit és beállításait ismerteti. A Time Series Insights-felfedező bemutatja a szolgáltatás által biztosított hatékony adatvizualizációs képességeket, és a saját környezetében is elérhető.

Az Azure Time Series Insights egy teljes körűen felügyelt elemző, tároló és megjelenítő szolgáltatás, amely megkönnyíti IoT-események milliárdjainak párhuzamos felderítését és elemzését. Globális képet ad az adatokról, így gyorsan ellenőrizheti az IoT-megoldást, és elkerülheti a kritikus fontosságú eszközök költséges állásidejét. Felfedezheti a rejtett trendeket, észlelheti az anomáliákat, és közel valós időben végezhet kiváltó okokat. A Time Series Insights explorer jelenleg nyilvános előzetes verzióban érhető el.

> [!TIP]
> A bemutató környezetben vezetett bemutató környezetben az [Azure Time Series Insights rövid útmutatóját](time-series-quickstart.md)olvassa el.

## <a name="video"></a>Videó

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Ismerje meg az adatok lekérdezését a Time Series Insights felfedező használatával. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Az előző <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">videó "A Time Series Insights használatának megkezdése egy Azure IoT-megoldásgyorsító használatával" című videóval.</a>

## <a name="prerequisites"></a>Előfeltételek

A Time Series Insights-kezelő használata előtt a következőket kell tennie:

- Hozzon létre egy Time Series Insights-környezetet. További információ: [Útmutató a Time Series Insights működéséhez.](./time-series-insights-get-started.md)
- [Hozzáférést biztosít](time-series-insights-data-access.md) a fiókjához a környezetben.
- Adjon hozzá egy [IoT hub](time-series-insights-how-to-add-an-event-source-iothub.md) vagy [eseményközpont](time-series-insights-how-to-add-an-event-source-eventhub.md) eseményforrást.

## <a name="explore-and-query-data"></a>Adatok feltárása és lekérdezése

Az eseményforrás nak a Time Series Insights-környezethez való csatlakoztatását követő perceken belül feltárhatja és lekérdezheti az idősorozat-adatokat.

1. A kezdéshez nyissa meg a [Time Series Insights explorert](https://insights.timeseries.azure.com/) a webböngészőben. Az ablak bal oldalán jelöljön ki egy környezetet. Minden olyan környezet, amelyhez hozzáférése van, betűrendben van felsorolva.

1. Miután kijelölt egy környezetet, használja a felső helyen a **Be és** **a To** konfigurációt, vagy jelölje ki és húzza át a kívánt időtartamot. Jelölje ki a jobb felső sarokban lévő nagyítót, vagy kattintson a jobb gombbal a kiválasztott időtartományra, és válassza a **Keresés parancsot.**

1. Az **Automatikus bekapcsolás** gombra kattintva percenként automatikusan frissítheti az elérhetőséget. Az **Automatikus bekapcsolás** gomb csak a rendelkezésre állási diagramra vonatkozik, a fő vizualizáció tartalmára nem.

1. Az Azure felhőikon az Azure Portalon a környezetéhez vezeti.

   [![A Time Series Insights környezet kiválasztása](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Ezután megjelenik egy diagram, amely a kiválasztott időtartam alatt az összes esemény számát jeleníti meg. Itt számos vezérlőt tud meg:

    - **Kifejezések szerkesztő panel**: A kifejezés szó, ahol lekérdezi a környezetben. A képernyő bal oldalán található:
      - **INTÉZKEDÉS**: Ez a legördülő lista az összes numerikus oszlopot (**páros**) tartalmazza.
      - **FELOSZTÁS:** Ez a legördülő lista kategorikus oszlopokat (**karakterláncokat )** jelenít meg.
      - Engedélyezheti a lépésinterpolációt, megjelenítheti a minimális és maximális értéket, és beállíthatja az y tengelyt a **vezérlőpultról**a MÉRTÉK elem mellett. Azt is beállíthatja, hogy a megjelenített adatok az adatok száma, átlaga vagy összege legyenek.You can also adjust whether data shown is a count, average, or sum of the data.
      - Legfeljebb öt kifejezést adhat hozzá, hogy ugyanabban az x tengelyen jelenjen meg. Válassza **a Hozzáadás** lehetőséget, ha új kifejezést szeretne hozzáadni, vagy a **Klónozás ez a kifejezés** gombbal hozzáadhat egy másolatot egy meglévő kifejezésről.

        [![Kifejezések kiválasztása, szűrése és lekérdezéspanelje](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predikátum:** Használja az állítmányt, hogy gyorsan szűrje az eseményeket az alábbi táblázatban felsorolt operandusok készletével. Ha a keresést kijelöléssel vagy kattintással végzi, az állítmány automatikusan frissül a keresés alapján. A támogatott operandstípusok a következők:

         |Művelet  |Támogatott típusok  |Megjegyzések  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Dupla**, **DateTime**, **TimeSpan**       |         |
         |**=**, **!=**,**<>**     | **String**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |         |
         |**IN**     | **String**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |  Minden operandusnak azonos típusúnak vagy **NULL** állandónak kell lennie.        |
         |**Hsa**     | **Sztring**        |  A jobb oldalon csak állandó karakterlánc-konstansok engedélyezettek. Üres karakterlánc és **NULL** nem engedélyezett.       |

      - **Példa lekérdezésekre**

         [![Példa GA-lekérdezésekre](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Az **Intervallumméret** csúszka eszközzel az intervallumokat nagyíthatja és kicsinyítheti ugyanabban az időtartamban. A csúszka pontosabban szabályozza a nagy időszeletek közötti mozgást, amely sima trendeket mutat le a milliszekundumos méretű szeletekig, ami lehetővé teszi az adatok részletes, nagy felbontású vágásainak megjelenítését és elemzését. A csúszka alapértelmezett kezdőpontja a kijelölésadatainak legoptimálisabb nézeteként van beállítva a felbontás, a lekérdezési sebesség és a részletesség egyensúlyához.

1. Az **Időecset** eszköz megkönnyíti a navigálást az egyik időtartományból a másikba.

1. A **Mentés** ikonra választva mentheti az aktuális lekérdezést, és megoszthatja azt a környezet többi felhasználójával. Ha a Megnyitás ikont **választja,** megtekintheti az összes mentett lekérdezést és a többi felhasználó megosztott lekérdezéseit olyan környezetekben, amelyekhez hozzáférése van.

   [![Lekérdezések](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Adatok vizualizációja

1. A **Perspektíva nézet** eszközzel egyszerre legfeljebb négy egyedi lekérdezést tekinthet meg. A **Perspektíva nézet** gomb a diagram jobb felső sarkában található.

   [![A perspektivikus ablaktáblához hozzáadni kívánt lekérdezések kiválasztása](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Diagram megtekintése az adatok vizuális feltárásához, valamint a **Diagram** eszközök használatához:

    - **Jelöljön ki** vagy **kattintson** egy adott időtartományra vagy egyetlen adatsorra.
    - Az időtartomány-kijelölésen belül nagyíthatja vagy felfedezheti az eseményeket.
    - Az adatsorokon belül feloszthatja az adatsorokat egy másik oszlopra, új kifejezésként hozzáadhatja az adatsort, csak a kijelölt adatsorokat jelenítheti meg, kizárhatja a kijelölt adatsorokat, pingelheti az adatsort, vagy felfedezheti a kiválasztott adatsor eseményeit.
    - A diagramtól balra lévő szűrőterületen áttekintheti az összes megjelenített adatsort, és átrendezheti az értéket vagy a nevet. Megtekintheti az összes adatsort vagy bármely rögzített vagy nem rögzített adatsort is. Kijelölhet egy adatsort, és feloszthatja az adatsort egy másik oszlopra, új kifejezésként hozzáadhatja az adatsort, csak a kijelölt adatsorokat jelenítheti meg, kizárhatja a kijelölt adatsorokat, rögzítheti az adatsort, vagy felfedezheti a kiválasztott adatsor eseményeit.
    - Ha egyszerre több kifejezést is megtekint, halmozhatja, feloldhatja az adatsorokra vonatkozó további adatokat, és ugyanazt az y tengelyt használhatja az összes kifejezésközött. Használja a diagram jobb felső sarkában található gombokat.

    [![A Diagram eszköz a jobb sarok jobb felső sarkában beállításbeállításai](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. A **hőtérkép** segítségével gyorsan észlelheti az egyedi vagy rendellenes adatsorokat egy adott lekérdezésben. Csak egy keresési kifejezés jeleníthető meg hőtérképként.

    [![GA explorer heatmap charting](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Ha az eseményeket a kijelöléssel vagy a jobb gombbal kattintva vizsgálja, az **ESEMÉNYEK** panel elérhetővé válik. Itt megtekintheti az összes nyers eseményt, és exportálhatja az eseményeket JSON- vagy CSV-fájlként. A Time Series Insights az összes nyers adatot tárolja.

    [![Események](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. A **minták** és az oszlopstatisztikák feltárása után válassza a STATISZTIKA lapot.

    - **Minták**: Ez a funkció proaktív módon a kiválasztott adatterület statisztikailag legjelentősebb mintáit jelenik meg. Nem kell több ezer eseményt nézned ahhoz, hogy megértsd, milyen minták igényelnek a legtöbb időt és energiát. A Time Series Insights segítségével közvetlenül ezekbe a statisztikailag szignifikáns mintákba ugorhat az elemzés folytatásához. Ez a funkció a korábbi adatok post mortem kivizsgálásához is hasznos.
    - **Oszlopstatisztika**: Az oszlopstatisztikák olyan diagramokat és táblázatokat biztosítanak, amelyek a kiválasztott adatsorok egyes oszlopainak adatait bontják le a kiválasztott időtartam alatt.

      [![STATS oszlopdiagram és beállítások](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Most már értesült a Time Series Insights explorer webalkalmazásban elérhető legfontosabb funkciókról, konfigurációs beállításokról és megjelenítési beállításokról.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [diagnosztizálhatja és oldhatja meg](time-series-insights-diagnose-and-solve-problems.md) a problémákat a Time Series Insights környezetben.

- Az [Azure Time Series Insights](time-series-quickstart.md) rövid útmutatóbemutatója.

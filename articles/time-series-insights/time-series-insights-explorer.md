---
title: Az adatelemzés a Azure Time Series Insights Explorer használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a webböngészőben a Azure Time Series Insights Explorer, hogy gyorsan megtekintse a big data globális nézetét, és érvényesítse a IoT-környezetet.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 9ea9240ce5fa791734ab7b650ebdfa56b3f8dced
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840563"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights Explorer

Ez a cikk a Azure Time Series Insights [Explorer webalkalmazás](https://insights.timeseries.azure.com/)általánosan elérhető funkcióit és lehetőségeit ismerteti. A Time Series Insights Explorer bemutatja a szolgáltatás által biztosított hatékony adatvizualizációs képességeket, és a saját környezetében érhető el.

Az Azure Time Series Insights egy teljes körűen felügyelt elemző, tároló és megjelenítő szolgáltatás, amely megkönnyíti IoT-események milliárdjainak párhuzamos felderítését és elemzését. Globális áttekintést nyújt az adatairól, így gyorsan ellenőrizheti IoT-megoldását, és elkerülheti az üzleti szempontból kritikus fontosságú eszközök költséges leállását. Közel valós időben derítheti fel a rejtett trendeket, észlelheti a rendellenességeket, és elvégezheti a kiváltó okok elemzését. A Time Series Insights Explorer jelenleg nyilvános előzetes verzióban érhető el.

> [!TIP]
> A demonstrációs környezettel kapcsolatos interaktív bemutatóért olvassa el a [Azure Time Series Insights](time-series-quickstart.md)rövid útmutatót.

## <a name="video"></a>Videó

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Tudnivalók az adatlekérdezésekről a Time Series Insights Explorer használatával. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Tekintse meg az előző <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"első lépések a Time Series Insights használatával az Azure IoT megoldás-gyorsító használatával</a> " című videót.

## <a name="prerequisites"></a>Előfeltételek

A Time Series Insights Explorer használata előtt a következőket kell tennie:

- Egy Time Series Insights környezetet hozhat létre. További információkért lásd: [a Time Series Insights első lépéseinek](./time-series-insights-get-started.md)megismerése.
- [Hozzáférés biztosítása](time-series-insights-data-access.md) a fiókhoz a környezetben.
- Adjon hozzá egy [IoT hub](time-series-insights-how-to-add-an-event-source-iothub.md) -vagy [Event hub](time-series-insights-how-to-add-an-event-source-eventhub.md) -esemény forrását.

## <a name="explore-and-query-data"></a>Adatgyűjtés és adatlekérdezés

Az eseményforrás a Time Series Insights-környezethez való csatlakoztatását perceken belül megtekintheti és lekérdezheti az idősorozat-adatait.

1. A kezdéshez nyissa meg a [Time Series Insights Explorert](https://insights.timeseries.azure.com/) a böngészőben. Az ablak bal oldalán válasszon ki egy környezetet. Minden olyan környezet, amelyhez hozzáférése van, betűrendben jelenik meg.

1. Miután kiválasztott egy környezetet, vagy használja a **from** és **a** konfigurációt a felső részen, vagy kattintson a kívánt TimeSpan. Válassza ki a Nagyítót a jobb felső sarokban, vagy kattintson a jobb gombbal a kiválasztott TimeSpan, és válassza a **Keresés**lehetőséget.

1. A rendelkezésre állást automatikusan is frissítheti percenként, ha az **automatikus** bekapcsolás gombra kattint. Az **automatikus** bekapcsolás gomb csak a rendelkezésre állási diagramra vonatkozik, nem a fő vizualizáció tartalmára.

1. Az Azure Cloud ikon a Azure Portalban található környezetére viszi.

   [![Time Series Insights környezet](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Ezután megjelenik egy diagram, amely megjeleníti az összes esemény számát a kiválasztott TimeSpan. Itt számos vezérlőelem van:

    - **Feltételek szerkesztője panel**: Az a hely, ahol lekérdezheti a környezetét. A képernyő bal oldalán található:
      - **Mérték**: Ez a legördülő lista az összes numerikus oszlopot (**Double**) jeleníti meg.
      - **Felosztás**: Ez a legördülő lista kategorikus oszlopokat (**karakterláncokat**) jelenít meg.
      - Engedélyezheti a lépésenkénti interpolációt, megtekintheti a minimális és a maximális értéket, és módosíthatja az y tengelyt a vezérlő paneljén a **mérték**elem mellett. Azt is beállíthatja, hogy a megjelenített adatmennyiség darabszám, átlag vagy összeg-e.
      - Akár öt kifejezést is hozzáadhat ugyanazon az x tengelyen. A **Másolás** gomb használatával adjon hozzá egy további kifejezést, vagy válassza a **Hozzáadás** lehetőséget a friss kifejezés hozzáadásához.

        [![Feltételek szerkesztője panel](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predikátum**: A predikátum használatával gyorsan szűrheti az eseményeket az alábbi táblázatban felsorolt operandusok használatával. Ha a keresést úgy végzi, hogy kiválasztja vagy rákattint a gombra, a rendszer a keresés alapján automatikusan frissíti a predikátumot. Operand támogatott típusok a következők:

         |Művelet  |Támogatott típusok  |Megjegyzések  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, időtartam       |         |
         |`=`, `!=`, `<>`     | String, Bool, Double, DateTime, az időtartam, NULL értékű        |         |
         |IN     | String, Bool, Double, DateTime, az időtartam, NULL értékű        |  Az összes operandusok azonos típusú legyen vagy NULL állandó.        |
         |HAS     | Sztring        |  A jobb oldalon csak konstans karakterlánc-literálok engedélyezettek. Üres karakterlánc és NULL érték nem engedélyezett.       |

      - **Példák a lekérdezésekre**

         [![Példák lekérdezésekre](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Az **intervallum mérete** csúszka eszköz használatával nagyíthatja és kicsinyítheti az intervallumokat ugyanazon a TimeSpan. A csúszka a nagy szeletek közötti mozgás pontosabb szabályozását teszi lehetővé, amely a rugalmas trendeket a szeletekre csökkenti, mint az ezredmásodpercet, ami lehetővé teszi, hogy megtekintse az adatainak részletes, nagy felbontású mennyiségét. A csúszka alapértelmezett kiindulási pontja úgy van beállítva, hogy a kiválasztásból származó adatok legalkalmasabb nézete legyen a felbontás, a lekérdezés sebessége és a részletesség.

1. Az **időecset** eszköz megkönnyíti az egyik TimeSpan a másikba való átjárását.

1. A **Save** paranccsal mentheti az aktuális lekérdezést, és megoszthatja azt a környezet más felhasználóival. Ha a **Open (Megnyitás**) lehetőséget használja, az összes mentett lekérdezést és a többi felhasználó megosztott lekérdezéseit az Ön számára elérhető környezetekben tekintheti meg.

   [![Lekérdezések](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Adatok vizualizációja

1. A **perspektíva nézet** eszközzel akár négy egyedi lekérdezés egyidejű nézetét is használhatja. A **perspektíva nézet** gomb a diagram jobb felső sarkában található.

   [![Perspektíva nézet](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Megtekintheti a diagramot, hogy vizuálisan vizsgálja meg az adatait, és használja a **diagram** -eszközöket:

    - **Válasszon ki** vagy **kattintson** egy adott TimeSpan vagy egyetlen adatsorozatra.
    - A TimeSpan kiválasztásán belül kinagyíthatja vagy megtekintheti az eseményeket.
    - Egy adatsorozaton belül feloszthatja a sorozatot egy másik oszlop alapján, hozzáadhatja a sorozatot új kifejezésként, csak a kiválasztott adatsorozatot, kizárhatja a kiválasztott adatsorozatot, pingelheti az adatsorozatot, vagy megtekintheti az eseményeket a kiválasztott sorozatból.
    - A diagram bal oldalán található szűrő területen az összes megjelenített adatsorozat és az átrendezés érték vagy név szerint látható. Az összes adatsorozatot vagy bármely rögzített vagy nem rögzített adatsorozatot is megtekintheti. Kiválaszthat egy adatsorozatot, és feloszthatja az adatsorozatot egy másik oszlop alapján, hozzáadhatja a sorozatot új kifejezésként, csak a kiválasztott adatsorozatot, kizárhatja a kiválasztott adatsorozatot, rögzítheti a sorozatot, vagy megtekintheti az eseményeket a kiválasztott adatsorozatból.
    - Ha egyszerre több kifejezést tekint meg, a verem, a leválasztás és az adatsorozatok további adatait is megtekintheti, és az összes kifejezésben ugyanazt az y tengelyt használhatja. Használja a diagram jobb felső sarkában lévő gombokat.

    [![Diagram eszköz](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. A **hő** segítségével gyorsan kipróbálhatja az egyedi vagy rendellenes adatsorozatokat egy adott lekérdezésben. Hő csak egy keresési kifejezés látható.

    [![Hő](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Ha az eseményeket a jobb gombbal kattintva tárja fel, az **események** panel elérhetővé válik. Itt láthatja az összes nyers eseményt, és exportálhatja az eseményeket JSON-vagy CSV-fájlként. Time Series Insights az összes nyers adathalmazt tárolja.

    [![Események](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Válassza a **stats (statisztika** ) lapot, miután megvizsgálta az eseményeket a minták és az oszlopok statisztikáinak megjelenítéséhez.

    - **Mintázatok**: Ez a funkció proaktív módon felfedi a legtöbb statisztikailag jelentős mintát egy kiválasztott adatterületen. Nem kell több ezer eseményt megvizsgálnia, hogy megértsük, milyen mintázatok szükségesek a legtöbb időt és energiát. A Time Series Insights segítségével közvetlenül a statisztikailag jelentős mintákba ugorhat, így folytathatja az elemzést. Ez a funkció hasznos lehet a múltbeli adatfeldolgozást követő vizsgálatok során is.
    - **Oszlop statisztikái**: Az oszlop statisztikái olyan diagramokat és táblákat biztosítanak, amelyek a kiválasztott adatsorozat egyes oszlopaiból származó adatokkal bontják le a kiválasztott TimeSpan.

      [![STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Most már látta a Time Series Insights Explorer webalkalmazáson belül elérhető különböző funkciókat és lehetőségeket.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [diagnosztizálhatja és megoldhatja](time-series-insights-diagnose-and-solve-problems.md) a Time Series Insights-környezetben felmerülő problémákat.
- Végezze el az interaktív [Azure Time Series Insights](time-series-quickstart.md) gyors bemutatóját.

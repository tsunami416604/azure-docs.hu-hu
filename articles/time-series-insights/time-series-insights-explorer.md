---
title: Adatok megismerése az Azure Time Series Insights explorer használatával |} A Microsoft Docs
description: Ez a cikk ismerteti a böngészőben az Azure Time Series Insights explorer segítségével gyorsan megjelenítheti a globális a big data- és az IoT-környezet ellenőrzése.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: cbb6d75b2ed6b73ce7e3421596520f6a3f1bf3b1
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399824"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights explorer

Ez a cikk a szolgáltatásait ismerteti, és az általános rendelkezésre állási lehetőségeket az Azure Time Series Insights [explorer webalkalmazás](https://insights.timeseries.azure.com/). A Time Series Insights explorer mutatja be a hatékony adatmegjelenítési képességeket a szolgáltatás által biztosított, és a saját környezetben érhető el.

Az Azure Time Series Insights egy teljes körűen felügyelt elemző, tároló és megjelenítő szolgáltatás, amely megkönnyíti IoT-események milliárdjainak párhuzamos felderítését és elemzését. Biztosít az adatokat, ami lehetővé teszi, hogy gyorsan ellenőrizheti IoT-megoldásait, és elkerülheti az üzletmenet szempontjából kritikus fontosságú eszközök költséges leállását, globális nézet készítése. Feltárhatja a rejtett trendeket, azonosíthatja a rendellenességeket, és a kiváltó okainak közel valós időben végezhet. A Time Series Insights explorer jelenleg nyilvános előzetes verzióban érhető el.

> [!TIP]
> A bemutató környezetben keresztül az interaktív bemutató, olvassa el a [Azure Time Series Insights gyors](time-series-quickstart.md).

## <a name="video"></a>Videó

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Ismerje meg az adatok lekérdezése a Time Series Insights explorer használatával. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Tekintse meg az előző <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Első lépések a Time Series Insights használatával egy Azure IoT-Megoldásgyorsítók."</a>

## <a name="prerequisites"></a>Előfeltételek

A Time Series Insights explorer használata előtt a következőket kell tennie:

- Egy Time Series Insights környezetet hozhat létre. További információkért lásd: [Ismerkedés a Time Series insights](./time-series-insights-get-started.md).
- [Hozzáférés biztosítása](time-series-insights-data-access.md) a fiókhoz a környezetben.
- Adjon hozzá egy [az IoT hub](time-series-insights-how-to-add-an-event-source-iothub.md) vagy [eseményközpont](time-series-insights-how-to-add-an-event-source-eventhub.md) eseményforrás rá.

## <a name="explore-and-query-data"></a>Ismerje meg, illetve adatokat kérdezhet le

Az eseményforrás kapcsolódni a Time Series Insights-környezet percen belül vizsgálatát és az idősoros adatokat lekérdezni.

1. Elindítja, nyissa meg a [Time Series Insights explorer](https://insights.timeseries.azure.com/) a böngészőben. Az ablak bal oldalán válasszon ki egy környezetet. Hozzáféréssel rendelkező összes környezet betűrend szerinti sorrendben vannak felsorolva.

1. Miután, válasszon ki egy környezetet, vagy használja a **a** és **a** konfigurációk a lap tetején, vagy kattintson és húzza át a kívánt timespan. Válassza a Nagyító ikonra a jobb felső sarokban, vagy kattintson a jobb gombbal a kiválasztott időtartomány, és válassza ki **keresési**.

1. Is frissítheti rendelkezésre állási automatikusan percenként kiválasztásával a **automatikus a** gombra. A **automatikus a** gomb csak a rendelkezésre állási diagram, a fő Vizualizáció a tartalom nem vonatkozik.

1. Az Azure-felhő ikon továbblép a környezet az Azure Portalon.

   [![Time Series Insights-környezet](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Ezután megjelenik egy diagram, amely az összes esemény száma a kiválasztott időtartam során. Itt számos olyan vezérlőt rendelkezik:

    - **Kifejezések szerkesztő panel**: A kifejezés hely, ahol lekérdezheti, ha a környezetben. A képernyő bal oldalán található:
      - **Mérték**: A legördülő listából válassza ki az összes numerikus oszlopokat jelenít meg (**Double típusú értékekkel**).
      - **Felosztási szempont**: A legördülő lista jeleníti meg a kategorikus oszlopok (**karakterláncok**).
      - Lépcsőzetes interpoláció engedélyezése, minimális és maximális megjelenítése, és állítsa be a Vezérlőpult y melletti **mérték**. Emellett módosíthatja legyen látható adatok egy száma, átlagos vagy az adatok összegét.
      - Ugyanazt az x tengelyen megtekintéséhez legfeljebb öt feltételeket adhat hozzá. Használja a **másolási legördülő** gombra kattintva adja hozzá egy további kifejezést, vagy válasszon **Hozzáadás** friss kifejezés hozzáadása.

        [![Kifejezések szerkesztő panel](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predikátum**: A predikátum használatával gyorsan szűrheti az eseményeket az alábbi táblázatban felsorolt operandusok készletét használatával. Ha egy fájlkeresést kiválasztásával, vagy kattintson a predikátum automatikus frissítések alapján, hogy a keresés. Operand támogatott típusok a következők:

         |Művelet  |Támogatott típusok  |Megjegyzések  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, időtartam       |         |
         |`=`, `!=`, `<>`     | String, Bool, Double, DateTime, az időtartam, NULL értékű        |         |
         |IN     | String, Bool, Double, DateTime, az időtartam, NULL értékű        |  Az összes operandusok azonos típusú legyen vagy NULL állandó.        |
         |HAS     | String        |  Csak állandó karakterlánc-literálnak jobb oldalán használhatók. Nem engedélyezett üres karakterlánc, és NULL.       |

      - **Példák a lekérdezések**

         [![Példa a lekérdezésekre](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Használhatja a **intervallumának mérete** csúszkaeszközt nagyítás adataikkal időközök az azonos időtartam alatt. A csúszka nagy szeletek időt, amely kisebb, az ezredmásodperces lefelé szeletek zökkenőmentes trendeket jelenít meg, amely lehetővé teszi az adatok részletes, nagy felbontású darabok közötti mozgás pontosabb felügyeletét biztosítja. A csúszka alapértelmezett kiindulási pontjaként van beállítva, az adatok optimális nézetét a kijelölésből megoldás, a lekérdezés sebesség és a granularitási.

1. A **ecset idő** eszköz megkönnyíti a kezdőlapról kiindulva egy timespan egy másikba.

1. Használja a **mentése** parancsot az aktuális lekérdezés mentéséhez, és ossza meg a környezet más felhasználóival. Ha használ **nyílt**, láthatja az összes a mentett lekérdezések és bármely más felhasználóinak van hozzáférése környezetek megosztott lekérdezések.

   [![Lekérdezések](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Adatok vizualizációja

1. Használja a **perspektíva nézet** eszköz legfeljebb négy egyedi lekérdezések egyidejű megjelenítéséhez. A **perspektíva nézet** gomb jelenik meg a diagram jobb felső sarkában.

   [![Perspektíva megtekintése](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. A diagram vizuálisan feltárhatja az adatait, és megtekintheti a **diagram** eszközök:

    - **Válassza ki** vagy **kattintson** egy adott időtartam vagy egy adatsort.
    - Időtartomány kijelölésének belül nagyítás, vagy események áttekintése.
    - Adatsorok feloszthatja az adatsorozat más oszlop alapján, az adatsorozat hozzáadása új kifejezésként, csak a kijelölt adatsorozat megjelenítése, a kiválasztott adatsorozat kizárása, ping paranccsal a sorozat, és ismerje meg a események a kiválasztott sorozat.
    - A szűrő területen, a diagram bal oldalán tekintse meg az összes megjelenített adatsor, és átrendezése érték vagy a neve. Emellett megtekintheti az összes adatsor vagy bármely rögzített vagy nem rögzített adatsorozat. Meg is egyetlen adatsor kijelölése és az adatsorozat felosztása egy másik oszlop alapján, az adatsorozat hozzáadása új kifejezésként, csak a kijelölt adatsorozat megjelenítése, kihagyása a kiválasztott sorozat, rögzítheti a sorozat vagy események tallózása a kiválasztott sorozatból.
    - Ha egyszerre több használati, zásobníku, halomszétszedési beállítások, adatsorra vonatkozó további adatok, és használja az azonos y tengely összes feltételek. A gombokkal a diagram jobb felső sarkában.

    [![Diagram](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Használja a **intenzitástérkép** gyors felfedezéséhez egyedi vagy rendellenes adatsor az egy adott lekérdezésre. Csak egy keresési kifejezést, egy intenzitástérkép jeleníthetők meg.

    [![Heatmap](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Amikor Ön kijelöl és feltár események kiválasztásával, vagy kattintson a jobb gombbal a **események** panel szeretné elérhetővé tenni. Itt láthatja az összes a nyers események és exportálása az eseményeket JSON vagy CSV-fájlként. A Time Series Insights minden nyers adatot tárol.

    [![események](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Válassza ki a **STATS** után, minták és oszlopstatisztikák elérhetővé események áttekintése lapon.

    - **Minták**: Ez a funkció proaktív módon a legtöbb statisztikailag minták a kiválasztott adatterületen feltárásával. Nem kell megérteni, milyen minták igényelnek a legtöbb időt és energiát események ezer meg. A Time Series Insights közvetlenül folytatja az elemzést végző statisztikailag mintákkal is ugorhat. Ez a funkció menthet vizsgálatot előzményadatokat is hasznos lehet.
    - **Oszlopstatisztikák**: Oszlopstatisztikák adja meg a diagramok és táblázatok, amelyek keresztül a kiválasztott időtartomány az egyes oszlopok kiválasztott adatsorozat adatainak felosztania.

      [![STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Most már most láthatta, a különböző funkciók és lehetőségek érhetők el a Time Series Insights explorer webalkalmazáson belül.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [problémák diagnosztizálása és megoldása](time-series-insights-diagnose-and-solve-problems.md) a Time Series Insights-környezetben.
- Az interaktív igénybe [Azure Time Series Insights gyors](time-series-quickstart.md) bemutatót.

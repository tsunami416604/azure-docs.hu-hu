---
title: 'Rövid útmutató: Azure Time Series Insights-felfedező – Azure Time Series Insights | Microsoft dokumentumok'
description: Ismerje meg, hogyan kezdheti el az Azure Time Series Insights-kezelőt. Nagy mennyiségű IoT-adat és a környezet főbb funkcióinak megjelenítése.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 01/06/2020
ms.openlocfilehash: a905054b1b2a04fa2b7865d2c1065ccee37cffc0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75860430"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Rövid útmutató: Ismerkedés az Azure Time Series Insightsszal

Ez az Azure Time Series Insights-felfedező rövid útmutató segítségével ingyenes bemutatókörnyezetben kezdheti el a Time Series Insights szolgáltatást. Ebben a rövid útmutatóban megtudhatja, hogyan használhatja a webböngészőt nagy mennyiségű IoT-adat megjelenítésére, és hogyan jelenítheti meg az általánosan elérhető kulcsfunkciókat.

Az Azure Time Series Insights egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amely leegyszerűsíti az IoT-események milliárdjainak egyidejű feltárását és elemzését. Globális képet ad az adatokról, így gyorsan ellenőrizheti az IoT-megoldást, és elkerülheti a kritikus fontosságú eszközök költséges állásidejét. Az Azure Time Series Insights segítségével közel valós időben fedezheti fel a rejtett trendeket, észlelheti az anomáliákat, és közel valós időben végezhet kiváltó okokat.

A nagyobb rugalmasság érdekében hozzáadhatja az Azure Time Series Insights ot egy már meglévő alkalmazáshoz a hatékony [REST API-k](./time-series-insights-update-tsq.md) és [az ügyfél SDK segítségével.](https://github.com/microsoft/tsiclient) Az API-k segítségével tárolhatja, lekérdezheti és felhasználhatja az idősorozat-adatokat egy ön által választott ügyfélalkalmazásban. Az ügyfél SDK-val felhasználói felület-összetevőket is hozzáadhat a meglévő alkalmazáshoz.

Ez a Time Series Insights explorer rövid útmutató tanévenként általánosan elérhető funkciókat.

> [!IMPORTANT]
> Hozzon létre egy [ingyenes Azure-fiókot,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) ha még nem hozott létre egyet.

## <a name="prepare-the-demo-environment"></a>A bemutató környezet előkészítése

1. A böngészőben nyissa meg az [általános elérhetőségi demót.](https://insights.timeseries.azure.com/demo)

1. Ha a rendszer kéri, jelentkezzen be a Time Series Insights explorer az Azure-fiók hitelesítő adatait használja.

1. Megjelenik a Time Series Insights rövid bemutató lapja. A gyors bemutató megkezdéséhez válassza a **Tovább** gombot.

   [![Rövid útmutató – válassza a Tovább gombot](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Fedezze fel a demo környezetet

1. Megjelenik **az Idő választópanel.** Ezen a panelen választhatja ki a megjeleníteni kívánt időkeretet.

   [![Időkiválasztás panel](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Jelöljön ki egy időkeretet, és húzza a régióban. Ezután válassza a **Keresés lehetőséget.**

   [![Időkeret kiválasztása](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   A Time Series Insights megjelenít egy diagramot a megadott időkerethez. A vonaldiagramon belül különböző műveleteket tehet. Szűrhet például, rögzíthet, rendezhet és halmozhat.

   Az Idő **választópanelre**való visszatéréshez jelölje ki a lefelé mutató nyilat a következők éppen:

   [![Diagram](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Új keresési kifejezés hozzáadásához válassza a **Hozzáadás** lehetőséget a **Feltételek panelen.**

   [![Keresési kifejezések hozzáadása panel](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. A diagramon válasszon ki egy régiót, kattintson rá a jobb gombbal, majd válassza az **Explore Events** (Események áttekintése) elemet.

   [![Események áttekintése](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   A nyers adatok rácsa a vizsgálatba kívánt területről jelenik meg.

   [![Események feltárása – rácsadatnézet](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Adatok kijelölése és szűrése

1. A kifejezések szerkesztésével módosíthatja a diagram értékeit. Adjon hozzá egy másik kifejezést a különböző típusú értékek keresztösszevetéséhez.

   [![Kifejezés hozzáadása](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Hagyja üresen a **Szűrő sorozat** mezőt az összes kijelölt keresési kifejezés megjelenítéséhez, vagy írjon be egy szűrőkifejezést a **Sorozatszűrés mezőbe** a rögtönzött sorozatszűréshez.

   [![Sorozatok szűrése](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   A rövid útmutatóhoz adja meg a **Station5** értéket az állomásra jellemző hőmérséklet és nyomás összevetéséhez.

A rövid útmutató befejezése után a mintaadatokkal kísérletezve különböző vizualizációkat hozhat létre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy befejezte az oktatóanyagot, tisztítsa meg a létrehozott erőforrásokat:

1. Az [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza az **Összes erőforrás**lehetőséget, keresse meg az Azure Time Series Insights erőforráscsoportot.
1. Törölje a teljes erőforráscsoportot (és a benne lévő összes erőforrást) az egyes erőforrások **törlése** vagy eltávolítása külön-külön lehetőség kiválasztásával.

## <a name="next-steps"></a>További lépések

Készen áll saját Time Series Insights-környezet létrehozására:
> [!div class="nextstepaction"]
> [Tervezze meg a Time Series Insights környezetet](time-series-insights-environment-planning.md)

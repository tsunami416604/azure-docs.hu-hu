---
title: 'Gyors útmutató: Azure Time Series Insights Explorer | Microsoft Docs'
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti meg a webböngészőben a Azure Time Series Insights Explorert, hogy nagy mennyiségű IoT-adatait jelenítse meg. A legfontosabb jellemzőkkel a bemutató környezetben ismerkedhet meg.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 07/29/2019
ms.openlocfilehash: 0a91db15fcf77a936905a68c6b69b507d4e32a78
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677697"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Gyors útmutató: Ismerkedés az Azure Time Series Insightsszal

Ez a Azure Time Series Insights Explorer rövid útmutató segítséget nyújt a Time Series Insights ingyenes bemutató környezetben való megkezdéséhez. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a webböngészőt nagy mennyiségű IoT-információ és az általánosan elérhető főbb funkciók megjelenítésére.

A Azure Time Series Insights egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amely leegyszerűsíti a IoT-események milliárdjainak egyidejű feltárását és elemzését. Globális áttekintést nyújt az adatairól, így gyorsan ellenőrizheti IoT-megoldását, és elkerülheti az üzleti szempontból kritikus fontosságú eszközök költséges leállását. Azure Time Series Insights segít felderíteni a rejtett trendeket, a helyszíni rendellenességeket és a kiváltó okok elemzését közel valós időben.

A további rugalmasság érdekében a hatékony [REST API](./time-series-insights-update-tsq.md) -kon és az [ügyfél-SDK](./tutorial-create-tsi-sample-spa.md)-n keresztül Azure Time Series Insights adhat hozzá egy meglévő alkalmazáshoz. Az API-k segítségével az idősorozat-adatai tárolhatók, lefoglalhatók és felhasználhatók az Ön által választott ügyfélalkalmazások számára. Az ügyfél-SDK használatával felhasználói felületi összetevőket is hozzáadhat a meglévő alkalmazáshoz.

Ez a Time Series Insights Explorer rövid útmutató az általánosan elérhető funkciókkal kapcsolatos interaktív bemutatót kínál.

> [!IMPORTANT]
> Hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , ha még nem hozott létre egyet.

## <a name="prepare-the-demo-environment"></a>A bemutató környezet előkészítése

1. A böngészőben nyissa meg az [általános elérhetőségi bemutatót](https://insights.timeseries.azure.com/demo).

1. Ha a rendszer kéri, jelentkezzen be a Time Series Insights Explorerbe az Azure-fiókja hitelesítő adataival.

1. Megjelenik a Time Series Insights gyors bemutató oldal. A gyors bemutató elindításához kattintson a **tovább** gombra.

   [![Válassza a tovább lehetőséget](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Ismerkedés a bemutató környezettel

1. Ekkor megjelenik az **időkijelölés panel** . Ezen a panelen választhatja ki a megjeleníteni kívánt időkeretet.

   [![Idő kiválasztása panel](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Válasszon ki egy időkeretet, és húzza a régióba. Ezután válassza a **Keresés**lehetőséget.

   [![Időkeret kiválasztása](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   A Time Series Insights megjelenít egy diagramot a megadott időkerethez. A vonalas diagramon különböző műveleteket végezhet el. Például szűrheti, rögzítheti, rendezheti és halmozhatja fel.

   Az időkijelölési **panelre**való visszatéréshez kattintson a lefelé mutató nyílra az ábrán látható módon:

   [![Diagram](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Új keresési kifejezés hozzáadásához a **feltételek panelen** válassza a **Hozzáadás** lehetőséget.

   [![Elem hozzáadása](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. A diagramon válasszon ki egy régiót, kattintson rá a jobb gombbal, majd válassza az **Explore Events** (Események áttekintése) elemet.

   [![Események megismerése](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   A nyers adatok egy rácsa jelenik meg a feltárt régióból.

   [![Rács nézet](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Az adatszűrő kiválasztása és szűrése

1. Szerkessze a használati feltételeket a diagram értékeinek módosításához. Adjon hozzá egy másik kifejezést a különböző típusú értékek kereszthivatkozásához.

   [![Kifejezés hozzáadása](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Adjon meg egy szűrési kifejezést a **szűrő** adatsorozat mezőjében az improvizált adatsorozat-szűréshez. A rövid útmutatóhoz adja meg a **Station5** értéket az állomásra jellemző hőmérséklet és nyomás összevetéséhez.

   [![Adatsorozat szűrése](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

A rövid útmutató befejezése után a mintaadatokkal kísérletezve különböző vizualizációkat hozhat létre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy elvégezte az oktatóanyagot, törölje a létrehozott erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden erőforrás**lehetőséget, keresse meg a Azure Time Series Insights erőforráscsoportot.
1. Törölje a teljes erőforráscsoportot (és az abban található összes erőforrást) úgy, hogy kiválasztja az egyes erőforrások törlését vagy eltávolítását.

## <a name="next-steps"></a>További lépések

Készen áll a saját Time Series Insights környezet létrehozására:
> [!div class="nextstepaction"]
> [Time Series Insights-környezet tervezése](time-series-insights-environment-planning.md)

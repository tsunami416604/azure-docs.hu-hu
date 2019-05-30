---
title: 'Gyors útmutató: Az Azure Time Series Insights explorer |} A Microsoft Docs'
description: Ez a rövid útmutató bemutatja, hogyan használatába az Azure Time Series Insights explorer webböngészőben jeleníthet meg nagy mennyiségű IoT-adatot. A legfontosabb jellemzőkkel a bemutató környezetben ismerkedhet meg.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 17aee2555a0d0364ca5eb8e260ae10432a19bce6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244059"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Gyors útmutató: Ismerkedés az Azure Time Series Insightsszal

Rövid útmutató az Azure Time Series Insights explorer használatának megkezdésébe vezeti be a Time Series Insights egy ingyenes bemutató környezetben. Rajta megismerheti, hogyan jeleníthet meg nagy mennyiségű IoT adatok és a bemutató fő funkciókat, amelyek már általánosan elérhető a webböngésző használatával.

Az Azure Time Series Insights egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amely leegyszerűsíti az vizsgálata és elemezése több milliárd IoT-események, hogyan. Biztosít egy globális nézet az adatokról, így gyorsan ellenőrizheti IoT-megoldásait és elkerülheti a kritikus fontosságú eszközök költséges leállását. Az Azure Time Series Insights segít felderíteni a rejtett trendeket, azonosíthatja a rendellenességeket, és a kiváltó okainak közel valós időben végezhet.

További rugalmasságot biztosít az Azure Time Series Insights adhat hozzá egy már létező alkalmazást a hatékony keresztül [REST API-k](./time-series-insights-update-tsq.md) és [ügyféloldali SDK-val](./tutorial-create-tsi-sample-spa.md). Az API-k használatával tárolja, lekérdezés, és a választott ügyfélalkalmazás idősoros adatainak felhasználását. Az ügyfél-SDK hozzáadása a meglévő alkalmazás felhasználói felületi összetevőket is használhatja.

A Time Series Insights explorer rövid útmutató egy útmutató, most már általánosan elérhető funkciók kínál.

## <a name="prepare-the-demo-environment"></a>A környezet előkészítése a bemutató

1. Hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ha már hozott egyet.

1. A böngészőben nyissa meg a [általános rendelkezésre állás bemutató](https://insights.timeseries.azure.com/demo).

1. Ha a rendszer kéri, jelentkezzen be a Time Series Insights Explorerbe az Azure-fiókja hitelesítő adataival.

1. A Time Series Insights gyors bemutató oldala jeleníti meg. Válassza ki **tovább** , a gyors bemutató indításához.

   [![Kattintson a Tovább gombra](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Fedezze fel a bemutató környezetben

1. A **időkiválasztás panel** jeleníti meg. Ezen a panelen választhatja ki a megjeleníteni kívánt időkeretet.

   [![Időkiválasztás panel](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Időkeret kiválasztása, és húzza a régióban. Válassza ki **keresési**.

   [![Időkeret kiválasztása](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   A Time Series Insights megjelenít egy diagramot a megadott időkerethez. A vonaldiagram belül különböző műveleteket végezhet. Például akkor is szűrése, rögzítése, rendezheti és zásobníku.

   Térjen vissza a **időkiválasztás panel**, válassza a lefelé mutató nyíl látható módon:

   [![Diagram](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Válassza ki **Hozzáadás** a a **kifejezések panel** hozzáadása egy új keresési kifejezés.

   [![Elem hozzáadása](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. A diagramon válasszon ki egy régiót, kattintson rá a jobb gombbal, majd válassza az **Explore Events** (Események áttekintése) elemet.

   [![Események tallózása](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Egy rácsot a nyers adatok jeleníti meg a régió, hogy vizsgál.

   [![Rácsnézet](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Válassza ki, és az adatok szűrése

1. A diagram az értékeket módosítsa a kifejezéseket szerkesztve. Adjon hozzá egy újabb kifejezést a különböző típusú értékek összevetéséhez.

   [![Kifejezés hozzáadása](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Adjon meg egy szűrőkifejezést a **sorozatok szűrése** veszélyes adatsorozat-szűrés be. A rövid útmutatóhoz adja meg a **Station5** értéket az állomásra jellemző hőmérséklet és nyomás összevetéséhez.

   [![Sorozatok szűrése](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

A rövid útmutató befejezése után a mintaadatokkal kísérletezve különböző vizualizációkat hozhat létre.

## <a name="next-steps"></a>További lépések

Készen áll a saját Time Series Insights-környezet létrehozása:
> [!div class="nextstepaction"]
> [Time Series Insights-környezet tervezése](time-series-insights-environment-planning.md)

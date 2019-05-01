---
title: 'Gyors útmutató: Az Azure Time Series Insights explorer |} A Microsoft Docs'
description: Ez a rövid útmutató ismerteti az Azure Time Series Insights Explorer webböngészőben való használatának első lépéseit, hogy nagy mennyiségű IoT-adatot tudjon megjeleníteni. A legfontosabb jellemzőkkel a bemutató környezetben ismerkedhet meg.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: be663520c9c1afd11ace57cd9dcb8ffb8219b86b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696958"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Gyors útmutató: Ismerkedés az Azure Time Series Insightsszal

Egy rövid explorer egy ingyenes bemutató környezetben az Azure Time Series Insights használatába van megadva. Rajta hogy megtudhatja, hogyan jeleníthet meg nagy mennyiségű IoT adatok és a bemutató a legfontosabb jellemzők jelenleg az általános elérhetőség a webböngésző használatával.

Az Azure Time Series Insights egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amely leegyszerűsíti az vizsgálata és elemezése több milliárd IoT-események, hogyan. Biztosít egy globális nézet az adatokról, így gyorsan ellenőrizheti IoT-megoldásait, és elkerülheti a kritikus fontosságú eszközök költséges leállását. Az Azure Time Series Insightson keresztül feltárhatja a rejtett trendeket, azonosíthatja a rendellenességeket, és a kiváltó okainak közel valós időben végezhet.

További rugalmasságot biztosít az Azure Time Series Insights egy meglévő alkalmazást a hatékony keresztül lehet hozzáadni [REST API-k](./time-series-insights-update-tsq.md) és [ügyféloldali SDK-val](./tutorial-create-tsi-sample-spa.md). Az API-k szeretné tárolni, a lekérdezés idősorozat-adatok, lehetővé teszik, és a választott ügyfélalkalmazás idősoros adatainak felhasználását. Dönthet úgy is, az ügyfél-SDK használatával ad hozzá a meglévő alkalmazás felhasználói felületi összetevőket.

A Time Series Insights explorer egy interaktív bemutatóját funkciókat jelenleg az általános rendelkezésre állás.

## <a name="prepare-the-demo-environment"></a>A környezet előkészítése a bemutató

1. Hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ha egyik még nem hozták létre.

1. A böngészőben navigáljon a [általános rendelkezésre állás bemutató](https://insights.timeseries.azure.com/demo).

1. Ha a rendszer kéri, jelentkezzen be a Time Series Insights explorer, az Azure-fiókja hitelesítő adataival.

1. Megjelenik a Time Series Insights gyors bemutató oldala. A gyors bemutató indításához kattintson a **Next** (Tovább) gombra.

   [![Kattintson a Tovább gombra](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Fedezze fel a bemutató környezetben

1. Megjelenik a **Time selection panel** (Időkiválasztás panel). Ezen a panelen választhatja ki a megjeleníteni kívánt időkeretet.

   [![Időkiválasztás panel](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Kattintson és húzzon a területen, majd kattintson a **Keresés** gombra.

   [![Időkeret kiválasztása](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   A Time Series Insights megjelenít egy diagramot a megadott időkerethez. A vonaldiagramokon belül számos műveletre van lehetősége, például szűrhet, rögzíthet, rendezhet és rétegezhet.

   Az **Időkiválasztás panelre** való visszatéréshez kattintson a lefelé nyílra, ahogy az ábrán is látható:

   [![Diagram](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Új keresési kifejezés felvételéhez kattintson a **Terms panel** (Kifejezések panel) **Add** (Hozzáadás) elemére.

   [![Elem hozzáadása](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. A diagramon válasszon ki egy régiót, kattintson rá a jobb gombbal, majd válassza az **Explore Events** (Események áttekintése) elemet.

   [![Események tallózása](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Megjelenik egy nyers adatokat tartalmazó, a vizsgált régióra vonatkozó rács:

   [![Rácsnézet](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Válassza ki, és az adatok szűrése

1. A kifejezéseket szerkesztve módosíthatja a diagram értékeit, és fel is vehet egy újabb kifejezést a különböző típusú értékek összevetéséhez:

   [![Kifejezés hozzáadása](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Adjon meg egy szűrőkifejezést a **sorozatok szűrése...**  veszélyes adatsorozat-szűrés be. A rövid útmutatóhoz adja meg a **Station5** értéket az állomásra jellemző hőmérséklet és nyomás összevetéséhez.

   [![Sorozatok szűrése](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

A rövid útmutató befejezése után a mintaadatokkal kísérletezve különböző vizualizációkat hozhat létre.

## <a name="next-steps"></a>További lépések

Készen áll a saját Time Series Insights-környezete létrehozására.
> [!div class="nextstepaction"]
> [Time Series Insights-környezet tervezése](time-series-insights-environment-planning.md)

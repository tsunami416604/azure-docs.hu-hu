---
title: "Gyors üzembe helyezés – Azure idő adatsorozat Insights explorer |} Microsoft Docs"
description: "A gyors üzembe helyezés bemutatja, hogyan Ismerkedés az Azure idő adatsorozat Insights explorer webböngészőben nagy mennyiségű adatot IoT megjelenítéséhez. Megismerkedhet a legfontosabb jellemzők bemutató környezetben."
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: v-mamcge
manager: jhubbard
editor: MarkMcGeeAtAquent, jasonwhowell, kfile, MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc
ms.date: 11/15/2017
ms.openlocfilehash: b1f2881da21849c3ac09b008640fc9f72dc158dd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Gyors üzembe helyezés: Azure idő adatsorozat Insights felfedezése
A gyors üzembe helyezés bemutatja, hogyan Ismerkedés az Azure idő adatsorozat Insights explorer szabad bemutató környezetben. Megismerheti, hogyan használatára, megjelenítheti, IoT adatok nagy mennyiségű, és megismerkedhet a kulcsfontosságú szolgáltatásokat a idő adatsorozat Insights explorer böngészőben. 

Az Azure Time Series Insights egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amellyel egyszerűen elvégezhető IoT-események milliárdjainak párhuzamos vizsgálata és elemezése. Biztosít egy globális nézet adatairól, és így gyorsan az IoT-megoldásból érvényesítése és költséges állásidőt kritikus fontosságú eszközökre elkerülheti, ha gondoskodik a rejtett trendek felderítése rendellenességeket azonnal, és közel valós idejű kiváltó okának elemzést.  Ha egy alkalmazás, amelyet a Store-ból vagy a lekérdezés adatsorozat időadatok, fejleszthet idő adatsorozat Insights REST API-k használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>A bemutató környezetében idő adatsorozat Insights explorer felfedezés

1. A böngészőben navigáljon [https://insights.timeseries.azure.com/demo](https://insights.timeseries.azure.com/demo). 

2. Ha a rendszer kéri, jelentkezzen be a idő adatsorozat Insights explorer Azure-fiók hitelesítő adataival. 
 
3. Az idő adatsorozat Insights kalauz lap is megjelenik. Kattintson a **tovább** a Kalauz megkezdéséhez.

   ![Kattintson a Tovább gombra](media/quickstart/quickstart1.png)

4. A **idő ablaktáblát** jelenik meg. Ezen a panelen jelölje be egy időkeretet megjelenítéséhez.

   ![Idő kiválasztása panel](media/quickstart/quickstart2.png)

5. Kattintson és húzza a régióban, majd kattintson a **keresési** gombra.
 
   ![Válassza ki a időszakra](media/quickstart/quickstart3.png) 

   Idő adatsorozat elemzések a megadott időszakon belül a diagram képi megjelenítés jeleníti meg. Belül a vonaldiagram, például a szűrést, a különböző műveleteket végezhet, rögzítési, rendezési és halmozási. 

   Visszatérhet a **idő ablaktáblát**, kattintson a lefelé mutató nyílra, látható módon:

   ![A diagram](media/quickstart/quickstart4.png)

6. Kattintson a **Hozzáadás** a a **feltételek panel** hozzáadása egy új kívánt keresőkifejezést.

   ![Elem hozzáadása](media/quickstart/quickstart5.png)

7. A diagramon, válasszon ki egy régiót, kattintson a jobb gombbal a régiót, és válassza ki **felfedezés események**.
 
   ![Események tallózása](media/quickstart/quickstart6.png)

   A nyers adatok a rács-régióban van felfedezése jelenik meg:

   ![Rácsvonalak megjelenítése](media/quickstart/quickstart7.png)

8. A feltételeket, hogy módosítsa az értékeket a diagram szerkesztése, és adja hozzá egy másik kifejezés a kereszt-korrelálja különböző típusú értékeket:

   ![Egy kifejezés hozzáadása](media/quickstart/quickstart8.png)

9. Adja meg a szűrési kifejezés a **adatsorozat szűréséhez...**  jelölését eseti adatsorozat szűréséhez. Adja meg a gyors üzembe helyezés **Station5** a kereszt-korrelálja hőmérséklet és nyomás ennél az állomásnál.
 
   ![Szűrő sorozat](media/quickstart/quickstart9.png)

A gyors üzembe helyezés befejezése után kísérletezhet minta adatkészlet másik képi megjelenítéseket készíthet. 

### <a name="next-steps"></a>Következő lépések
Készen áll a saját idő adatsorozat Insights környezetet hozhat létre:
> [!div class="nextstepaction"]
> [A idő adatsorozat Insights környezet megtervezése](time-series-insights-environment-planning.md)

---
title: "Megőrzési felhasználóelemzés webes alkalmazásokhoz az Azure Application insights szolgáltatással |} Microsoft docs"
description: "Hány felhasználó az alkalmazáshoz való visszatérésre?"
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 98ceee23f49d72034432f38307a4f5e8ab87c013
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Megőrzési felhasználóelemzés webes alkalmazásokhoz az Application insights szolgáltatással

A megőrzési szolgáltatásából [Azure Application Insights](app-insights-overview.md) hány felhasználó térjen vissza az alkalmazást, és milyen gyakran bizonyos feladatok végrehajtása, vagy célok elérése elemez nyújt segítséget. Ha futtatja a game hely, például sikerült felhasználók számától játék elvesztése után térjen vissza a hely számára került ki nyertesen után térjen vissza a számok összehasonlítása. A Tudásbázis segítségével javítható a felhasználói élmény és az üzleti stratégia is.

## <a name="get-started"></a>Bevezetés

Ha még nem látja az Application Insights portáljáról, megőrzés eszközének adatok [megtudhatja, hogyan lásson a használati eszközök](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>A megőrzési eszköz

![Megtartási eszköz](./media/app-insights-usage-retention/retention.png)

1. Az eszköztár lehetővé teszi a felhasználóknak új megőrzési jelentéseket készíthet, nyissa meg a meglévő adatmegőrzési jelentések, aktuális megőrzési jelentés mentése vagy menteni, mert visszaállítani a mentett jelentés végrehajtott módosításokat, a jelentés adatainak frissítése, e-mailben vagy közvetlen hivatkozást jelentés megosztása és a dokumentációja lap. 
2. Alapértelmezés szerint a megőrzési minden felhasználó, aki nem végzett majd visszatért, és nem végzett más egy meghatározott időtartam során jeleníti meg. Kiválaszthatja, hogy az adott felhasználói tevékenységek szűkítése események különböző kombinációja.
3. Egy vagy több szűrők hozzáadása tulajdonsághoz. Például összpontosíthat felhasználók egy adott országban vagy régióban. Kattintson a **frissítés** a szűrők beállítását követően. 
4. A teljes megőrzési diagram felhasználói megőrzési összegzését jeleníti meg a kijelölt időszakot között. 
5. A rács őrzi meg a Lekérdezésszerkesztőben #2 szerint felhasználók számát mutatja. Minden sor egy kohorszok időszak megjelenített idő bármely esemény elvégző felhasználók jelöli. A sorban szereplő mindegyik cellához jeleníti meg, hány adott kohorszok visszaadott legalább egyszer újabb időn belül. Egyes felhasználók több időszakban adhat vissza. 
6. Az elemzések kártyák felső öt kezdeményező eseményeket, és lehetőséget nyújt a felhasználóknak egy szeretné jobban megismerni a megőrzési jelentés felső öt visszaadott események megjelenítése. 

![Megőrzési egérrel való rámutatáskor használt](./media/app-insights-usage-retention/hover.png)

A megőrzési eszköz elmagyarázza, mit jelent a cella analytics gombra és eszköztippek eléréséhez a cellák felhasználók is mutasson. Az elemzés gombra a felhasználók számára az előre megadott lekérdezéssel elemzőeszköz felhasználók generálása a cella vesz igénybe. 

## <a name="use-business-events-to-track-retention"></a>Az üzleti eseményeket segítségével nyomon követheti a megőrzési

Ahhoz, hogy a leghasznosabb megőrzési elemzés, mérjük az eseményeket, amelyek megfelelnek a jelentős üzleti tevékenységét. 

Például számos felhasználó előfordulhat, hogy a weblapot az alkalmazás megjeleníti a játék nélkül. Nyomon követése a lapmegtekintések ezért adjon volna pontos becslést hányan térjen vissza a játék korábban élvező azt követően. Ahhoz, hogy az adatszolgáltató játékosok világossá, az alkalmazás küldjön egy egyéni eseményt, amikor egy felhasználó ténylegesen játszik.  

Tanácsos egyéni események, amelyek megfelelnek a fő üzleti műveletek code, és használhatja az adatmegőrzési elemzések végrehajtását. A game eredményének rögzítéséhez kell írnia egy egyéni eseményt küldeni az Application Insights kódsort. Ön írás a weblap kód vagy a Node.JS, néz ki:

```JavaScript
    appinsights.trackEvent("won game");
```

Vagy az ASP.NET kiszolgálóoldali kódban:

```C#
   telemetry.TrackEvent("won game");
```

[További tudnivalók az egyéni események írása](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Következő lépések
- Ahhoz, hogy a használati tapasztalatok, küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [lapmegtekintés](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha egyéni események vagy Lapmegtekintések már küld, megismerkedhet a használati eszközök további, a szolgáltatás használatát a felhasználók.
    - [Felhasználók, munkamenetek, események](app-insights-usage-segmentation.md)
    - [Tölcsérek](usage-funnels.md)
    - [Felhasználói folyamatok](app-insights-usage-flows.md)
    - [Munkafüzetek](app-insights-usage-workbooks.md)
    - [Felhasználói környezet hozzáadása](app-insights-usage-send-user-context.md)



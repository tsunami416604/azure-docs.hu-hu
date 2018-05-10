---
title: Az Azure Application Insights használatelemzés hibaelhárítása
description: Hibaelhárítási útmutató - elemzése a hely és az alkalmazás használati az Application insights szolgáltatással.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/16/2018
ms.author: mbullwin;daviste
ms.openlocfilehash: 654b99085c406f13fe95476457234761bf840422
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="troubleshoot-usage-analytics-in-application-insights"></a>Az Application Insightsban használatelemzés hibaelhárítása
Kapcsolatos kérdése van a [használati elemzőeszközök az Application Insightsban](app-insights-usage-overview.md): [felhasználók munkamenetek, események](app-insights-usage-segmentation.md), [tölcsérek](usage-funnels.md), [felhasználói Forgalomáramlás](app-insights-usage-flows.md), [Megőrzési](app-insights-usage-retention.md), vagy Cohorts? Az alábbiakban néhány.

## <a name="counting-users"></a>Felhasználók számlálási
**A használatelemzés eszközök jelzik, hogy az alkalmazás rendelkezik egy felhasználó/munkamenet, de tudható, hogy az alkalmazás számos felhasználói/munkamenetek tartoznak. Hogyan megoldhatja e helytelen számlálási?**

Az Application Insightsban összes telemetriai események rendelkezik egy [névtelen felhasználói azonosító](application-insights-data-model-context.md) és egy [munkamenet-azonosító](application-insights-data-model-context.md) , két szabványos tulajdonságaikat. Alapértelmezés szerint a használati analytics eszközök száma felhasználók és a munkamenetek ezek alapján. Ha a standard tulajdonságok nem alatt feltöltve minden felhasználó és az alkalmazás munkamenet egyedi azonosítóval, megjelenik a felhasználók és a használati elemzőeszközök munkamenetekben helytelen számú.

Ha a webes alkalmazás figyelés a legegyszerűbb megoldás az hozzáadása a [Application Insights JavaScript SDK](app-insights-javascript.md) az alkalmazást, és győződjön meg arról, hogy a a parancsfájl kódrészletet a figyelni kívánt minden oldalon be van töltve. A JavaScript SDK automatikusan névtelen felhasználó és a munkamenet-azonosítókat hoz létre, akkor tölti fel a telemetriai események ezek az azonosítók, azok még elküldve az alkalmazásból.

Ha egy webszolgáltatás-bővítmény (nincs felhasználói felület), hogy figyelési [hozzon létre egy telemetriai inicializáló a névtelen felhasználói Azonosítót és a munkamenet azonosítója tulajdonságok feltöltő](app-insights-usage-send-user-context.md) egyedi felhasználók és a munkamenetek a szolgáltatás vélemények alapján.

Ha az alkalmazás küldi [hitelesített felhasználói azonosítók](app-insights-api-custom-events-metrics.md#authenticated-users), akkor is száma alapján hitelesített felhasználói azonosítók a felhasználók eszközben. A "Megjelenítés" legördülő menüben válassza a "Hitelesített felhasználók".

A használati elemzőeszközök jelenleg nem támogatják a számlálási felhasználók vagy a munkamenetek nem névtelen felhasználói Azonosítóját, a hitelesített felhasználói azonosító vagy a munkamenet-azonosítót. tulajdonságok alapján

## <a name="naming-events"></a>Elnevezési események
**Saját alkalmazás még több ezer különböző nézet és egyéni esemény neve. Nehezen megkülönböztetését, és a használati elemzőeszközök gyakran válaszképtelen lesz. Hogyan javíthatók a elnevezési problémát?**

Lapmegtekintés és egyéni esemény nevét a használati elemzőeszközök egyaránt használatban. Események elnevezési is fontos érték lekérése ezek az eszközök számára. A cél, közötti egyensúly túl kevés, túlságosan általános neve ("gombra kattintott") rendelkezik, és hogy túl sok, túlságosan egyedi nevek ("Szerkesztés gomb kattintott http://www.contoso.com/index").

A nézet neve, az alkalmazás küld egyéni esemény neve módosítani, módosítani szeretné az alkalmazás forráskódját, és helyezze üzembe újra. **Az Application Insights adatait 90 napig tárolja, és nem lehet törölni az összes telemetriai**, így az esemény neve végrehajtott módosítások érvénybe teljesen manifest 90 nap. Név módosítások elvégzése után 90 napig is a régi és az új esemény neve lesz jelenik meg a telemetriai, úgy állítsa be a lekérdezések és ennek megfelelően az adapterekhez közölje.

Ha az alkalmazás túl sok lap Nézetnevek küldi, ellenőrizze, hogy a lap nézet neve meg van határozva manuálisan a kódban, vagy ha azok még küldött automatikusan az Application Insights JavaScript SDK által:

* Ha a nézet neveket manuálisan megadott kód használatával a [ `trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), tartalmazó nevet megadni. Például a nevében a nézet URL-CÍMÉT és gyakori hibák elkerülése érdekében. Használja ezt az URL-paramétert a `trackPageView` API. Helyezze át egyéb részleteket a lap nézet neve egyéni tulajdonságokat.

* Az Application Insights JavaScript SDK nézet neveket automatikusan küld, ha a lapok címek módosítása, vagy manuálisan küldése az oldalon a Nézetnevek váltani. Az SDK-t küld a [cím](https://developer.mozilla.org/docs/Web/HTML/Element/title) oldalnévként nézet, alapértelmezés szerint minden lapon. Általános, de vegye figyelembe a keresőmotor-Optimalizáláshoz és más hatások eredményezhet. a módosítás a címeket is módosíthatja. A manuálisan adja meg a nézet nevét a `trackPageView` API felülbírálja a automatikusan összegyűjtött nevek, akkor küldhet általánosabb nevek telemetriai adatokat a lap címek módosítása nélkül.   

Ha az alkalmazás túl sok egyéni esemény név küldi, megadni, hogy a kódban nevének módosítása Ebben az esetben ne URL-címek és más / oldal vagy dinamikus adatokat egyéni esemény nevét, amely közvetlenül. Ehelyett ezeket az adatokat áthelyezi az egyéni esemény az egyéni tulajdonságok a `trackEvent` API. Például ahelyett, hogy `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, javasoljuk, hogy hasonlót `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>További lépések

* [Használati elemzés áttekintése](app-insights-usage-overview.md)

## <a name="get-help"></a>Segítségkérés
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)


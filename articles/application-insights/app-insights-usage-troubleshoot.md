---
title: Felhasználói viselkedés elemzési eszközök az Azure Application Insights – hibaelhárítás
description: Hibaelhárítási útmutató – az Application insights segítségével a hely és az alkalmazás használati elemzése.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: c1e5b420c4821732aa8cc19328b0955070100926
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138504"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Felhasználói viselkedés elemzési eszközök az Application Insights – hibaelhárítás
Kapcsolatos kérdése van a [felhasználói viselkedés elemzési eszközök az Application Insights](app-insights-usage-overview.md): [felhasználók, munkamenetek, események](app-insights-usage-segmentation.md), [tölcsérek](usage-funnels.md), [felhasználói folyamatok](app-insights-usage-flows.md), [Megőrzési](app-insights-usage-retention.md), vagy a Kohorszok? Az alábbiakban néhány válasz.

## <a name="counting-users"></a>Leltár felhasználók
**A felhasználói viselkedés elemzési eszközök megjelenítéséhez, hogy az alkalmazás rendelkezik egy munkamenetben, de tudni, hogy az alkalmazás számos felhasználók és munkamenetek van. Hogyan javíthatók a nem megfelelő számát?**

Az Application Insights összes telemetriai esemény van egy [névtelen felhasználó azonosítója](application-insights-data-model-context.md) és a egy [munkamenet-azonosító](application-insights-data-model-context.md) , azok alapvető tulajdonságok közül kettő. Alapértelmezés szerint az összes a használatelemző eszközökkel száma felhasználók és munkamenetek azonosítóit a részletekben alapján. Ha a standard tulajdonságok nincs folyamatban van feltöltve adatokkal, minden felhasználó és az alkalmazás munkamenet egyedi azonosítóval, látni fogja a felhasználók és munkamenetek a használatelemző eszközökkel a helytelen számú.

Webes alkalmazás használja a megfigyeléshez, a legegyszerűbb megoldás-e hozzáadni a [Application Insights JavaScript SDK](app-insights-javascript.md) az alkalmazást, és ellenőrizze, hogy a parancsfájl kódrészletet minden oldalon a figyelni kívánt betöltve. A JavaScript SDK automatikusan névtelen felhasználó és a munkamenet-azonosítók hoz létre, majd tölti fel ezeket az azonosítókat megtalálhatja a telemetria-eseményeinek, az alkalmazás számítva.

Ha egy webszolgáltatást (nincs felhasználói felület) használja a megfigyeléshez [hozzon létre egy telemetriainicializáló, amely feltölti a névtelen felhasználói Azonosítót és a munkamenet azonosítója tulajdonságok](app-insights-usage-send-user-context.md) egyedi felhasználók és munkamenetek a szolgáltatás fogalmakkal megfelelően.

Ha az alkalmazás által küldött [hitelesített felhasználói azonosítók](app-insights-api-custom-events-metrics.md#authenticated-users), a felhasználók eszköz alapján hitelesített felhasználó azonosítók megszámlálható. A "Show" legördülő listában válassza a "Hitelesített felhasználók."

A felhasználói viselkedés elemzési eszközök jelenleg nem támogatják a felhasználók és munkamenetek névtelen felhasználói Azonosítóját, a hitelesített felhasználó azonosítója és a munkamenet-azonosítót. tulajdonságok alapján

## <a name="naming-events"></a>Elnevezési események
**Az alkalmazás több ezer különböző lapmegtekintés és egyéni események neve van. Nehéz megkülönböztetni őket, és a felhasználói viselkedés elemzőeszközök gyakran nem válaszol. Hogyan javíthatom elnevezési problémák?**

Lapmegtekintés és egyéni esemény nevének során a felhasználói viselkedés elemzési eszközök használhatók. Események és elnevezési, kritikus fontosságú, ki ezeket az eszközöket. A cél közötti egyensúly, hogy túl kevés, túl általános neveket ("gombra való kattintás"), és a túl sok, túlságosan egyedi neveket ("Szerkesztés gombra kattint, a http://www.contoso.com/index").

Végezze el a módosításokat a lapmegtekintés és egyéni események neve, az alkalmazás által küldött, hogy módosítani szeretné az alkalmazás forráskódja és ismételt üzembe helyezése. **Az összes telemetriát az Application Insights adatait 90 napig tárolja, és nem lehet törölni**, így az események neve módosítás 90 nap teljes mértékben manifest vesz igénybe. Név módosítások elvégzése után 90 napig is a régi és új események neve lesz a telemetriai adatok megjelennek, úgy állítsa be a lekérdezések, és közölje belül munkatársait, ennek megfelelően.

Ha az alkalmazás túl sok oldal nézet nevet küldi, tekintse meg e ezen oldal nézet neve meg van határozva manuálisan a kódban, vagy ha azok még automatikusan által küldött az Application Insights JavaScript SDK:

* Ha az oldal nézet neve manuálisan meg van határozva a kód használatával a [ `trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), módosítsa a nevet megadni. Például írja be az oldal nézet nevét az URL-cím gyakori hibák elkerülése érdekében. Használja ezt az URL paramétert a `trackPageView` API-t. Helyezze át egyéb részleteket az oldal nézet nevét az egyéni tulajdonságokat.

* Ha az Application Insights JavaScript SDK automatikusan küldi oldal nézet nevét, a weblapok címek módosítása, vagy váltson át manuális küldése az oldal nézet nevét. Az SDK-t küld a [cím](https://developer.mozilla.org/docs/Web/HTML/Element/title) az egyes oldalak, az oldal nézet neve alapértelmezés szerint. Ezt a címek, de fordítson keresőmotor-Optimalizálást és más hatások, ez a változás lehetnek további általános is módosíthatja. A neveket manuálisan adja meg az oldal nézet a `trackPageView` API felülírja az automatikusan gyűjtött nevek, így elküldheti általános nevek telemetriai adatokat a lap címek módosítása nélkül.   

Ha az alkalmazás túl sok egyéni esemény nevének küldi, módosítsa a kódot úgy, hogy kevésbé specifikus a nevét. Újra kerülje az URL-címeket és egyéb oldalanként vagy a dinamikus információk egyéni esemény nevében szereplő üzembe közvetlenül. Ehelyett ezeket az adatokat áthelyezni az egyéni esemény az egyéni tulajdonságokat a `trackEvent` API-t. Például, nem pedig `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, javasoljuk, hogy valahogy `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>További lépések

* [Felhasználói viselkedés elemzési eszközök áttekintése](app-insights-usage-overview.md)

## <a name="get-help"></a>Segítségkérés
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)


---
title: Felhasználói elemzési eszközök – Azure Application Insights
description: Hibaelhárítási útmutató – a webhely- és alkalmazáshasználat elemzése az Application Insights segítségével.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8d2e573f34895207a455838b5fc64f95560943d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670916"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Felhasználói viselkedéselemző eszközök – problémamegoldás az Application Insights ban
Kérdése van a [felhasználói viselkedéselemző eszközökkel kapcsolatban az Application Insightsban:](usage-overview.md) [Felhasználók, munkamenetek, események,](usage-segmentation.md) [csatornák,](usage-funnels.md) [felhasználói folyamatok,](usage-flows.md) [megőrzés](usage-retention.md)vagy kohorszok? Íme néhány válasz.

## <a name="counting-users"></a>Felhasználók számlálása
**A felhasználói viselkedéselemző eszközök azt mutatják, hogy az alkalmazásomnak egy felhasználója/munkamenete van, de tudom, hogy az alkalmazásomnak sok felhasználója/munkamenete van. Hogyan javíthatom ezeket a helytelen számokat?**

Az Application Insights minden telemetriai eseménye rendelkezik egy [névtelen felhasználói azonosítóval](../../azure-monitor/app/data-model-context.md) és egy [munkamenet-azonosítóval,](../../azure-monitor/app/data-model-context.md) amelyek két szabványos tulajdonságuk. Alapértelmezés szerint az összes használatelemzési eszköz számolja a felhasználókat és a munkameneteket ezen azonosítók alapján. Ha ezek a szabványos tulajdonságok nincsenek feltöltve egyedi azonosítókkal az alkalmazás minden egyes felhasználójára és munkamenetére vonatkozóan, a használati elemzési eszközökben helytelenül fogja látni a felhasználók és munkamenetek számát.

Ha egy webalkalmazást figyel, a legegyszerűbb megoldás az [Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md) hozzáadása az alkalmazáshoz, és győződjön meg arról, hogy a parancsfájlkódrészlet be van töltve minden oldalon, amelyet figyelni szeretne. A JavaScript SDK automatikusan létrehozza a névtelen felhasználói és munkamenet-azonosítókat, majd feltölti a telemetriai eseményeket ezekkel az azonosítókkal, ahogy azok az alkalmazásból kerülnek elküldésre.

Ha egy webszolgáltatás (nincs felhasználói felület), [hozzon létre egy telemetriai inicializáló, amely feltölti a névtelen felhasználói azonosító és a munkamenet-azonosító tulajdonságai](usage-send-user-context.md) szerint a szolgáltatás fogalmak egyedi felhasználók és munkamenetek.

Ha az alkalmazás [hitelesített felhasználói azonosítókat](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)küld, a Felhasználók eszközben hitelesített felhasználói azonosítók alapján számolhat. A "Megjelenítés" legördülő menüben válassza a "Hitelesített felhasználók" lehetőséget.

A felhasználói viselkedéselemző eszközök jelenleg nem támogatják a számláló felhasználók vagy munkamenetek alapján tulajdonságok nem névtelen felhasználói azonosító, hitelesített felhasználói azonosító, vagy a munkamenet-azonosító.

## <a name="naming-events"></a>Elnevezési események
**Az alkalmazásom több ezer különböző oldalmegtekintési és egyéni eseménynévvel rendelkezik. Nehéz megkülönböztetni őket, és a felhasználói viselkedéselemző eszközök gyakran nem válaszolnak. Hogyan oldhatom meg ezeket az elnevezési problémákat?**

Az oldalnézet és az egyéni eseménynevek a felhasználói viselkedéselemző eszközökben használatosak. Az események helyes elnevezése elengedhetetlen ahhoz, hogy ezek az eszközök értéket kapjanak. A cél az egyensúly a túl kevés, túlságosan általános név ("Kattintással gomb" és a túl sok,\/túlságosan specifikus név között ("A szerkesztés gombra kattintva http: /www.contoso.com/index").

Ha módosítani szeretné az alkalmazás által küldött oldalnézetet és egyéni eseményneveket, módosítania kell az alkalmazás forráskódját, és újra kell telepítenie azokat. **Az Application Insights összes telemetriai adata 90 napig tárolódik, és nem törölhető,** így az eseményneveken végzett módosítások 90 napig tart a teljes jegyzékbe. A névváltoztatást követő 90 napon belül a régi és az új eseménynevek is megjelennek a telemetriai adatokban, ezért módosítsa a lekérdezéseket és kommunikáljon a csapatokon belül, ennek megfelelően.

Ha az alkalmazás túl sok oldalnézetnevet küld, ellenőrizze, hogy ezek az oldalnézetnevek manuálisan vannak-e megadva a kódban, vagy az Application Insights JavaScript SDK automatikusan küldi őket:

* Ha az oldalnézet neveit manuálisan [ `trackPageView` ](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)adja meg a kód az API használatával, módosítsa a nevet, hogy kevésbé specifikus legyen. Kerülje a gyakori hibákat, például az URL-t az oldalnézet nevébe. Ehelyett használja az `trackPageView` API URL-paraméterét. Helyezze át az egyéb részleteket az oldalnézet nevéből az egyéni tulajdonságokba.

* Ha az Application Insights JavaScript SDK automatikusan oldalnézetneveket küld, módosíthatja az oldalak címét, vagy átválthat az oldalnézet nevek manuális küldésére. Az SDK alapértelmezés szerint az egyes lapok [címét](https://developer.mozilla.org/docs/Web/HTML/Element/title) küldi el oldalnézet névként. Meg tudná változtatni a címeket, hogy általánosabb, de szem előtt tartva a SEO és egyéb hatások ez a változás lehetett volna. Az oldalnézet nevek manuális `trackPageView` megadása az API-val felülbírálja az automatikusan gyűjtött neveket, így az oldalcímek módosítása nélkül több általános nevet küldhet a telemetriai adatokban.   

Ha az alkalmazás túl sok egyéni eseménynevet küld, módosítsa a kódot a név kevésbé specifikusakra. Ismét kerülje az URL-címek és más oldalankénti vagy dinamikus információk elhelyezését közvetlenül az egyéni eseménynevekben. Ehelyett helyezze át ezeket az adatokat `trackEvent` az egyéni esemény egyéni tulajdonságaiba az API-val. A helyett például a `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`( `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`a) helyett valami ilyesmit javasolunk.

## <a name="next-steps"></a>További lépések

* [Felhasználói viselkedéselemző eszközök – áttekintés](usage-overview.md)

## <a name="get-help"></a>Segítségkérés
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)


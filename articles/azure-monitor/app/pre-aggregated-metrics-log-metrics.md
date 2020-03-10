---
title: Naplózási és előre aggregált mérőszámok az Azure Application Insightsban | Microsoft Docs
description: Miért érdemes a log-alapú és az előre összevont mérőszámokat használni az Azure-ban Application Insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 65abc9c7153aaf2973d5927400e27467066098f9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384458"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Napló alapú és előre összevont metrikák a Application Insights

Ez a cikk a "hagyományos" Application Insights a naplókon alapuló mérőszámok és a jelenleg nyilvános előzetes verzióban elérhető előre összevont mérőszámok közötti különbséget ismerteti. Mindkét típusú metrika elérhető a Application Insights felhasználók számára, és mindegyik egyedi értéket biztosít az alkalmazás állapotának, diagnosztizálásának és elemzésének figyeléséhez. Az alkalmazásokat kiépítő fejlesztők eldönthetik, hogy az alkalmazás méretétől, a várt telemetria és a metrikák pontosságára és riasztására vonatkozó üzleti követelményektől függően milyen típusú metrika felel meg legjobban az adott forgatókönyvnek.

## <a name="log-based-metrics"></a>Log-alapú metrikák

Egészen a közelmúltig a Application Insights alkalmazás-figyelési telemetria adatmodellje kizárólag kis számú előre definiált típusú eseménytől, például kérések, kivételek, függőségi hívások, oldalletöltések stb. alapján történt. A fejlesztők az SDK-t használhatják az események manuális kiküldésére (kód írásához, amely explicit módon meghívja az SDK-t), vagy az automatikus rendszerállapotból származó események automatikus gyűjtésére számíthatnak. Mindkét esetben a Application Insights-háttér az összes összegyűjtött eseményt naplófájlként tárolja, és a Azure Portal Application Insights pengéi analitikai és diagnosztikai eszközként szolgálnak az eseményvezérelt adatoknak a naplókból való megjelenítéséhez.

Az események teljes készletének megtartására szolgáló naplók használatával nagyszerű analitikai és diagnosztikai érték adható meg. Megadhatja például, hogy az adott URL-címre érkező kérések pontos száma pontosan hány különböző felhasználó számára készült. Vagy részletes diagnosztikai nyomkövetést is beolvashat, beleértve a kivételeket és a függőségi hívásokat a felhasználói munkamenetek esetében. Az ilyen típusú információk jelentős mértékben javíthatják az alkalmazás állapotát és használatát, így az alkalmazással kapcsolatos problémák diagnosztizálásához szükséges időt is csökkentheti. 

Ugyanakkor előfordulhat, hogy az események teljes készletének begyűjtése nem praktikus (vagy akár lehetetlen) olyan alkalmazások esetében, amelyek sok telemetria hoznak létre. Olyan helyzetekben, amikor az események mennyisége túl magas, Application Insights több telemetria-csökkentési módszert valósít meg, például a [mintavételt](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) és a [szűrést](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) , amely csökkenti az összegyűjtött és tárolt események számát. Sajnos a tárolt események számának csökkentése csökkenti a mutatók pontosságát is, amely a jelenetek mögött a naplókban tárolt események lekérdezési idejű összesítéseit is el kell végeznie.

> [!NOTE]
> Application Insights a naplókon tárolt események és mérések lekérdezési idejű összesítésén alapuló mérőszámokat log-alapú mérőszámoknak nevezzük. Ezek a metrikák jellemzően számos dimenzióval rendelkeznek az esemény tulajdonságaiból, így azok az elemzésekhez is magasabbak, de a mérőszámok pontossága negatívan befolyásolja a mintavételezést és a szűrést.

## <a name="pre-aggregated-metrics"></a>Előre összevont mérőszámok

A log-alapú 2018 mérőszámok mellett a Application Insights csapata az idősorozatra optimalizált speciális adattárban tárolt metrikák nyilvános előzetes verzióját is elküldte. Az új metrikák már nem tekinthetők meg olyan egyedi eseményekként, amelyeknek sok tulajdonsága van. Ehelyett a rendszer előre összesített idősorozatként tárolja őket, és csak a legfontosabb dimenziókkal. Így az új metrikák a lekérdezési időpontnál jobbak: az adatok beolvasása sokkal gyorsabban történik, és kevesebb számítási teljesítményt igényel. Ez Következésképpen olyan új forgatókönyveket tesz lehetővé, mint a [közel valós idejű riasztás a mérőszámok méreteivel](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts), rugalmasabb [irányítópultokkal](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)és egyebekkel.

> [!IMPORTANT]
> Mind a naplózási, mind az előre aggregált mérőszámok párhuzamosan léteznek a Application Insightsban. A kettő megkülönböztetéséhez az Application Insights UX-ben az előre összevont metrikák mostantól "standard metrikák (előzetes verzió)" néven jelennek meg, míg az események hagyományos mérőszámait átnevezték "log-alapú metrikák" névre.

Az újabb SDK-k ([Application Insights 2,7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK vagy újabb verziók a .net-hez) előre összevont metrikák a gyűjtemény során, mielőtt telemetria a mennyiségi csökkentési technikák beindítását. Ez azt jelenti, hogy az új metrikák pontossága nem befolyásolja a mintavételezést és a szűrést a legújabb Application Insights SDK-k használatakor.

Az olyan SDK-k esetében, amelyek nem implementálják az előzetes összesítést (azaz a Application Insights SDK-k vagy a böngésző-rendszerállapot korábbi verzióit), a Application Insights háttérrendszer az alkalmazás által fogadott események összesítésével továbbra is feltölti az új metrikákat. Az események gyűjtési végpontja. Ez azt jelenti, hogy miközben nem használja ki a hálózaton keresztül továbbított adatmennyiséget, továbbra is használhatja az előre összevont mérőszámokat, és a közel valós idejű dimenziós riasztások jobb teljesítményét és támogatását tapasztalhatja az SDK-k használatával, amelyek nem előre összevont metrikák a gyűjtemény során.

Érdemes megemlíteni, hogy a gyűjtemény végpontja előre összesíti az eseményeket a betöltési mintavételezés előtt, ami azt jelenti, hogy a betöltési [mintavételezés](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) soha nem befolyásolja az előre aggregált mérőszámok pontosságát, függetlenül az alkalmazáshoz használt SDK-verziótól.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Előzetes összesítés használata Application Insights egyéni metrikákkal

Az összesítést egyéni metrikákkal is használhatja. A két fő előnye az egyéni metrika dimenziójának konfigurálása és riasztása, valamint az SDK-ból a Application Insights-gyűjtemény végpontja számára továbbított adatok mennyiségének csökkentése.

[Az Application INSIGHTS SDK-ból többféleképpen küldhet egyéni metrikákat](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Ha az SDK verziója a [GetMetric és a TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) metódust is tartalmazza, akkor ez az egyéni metrikák küldésének előnyben részesített módja, mivel ebben az esetben az előzetes ÖSSZESÍTÉS az SDK-ban történik, nem csak az Azure-ban tárolt adatok mennyiségének csökkentése, hanem az SDK-ból a Application Insightsra továbbított adatok mennyisége is. Ellenkező esetben használja a [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) metódust, amely előre összesíti a metrikai eseményeket az adatok betöltése során.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Egyéni metrikák méretei és előzetes összesítés

A [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) vagy a [GetMetric és a TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) API-hívások használatával küldött összes mérőszámot a rendszer automatikusan tárolja a naplók és a metrikák tárolókban. Ugyanakkor bár az egyéni metrika naplózási alapú verziója mindig megőrzi az összes dimenziót, a metrika előre összevont verziója alapértelmezés szerint nincs dimenzió nélkül tárolva. Az egyéni metrikák dimenzióinak gyűjteményét bekapcsolhatja a [használati és becsült költségek](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) lapon a "riasztás engedélyezése az egyéni metrika-dimenziókban" lehetőségre kattintva: 

![Használat és becsült költségek](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Miért van alapértelmezés szerint kikapcsolva az egyéni metrikák méreteinek gyűjteménye?

Az egyéni metrikák dimenzióinak gyűjteménye alapértelmezés szerint ki van kapcsolva, mert az egyéni metrikák a dimenziókkal való tárolásának jövőbeli elszámolása külön történik a Application Insightstól, miközben a nem dimenziós egyéni metrikák tárolása ingyenes marad (akár kvótáig) is. . A hivatalos [díjszabási oldalon](https://azure.microsoft.com/pricing/details/monitor/)megismerheti a közelgő díjszabási modell változásait.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Diagramok létrehozása és a naplózási és standard szintű előre összesített mérőszámok feltárása

[Azure Monitor Metrikaböngésző](../platform/metrics-getting-started.md) használatával diagramokat rajzolhat előre összesített és naplózott metrikák alapján, valamint irányítópultokat hozhat létre a diagramokkal. A kívánt Application Insights erőforrás kiválasztása után a névtér-választóval válthat a standard (előzetes) és a log-alapú metrikák között, vagy kijelölhet egy egyéni metrikai névteret:

![Metrikai névtér](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>Következő lépések

* [Közel valós idejű riasztás](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric és TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)

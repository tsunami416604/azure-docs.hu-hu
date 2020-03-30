---
title: Naplóalapú és előre összesített metrikák az Azure Application Insightsban | Microsoft dokumentumok
description: Miért érdemes naplóalapú és előre összesített metrikákat használni az Azure Application Insightsban?
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 65abc9c7153aaf2973d5927400e27467066098f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275840"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Naplóalapú és előre összesített metrikák az Application Insightsban

Ez a cikk ismerteti a különbséget a "hagyományos" Application Insights-metrikák, amelyek naplók on alapulnak, és előre összesített metrikák, amelyek jelenleg nyilvános előzetes verzióban. Mindkét típusú metrikák érhetők el a felhasználók az Application Insights, és minden hoz egy egyedi értéket az alkalmazás állapotának, diagnosztika és elemzés figyelése. A fejlesztők, akik az alkalmazások műszerezése eldöntheti, hogy milyen típusú metrika a legmegfelelőbb egy adott forgatókönyv, méretétől függően az alkalmazás, a várható mennyiségű telemetriai adatok és az üzleti követelmények metrikák pontossága és riasztása.

## <a name="log-based-metrics"></a>Naplóalapú mérőszámok

Egészen a közelmúltig az alkalmazás figyelése telemetriai adatok modell Az Application Insights kizárólag egy kis számú előre meghatározott típusú események, például a kérelmek, kivételek, függőségi hívások, oldalnézetek stb. A fejlesztők használhatják az SDK-t, hogy manuálisan kibocsáthassák ezeket az eseményeket (az SDK-t explicit módon meghívó kód írásával), vagy támaszkodhatnak az automatikus instrumentációból származó események automatikus gyűjteményére. Mindkét esetben az Application Insights-háttérrendszer az összes összegyűjtött eseményt naplókként tárolja, és az Azure Insights-panelek az Azure Portalon analitikai és diagnosztikai eszközként működnek a naplók eseményalapú adatainak megjelenítéséhez.

Naplók használatával az események teljes készletének megőrzéséhez hozhat nagy analitikai és diagnosztikai értéket. Például lekaphatja egy adott URL-címre érkező kérelmek pontos számát a hívásokat kezdeményező különböző felhasználók számával. Vagy részletes diagnosztikai nyomkövetéseket is lekaphat, beleértve a kivételeket és a függőségi hívásokat bármely felhasználói munkamenethez. Az ilyen típusú információk jelentősen javíthatják az alkalmazás állapotának és használatának láthatóságát, lehetővé téve az alkalmazással kapcsolatos problémák diagnosztizálásához szükséges idő csökkentését. 

Ugyanakkor az események teljes készletének gyűjtése nem praktikus (vagy akár lehetetlen) a sok telemetriai adatokat generáló alkalmazások számára. Olyan helyzetekben, amikor az események mennyisége túl magas, az Application Insights több telemetriai kötet-csökkentési technikákat valósít meg, például [mintavételezési](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) és [szűrési,](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) amelyek csökkentik az összegyűjtött és a tárolt események számát. Sajnos a tárolt események számának csökkentése is csökkenti a metrikák pontosságát, amelyek a színfalak mögött, lekérdezési idejű összesítéseket kell végrehajtania a naplókban tárolt események.

> [!NOTE]
> Az Application Insights ban a metrikák, amelyek a naplókban tárolt események és mérések lekérdezés-idő összesítésén alapulnak, log-alapú metrikáknak nevezzük. Ezek a metrikák általában sok dimenziót az esemény tulajdonságait, ami miatt kiváló az elemzéshez, de ezek a metrikák pontosságát negatívan befolyásolja a mintavétel és a szűrés.

## <a name="pre-aggregated-metrics"></a>Előre összesített mutatók

A naplóalapú metrikák mellett 2018 őszén az Application Insights-csapat nyilvános előzetes verziót adott le az idősorozatokra optimalizált speciális tárházban tárolt mérőszámokról. Az új metrikák már nem tartják meg az egyes események sok tulajdonsággal. Ehelyett előre összesített idősorozatként tárolódnak, és csak kulcsfontosságú dimenziókkal. Ez teszi az új metrikák kiváló lekérdezési időben: az adatok beolvasása történik sokkal gyorsabb, és kevesebb számítási teljesítményt igényel. Ez lehetővé teszi az új forgatókönyveket, például [a közel valós idejű riasztást a metrikák dimenzióiról,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)a rugalmasabb irányítópultokról és egyebekről. [dashboards](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)

> [!IMPORTANT]
> Mindkettő, a naplóalapú és az előre összesített metrikák egymás mellett léteznek az Application Insightsban. A kettő megkülönböztetéséhez az Application Insights ux-ben az előre összesített metrikák neve most "Standard metrikák (előzetes verzió)", míg a hagyományos metrikák az események átnevezték "Log-alapú metrikák".

Az újabb SDK-k ([Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK vagy újabb a .NET) pre-aggregate metrikák a gyűjtés során, mielőtt telemetriai kötet csökkentési technikák beindulnak. Ez azt jelenti, hogy az új metrikák pontosságát nem befolyásolja a mintavétel és a szűrés a legújabb Application Insights SDK-k használatakor.

Az SDK-k, amelyek nem valósítják meg az előzetes összesítés (azaz az Application Insights SDK-k régebbi verziói vagy a böngésző instrumentation) az Application Insights-háttértovábbra is feltölti az új metrikák az alkalmazás által kapott események összesítésével Insights eseménygyűjtemény-végpont. Ez azt jelenti, hogy bár nem élvezheti a vezetéken keresztül továbbított adatok csökkentett mennyiségét, továbbra is használhatja az előre összesített mutatókat, és jobb teljesítményt és támogatást tapasztalhat a közel valós idejű dimenziós riasztásokhoz olyan SDK-kkal, amelyek nem a gyűjtés során az előzetes összesített mutatókat.

Érdemes megemlíteni, hogy a gyűjtemény végpont előzetes en-aggregálási mintavételezés előtt, ami azt jelenti, hogy [a betöltési mintavételi](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) soha nem befolyásolja az előre összesített metrikák pontosságát, függetlenül attól, hogy az alkalmazás sal használt SDK-verzió.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Előzetes összesítés használata az Application Insights egyéni mérőszámaival

Az előzetes összesítést egyéni mérőszámokkal is használhatja. A két fő előnye az egyéni metrika dimenziójának konfigurálása és riasztása, és az SDK-ból az Application Insights-gyűjtemény végpontra küldött adatok mennyiségének csökkentése.

Az [Application Insights SDK-ból többféleképpen küldhet egyéni metrikákat.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) Ha az SDK-verziója a [GetMetric és](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) a TrackValue metódusokat kínálja, ez az egyéni metrikák küldésének előnyben részesített módja, mivel ebben az esetben az előzetes összesítés az SDK-n belül történik, nem csak az Azure-ban tárolt adatok mennyiségét, hanem az SDK-ból az Application Insightsba továbbított adatok mennyiségét is. Ellenkező esetben használja a [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) metódust, amely az adatok betöltése során előre összesíti a metrikaeseményeket.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Egyéni metrikák dimenziói és előzetes összesítése

A trackMetric vagy [GetMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) [és TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) API-hívások használatával küldött összes metrika automatikusan tárolódik a naplókban és a metrikákban. Bár az egyéni metrika naplóalapú verziója mindig megőrzi az összes dimenziót, a metrika előre összesített verziója alapértelmezés szerint dimenziók nélkül tárolódik. A használat és a [becsült költség](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) lapon az egyéni metrikák dimenzióinak gyűjteményét az "Értesítés engedélyezése egyéni metrikadimenziókon" (Riasztás engedélyezése egyéni metrikadimenziókon) című lapon kapcsolhatja be: 

![Felhasználás és becsült költség](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Miért van alapértelmezés szerint az egyéni metrikák dimenzióinak gyűjteménye?

Az egyéni metrikák dimenzióinak gyűjteménye alapértelmezés szerint ki van kapcsolva, mert a jövőben a dimenziókkal rendelkező egyéni metrikák tárolása az Application Insightstól elkülönítve kerül számlázásra, míg a nem dimenziós egyéni metrikák tárolása ingyenes marad (a kvótáig) . A közelgő árazási modell változásairól hivatalos [árképzési oldalunkon](https://azure.microsoft.com/pricing/details/monitor/)olvashat.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Diagramok létrehozása, valamint naplóalapú és szabványos előre összesített mutatók feltárása

Az [Azure Monitor Metrics Explorer](../platform/metrics-getting-started.md) segítségével előre összesített és naplóalapú metrikákból ábrázolhatók a diagramok, valamint irányítópultok diagramokkal való létrehozása. A kívánt Application Insights-erőforrás kiválasztása után a névtérválasztóval válthat a szabványos (előzetes verzió) és a naplóalapú metrikák között, vagy válasszon egy egyéni metrikanévteret:

![Metrika névtér](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>További lépések

* [Közel valós idejű riasztás](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric és TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)

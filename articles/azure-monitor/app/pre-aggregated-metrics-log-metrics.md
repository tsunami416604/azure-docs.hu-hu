---
title: Napló-alapú, és előre összesített mérőszámok az Azure Application Insights |} A Microsoft Docs
description: Napló alapú előre összesített mérőszámok és használatát az Azure Application insights szolgáltatásban
services: application-insights
keywords: ''
author: vgorbenko
ms.author: vitaly.gorbenko
ms.reviewer: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d095be8c5d921001ab4a492d2385938020fa8ce0
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54003541"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Napló-alapú és előre összesített mérőszámok az Application insights szolgáltatásban

Ez a cikk ismerteti a különbség a naplók alapuló "hagyományos" Application Insights-metrikák, és előre összesített mérőszámok, amelyek jelenleg nyilvános előzetes verzióban érhető el. Mindkét típusú metrikák érhetők el a felhasználók az Application Insights, és minden egyes lehetővé, hogy az alkalmazás állapota, diagnosztikai és elemzési figyelési egyedi értéket. Alkalmazások vannak való műszerezéséről fejlesztők eldöntheti, melyik metrikus típus ajánlott olyan forgatókönyvre jellemzők, az alkalmazás méretétől függően várt mennyiségű, telemetriát, és az üzleti követelmények metrikák pontosság és riasztás céljából.

## <a name="log-based-metrics"></a>Napló-mérőszámok

Nemrég, amíg az alkalmazás figyelése az Application Insights telemetria adatmodell kizárólag alapján történt egy kis számú előre meghatározott típusú eseményekről, például a kérések, kivételek, függőségi hívások, oldalmegtekintéseket, stb. A fejlesztők az SDK használatával vagy manuálisan (, ha a kódot, amely explicit módon hívja meg az SDK-t) gridre bocsáthatja ki ezeket az eseményeket, vagy automatikus – rendszerállapot-események az automatikus gyűjtését is támaszkodnak. Mindkét esetben az Application Insights-háttérrendszer tárolja az összes összegyűjtött események naplókat, és az Azure Portalon az Application Insights paneleken szerepét egy elemzési és diagnosztikai eszköz használatával megjelenítheti az eseményalapú adatokat a naplókból.

Események teljes körét megőrizni a naplók segítségével gyűjtheti az elemzési és diagnosztikai származzon. Például egy pontosan egy adott URL-kérelmek száma lekérheti azokat a hívásokat a különböző felhasználók számát. Megjelenik a részletes diagnosztikai nyomkövetéseket, kivételeket is beleértve, illetve függőségi hívás minden felhasználói munkamenetet. Az ilyen információkat kellene jelentősen javíthatja az alkalmazás állapotáról és a használat, lehetővé téve az alkalmazással kapcsolatos problémák diagnosztizálásához szükséges idejét kivágása. 

Egy időben, események teljes készletének összegyűjtése pedig praktikus (vagy még nem lehetséges) alkalmazásokhoz, amelyek sok telemetriát generálnak. Olyan esetekben, amikor események értéke túl magas, az Application Insights valósít meg több telemetrikus kötet megoldásokat, például [mintavételi](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) és [szűrés](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) , amely a számának csökkentése az összegyűjtött és tárolt események. Sajnos azonban csökkenti a tárolt események száma is csökkenti a pontosságát a metrikák, a színfalak mögött a lekérdezési idő összesítések naplókat tárolja az eseményeket kell végrehajtania.

> [!NOTE]
> Az Application insights szolgáltatásban a metrikák, a lekérdezés-idő összesítése események és naplók tárolt mérések alapuló log mérőszámok nevezik. Ezeket a metrikákat általában kell az esemény tulajdonságai, így minden felső szintű Analytics, a számos dimenziók, de ezeket a metrikákat pontosságát mintavételezés és szűrés negatív hatással.

## <a name="pre-aggregated-metrics"></a>Előre összesített mérőszámok

Napló alapú metrikák mellett Fall 2018, az Application Insights csapata szállítva nyilvános előzetes verziója a time series-hoz optimalizált speciális adattárban tárolt metrikák. Az új mérőszámok már nem őrzi meg a Tulajdonságok sok egyes eseménynek számít. Ehelyett azok tárolódnak, előre összesített idősorozat, és csak a kulcsfontosságú dimenziókat. Ez lehetővé teszi az új mérőszámok kiváló lekérdezéskor: adatok beolvasása közben történik, sokkal gyorsabban és kevesebb számítási teljesítményt igényel. Ennek következtében ezáltal új forgatókönyvek például [közel valós idejű riasztások a metrikák dimenziókon történő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts), dinamikusabb [irányítópultok](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards), stb.

> [!IMPORTANT]
> Az Application Insights, log-alapú mind előre összesített mérőszámok szerepelhet egyszerre. Az Application Insights UX az előre összesített mérőszámok most nevezzük "Standard metrikák (előzetes verzió)", míg az eseményeket a hagyományos metrikáit is nevet kapott: "Log-alapú metrikák" a kettő megkülönböztetéséhez.

Az új SDK-k ([Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK vagy újabb verziója a .NET-hez) előre összesített metrikái gyűjteményben, mielőtt telemetrikus kötet megoldásokat során jelentkezik. Ez azt jelenti, hogy az új mérőszámok pontosságát mintavételezés és szűrés a legújabb Application Insights SDK-k használata esetén nem érinti.

Az SDK-k előzetes összesítéssel (vagyis a régebbi verziójú Application Insights SDK-k vagy a böngésző Instrumentation) ne alkalmazza a az Application Insights-háttérrendszer továbbra is tölti fel az új mérőszámok az alkalmazás által fogadott események összesítésével Insights esemény végpont. Ez azt jelenti, hogy közben nem előnyökkel jár a csökkentett a hálózaton keresztül továbbított adatok mennyisége, továbbra is használhatja az előre összesített mérőszámok és jobb teljesítményt és támogatás, a közel valós idejű dimenzionális riasztási az SDK-kat nem előre összesített metrikái gyűjtés során.

Érdemes megemlíteni, hogy a végpont előre összesíti az események betöltési mintavételt, ami azt jelenti, hogy mielőtt [betöltési mintavételt](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) soha nem lesz hatással előre összesített mérőszámok, az SDK-verziótól függetlenül pontosságát, az alkalmazással használhat.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Előzetes összesítéssel használata egyéni metrikákat az Application Insights

Egyéni metrikák előzetes összesítéssel használhat. A két fő előnyök konfigurálása és a riasztás egy dimenzió egy egyéni metrika és az Application Insights végpont az SDK által küldött adatok mennyisége csökkenti a rendszer.

Több [módszerekkel egyéni metrikákat küldését az Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Ha az SDK-t az Ön kínál a [GetMetric és TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) módszerek, ez az előnyben részesített módja a küldő egyéni metrikákat, mivel ebben az esetben előzetes összesítéssel történik, az SDK-t, nem csak a tárolt adatok mennyisége csökkenti belül Az Azure, de még az Application Insights SDK továbbított adatok mennyisége. Ellenkező esetben a [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) metódussal, amely előre metrika események összesítése az adatbetöltés során.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Egyéni metrikák dimenziókat és előzetes összesítéssel

Minden metrikák használatával elküldött [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) vagy [GetMetric és TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) API-hívások egyben a tárolt naplókat és metrikákat is. Azonban amíg a napló-alapú verzióját az egyéni metrika mindig megőrzi minden, a metrika előzetes összesítésére verziója tárolt nincs dimenzió alapértelmezés szerint. Egyéni metrikák méretei gyűjteményében bekapcsolhatja a a [használat és becsült költségek](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) ellenőrzésével "Egyéni metrika dimenziók számlálóihoz engedélyezése" lapon: 

![Használat és becsült költségek](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Miért érdemes az egyéni metrikákat dimenziók gyűjtemény ki van kapcsolva alapértelmezés szerint?

Egyéni metrikák dimenziók a gyűjtemény ki van kapcsolva alapértelmezés szerint, mert a jövőben tárolásához egyéni dimenziókkal rendelkező metrikák külön lesznek számlázva az Application Insightsból, amíg a dimenziók nélküli egyéni metrikákat tárolására (akár egy kvótát) ingyenes lesz . Megismerheti a közelgő díjszabási modell változások a hivatalos [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Diagramok létrehozása és a napló- és a standard előre összesített metrikák felfedezése

Az Azure Monitor Metrikaböngészőjének használatával diagramokat előzetes összesítésére és a napló-alapú metrikákat, és a szerző irányítópultok diagramokkal. Miután kiválasztotta a kívánt Application Insights-erőforrást, a névtér-választó használatával Váltás normál (előzetes verzió) és a napló mérőszámok, vagy válasszon egy egyéni metrika névteret:

![Metrika névtér](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>További lépések

* [Közel valós idejű riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric és TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)

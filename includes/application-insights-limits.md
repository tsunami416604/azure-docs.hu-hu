Bizonyos korlátozások érvényesek a mérőszámok számára és az alkalmazásonkénti eseményekre (kialakítási kulcsonként értendő). A korlátozások a választott [díjszabási csomagtól](https://azure.microsoft.com/pricing/details/application-insights/) függően változnak.

| Erőforrás | Alapértelmezett korlát | Megjegyzés
| --- | --- | --- |
| Napi teljes adatmennyiség | 100 GB | Egy korlát beállításával csökkenthető az adatmennyiség. Ha további adatokra van szükségük, a portálon, a korlát növeléséhez-e legfeljebb 1000 GB. Üzenet küldése a 1000 GB-nál nagyobb kapacitás, AIDataCap@microsoft.com.
| Havi ingyenes adatmennyiség<br/> (Alapvető tarifacsomagot) | 1 GB | A további adatok számlázása GB-onként történik.
| Throttling | 32 KB-os esemény/másodperc | A korlát megállapítása egy percnyi mérés alapján történik.
| Adatmegőrzés | 90 nap | Ez az erőforrás a [Keresés](../articles/application-insights/app-insights-diagnostic-search.md), [Elemzés](../articles/application-insights/app-insights-analytics.md) és [Metrikaböngésző](../articles/application-insights/app-insights-metrics-explorer.md) esetén használható.
| [Többlépéses rendelkezésre állási teszt](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) a részletes eredmények megőrzésével | 90 nap | Ez az erőforrás minden lépésről részletes eredményeket biztosít.
| A maximális eseményméret | 64 K | 
| Tulajdonság- és metrikanév hossza | 150 | Lásd: [írja be a sémák](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Tulajdonságérték-karakterlánc hossza | 8,192 | Lásd: [írja be a sémák](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Nyomkövetési és kivételüzenet hossza | 10E | Lásd: [írja be a sémák](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| [Rendelkezésre állási tesztek](../articles/application-insights/app-insights-monitor-web-app-availability.md) száma alkalmazásonként | 100 |
| [Profilkészítő](../articles/application-insights/app-insights-profiler.md) adatok megőrzése | 5 nap |
| [Profilkészítő](../articles/application-insights/app-insights-profiler.md) naponta küldött adatok | 10 GB |

További információt [az Application Insights árait és kvótáit](../articles/application-insights/app-insights-pricing.md) ismertető cikkben talál.


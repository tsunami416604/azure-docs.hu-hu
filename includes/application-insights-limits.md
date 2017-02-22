Bizonyos korlátozások érvényesek a mérőszámok számára és az alkalmazásonkénti eseményekre (kialakítási kulcsonként értendő). A korlátozások a választott [díjszabási csomagtól](https://azure.microsoft.com/pricing/details/application-insights/) függően változnak.

| **Erőforrás** | **Alapértelmezett korlát** | **Megjegyzés**
| --- | --- | --- |
| Napi teljes adatmennyiség | 500 GB | Egy korlát beállításával csökkenthető az adatmennyiség. Ha többre van szüksége, írjon a következő címre: AIDataCap@microsoft.com
| Havi ingyenes adatmennyiség<br/> (Alapszintű árképzési csomag) | 1 GB | A további adatok számlázása GB-onként történik.
| Szabályozás | 32 ezer esemény/másodperc | A korlát megállapítása egy percnyi mérés alapján történik.
| Adatmegőrzés | 90 nap | Ez az erőforrás a [Keresés](../articles/application-insights/app-insights-diagnostic-search.md), [Elemzés](../articles/application-insights/app-insights-analytics.md) és [Metrikaböngésző](../articles/application-insights/app-insights-metrics-explorer.md) esetén használható.
| [Többlépéses rendelkezésre állási teszt](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) a részletes eredmények megőrzésével | 90 nap | Ez az erőforrás minden lépésről részletes eredményeket biztosít.
| Tulajdonság- és metrikanév hossza | 150 |
| Tulajdonságérték-karakterlánc hossza | 8,192 |
| Nyomkövetési és kivételüzenet hossza | 10 ezer |
| [Rendelkezésre állási tesztek](../articles/application-insights/app-insights-monitor-web-app-availability.md) száma alkalmazásonként  | 10 |

További információt [az Application Insights árait és kvótáit](../articles/application-insights/app-insights-pricing.md) ismertető cikkben talál.


<!--HONumber=Feb17_HO2-->



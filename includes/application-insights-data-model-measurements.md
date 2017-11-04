Egyéni mérési adatok gyűjtése. A telemetriai adatok elemhez tartozó mérési nevű jelentés a gyűjtemény használható. A tipikus használati esetek a következők:
- – Függőségi Telemetria tartalom mérete
- a Telemetriai kérelem által feldolgozott várólista elemek száma.
- idő tartozó ügyfél varázsló lépés befejezése esemény telemetriai adatokat a lépés elvégzéséhez.

Lekérheti [egyéni mértékek](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) alkalmazás Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Egyéni mértékek társítva a telemetriai adatok elem tartoznak. A fenti mérések tartalmazó telemetriai elem vannak mintavételi vonatkoznak. Egy mérték, amely független a más telemetriai értéke követheti nyomon, használjon [metrika telemetriai](../articles/application-insights/app-insights-api-custom-events-metrics.md).

Maximális kulcshossz: 150

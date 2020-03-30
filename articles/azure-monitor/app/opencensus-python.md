---
title: Python-alkalmazások figyelése az Azure Monitorsegítségével (előzetes verzió) | Microsoft dokumentumok
description: Utasításokat tartalmaz az OpenCensus Python és az Azure Monitor vezetékezésére
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537108"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Az Azure Monitor beállítása a Python-alkalmazáshoz

Az Azure Monitor támogatja a Python-alkalmazások elosztott nyomkövetését, metrikus gyűjtését és naplózását az [OpenCensus](https://opencensus.io)szolgáltatással való integráció révén. Ez a cikk végigvezeti az OpenCensus python-hoz beállításának folyamatán, és elküldi a figyelési adatokat az Azure Monitornak.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
- Python telepítés. Ez a cikk [a Python 3.7.0-s](https://www.python.org/downloads/)verzióját használja, bár a korábbi verziók valószínűleg kisebb módosításokkal fognak működni.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Application Insights-erőforrás létrehozása az Azure Monitorban

Először létre kell hoznia egy Application Insights-erőforrást az Azure Monitorban, amely létrehoz egy instrumentation kulcsot (ikey). Az ikey ezután konfigurálja az OpenCensus SDK telemetriai adatokat az Azure Monitor.

1. Válassza az > **Erőforrás-fejlesztői eszközök** >  **létrehozása****Alkalmazáselemzési**lehetőséget.

   ![Application Insights-erőforrás hozzáadása](./media/opencensus-python/0001-create-resource.png)

1. Megjelenik egy konfigurációs mező. Az alábbi táblázat segítségével töltse ki a beviteli mezőket.

   | Beállítás        | Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Név**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Erőforráscsoport**     | myResourceGroup      | Az Application Insights-adatok üzemeltetéséhez az új erőforráscsoport neve |
   | **Helyen** | USA keleti régiója | Az Ön közelében vagy az alkalmazás üzemeltetésének helyéhez közeli hely |

1. Kattintson a **Létrehozás** gombra.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Eszköz OpenCensus Python SDK for Azure Monitor

Telepítse az OpenCensus Azure Monitor exportőreit:

```console
python -m pip install opencensus-ext-azure
```

A csomagok és integrációk teljes listáját az [OpenCensus csomagok](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)ban található.

> [!NOTE]
> A `python -m pip install opencensus-ext-azure` parancs feltételezi, hogy `PATH` a Python-telepítéshez beállított egy környezeti változó. Ha még nem konfigurálta ezt a változót, meg kell adnia a teljes könyvtárelérési utat, ahol a Python végrehajtható fájl található. Az eredmény egy parancs, `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`mint ez: .

Az SDK három Azure Monitor-exportőrt használ a különböző típusú telemetriai adatok küldéséhez az Azure Monitornak: nyomkövetés, metrikák és naplók. Ezekről a telemetriai típusokról az [adatplatform áttekintése](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)című témakörben olvashat bővebben. Az alábbi utasításokat, hogy küldje el ezeket a telemetriai típusok a három exportőr.

## <a name="telemetry-type-mappings"></a>Telemetriai típus-hozzárendelések

Az opencensus által biztosított exportőrök az Azure Monitorban látható telemetriai adatok típusaira vannak leképezve.

![Képernyőkép: telemetriai típusok leképezése az OpenCensus szolgáltatásból az Azure Monitorba](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Nyomkövetés

> [!NOTE]
> `Trace`az OpenCensus részben [az elosztott nyomkövetésre](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)utal. A `AzureExporter` `requests` küldés és `dependency` a telemetriai adatok az Azure Monitorba.

1. Először is, hozzon létre néhány nyomkövetési adatokat helyben. A Python IDLE,vagy a szerkesztő a választás, írja be a következő kódot.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. A kód futtatása ismételten kéri egy érték megadását. Minden bejegyzésnél az érték a rendszerhéjra lesz nyomtatva, és az `SpanData`OpenCensus Python modul létrehoz egy megfelelő darabot. Az OpenCensus projekt a [nyomkövetést ívfaként](https://opencensus.io/core-concepts/tracing/)határozza meg.
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. Bár az értékek megadása bemutató célokra hasznos, végső `SpanData` soron azt szeretnénk, hogy az Azure Monitor. Adja át a kapcsolati karakterláncot közvetlenül az exportőrnek, vagy megadhatja azt egy környezeti változóban. `APPLICATIONINSIGHTS_CONNECTION_STRING` Módosítsa a kódot az előző lépésből a következő kódminta alapján:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. Most, amikor futtatja a Python-szkriptet, továbbra is meg kell kérnie az értékek bevitelét, de csak az érték van nyomtatva a rendszerhéjban. A `SpanData` létrehozott lesz elküldve az Azure Monitor. A kibocsátott átnyúló adatokat `dependencies`a csoportban találja. A kimenő kérelmekkel kapcsolatos további részleteket az OpenCensus [Python-függőségek](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency).for more details on going going requests, see OpenCensus Python dependencies .
A bejövő kérelmekkel kapcsolatos további részletekért olvassa el az OpenCensus [Python-kérelmek et.](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)

#### <a name="sampling"></a>Mintavételezés

Az OpenCensus című részben a [mintavételezésről](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)az OpenCensus alkalmazásban talál információt.

#### <a name="trace-correlation"></a>Nyomkövetési korreláció

A nyomkövetési adatok telemetriai korrelációjának részleteit az OpenCensus Python [telemetriai korrelációja.](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)

#### <a name="modify-telemetry"></a>Telemetria módosítása

A nyomon követett telemetriai adatok azure-figyelőbe való elküldése előtti módosításáról az OpenCensus Python [telemetriai processzorok](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)című témakörben talál részleteket.

### <a name="metrics"></a>Mérőszámok

1. Először hozzon létre néhány helyi metrikaadatokat. Létrehozunk egy egyszerű mérőszámot, amely nyomon követi, hogy a felhasználó hányszor nyomja meg az Entert.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. A kód futtatása ismételten az Enter billentyű lenyomására kéri. Létrejön egy mérőszám, amely nyomon követi, hogy hányszor nyomták meg az Enter billentyűt. Minden bejegyzésnél az érték növekszik, és a metrikaadatok megjelennek a konzolon. Az információ tartalmazza az aktuális értéket és az aktuális időbélyegzőt a metrika frissítésekor.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Bár az értékek megadása bemutató célokra hasznos, végső soron szeretnénk kikérni a metrikaadatokat az Azure Monitorba. Adja át a kapcsolati karakterláncot közvetlenül az exportőrnek, vagy megadhatja azt egy környezeti változóban. `APPLICATIONINSIGHTS_CONNECTION_STRING` Módosítsa a kódot az előző lépésből a következő kódminta alapján:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. Az exportőr meghatározott időközönként elküldi a metrikaadatokat az Azure Monitornak. Az alapértelmezett érték 15 másodpercenként. Egyetlen mutatót követünk nyomon, így ez a metrikaadatok, bármilyen értéket és időbélyegzőt tartalmaznak, minden időközönként elküldésre kerülnek. Az adatokat a `customMetrics`csoportban találja.

#### <a name="standard-metrics"></a>Szabványos mérőszámok

Alapértelmezés szerint a metrikák exportőr e szabványos metrikák az Azure Monitor. Ezt letilthatja, `enable_standard_metrics` ha `False` a jelzőt a metrikaexportőr konstruktorában állítja be.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
Az alábbi lista a jelenleg elküldött szabványos mutatókat tartalmazza:

- Rendelkezésre álló memória (bájt)
- PROCESSZORProcesszor-idő (százalék)
- Bejövő kérelmek aránya (másodpercenként)
- Bejövő kérelem átlagos végrehajtási ideje (ezredmásodperc)
- Kimenő kérelmek aránya (másodpercenként)
- Processzorhasználat feldolgozása (százalék)
- Magánbájtok (bájt) feldolgozása

Ezeket a mutatókat a `performanceCounters`ban kell látnia. A bejövő kérelmek aránya `customMetrics`a alatt lenne. További információt a [teljesítményszámlálók című témakörben talál.](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters)

#### <a name="modify-telemetry"></a>Telemetria módosítása

A nyomon követett telemetriai adatok azure-figyelőbe való elküldése előtti módosításáról az OpenCensus Python [telemetriai processzorok](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)című témakörben talál részleteket.

### <a name="logs"></a>Naplók

1. Először hozzon létre néhány helyi naplóadatokat.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  A kód folyamatosan kéri egy érték beírását. A rendszer naplóbejegyzést bocsát ki minden beírt értékhez.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Bár az értékek megadása demonstrációs célokra hasznos, végső soron szeretnénk kikérni a naplóadatokat az Azure Monitorba. Adja át a kapcsolati karakterláncot közvetlenül az exportőrnek, vagy megadhatja azt egy környezeti változóban. `APPLICATIONINSIGHTS_CONNECTION_STRING` Módosítsa a kódot az előző lépésből a következő kódminta alapján:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. Az exportőr elküldi a naplóadatokat az Azure Monitornak. Az adatokat a `traces`csoportban találja. 

> [!NOTE]
> `traces`ebben az összefüggésben nem `Tracing`ugyanaz, mint . `traces`a telemetriai adatok típusára utal, amelyet az Azure `AzureLogHandler`Monitorban fog látni a használatával. `Tracing`az OpenCensus egyik fogalomra utal, és [az elosztott nyomkövetésre](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)vonatkozik.

5. A naplóüzenetek formázásához használhatja `formatters` a beépített Python [naplózási API-t.](https://docs.python.org/3/library/logging.html#formatter-objects)

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. A custom_dimensions mezővel egyéni tulajdonságokat is hozzáadhat a naplóüzenetekhez az *extra* kulcsszóargumentumban. Ezek kulcs-érték párokként jelennek meg az Azure Monitorban. `customDimensions`
> [!NOTE]
> Ahhoz, hogy ez a funkció működjön, át kell adnia egy szótárat a custom_dimensions mezőben. Ha bármilyen más típusú argumentumot ad át, a naplózó figyelmen kívül hagyja őket.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="sending-exceptions"></a>Kivételek küldése

OpenCensus Python nem automatikusan `exception` nyomon követi és elküldi a telemetriai adatokat. A python naplózási könyvtáron keresztül kivételekkel keresztül küldi őket `AzureLogHandler` a rendszer. A normál naplózáshoz hasonlóan egyéni tulajdonságokat is hozzáadhat.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in exception logs
    try:
        result = 1 / 0  # generate a ZeroDivisionError
    except Exception:
        logger.exception('Captured an exception.', extra=properties)
    ```
Mivel a kivételeket explicit módon kell naplóznia, a felhasználónak kell bejelentkeznie a nem kezelt kivételekben. OpenCensus nem korlátozza, hogy a felhasználó kívánja ezt, mindaddig, amíg azok kifejezetten naplót egy kivétel telemetriai adatokat.

#### <a name="sampling"></a>Mintavételezés

Az OpenCensus című részben a [mintavételezésről](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)az OpenCensus alkalmazásban talál információt.

#### <a name="log-correlation"></a>Napló-összefüggések

A naplók nyomkövetési környezeti adatokkal való gazdagításáról az OpenCensus Python [naplók integrációjában](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)talál további információt.

#### <a name="modify-telemetry"></a>Telemetria módosítása

A nyomon követett telemetriai adatok azure-figyelőbe való elküldése előtti módosításáról az OpenCensus Python [telemetriai processzorok](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)című témakörben talál részleteket.

## <a name="view-your-data-with-queries"></a>Az adatok megtekintése lekérdezésekkel

Megtekintheti az alkalmazásból küldött telemetriai adatokat a **Naplók (Analytics)** lapon keresztül.

![Képernyőkép az áttekintő ablaktábláról, amelyen a "Naplók (Analytics)" beállítás van kijelölve a piros mezőben](./media/opencensus-python/0010-logs-query.png)

Az Aktív csoportban található az **:**

- Az Azure Monitor nyomkövetési exportőrével küldött telemetriai adatok esetén a bejövő kérelmek a területen `requests`jelennek meg. A kimenő vagy folyamaton `dependencies`ként i kérelmek a területen jelennek meg.
- Az Azure Monitor metrikák exportőrével küldött telemetriai adatok esetében az elküldött metrikák a területen `customMetrics`jelennek meg.
- Az Azure Monitor naplóinak exportőrével küldött telemetriai adatok esetén a naplók a területen `traces`jelennek meg. A kivételek `exceptions`a alatt jelennek meg.

A lekérdezések és naplók használatáról a [Naplók az Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)című témakörben talál további információt.

## <a name="learn-more-about-opencensus-for-python"></a>További információ az OpenCensus for Pythonról

* [OpenCensus Python a GitHubon](https://github.com/census-instrumentation/opencensus-python)
* [Testreszabás](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor-exportőrök a GitHubon](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus integrációk](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Figyelő mintaalkalmazásai](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>További lépések

* [Bejövő kérések nyomon követése](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Kimenő kérelmek nyomon követése](./../../azure-monitor/app/opencensus-python-request.md)
* [Alkalmazástérkép](./../../azure-monitor/app/app-map.md)
* [Végpontok között a teljesítmény figyelése](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Riasztások

* [Rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md): Hozzon létre teszteket, hogy megbizonyosodjon róla, oldala látható a weben.
* [Intelligens diagnosztika](../../azure-monitor/app/proactive-diagnostics.md): Ezek a tesztek automatikusan futnak, a beállításukhoz semmit sem kell tennie. Értesítést kap, ha az alkalmazásában szokatlanul magas a meghiúsult kérelmek száma.
* [Metrikariasztások:](../../azure-monitor/app/alerts.md)Állítson be riasztásokat, amelyek figyelmeztetik, ha egy metrika átlép egy küszöbértéket. Az alkalmazás kódjába beépített egyedi metrikákhoz is állíthat be riasztásokat.

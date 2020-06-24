---
title: Python-alkalmazások figyelése Azure Monitorokkal (előzetes verzió) | Microsoft Docs
description: Útmutatást nyújt a OpenCensus Python Azure Monitor
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.custom: tracking-python
ms.openlocfilehash: 04581826ab6b05333e910a162c7a0ca9566ec334
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85079122"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Azure Monitor beállítása a Python-alkalmazáshoz

Azure Monitor támogatja a Python-alkalmazások elosztott nyomkövetését, metrika-gyűjtését és naplózását a [OpenCensus](https://opencensus.io)-vel való integráción keresztül. Ez a cikk végigvezeti a Python-OpenCensus beállításának folyamatán, és a figyelési adatok küldésének Azure Monitor.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
- Python-telepítés. Ez a cikk a [Python 3.7.0](https://www.python.org/downloads/)használja, bár a korábbi verziók valószínűleg kisebb módosításokkal fognak működni.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Application Insights erőforrás létrehozása Azure Monitor

Először létre kell hoznia egy Application Insights erőforrást a Azure Monitorban, amely létrehoz egy kialakítási kulcsot (rendszerállapotkulcsot). A rendszerállapotkulcsot ezt követően a OpenCensus SDK konfigurálására szolgál a telemetria-adatküldés Azure Monitor.

1. Válassza **az erőforrás létrehozása**  >  **fejlesztői eszközök**  >  **Application Insights**elemet.

   ![Application Insights erőforrás hozzáadása](./media/opencensus-python/0001-create-resource.png)

1. Megjelenik egy konfigurációs ablak. A beviteli mezők kitöltéséhez használja az alábbi táblázatot.

   | Beállítás        | Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Name (Név)**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Erőforráscsoport**     | myResourceGroup      | Az új erőforráscsoport neve Application Insights-adattároláshoz |
   | **Hely** | USA keleti régiója | Az Ön közelében található hely, vagy a közelében, ahol az alkalmazás üzemeltetve van |

1. Kattintson a **Létrehozás** gombra.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Eszköz a OpenCensus Python SDK-val Azure Monitor

Telepítse a OpenCensus Azure Monitor-exportőröket:

```console
python -m pip install opencensus-ext-azure
```

A csomagok és integrációk teljes listájáért lásd: [OpenCensus-csomagok](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> A `python -m pip install opencensus-ext-azure` parancs feltételezi, hogy rendelkezik a `PATH` Python-telepítéshez beállított környezeti változóval. Ha még nem konfigurálta ezt a változót, meg kell adnia a teljes könyvtár elérési útját, ahol a Python-végrehajtható fájl található. Az eredmény az alábbihoz hasonló parancs: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure` .

Az SDK három Azure Monitor-exportálót használ különböző típusú telemetria küldéséhez Azure Monitor: nyomkövetés, metrikák és naplók. További információ ezekről a telemetria-típusokról: [az adatplatform áttekintése](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Az alábbi útmutatást követve elküldheti ezeket a telemetria-típusokat a három-exportőr használatával.

## <a name="telemetry-type-mappings"></a>Telemetria típusú leképezések

Itt láthatók azok az exportőrök, amelyeket a OpenCensus biztosít a Azure Monitorban megjelenő telemetria-típusokhoz.

![Képernyőkép a telemetria-típusok hozzárendeléséről a OpenCensus-ből Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Nyomkövetés

> [!NOTE]
> `Trace`a OpenCensus az [elosztott nyomkövetésre](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)hivatkozik. A `AzureExporter` Küldés `requests` és a `dependency` telemetria Azure monitor.

1. Először is hozzon egy nyomkövetési adat helyi előállítását. A Python inaktív vagy a választott szerkesztőben adja meg a következő kódot.

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

2. A kód futtatása többször is felszólítja, hogy adjon meg egy értéket. Az egyes bejegyzések esetében az érték a rendszerhéjra lesz kinyomtatva, és a OpenCensus Python-modul létrehozza a megfelelő darabot `SpanData` . A OpenCensus-projekt egy [nyomkövetési struktúrát](https://opencensus.io/core-concepts/tracing/)határoz meg.
    
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

3. Bár az értékek beírása a bemutató céljára hasznos, végső soron azt szeretnénk kibocsátani a `SpanData` Azure monitor. Adja át a hozzáférési karakterláncot közvetlenül az exportőrnek, vagy megadhatja egy környezeti változóban `APPLICATIONINSIGHTS_CONNECTION_STRING` . Módosítsa a kódot az előző lépésből a következő kód minta alapján:

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

4. A Python-szkript futtatásakor a rendszer továbbra is kéri az értékek megadását, de csak az érték lesz kinyomtatva a rendszerhéjban. A `SpanData` rendszer elküldi a létrehozott Azure monitor. A kibocsátott span-adatmennyiségek a alatt találhatók `dependencies` . A kimenő kérelmekkel kapcsolatos további információkért lásd: OpenCensus Python- [függőségek](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency).
További információ a bejövő kérésekről: OpenCensus Python- [kérelmek](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request).

#### <a name="sampling"></a>Mintavételezés

A OpenCensus-mintavételezéssel kapcsolatos információkért tekintse meg a [mintavételezést a OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Nyomkövetési korreláció

A nyomkövetési adatok telemetria kapcsolatos részletekért tekintse meg a OpenCensus Python [telemetria korrelációját](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python).

#### <a name="modify-telemetry"></a>Telemetria módosítása

A nyomon követett telemetria Azure Monitorba való elküldése előtt történő módosításával kapcsolatos részletekért lásd: OpenCensus Python [telemetria processzorok](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors).

### <a name="metrics"></a>Mérőszámok

1. Először hozzon elő néhány helyi metrikai adatokat. Egy egyszerű mérőszámot hozunk létre, amely nyomon követheti, hogy a felhasználó hányszor nyomja meg az ENTER billentyűt.

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
2. A kód futtatása többször is felszólítja, hogy nyomja meg az ENTER billentyűt. A rendszer mérőszámot hoz létre a megadott számú idő megnyomásának nyomon követésére. Az egyes bejegyzéseknél az érték megnő, és a metrika adatai megjelennek a konzolon. Az információ tartalmazza az aktuális értéket és a jelenlegi időbélyeget, amikor a metrika frissült.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Bár az értékek beírása a bemutató céljára hasznos, végső soron azt szeretnénk, hogy a metrikus adatokat Azure Monitor. Adja át a hozzáférési karakterláncot közvetlenül az exportőrnek, vagy megadhatja egy környezeti változóban `APPLICATIONINSIGHTS_CONNECTION_STRING` . Módosítsa a kódot az előző lépésből a következő kód minta alapján:

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

4. Az exportőr a metrikus adatokat a Azure Monitor rögzített időközönként küldi el. Az alapértelmezett érték 15 másodpercenként. Egyetlen mérőszámot követünk nyomon, ezért a metrikai adatok minden, a benne foglalt értékkel és időbélyegzővel elküldve lesznek minden intervallumban. Az adat a alatt található `customMetrics` .

#### <a name="standard-metrics"></a>Standard mérőszámok

Alapértelmezés szerint a metrikák exportőre szabványos mérőszámok egy készletét küldi Azure Monitorra. Ezt úgy tilthatja le, ha a `enable_standard_metrics` jelölőt a `False` metrika-exportőr konstruktorában állítja be.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```
Az alábbi lista a jelenleg eljuttatott szabványos metrikákat tartalmazza:

- Rendelkezésre álló memória (bájt)
- CPU-processzoridő (százalék)
- Bejövő kérelmek aránya (másodpercenként)
- Bejövő kérelem átlagos végrehajtási ideje (ezredmásodperc)
- Kimenő kérelmek aránya (másodpercenként)
- CPU-használat feldolgozása (százalék)
- Folyamat saját bájtjai (bájt)

Ezeket a mérőszámokat a alkalmazásban tekintheti meg `performanceCounters` . A bejövő kérelmek aránya a következő lesz: `customMetrics` . [További információ: teljesítményszámlálók](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters).

#### <a name="modify-telemetry"></a>Telemetria módosítása

A nyomon követett telemetria Azure Monitorba való elküldése előtt történő módosításával kapcsolatos részletekért lásd: OpenCensus Python [telemetria processzorok](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors).

### <a name="logs"></a>Naplók

1. Először hozzon elő néhány helyi naplózási adatszolgáltatást.

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

2.  A kód folyamatosan kérni fogja a beírni kívánt értéket. Minden megadott értékhez naplóbejegyzés van kibocsátva.

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

3. Bár az értékek beírása a bemutató céljára hasznos, végső soron azt szeretnénk, hogy a napló adatait Azure Monitor. Adja át a hozzáférési karakterláncot közvetlenül az exportőrnek, vagy megadhatja egy környezeti változóban `APPLICATIONINSIGHTS_CONNECTION_STRING` . Módosítsa a kódot az előző lépésből a következő kód minta alapján:

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

4. Az exportőr a Azure Monitorba küldi a naplófájlokat. Az adat a alatt található `traces` . 

    > [!NOTE]
    > `traces`Ebben a kontextusban nem ugyanaz, mint a `Tracing` . `traces`arra a telemetria-típusra hivatkozik, amelyet a Azure Monitor fog látni, amikor a-t használja `AzureLogHandler` . `Tracing`egy OpenCensus-koncepcióra hivatkozik, és az [elosztott nyomkövetésre](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)vonatkozik.

    > [!NOTE]
    > A gyökérszintű naplózó a szint FIGYELMEZTETÉSével van konfigurálva. Ez azt jelenti, hogy minden olyan naplót figyelmen kívül hagy a rendszer, amelyet a súlyosságnál kisebb értékkel küld, és a rendszer nem küldi el a Azure Monitor. További részletekért tekintse meg ezt a [dokumentációt](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel) .

5. Az *extra* kulcsszó argumentumban egyéni tulajdonságokat is hozzáadhat a naplóüzenetek számára a custom_dimensions mező használatával. Ezek kulcs-érték párokként fognak megjelenni a `customDimensions` Azure monitorban.
    > [!NOTE]
    > Ahhoz, hogy ez a funkció működjön, át kell adnia egy szótárt a custom_dimensions mezőbe. Ha más típusú argumentumokat ad át, a naplózó figyelmen kívül hagyja őket.

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

#### <a name="configure-logging-for-django-applications"></a>Django-alkalmazások naplózásának konfigurálása

Az alkalmazás kódjában explicit módon konfigurálhatja a naplózást a Django-alkalmazásokhoz, vagy megadhatja azt a Django naplózási konfigurációjában. Ez a kód bármely, a Django-beállítások konfigurálásához használt fájlt beléphet. A naplózás konfigurálásával kapcsolatos további információkért lásd: [Django](https://docs.djangoproject.com/en/3.0/topics/settings/) -beállítások a Django beállításainak és a [Django naplózásának](https://docs.djangoproject.com/en/3.0/topics/logging/) konfigurálásához.

    ```python
    LOGGING = {
        "handlers": {
            "azure": {
                "level": "DEBUG",
                "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
                "instrumentation_key": "<your-ikey-here>",
            },
            "console": {
                "level": "DEBUG",
                "class": "logging.StreamHandler",
                "stream": sys.stdout,
            },
        },
        "loggers": {
            "logger_name": {"handlers": ["azure", "console"]},
        },
    }
    ```

Ügyeljen arra, hogy a naplózó a konfigurációban megadott névvel megegyező néven legyen használatban.

    ```python
    import logging
        
    logger = logging.getLogger("logger_name")
    logger.warning("this will be tracked")
    ```

#### <a name="sending-exceptions"></a>Küldési kivételek

A OpenCensus Python nem követ automatikusan nyomon és nem küld `exception` telemetria. Ezeket a rendszer a `AzureLogHandler` használatával a Python naplózási könyvtárán keresztül a kivételek használatával továbbítja. A normál naplózáshoz hasonlóan egyéni tulajdonságokat is hozzáadhat.

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
Mivel a kivételeket explicit módon kell naplózni, a felhasználó a nem kezelt kivételek naplózásához. A OpenCensus nem korlátozza azt a korlátozást, hogy a felhasználó hogyan kívánja ezt megtenni, ha a kivételek telemetria explicit módon naplózzák.

#### <a name="sampling"></a>Mintavételezés

A OpenCensus-mintavételezéssel kapcsolatos információkért tekintse meg a [mintavételezést a OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Napló-összefüggések

A naplók nyomkövetési környezeti adatokkal való bővítésével kapcsolatos részletekért lásd: OpenCensus Python- [naplók integrációja](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation).

#### <a name="modify-telemetry"></a>Telemetria módosítása

A nyomon követett telemetria Azure Monitorba való elküldése előtt történő módosításával kapcsolatos részletekért lásd: OpenCensus Python [telemetria processzorok](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors).

## <a name="configure-azure-monitor-exporters"></a>Azure Monitor-exportőrök konfigurálása

Ahogy az a fentiekben is látható, három különböző Azure Monitor-exportőr támogatja a OpenCensus-t, és mindegyik különböző típusú telemetria küld Azure Monitor. Ha szeretné megtekinteni, hogy az egyes exportőrök milyen típusú telemetria küldenek, tekintse meg az alábbi szakaszt.

Mindegyik exportőr elfogadja a konfigurációhoz tartozó argumentumokat, amelyeket a konstruktorok továbbítanak. Az alábbi részleteket láthatja.

1. `connection_string`– A Azure Monitor erőforráshoz való kapcsolódáshoz használt kapcsolati karakterlánc. Elsőbbséget élvez `instrumentation_key` .
2. `enable_standard_metrics`-Használatban van `AzureMetricsExporter` . Azt jelzi, hogy az exportőr automatikusan elküldi a [teljesítményszámláló](https://docs.microsoft.com/azure/azure-monitor/platform/app-insights-metrics#performance-counters) -metrikákat a Azure monitor. Alapértelmezett értéke `True` .
3. `export_interval`-Az Exportálás másodpercben megadott gyakoriságának meghatározására szolgál.
4. `instrumentation_key`– A Azure Monitor erőforráshoz való csatlakozáshoz használt rendszerállapot-kulcs.
5. `logging_sampling_rate`-Használatban van `AzureLogHandler` . Mintavételezési sebességet biztosít [1,0] a naplók exportálásához. Az alapértelmezett érték a 1,0.
6. `max_batch_size`-Az egyszerre exportált telemetria maximális méretét adja meg.
7. `proxies`-Az adatok Azure Monitorba küldéséhez használandó proxyk sorozatát adja meg. További részletekért tekintse meg a [proxykat](https://requests.readthedocs.io/en/master/user/advanced/#proxies) .
8. `storage_path`– A helyi tárolási mappa helyének elérési útja (telemetria elküldve). A `opencensus-ext-azure` v 1.0.3-től kezdve az alapértelmezett elérési út az operációs rendszer TEMP Directory + `opencensus-python`  +  `your-ikey` . Az előre v 1.0.3 esetében az alapértelmezett elérési út $USER + `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Az adataikat a lekérdezésekkel tekintheti meg

A **naplók (Analytics)** lapon megtekintheti az alkalmazásból elküldett telemetria-adatait.

![Képernyőkép az áttekintő panelről, amelyen a "naplók (Analytics)" be van jelölve egy piros mezőben](./media/opencensus-python/0010-logs-query.png)

A listában az **aktív**:

- A Azure Monitor Trace exportőrrel küldött telemetria esetében a bejövő kérelmek a alatt jelennek meg `requests` . A kimenő vagy folyamaton belüli kérelmek megjelennek a alatt `dependencies` .
- A Azure Monitor metrikákkal ellátott telemetria esetében az eljuttatott metrikák a alatt jelennek meg `customMetrics` .
- A Azure Monitor naplók exportőrével eljuttatott telemetria esetében a naplók a alatt jelennek meg `traces` . A kivételek a alatt jelennek meg `exceptions` .

További információ a lekérdezések és naplók használatáról: [naplók a Azure monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>További információ a Pythonhoz készült OpenCensus

* [OpenCensus Python a GitHubon](https://github.com/census-instrumentation/opencensus-python)
* [Testreszabás](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor-exportőrök a GitHubon](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus-integrációk](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor minta alkalmazások](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>További lépések

* [Bejövő kérelmek nyomon követése](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Folyamatban lévő kérelmek nyomon követése](./../../azure-monitor/app/opencensus-python-request.md)
* [Alkalmazás-hozzárendelés](./../../azure-monitor/app/app-map.md)
* [Végpontok közötti teljesítmény figyelése](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Riasztások

* [Rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md): Hozzon létre teszteket, hogy megbizonyosodjon róla, oldala látható a weben.
* [Intelligens diagnosztika](../../azure-monitor/app/proactive-diagnostics.md): Ezek a tesztek automatikusan futnak, a beállításukhoz semmit sem kell tennie. Értesítést kap, ha az alkalmazásában szokatlanul magas a meghiúsult kérelmek száma.
* [Metrikai riasztások](../../azure-monitor/platform/alerts-log.md): riasztások beállítása, amely figyelmezteti, ha egy metrika átlépi a küszöbértéket. Az alkalmazás kódjába beépített egyedi metrikákhoz is állíthat be riasztásokat.

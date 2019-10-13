---
title: Python-alkalmazások figyelése Azure Monitorokkal | Microsoft Docs
description: Útmutatást nyújt a OpenCensus Python Azure Monitor
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 1316cf6808f6ccfc4165ad162c51421638b130be
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293999"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Azure Monitor beállítása a Python-alkalmazáshoz

A Azure Monitor támogatja a Python-alkalmazások elosztott nyomkövetését, metrikus összegyűjtését és naplózását a [OpenCensus](https://opencensus.io)-vel való integráción keresztül. Ez a cikk lépésről lépésre bemutatja, hogyan állíthatja be a OpenCensus for Pythont, és hogyan végezheti el a figyelési adatait Azure Monitor.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell Azure-előfizetéssel.
- A Pythont telepíteni kell, ez a cikk a [Python 3.7.0](https://www.python.org/downloads/)használja, bár a korábbi verziók valószínűleg kisebb módosításokkal fognak működni.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-application-insights-resource-in-azure-monitor"></a>Application Insights erőforrás létrehozása Azure Monitor

Először létre kell hoznia egy Application Insights erőforrást a Azure Monitorban, amely létrehoz egy kialakítási kulcsot (rendszerállapotkulcsot). A rendszerállapotkulcsot ezt követően a OpenCensus SDK konfigurálására szolgál a telemetria-adatküldés Azure Monitor.

1. Válassza **az erőforrás létrehozása** > **fejlesztői eszközök** > **Application Insights**lehetőséget.

   ![Application Insights-erőforrások hozzáadása](./media/opencensus-python/0001-create-resource.png)

   Megjelenik egy konfigurációs mező. Az adatbeviteli mezők kitöltéséhez használja az alábbi táblát.

    | Beállítások        | Value (Díj)           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Name (Név)**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Erőforráscsoport**     | myResourceGroup      | Az App Insights-adatokat futtató új erőforráscsoport neve |
   | **Hely** | USA keleti régiója | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

2. Kattintson a  **Create** (Létrehozás) gombra.

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>A OpenCensus Python SDK-val való Azure Monitor

1. Telepítse a OpenCensus Azure Monitor-exportőröket:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > a `python -m pip install opencensus-ext-azure` feltételezi, hogy rendelkezik a Python-telepítéshez beállított PATH környezeti változóval. Ha nem konfigurálta ezt a beállítást, meg kell adnia a teljes könyvtár elérési útját, ahol a Python-végrehajtható fájl található, amely a következőhöz hasonló parancsot eredményez: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Az SDK három Azure Monitor-exportálót használ különböző típusú telemetria küldéséhez Azure Monitor: nyomkövetés, metrikák és naplók. Tekintse át [az adatplatform áttekintését](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) , ahol további információkat talál a különböző típusokról. Az alábbi útmutatást követve megtudhatja, hogyan küldje el ezeket a különböző típusokat a három exportőr használatával.

### <a name="trace"></a>Nyomkövetés

1. Először is hozzon elő egy nyomkövetési adat helyi létrehozását. A Python inaktív vagy a választott szerkesztőben adja meg a következő kódot.

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

2. A kód futtatása többször is felszólítja, hogy adjon meg egy értéket. Minden egyes bejegyzés esetében az érték a rendszerhéjba lesz kinyomtatva, és a OpenCensus Python-modul létrehozza a megfelelő **SpanData** . A OpenCensus-projekt egy [_nyomkövetési struktúrát_](https://opencensus.io/core-concepts/tracing/)határoz meg.
    
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

3. A bemutató szempontjából hasznosnak bizonyulhat, végső soron a `SpanData` értéket szeretnénk kibocsátani a Azure Monitor. Módosítsa a kódot az előző lépésből a következő kód minta alapján:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
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

4. Most, hogy a Python-szkriptet futtatja, a rendszer továbbra is kéri az értékek megadását, de most csak az érték lesz kinyomtatva a rendszerhéjban. A rendszer a `SpanData` értéket küldi el a Azure Monitornak. A kibocsátott span-adat a `dependencies` alatt található.

### <a name="metrics"></a>Metrikák

1. Először hozzon elő néhány helyi metrikai adatokat. Létre fogunk hozni egy egyszerű mérőszámot, amely nyomon követheti, hogy a felhasználó hányszor nyomja meg az ENTER billentyűt.

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
2. A kód futtatása többször is felszólítja, hogy nyomja meg az ENTER billentyűt. A rendszer mérőszámot hoz létre a megnyomott bevitelek számának nyomon követéséhez. Az egyes bejegyzések esetében az érték megnő, és a metrika adatai megjelennek a konzolon, az aktuális értékkel és az aktuális időbélyeggel, amikor a metrika frissült.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. A bemutató szempontjából hasznosnak bizonyulhat, végső soron a metrikai adatokat Azure Monitorra szeretnénk kibocsátani. Módosítsa a kódot az előző lépésből a következő kód minta alapján:

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
    )
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

4. Az exportőr a metrikus adatokat rögzített időközönként Azure Monitor küldi, az alapértelmezett érték pedig 15 másodpercenként. Egyetlen mérőszámot követünk nyomon, így ez a metrikai adat minden egyes intervallumot és időbélyeget tartalmaz. Az `customMetrics` alatti adat található.

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

2.  A kód folyamatosan kéri a beírni kívánt érték megadását. A rendszer minden olyan értéket naplóz, amely az említett értéket tartalmazza.

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

3. A bemutató szempontjából hasznosnak bizonyulhat, végső soron a metrikai adatokat Azure Monitorra szeretnénk kibocsátani. Módosítsa a kódot az előző lépésből a következő kód minta alapján:

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

4. Az exportőr a Azure Monitorba küldi a naplófájlokat. Az `traces` alatti adat található.

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

1. Most újra megnyithatja a Azure Portal Application Insights **Áttekintés** lapját, ahol megtekintheti az aktuálisan futó alkalmazás részleteit. Válassza az **élő metrika stream**lehetőséget.

   ![Képernyőkép az áttekintő panelről a Vörös mezőben kiválasztott élő metrikai streamtel](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Térjen vissza az **Áttekintés** lapra, és válassza ki az **alkalmazás-hozzárendelést** a függőségi kapcsolatok vizualizációs elrendezéséhez, és hívja meg az időzítést az alkalmazás-összetevők között.

    ![Az alapszintű alkalmazás-hozzárendelés képernyőképe](./media/opencensus-python/0007-application-map.png)

    Mivel csak egyetlen metódust hívunk meg, az alkalmazásunk térképe nem annyira érdekes. Az alkalmazás-hozzárendelés azonban a sokkal több elosztott alkalmazás megjelenítésére is méretezhető:

   ![Alkalmazástérkép](media/opencensus-python/application-map.png)

3. A teljesítmény **vizsgálata** elemre kattintva részletes teljesítmény-elemzést végezhet, és meghatározhatja a lassú teljesítmény okát.

    ![A teljesítmény panel képernyőképe](./media/opencensus-python/0008-performance.png)

4. A **minták** kiválasztásával, majd a jobb oldali panelen megjelenő minták bármelyikére kattintva elindíthatja a végpontok közötti tranzakció részleteinek élményét. Habár a minta alkalmazásunk egyetlen eseményt mutat be, egy összetettebb alkalmazás lehetővé teszi, hogy a végpontok közötti tranzakciót egy adott esemény hívási verem szintjére tárja fel.

     ![Képernyőkép a végpontok közötti tranzakciós kezelőfelületről](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>Az adataikat a lekérdezésekkel tekintheti meg

1. A naplók (Analytics) lapon megtekintheti az alkalmazásból elküldett telemetria-adatait.

    ![Képernyőkép az áttekintő panelről a piros mezőben kiválasztott naplókkal (Analytics)](./media/opencensus-python/0010-logs-query.png)

2. A Azure Monitor nyomkövetési exportőrrel küldött telemetria esetében a bejövő kérések a `requests` és a kimenő/folyamatban lévő kérelmek alatt jelennek meg `dependencies` alatt.

3. A Azure Monitor metrikákkal ellátott telemetria esetében az elküldett metrikák a `customMetrics` alatt jelennek meg.

4. A Azure Monitor naplók exportőrével elküldett telemetria a naplók a `traces` alatt jelennek meg, a kivételek pedig az `exceptions` alatt jelennek meg.

5. A lekérdezések és naplók használatával kapcsolatos részletes információkért tekintse meg a [Azure monitor naplóit](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) .

## <a name="opencensus-for-python"></a>OpenCensus a Pythonhoz

* [OpenCensus Python a GitHubon](https://github.com/census-instrumentation/opencensus-python)
* [Testreszabási](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Lombik-integráció](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django-integráció](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL-integráció](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Következő lépések

* [API-összefoglalás](./../../azure-monitor/app/api-custom-events-metrics.md)
* [Alkalmazás-hozzárendelés](./../../azure-monitor/app/app-map.md)
* [Végpontok közötti teljesítmény figyelése](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Értesítések

* [Rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md): Hozzon létre teszteket, hogy megbizonyosodjon róla, oldala látható a weben.
* [Intelligens diagnosztika](../../azure-monitor/app/proactive-diagnostics.md): Ezek a tesztek automatikusan futnak, a beállításukhoz semmit sem kell tennie. Értesítést kap, ha az alkalmazásában szokatlanul magas a meghiúsult kérelmek száma.
* [Metrikai riasztások](../../azure-monitor/app/alerts.md): riasztások beállítása, amely figyelmezteti, ha egy metrika átlépi a küszöbértéket. Az alkalmazás kódjába beépített egyedi metrikákhoz is állíthat be riasztásokat.
---
title: Az Azure Application Insights Python-alkalmazások monitorozása |} A Microsoft Docs
description: Kapcsolatos utasítások az Application Insights OpenCensus Python beállítása
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541440"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Elosztott nyomkövetések gyűjtéséhez a Python (előzetes verzió)

Az Application Insights most már támogatja az elosztott nyomkövetést Python-alkalmazások integrációja révén [OpenCensus](https://opencensus.io). Ez a cikk részletesen OpenCensus beállítása a Pythonhoz készült, és az Application Insights figyelési adatok első folyamatát.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell Azure-előfizetéssel.
- Python-nek telepítve kell lennie, ez a cikk [Python 3.7.0](https://www.python.org/downloads/), bár a korábbi verzióiban valószínűleg kisebb helyesbítéssel fognak működni.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Először meg kell létrehoznia az Application Insights-erőforrás, amely létrehoz egy kialakítási key(ikey). A rendszerállapotkulcsot az Application Insights telemetriai adatokat küldeni a OpenCensus SDK konfigurálása majd szolgál.

1. Válassza ki **erőforrás létrehozása** > **fejlesztői eszközök** > **Application Insights**.

   ![Application Insights-erőforrások hozzáadása](./media/opencensus-python/0001-create-resource.png)

   Megjelenik egy konfigurációs mező. Az adatbeviteli mezők kitöltéséhez használja az alábbi táblát.

    | Beállítások        | Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Name**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Erőforráscsoport**     | myResourceGroup      | Az App Insights-adatokat futtató új erőforráscsoport neve |
   | **Location** | East US | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

2. Kattintson a **Create** (Létrehozás) gombra.

## <a name="install-opencensus-azure-monitor-exporters"></a>OpenCensus Azure Monitor exportőrök telepítése

1. Telepítse a OpenCensus Azure Monitor exportőrök:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` feltételezi, hogy a PATH környezeti változóhoz, a Python-telepítés beállítása. Ha nincs konfigurálva ez, hogy arra, ahol a Python végrehajtható fájl található amely szeretné eredménye egy, a teljes elérési útja kell: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Először hozzunk a nyomkövetési adatok helyben létrehozásához. Python TÉTLEN, vagy a választott szerkesztővel adja meg a következő kódot:

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

3. A kód futtatása ismételten kérni fogja, adjon meg egy értéket. Minden bejegyzés az értéket nyomtatja a rendszerhéj, és a egy megfelelő részét **SpanData** generál a OpenCensus Python modulban. Meghatározza a OpenCensus projektet egy [ _álló átnyúlások nyomkövetési_](https://opencensus.io/core-concepts/tracing/).
    
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

4. Miközben hasznos bemutatási célokra, végső soron szeretnénk gridre bocsáthatja ki az Application insights SpanData. Módosítsa a kódot, az alábbi kódmintában alapján az előző lépésből:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
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
5. Mostantól Ha futtatja a Python-szkriptet, továbbra is a rendszer kéri, adja meg az értékeket, de most csak az érték nyomtatása történik a rendszerhéjat.

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

1. Most megnyithatja ismét az Application Insights **áttekintése** a jelenleg futó alkalmazás részleteinek megtekintése az Azure Portalon lapot. Válassza ki **élő Metric Stream**.

   ![Az élő metrikai streamet vörös téglalappal jelölve áttekintő panel képernyőképe](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Lépjen vissza a **áttekintése** lapon, és válassza **Alkalmazástérkép** mutató vizuális elrendezés az alkalmazás-összetevők közötti hívások időzítését és a függőségi viszonyok.

    ![Képernyőkép az alapvető alkalmazástérkép](./media/opencensus-python/0007-application-map.png)

    Azt is csak nyomkövetés egy metódus hívása, mert az alkalmazás-hozzárendelés nem is érdekes. De alkalmazástérkép méretezhető sokkal könnyebben elosztott alkalmazás megjelenítése:

   ![Alkalmazástérkép](media/opencensus-python/application-map.png)

3. Válassza ki **teljesítményének vizsgálata** részletes teljesítményelemzését végez, és a lassú teljesítmény okának meghatározásához.

    ![Teljesítmény panel képernyőképe](./media/opencensus-python/0008-performance.png)

4. Kiválasztásával **minták** és bármely, a minták jelennek meg a jobb oldali ablaktáblán, majd kattintson a végpontok közötti tranzakció részletei élmény elindul. Mintaalkalmazásunk ugyanúgy jelennek meg velünk a kapcsolatot egy adott eseményhez, miközben olyan összetettebb alkalmazást szeretne tárhatja fel az egyéni esemény hívási verem szintre a végpontok közötti tranzakció.

     ![Végpontok közötti tranzakció felület képernyőképe](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-for-python"></a>Pythonhoz készült OpenCensus

* [Testreszabás](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask-integráció](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django-integráció](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL Integration](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>További lépések

* [A Githubon OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [Alkalmazástérkép](./../../azure-monitor/app/app-map.md)
* [Teljes körű alkalmazásteljesítmény-figyelés](./../../azure-monitor/learn/tutorial-performance.md)
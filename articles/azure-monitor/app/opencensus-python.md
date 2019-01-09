---
title: Az Azure Application Insights OpenCensus Python nyomkövetés |} A Microsoft Docs
description: Bemutatja, hogyan lehet beállítani a helyi továbbító és az Application Insights nyomkövetés OpenCensus Python
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 885f4da5ec9b360605a3e46ee8be8d338a638ede
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102669"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Elosztott nyomkövetések gyűjtéséhez a Python (előzetes verzió)

Az Application Insights most már támogatja az elosztott nyomkövetést Python-alkalmazások integrációja révén [OpenCensus](https://opencensus.io) és az új [helyi továbbító](./../../azure-monitor/app/opencensus-local-forwarder.md). Ez a cikk részletesen OpenCensus beállítása a Python és a nyomkövetési adatok az Application insights szolgáltatásba a folyamatot.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell Azure-előfizetéssel.
- Python-nek telepítve kell lennie, ez a cikk [Python 3.7.0](https://www.python.org/downloads/), bár a korábbi verzióiban valószínűleg kisebb helyesbítéssel fognak működni.
- Kövesse az utasításokat követve telepítse a [helyi továbbító Windows szolgáltatásként](./../../azure-monitor/app/opencensus-local-forwarder.md#windows-service)

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Először meg kell létrehoznia az Application Insights-erőforrást egy kialakítási key(ikey) hoz létre, amely. A rendszerállapotkulcsot szolgál majd a helyi továbbító elosztott nyomkövetési elküldeni a kialakítva OpenCensus alkalmazását, az Application Insights konfigurálása.   

1. Válassza ki **erőforrás létrehozása** > **fejlesztői eszközök** > **Application Insights**.

   ![Application Insights-erőforrások hozzáadása](./media/opencensus-python/0001-create-resource.png)

   Megjelenik egy konfigurációs mező. Az adatbeviteli mezők kitöltéséhez használja az alábbi táblát.

    | Beállítások        | Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Name (Név)**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Alkalmazás típusa** | Általános kérdések | A figyelt alkalmazás típusa |
   | **Erőforráscsoport**     | myResourceGroup      | Az App Insights-adatokat futtató új erőforráscsoport neve |
   | **Hely** | USA keleti régiója | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

2. Kattintson a **Create** (Létrehozás) gombra.

## <a name="configure-local-forwarder"></a>Helyi-továbbító konfigurálása

1. Válassza az **Áttekintés** > **Alapvető erőforrások** elemet, és másolja ki az alkalmazás **Kialakítási kulcsát**.

   ![Képernyőkép az eszközkulcs](./media/opencensus-python/0003-instrumentation-key.png)

2. Szerkessze a `LocalForwarder.config` fájlt, és adja hozzá a kialakítási kulcsot. Ha követte a lépéseket a következő témakör utasításait a [előfeltételeként](./../../azure-monitor/app/opencensus-local-forwarder.md#windows-service) a fájl `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Az alkalmazás újraindítása **helyi továbbító** szolgáltatás.

## <a name="opencensus-python-package"></a>OpenCensus Python-csomag

1. Telepítse a nyílt népszámlálási csomagot a Python pip vagy a parancssorból pipenv:

    ```python
    python -m pip install opencensus
    # pip env install opencensus
    ```

    > [!NOTE]
    > `python -m pip install opencensus` feltételezi, hogy a PATH környezeti változóhoz, a Python-telepítés beállítása. Ha nincs konfigurálva ez, hogy arra, ahol a Python végrehajtható fájl található amely szeretné eredménye egy, a teljes elérési útja kell: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus`.

2. Először hozzunk a nyomkövetési adatok helyben létrehozásához. Python TÉTLEN, vagy a választott szerkesztővel adja meg a következő kódot:

    ```python
    from opencensus.trace.tracer import Tracer
    
    def main():
        while True:
            valuePrompt()
    
    def valuePrompt():
        tracer = Tracer()
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)
    
    if __name__ == "__main__":
        main()
    
    ```

3. A kód futtatása ismételten kérni fogja, adjon meg egy értéket. Minden bejegyzés az értéket nyomtatja a rendszerhéj, és a egy megfelelő részét **SpanData** generál a OpenCensus Python modulban. Meghatározza a OpenCensus projektet egy [ _álló átnyúlások nyomkövetési_](https://opencensus.io/core-concepts/tracing/).
    
    ```python
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=1f07f062ac394c50925f2ae61e635e14, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='5c17a4ad6ba14299', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:15.847292Z', end_time='2018-09-15T20:42:17.615664Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=c71b4e88a22a495da61df52ce3eee3e1, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='51547c0af5f046eb', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:17.615664Z', end_time='2018-09-15T20:48:11.160314Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=b4cdcc9e6df44a8fbb6e8ddeccc1351c, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f2caacf7892744d1', parent_span_id=None, attributes={}, start_time='2018-09-15T20:48:11.175931Z', end_time='2018-09-15T20:48:12.629178Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

4. Bár ez hasznos bemutatási célokra, végső soron szeretnénk gridre bocsáthatja ki az SpanData úgy, hogy azt is észlelnie kell a **helyi továbbító szolgáltatásának** és elküldött továbbítása az Application Insights. Módosítsa a kód az előző lépésben a következőhöz:

    ```python
    from opencensus.trace.tracer import Tracer
    from opencensus.trace import config_integration
    from opencensus.trace.exporters.ocagent import trace_exporter
    from opencensus.trace import tracer as tracer_module
    
    import os
    
    def main():        
        while True:
            valuePrompt()
    
    def valuePrompt():
        export_LocalForwarder = trace_exporter.TraceExporter(
        service_name=os.getenv('SERVICE_NAME', 'python-service'),
        endpoint=os.getenv('OCAGENT_TRACE_EXPORTER_ENDPOINT'))
        
        tracer = Tracer(exporter=export_LocalForwarder)
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)
    
    if __name__ == "__main__":
        main()
    ```

5. Ha menteni, és próbálja meg futtatni a fenti modul, előfordulhat, hogy kap egy `ModuleNotFoundError` a `grpc`. Ha ez történik, futtassa a következő telepítéséhez a [grpcio csomag](https://pypi.org/project/grpcio/) együtt:

    ```
    python -m pip install grpcio
    ```

6. Most a Python-szkriptnek a fent futtatásakor, továbbra is a rendszer kéri, adja meg az értékeket, de most csak az értéket a rendszerhéj nyomtatása történik.

7. Ellenőrizheti, hogy a **helyi továbbító** vesz fel a nyomkövetések ellenőrzés a `LocalForwarder.config` fájlt. Ha követte a lépéseket a [előfeltétel](https://docs.microsoft.com/azure/application-insights/local-forwarder#windows-service), kell elhelyezni a `C:\LF-WindowsServiceHost`.

    Az alábbi ábrán a naplófájl, láthatja, hogy hol hozzáadtunk egy exportáló a második szkript futtatása előtt `OpenCensus input BatchesReceived` 0. Hogy megkezdése a frissített szkript futtatása után `BatchesReceived` azt a megadott értékek száma egyenlő a növekménye:
    
    ![Új App Insights-erőforrás űrlap](./media/opencensus-python/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

1. Most megnyithatja ismét az Application Insights **áttekintése** a jelenleg futó alkalmazás részleteinek megtekintése az Azure Portalon lapot. Válassza ki **élő Metric Stream**.

   ![Az élő metrikai streamet vörös téglalappal jelölve áttekintő panel képernyőképe](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Ha futtatja a második Python-szkriptet újra és értékeket ad kezdő, látni fogja élő nyomkövetési adatok, a helyi továbbító szolgáltatásból beérkező az Application Insightsban.

   ![Képernyőkép a teljesítményadatok jelenik meg az élő metrikai streamet](./media/opencensus-python/0006-stream.png)

3. Lépjen vissza a **áttekintése** lapon, és válassza **Alkalmazástérkép** mutató vizuális elrendezés az alkalmazás-összetevők közötti hívások időzítését és a függőségi viszonyok.

    ![Képernyőkép az alapvető alkalmazástérkép](./media/opencensus-python/0007-application-map.png)

    Azt is csak nyomkövetés egy metódus hívása, mert az alkalmazás-hozzárendelés nem is érdekes. De alkalmazástérkép méretezhető sokkal könnyebben elosztott alkalmazás megjelenítése:

   ![Alkalmazástérkép](media/opencensus-python/application-map.png)

4. Válassza ki **teljesítményének vizsgálata** részletes teljesítményelemzését végez, és a lassú teljesítmény okának meghatározásához.

    ![Teljesítmény panel képernyőképe](./media/opencensus-python/0008-performance.png)

5. Kiválasztásával **minták** és bármely, a minták jelennek meg a jobb oldali ablaktáblán, majd kattintson a végpontok közötti tranzakció részletei élmény elindul. Mintaalkalmazásunk ugyanúgy jelennek meg velünk a kapcsolatot egy adott eseményhez, miközben olyan összetettebb alkalmazást szeretne tárhatja fel az egyéni esemény hívási verem szintre a végpontok közötti tranzakció.

     ![Végpontok közötti tranzakció felület képernyőképe](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-python"></a>Python-OpenCensus nyomkövetési

Csak megismerte a háttérszolgáltatást OpenCensus be a Python a helyi továbbító és az Application Insights alapjait. A hivatalos használati útmutatásért összetettebb témákra, például a következőkkel foglalkozik:

* [Vételezők](https://opencensus.io/api/python/trace/usage.html#samplers)
* [Flask-integráció](https://opencensus.io/api/python/trace/usage.html#flask)
* [Django-integráció](https://opencensus.io/api/python/trace/usage.html#django)
* [MySQL-integráció](https://opencensus.io/api/python/trace/usage.html#service-integration)
* [PostgreSQL](https://opencensus.io/api/python/trace/usage.html#postgresql)
  
## <a name="next-steps"></a>További lépések

* [OpenCensus Python – használati útmutató](https://opencensus.io/api/python/trace/usage.html)
* [Alkalmazástérkép](./../../azure-monitor/app/app-map.md)
* [Teljes körű alkalmazásteljesítmény-figyelés](./../../azure-monitor/learn/tutorial-performance.md)

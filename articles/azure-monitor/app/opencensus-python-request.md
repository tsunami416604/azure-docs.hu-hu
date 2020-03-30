---
title: Bejövő kérelmek nyomon követése az Azure Application Insights ban az OpenCensus Python nal | Microsoft dokumentumok
description: Figyelheti a Python-alkalmazások hívásait az OpenCensus Python on keresztül.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669947"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>A bejövő kérelmek nyomon követése az OpenCensus Python nal

A bejövő kérelem adatok at opencensus Python és a különböző integrációk használatával gyűjtik. Nyomon követheti a népszerű webes keretrendszerekre épülő webes `django` `flask` alkalmazásoknak küldött bejövő kérelemadatokat, és `pyramid`. Az adatokat ezután telemetriaként `requests` az Application Insights az Azure Monitor alatt elküldi.

Először a Python-alkalmazást a legújabb [OpenCensus Python SDK-val](../../azure-monitor/app/opencensus-python.md)instrumental.

## <a name="tracking-django-applications"></a>Django alkalmazások nyomon követése

1. Töltse le `opencensus-ext-django` és telepítse a [PyPI-ből,](https://pypi.org/project/opencensus-ext-django/) és hangszerelje az alkalmazást a `django` köztes szoftverrel. Az `django` alkalmazásnak küldött bejövő kérelmeket a rendszer nyomon követi.

2. Szerepeljen `opencensus.ext.django.middleware.OpencensusMiddleware` `settings.py` a `MIDDLEWARE`fájlban a területen.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Győződjön meg arról, `settings.py` hogy `OPENCENSUS`az AzureExporter megfelelően van konfigurálva az alatta.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
        }
    }
    ```

4. A nem nyomon követni `settings.py` `BLACKLIST_PATHS` kívánt kérésekhez url-eket is hozzáadhat.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Lombikok nyomon követése

1. Töltse le `opencensus-ext-flask` és telepítse a [PyPI-ből,](https://pypi.org/project/opencensus-ext-flask/) és hangszerelje az alkalmazást a `flask` köztes szoftverrel. Az `flask` alkalmazásnak küldött bejövő kérelmeket a rendszer nyomon követi.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. A köztes `flask` szoftvert közvetlenül a kódban állíthatja be. Az olyan URL-ektől érkező kérések esetén, `BLACKLIST_PATHS`amelyeket nem kíván nyomon követni, adja hozzá őket a hoz.

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Piramis alkalmazások nyomon követése

1. Töltse le `opencensus-ext-django` és telepítse a [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) és a műszer az alkalmazás a `pyramid` tween. Az `pyramid` alkalmazásnak küldött bejövő kérelmeket a rendszer nyomon követi.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Beállíthatja a `pyramid` tween közvetlenül a kódot. Az olyan URL-ektől érkező kérések esetén, `BLACKLIST_PATHS`amelyeket nem kíván nyomon követni, adja hozzá őket a hoz.

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>További lépések

* [Alkalmazástérkép](../../azure-monitor/app/app-map.md)
* [Rendelkezésre állás](../../azure-monitor/app/monitor-web-app-availability.md)
* [Keresés](../../azure-monitor/app/diagnostic-search.md)
* [Napló (Analytics) lekérdezés](../../azure-monitor/log-query/log-query-overview.md)
* [Tranzakció diagnosztikája](../../azure-monitor/app/transaction-diagnostics.md)

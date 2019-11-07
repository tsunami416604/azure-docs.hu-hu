---
title: Bejövő kérelmek nyomon követése az Azure Application Insights a OpenCensus Pythonban | Microsoft Docs
description: A Python-alkalmazások OpenCensus Python-n keresztüli hívásának figyelése.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: f6b06e7bb2bb8637ca28b2fa4185754f8686798e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587926"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>A beérkező kérelmek nyomon követése a OpenCensus Pythonban

A beérkező kérések adatai a OpenCensus Python és annak különböző integrációi használatával gyűjthetők össze. A webalkalmazásoknak továbbított beérkező kérések adatai nyomon követése `django`, `flask` és `pyramid`népszerű webes keretrendszerekre épül. Ezt követően a rendszer a Azure Monitor `requests` telemetria Application Insights küldi el az adatait.

Először is a Python-alkalmazást a legújabb [OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)-val alakíthatja ki.

## <a name="tracking-django-applications"></a>Django-alkalmazások nyomon követése

1. Töltse le és telepítse a `opencensus-ext-django`t a [PyPI](https://pypi.org/project/opencensus-ext-django/) -ből, és tegye az alkalmazást az `django` middleware-vel. A rendszer a `django` alkalmazásnak küldött bejövő kérelmeket fogja követni.

2. `opencensus.ext.django.middleware.OpencensusMiddleware` belefoglalása az `settings.py`-fájlba `MIDDLEWARE`alatt.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Győződjön meg arról, hogy a AzureExporter megfelelően van konfigurálva a `settings.py` `OPENCENSUS`alatt.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
        }
    }
    ```

4. `settings.py` URL-címeket is hozzáadhat `BLACKLIST_PATHS` azon kérelmekhez, amelyeket nem kíván nyomon követni.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>A lombik alkalmazásainak nyomon követése

1. Töltse le és telepítse a `opencensus-ext-flask`t a [PyPI](https://pypi.org/project/opencensus-ext-flask/) -ből, és tegye az alkalmazást az `flask` middleware-vel. A rendszer a `flask` alkalmazásnak küldött bejövő kérelmeket fogja követni.

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

2. Közvetlenül a kódban konfigurálhatja a `flask` middleware-t. A nyomon követni nem kívánt URL-címektől érkező kérések hozzáadásához vegye fel őket a `BLACKLIST_PATHS`.

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Piramis-alkalmazások nyomon követése

1. Töltse le és telepítse a `opencensus-ext-django`t a [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) -ből, és az alkalmazást az `pyramid` Tween-vel. A rendszer a `pyramid` alkalmazásnak küldött bejövő kérelmeket fogja követni.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Közvetlenül a kódban is konfigurálhatja a `pyramid` Tween-t. A nyomon követni nem kívánt URL-címektől érkező kérések hozzáadásához vegye fel őket a `BLACKLIST_PATHS`.

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    service_name='foobar',
                )''',
                'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>További lépések

* [Alkalmazástérkép](../../azure-monitor/app/app-map.md)
* [Rendelkezésre állás](../../azure-monitor/app/monitor-web-app-availability.md)
* [Keresés](../../azure-monitor/app/diagnostic-search.md)
* [Log (Analytics) lekérdezés](../../azure-monitor/log-query/log-query-overview.md)
* [Tranzakció-diagnosztika](../../azure-monitor/app/transaction-diagnostics.md)

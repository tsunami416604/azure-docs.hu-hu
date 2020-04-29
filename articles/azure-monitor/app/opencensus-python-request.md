---
title: Bejövő kérelmek nyomon követése az Azure Application Insights a OpenCensus Pythonban | Microsoft Docs
description: A Python-alkalmazások OpenCensus Python-n keresztüli hívásának figyelése.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669947"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>A beérkező kérelmek nyomon követése a OpenCensus Pythonban

A beérkező kérések adatai a OpenCensus Python és annak különböző integrációi használatával gyűjthetők össze. Nyomon követheti a beérkező kérések adatait `django`, `flask` amelyeket a népszerű webes keretrendszerekre épülő webalkalmazásoknak küldenek. `pyramid` Ezután a rendszer elküldi az adat Application Insights Azure Monitor alatt `requests` telemetria.

Először is a Python-alkalmazást a legújabb [OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)-val alakíthatja ki.

## <a name="tracking-django-applications"></a>Django-alkalmazások nyomon követése

1. Töltse le és `opencensus-ext-django` telepítse a [PyPI](https://pypi.org/project/opencensus-ext-django/) -ből, és az `django` alkalmazást a middleware-mel. Az alkalmazásnak küldött bejövő `django` kéréseket a rendszer nyomon fogja követni.

2. Adja `opencensus.ext.django.middleware.OpencensusMiddleware` meg `settings.py` a fájlt a `MIDDLEWARE`fájlban.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Győződjön meg arról, hogy a AzureExporter megfelelően `settings.py` van `OPENCENSUS`konfigurálva a alatt.

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

4. A `settings.py` alá `BLACKLIST_PATHS` tartozó URL-címeket is hozzáadhat olyan kérelmekhez, amelyeket nem kíván nyomon követni.

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

## <a name="tracking-flask-applications"></a>A lombik alkalmazásainak nyomon követése

1. Töltse le és `opencensus-ext-flask` telepítse a [PyPI](https://pypi.org/project/opencensus-ext-flask/) -ből, és az `flask` alkalmazást a middleware-mel. Az alkalmazásnak küldött bejövő `flask` kéréseket a rendszer nyomon fogja követni.

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

2. A `flask` middleware-t közvetlenül a kódban is konfigurálhatja. A nem nyomon követni kívánt URL-címekről érkező kérések hozzáadásához vegye `BLACKLIST_PATHS`fel a következőt:.

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

## <a name="tracking-pyramid-applications"></a>Piramis-alkalmazások nyomon követése

1. Töltse le és `opencensus-ext-django` telepítse a [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) -ból, és az `pyramid` alkalmazását a Tween-mel. Az alkalmazásnak küldött bejövő `pyramid` kéréseket a rendszer nyomon fogja követni.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Az `pyramid` Tween-t közvetlenül a kódban is konfigurálhatja. A nem nyomon követni kívánt URL-címekről érkező kérések hozzáadásához vegye `BLACKLIST_PATHS`fel a következőt:.

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
* [Log (Analytics) lekérdezés](../../azure-monitor/log-query/log-query-overview.md)
* [Tranzakció diagnosztikája](../../azure-monitor/app/transaction-diagnostics.md)

---
title: Bejövő kérelmek nyomon követése az Azure Application Insights a OpenCensus Pythonban | Microsoft Docs
description: A Python-alkalmazások OpenCensus Python-n keresztüli hívásának figyelése.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: tracking-python
ms.openlocfilehash: c9d69c0f39d9cad52dc86c3ab33d202c88131ab0
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84753207"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>A beérkező kérelmek nyomon követése a OpenCensus Pythonban

A beérkező kérések adatai a OpenCensus Python és annak különböző integrációi használatával gyűjthetők össze. Nyomon követheti a beérkező kérések adatait, amelyeket a népszerű webes keretrendszerekre épülő webalkalmazásoknak küldenek `django` `flask` `pyramid` . Ezután a rendszer elküldi az adat Application Insights Azure Monitor alatt `requests` telemetria.

Először is a Python-alkalmazást a legújabb [OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)-val alakíthatja ki.

## <a name="tracking-django-applications"></a>Django-alkalmazások nyomon követése

1. Töltse le és telepítse a `opencensus-ext-django` [PyPI](https://pypi.org/project/opencensus-ext-django/) -ből, és az alkalmazást a middleware-mel `django` . Az alkalmazásnak küldött bejövő kéréseket a `django` rendszer nyomon fogja követni.

2. Adja `opencensus.ext.django.middleware.OpencensusMiddleware` meg a `settings.py` fájlt a fájlban `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Győződjön meg arról, hogy a AzureExporter megfelelően van konfigurálva a `settings.py` alatt `OPENCENSUS` . A nem nyomon követni kívánt URL-címekről érkező kérések hozzáadásához vegye fel a következőt: `BLACKLIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>A lombik alkalmazásainak nyomon követése

1. Töltse le és telepítse a `opencensus-ext-flask` [PyPI](https://pypi.org/project/opencensus-ext-flask/) -ből, és az alkalmazást a middleware-mel `flask` . Az alkalmazásnak küldött bejövő kéréseket a `flask` rendszer nyomon fogja követni.

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

2. Az alkalmazást a használatával is konfigurálhatja `flask` `app.config` . A nem nyomon követni kívánt URL-címekről érkező kérések hozzáadásához vegye fel a következőt: `BLACKLIST_PATHS` .

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

1. Töltse le és telepítse a `opencensus-ext-django` [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) -ból, és az alkalmazását a Tween-mel `pyramid` . Az alkalmazásnak küldött bejövő kéréseket a `pyramid` rendszer nyomon fogja követni.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Az `pyramid` Tween-t közvetlenül a kódban is konfigurálhatja. A nem nyomon követni kívánt URL-címekről érkező kérések hozzáadásához vegye fel a következőt: `BLACKLIST_PATHS` .

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
* [Search](../../azure-monitor/app/diagnostic-search.md)
* [Log (Analytics) lekérdezés](../../azure-monitor/log-query/log-query-overview.md)
* [Tranzakció diagnosztikája](../../azure-monitor/app/transaction-diagnostics.md)

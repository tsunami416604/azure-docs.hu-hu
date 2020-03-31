---
title: Függőségkövetés az Azure Application Insights ban az OpenCensus Python nal | Microsoft dokumentumok
description: Figyelheti a Python-alkalmazások függőségi hívásait az OpenCensus Python on keresztül.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669930"
---
# <a name="track-dependencies-with-opencensus-python"></a>Függőségek nyomon követése az OpenCensus Pythonnal

A függőség egy külső összetevő, amelyet az alkalmazás hív meg. A függőségi adatok gyűjtése az OpenCensus Python és a különböző integrációk használatával történik. Az adatokat ezután telemetriaként `dependencies` az Application Insights az Azure Monitor alatt elküldi.

Először a Python-alkalmazást a legújabb [OpenCensus Python SDK-val](../../azure-monitor/app/opencensus-python.md)instrumental.

## <a name="in-process-dependencies"></a>Folyamaton belüli függőségek

OpenCensus Python SDK az Azure Monitor lehetővé teszi, hogy küldjön "folyamaton belüli" függőség telemetriai adatok (az alkalmazáson belül előforduló információk és logika). A folyamaton belüli függőségek `INPROC` az elemzéshez ugyanúgy rendelkeznek a `type` mezővel.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Függőségek "kérések" integrációval

Kövesse nyomon a kimenő kérelmeket az OpenCensus `requests` integrációval.

Töltse le `opencensus-ext-requests` és telepítse a [PyPI-ből,](https://pypi.org/project/opencensus-ext-requests/) és adja hozzá a nyomkövetési integrációkhoz. A [Python-kérelmek](https://pypi.org/project/requests/) könyvtárával küldött kérelmeket a rendszer nyomon követi.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Függőségek "httplib" integrációval

Kövesse nyomon a kimenő `httplib` kérelmeket OpenCensus integrációval.

Töltse le `opencensus-ext-httplib` és telepítse a [PyPI-ből,](https://pypi.org/project/opencensus-ext-httplib/) és adja hozzá a nyomkövetési integrációkhoz. A [http.client](https://docs.python.org/3.7/library/http.client.html) for Python3 vagy [a httplib](https://docs.python.org/2/library/httplib.html) for Python2 használatával küldött kérelmeket a rendszer nyomon követi.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Függőségek a "django" integrációval

Kövesse nyomon a kimenő Django-kérelmeket az OpenCensus `django` integrációval.

Töltse le `opencensus-ext-django` és telepítse a [PyPI-ből,](https://pypi.org/project/opencensus-ext-django/) és adja hozzá a következő sort a `MIDDLEWARE` Django `settings.py` fájl szakaszához.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

További konfigurációk is rendelkezésre állnak, olvassa el a [testreszabásokat](https://github.com/census-instrumentation/opencensus-python#customization) a teljes referencia érdekében.

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

## <a name="dependencies-with-mysql-integration"></a>Függőségek a "mysql" integrációval

Kövesse nyomon MYSQL-függőségeit `mysql` az OpenCensus integrációval. Ez az integráció támogatja a [mysql-összekötő](https://pypi.org/project/mysql-connector-python/) könyvtárat.

Töltse le `opencensus-ext-mysql` és telepítse a [PyPI-ből,](https://pypi.org/project/opencensus-ext-mysql/) és adja hozzá a következő sorokat a kódhoz.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Függőségek a "pymysql" integrációval

Kövesse nyomon a PyMySQL-függőségeket az OpenCensus `pymysql` integrációval.

Töltse le `opencensus-ext-pymysql` és telepítse a [PyPI-ből,](https://pypi.org/project/opencensus-ext-pymysql/) és adja hozzá a következő sorokat a kódhoz.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Függőségek a "postgresql" integrációval

Kövesse nyomon a PostgreSQL függőségeit az OpenCensus `postgresql` integrációval. Ez az integráció támogatja a [psycopg2](https://pypi.org/project/psycopg2/) könyvtárat.

Töltse le `opencensus-ext-postgresql` és telepítse a [PyPI-ből,](https://pypi.org/project/opencensus-ext-postgresql/) és adja hozzá a következő sorokat a kódhoz.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Függőségek a "pymongo" integrációval

Kövesse nyomon a MongoDB függőségeit az OpenCensus `pymongo` integrációval. Ez az integráció támogatja a [pymongo](https://pypi.org/project/pymongo/) könyvtárat.

Töltse le `opencensus-ext-pymongo` és telepítse a [PyPI-ből,](https://pypi.org/project/opencensus-ext-pymongo/) és adja hozzá a következő sorokat a kódhoz.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Függőségek "sqlalchemy" integrációval

Kövesse nyomon függőségeit az SQLAlchemy használatával az OpenCensus `sqlalchemy` integráció használatával. Ez az integráció nyomon követi az [sqlalchemy](https://pypi.org/project/SQLAlchemy/) csomag használatát, függetlenül az alapul szolgáló adatbázistól.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>További lépések

* [Alkalmazástérkép](../../azure-monitor/app/app-map.md)
* [Rendelkezésre állás](../../azure-monitor/app/monitor-web-app-availability.md)
* [Keresés](../../azure-monitor/app/diagnostic-search.md)
* [Napló (Analytics) lekérdezés](../../azure-monitor/log-query/log-query-overview.md)
* [Tranzakció diagnosztikája](../../azure-monitor/app/transaction-diagnostics.md)

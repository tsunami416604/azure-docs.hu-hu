---
title: Python-fejlesztői referencia az Azure Functionshez
description: Ismerje meg, hogyan fejleszthet funkciókat a Pythonnal
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 30f40db33b6aa8b40202c023f301265565257180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276685"
---
# <a name="azure-functions-python-developer-guide"></a>Az Azure Functions Python fejlesztői útmutatója

Ez a cikk az Azure Functions Python használatával történő fejlesztésének bemutatkozása. Az alábbi tartalom feltételezi, hogy már elolvasta az [Azure Functions fejlesztői útmutatóját.](functions-reference.md) 

Az önálló függvény mintaprojektek pythonban tekintse meg a [Python függvényminták.](/samples/browse/?products=azure-functions&languages=python) 

## <a name="programming-model"></a>A programozási modell

Az Azure Functions elvárja, hogy egy függvény egy állapotmentes metódus a Python-parancsfájlban, amely feldolgozza a bemeneti és kimeneti eredményeket. Alapértelmezés szerint a futásidejű elvárja, hogy a metódus `main()` a `__init__.py` fájlban megnevezett globális metódusként kerül-e bevezetésre. Alternatív belépési pontot is [megadhat.](#alternate-entry-point)

Az eseményindítókból és kötésekből származó adatok `name` a függvény metódusattribútumain keresztül kötődnek a függvényhez a *function.json* fájlban definiált tulajdonság használatával. Az alábbi _function.json_ például egy egyszerű függvényt ír `req`le, amelyet egy HTTP-kérelem nevezett el:

:::code language="son" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

E definíció alapján `__init__.py` a függvénykódot tartalmazó fájl a következő példához hasonló lehet:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Explicit módon deklarálhatja az attribútumtípusokat és a visszatérési típust a függvényben python típusú jegyzetek használatával. Ez segít az intellisense és az automatikus kiegészítés számos Python kódszerkesztő által biztosított funkciók használatában.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Használja az [azure.functions-ben](/python/api/azure-functions/azure.functions?view=azure-python) található Python-jegyzeteket.* a bemeneti és kimeneti műveleteket a metódusokhoz.

## <a name="alternate-entry-point"></a>Alternatív belépési pont

Egy függvény alapértelmezett viselkedését a *function.json* `entryPoint` fájlban lévő `scriptFile` tulajdonságok és tulajdonságok tetszés szerint módosíthatja. Például a _function.json_ alatt azt mondja `customentry()` a futásidejű, hogy a metódust a _main.py_ fájlban, mint az Azure-függvény belépési pont.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Mappastruktúra

A Python Functions projekt ajánlott mappastruktúrája a következő példához hasonlóan néz ki:

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 tests
```
A projekt fő\_\_\_\_mappája ( alkalmazás ) a következő fájlokat tartalmazhatja:

* *local.settings.json*: Az alkalmazásbeállítások és a kapcsolati karakterláncok tárolására szolgál helyi futtatáskor. Ez a fájl nem jelenik meg az Azure-ban. További információ: [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: A rendszer által az Azure-ban való közzétételkor telepített csomagok listáját tartalmazza.
* *host.json*: Globális konfigurációs beállításokat tartalmaz, amelyek egy függvényalkalmazás összes függvényét érintik. Ez a fájl nem kap közzé az Azure-ban. Nem minden beállítás támogatott helyi futtatásesetén. További információ: [host.json](functions-host-json.md).
* *.funcignore*: (Nem kötelező) deklarálja azokat a fájlokat, amelyeket nem kellene közzétenni az Azure-ban.
* *.gitignore*: (Nem kötelező) deklarálja a git-tárterületből kizárt fájlokat, például a local.settings.json fájlt.

Minden függvény saját kódfájllal és kötési konfigurációs fájllal (function.json) rendelkezik. 

Amikor a projektet egy függvényalkalmazásba telepíti az Azure-ban, a fő projekt (*\_\_alkalmazás)\_* mappa teljes tartalmát a csomagban kell szerepeltetni, de magát a mappát nem. Azt javasoljuk, hogy a teszteket a projektmappától elkülönített `tests`mappában tartsa fenn ebben a példában. Ez megakadályozza, hogy tesztkódot telepítsen az alkalmazással. További információ: [Unit Testing](#unit-testing).

## <a name="import-behavior"></a>Importálási viselkedés

A függvénykódba modulokat importálhat explicit relatív és abszolút hivatkozások használatával. A fenti mappastruktúra alapján a következő importmunka a * \_ \_\_\_\my\_\_first\\\_function\_\__ init .py függvényalkalmazásból*származik:

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

A következő *importálások nem működnek* ugyanabból a fájlból:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

A megosztott kódot az * \_ \_alkalmazás\_* külön mappájában kell tárolni. A *\_megosztott kódmappában* lévő modulokra való hivatkozáshoz a következő szintaxist használhatja:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Eseményindítók és bemenetek

A bemenetek két kategóriába sorolhatók az Azure Functionsben: az eseményindító bemenet és a további bemenetek. Bár a fájlban `function.json` eltérőek, a használat megegyezik a Python-kódban.  Az eseményindítók és a bemeneti források `local.settings.json` kapcsolati karakterláncai vagy titkos fájljai a helyi futtatáskor a fájlban lévő értékekhez, valamint az Azure-ban való futtatáskor az alkalmazásbeállításokhoz vannak leképezve. 

Például a következő kód bemutatja a kettő közötti különbséget:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

A függvény meghívásakor a HTTP-kérelem átkerül `req`a függvénybe . Egy bejegyzés lesz beolvasni az Azure Blob Storage az _azonosító_ alapján az `obj` útvonal URL-cím, és elérhetővé tette, mint a függvény törzsében.  Itt a megadott tárfiók az AzureWebJobsStorage alkalmazás beállításában található kapcsolati karakterlánc, amely a függvényalkalmazás által használt tárfiók.


## <a name="outputs"></a>Kimenetek

A kimenet mind a visszatérési értékben, mind a kimeneti paraméterekben kifejezhető. Ha csak egy kimenet van, javasoljuk, hogy használja a visszatérési értéket. Több kimenetesetén kimeneti paramétereket kell használnia.

Ha egy függvény visszatérési értékét kimeneti kötés értékeként szeretné használni, `name` a `$return` `function.json`kötés tulajdonságát a értékre kell állítani.

Több kimenet létrehozásához használja `set()` az [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) interfész által biztosított módszert, hogy értéket rendeljen a kötéshez. A következő függvény például leküldhet egy üzenetet egy várólistába, és http-választ is visszaadhat.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Naplózás

Az Azure Functions futásidejű naplózóhoz való [`logging`](https://docs.python.org/3/library/logging.html#module-logging) hozzáférés a függvényalkalmazás gyökérkezelőjében keresztül érhető el. Ez a naplózó az Application Insights hoz van kötve, és lehetővé teszi a függvény végrehajtása során észlelt figyelmeztetések és hibák megjelölése.

A következő példa naplózza az információs üzenetet, ha a függvényhttp-eseményindítón keresztül hívható meg.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

További naplózási módszerek állnak rendelkezésre, amelyek lehetővé teszik, hogy különböző nyomkövetési szinteken írjon a konzolra:

| Módszer                 | Leírás                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Ír egy üzenetet szint kritikus a gyökér logger.  |
| **`error(_message_)`**   | Hiba szinttel rendelkező üzenetet ír a gyökérnaplózóra.    |
| **`warning(_message_)`**    | A gyökérnaplózóra figyelmeztetési szintű üzenetet ír.  |
| **`info(_message_)`**    | A gyökérnaplózóra info szintű üzenetet ír.  |
| **`debug(_message_)`** | Debug szintű üzenetet ír a gyökérnaplózóra.  |

A naplózásról az [Azure-függvények figyelése című témakörben olvashat bővebben.](functions-monitoring.md)

## <a name="http-trigger-and-bindings"></a>HTTP-eseményindító és kötések

A HTTP-eseményindító a function.jon fájlban van definiálva. A `name` kötésnek meg kell egyeznie a függvényben szereplő elnevezett paraméterrel. Az előző példákban `req` egy kötési nevet használ a használat. Ez a paraméter egy [HttpRequest] objektum, és egy [HttpResponse] objektumot ad vissza.

A [HttpRequest] objektumból kérésfejléceket, lekérdezési paramétereket, útvonalparamétereket és az üzenet törzsét kaphatja meg. 

A következő példa a [Python HTTP-eseményindító sablonjából származik.](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python) 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

Ebben a függvényben a `name` lekérdezési paraméter értéke `params` a [HttpRequest] objektum paraméteréből származik. A JSON-kódolású üzenet törzse `get_json` a módszerrel olvasható. 

Hasonlóképpen beállíthatja a `status_code` `headers` válaszüzenetet a visszaadott [HttpResponse] objektumban.

## <a name="scaling-and-concurrency"></a>Méretezés és egyidejűség

Alapértelmezés szerint az Azure Functions automatikusan figyeli az alkalmazás terhelését, és szükség szerint további állomáspéldányokat hoz létre a Pythonszámára. A függvények beépített (nem felhasználó által konfigurálható) küszöbértékeket használ a különböző eseményindító-típusokhoz, hogy eldöntsék, mikor vegyenek fel példányokat, például az üzenetek korát és a QueueTrigger várólistaméretét. További információ: [How the Consumption and Premium plans is work.](functions-scale.md#how-the-consumption-and-premium-plans-work)

Ez a skálázási viselkedés számos alkalmazás számára elegendő. Az alábbi jellemzők bármelyikével rendelkező alkalmazások azonban nem méretezhetők olyan hatékonyan:

- Az alkalmazásnak számos egyidejű meghívást kell kezelnie.
- Az alkalmazás nagyszámú I/O-eseményt dolgoz fel.
- Az alkalmazás I/O-kötött.

Ilyen esetekben tovább javíthatja a teljesítményt az aszinkron minták használatával és több nyelvi munkavégző folyamat használatával.

### <a name="async"></a>Aszinkron

Mivel a Python egyegyszálas futásidejű, a Python gazdagéppéldánya egyszerre csak egy függvénymeghívást tud feldolgozni. A nagyszámú I/O-eseményt feldolgozó és/vagy I/O-kötött alkalmazások esetében a függvények aszinkron futtatásával javíthatja a teljesítményt.

A függvény aszinkron futtatásához használja a `async def` utasítást, amely közvetlenül az asyncio függvényt [futtatja:](https://docs.python.org/3/library/asyncio.html)

```python
async def main():
    await some_nonblocking_socket_io_op()
```

A `async` kulcsszó nélküli függvény automatikusan fut egy asyncio szálkészletben:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Több nyelvi munkavégző folyamat használata

Alapértelmezés szerint minden Functions állomáspéldány egyetlen nyelvi munkavégző folyamattal rendelkezik. A alkalmazásbeállítás [FUNCTIONS_WORKER_PROCESS_COUNTsal](functions-app-settings.md#functions_worker_process_count) növelheti a gazdagépenkénti munkavégző folyamatok számát (legfeljebb 10). Az Azure Functions ezután megpróbálja egyenletesen elosztani az egyidejű függvénymeghívásokat ezek között a dolgozók között. 

A FUNCTIONS_WORKER_PROCESS_COUNT minden állomásra vonatkozik, amelyet a Functions létrehoz, amikor az alkalmazást az igényeknek megfelelően skálázhatja. 

## <a name="context"></a>Környezet

Ha egy függvény meghívási környezetét szeretné [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) beszerezni a végrehajtás során, írja be az argumentumot az aláírásába. 

Példa:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A [**Környezet**](/python/api/azure-functions/azure.functions.context?view=azure-python) osztály a következő karakterlánc-attribútumokkal rendelkezik:

`function_directory`  
Az a könyvtár, amelyben a függvény fut.

`function_name`  
A függvény neve.

`invocation_id`  
Az aktuális függvény meghívásának azonosítója.

## <a name="global-variables"></a>Globális változók

Nem garantált, hogy az alkalmazás állapota megmarad a jövőbeli végrehajtások esetén. Azonban az Azure Functions futásidejű gyakran újra felhasználja ugyanazt a folyamatot több végrehajtások ugyanazon alkalmazás. Annak érdekében, hogy gyorsítótárazhassa egy költséges számítás eredményeit, deklarálja globális változóként. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Környezeti változók

A Függvények alkalmazásban [az alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatáskapcsolati karakterláncok környezeti változókként jelennek meg a végrehajtás során. Ezeket a beállításokat `import os` a deklarálással, majd a használatával érheti el. `setting = os.environ["setting-name"]`

A következő példa az [alkalmazás beállítást](functions-how-to-use-azure-function-app-settings.md#settings) `myAppSetting`kapja meg, amelynek neve a kulcs:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Helyi fejlesztés esetén az alkalmazásbeállítások [a local.settings.json fájlban maradnak meg.](functions-run-local.md#local-settings-file)  

## <a name="python-version"></a>Python-verzió 

Az Azure Functions a következő Python-verziókat támogatja:

| Függvények verziója | Python-verziók<sup>*</sup> |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2,x | 3.7<br/>3.6 |

<sup>*</sup>Hivatalos CPython disztribúciók

Ha egy adott Python-verziót szeretne kérni, amikor `--runtime-version` létrehozza [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) a függvényalkalmazást az Azure-ban, használja a parancs lehetőséget. A Functions futásidejű verzióját `--functions-version` a beállítás állítja be. A Python-verzió a függvényalkalmazás létrehozásakor van beállítva, és nem módosítható.  

Helyi futtatásakor a futtatóönként a rendelkezésre álló Python-verziót használja. 

## <a name="package-management"></a>Csomagkezelés

Ha helyileg fejleszti az Azure Functions Core Tools vagy a Visual Studio Code `requirements.txt` használatával, adja `pip`hozzá a szükséges csomagok nevét és verzióit a fájlhoz, és telepítse őket a használatával. 

Például a következő követelmények fájl és pip parancs `requests` segítségével telepítheti a csomagot pypi.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Közzététel az Azure-ban

Ha készen áll a közzétételre, győződjön meg arról, hogy az összes nyilvánosan elérhető függőség szerepel a requirements.txt fájlban, amely a projektkönyvtár gyökerében található. 

A közzétételből kizárt projektfájlok és mappák, beleértve a virtuális környezet mappát is, a .funcignore fájlban jelennek meg.

A Python-projekt Azure-ban való közzétételéhez három buildművelet támogatott:

+ Távoli létrehozás: A függőségek a requirements.txt fájl tartalma alapján távolról szerezhetők be. [A távoli létrehozás](functions-deployment-technologies.md#remote-build) az ajánlott létrehozási módszer. Távoli is az azure-eszközök alapértelmezett buildbeállítása. 
+ Helyi build: A függőségek helyibe bekéselése a requirements.txt fájl tartalma alapján. 
+ Egyéni függőségek: A projekt olyan csomagokat használ, amelyek nyilvánosan nem érhetők el az eszközeink számára. (Docker szükséges.)

A függőségek létrehozásához és a folyamatos kézbesítési (CD)rendszer használatával történő [közzétételhez használja az Azure Pipelines szolgáltatást.](functions-how-to-azure-devops.md)

### <a name="remote-build"></a>Távoli összeállítás

Alapértelmezés szerint az Azure Functions Core Tools távoli buildet kér, ha a következő [func azure functionapp közzétételi](functions-run-local.md#publish) parancsot használja a Python-projekt Azure-ban való közzétételéhez. 

```bash
func azure functionapp publish <APP_NAME>
```

Ne felejtse el lecserélni `<APP_NAME>` a függvényalkalmazás nevét az Azure-ban.

Az [Azure Functions Extension for Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) alapértelmezés szerint távoli buildet is kér. 

### <a name="local-build"></a>Helyi építés

Megakadályozhatja egy távoli build et a következő [func azure-közzétételi közzétételi](functions-run-local.md#publish) paranccsal a helyi buildel történő közzétételhez. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Ne felejtse el lecserélni `<APP_NAME>` a függvényalkalmazás nevét az Azure-ban. 

A `--build local` beállítás sal a projektfüggőségek a requirements.txt fájlból olvashatók be, és a függő csomagok helyileg töltődnek le és települnek. A projektfájlok és -függőségek a helyi számítógépről az Azure-ba kerülnek. Ez azt eredményezi, hogy egy nagyobb központi telepítési csomag töltődik fel az Azure-ba. Ha valamilyen okból a requirements.txt fájl függőségeit a Core Tools nem tudja beszerezni, akkor az egyéni függőségek beállítást kell használnia a közzétételhez. 

### <a name="custom-dependencies"></a>Egyéni függőségek

Ha a projekt olyan csomagokat használ, amelyek nyilvánosan nem érhetők el az \_ \_eszközeink számára, elérhetővé teheti azokat az alkalmazás számára az alkalmazás\_\_/.python_packages könyvtárba való behelyezésével. A közzététel előtt futtassa a következő parancsot a függőségek helyi telepítéséhez:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Egyéni függőségek használata esetén a `--no-build` közzétételi beállítást kell használnia, mivel már telepítette a függőségeket.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Ne felejtse el lecserélni `<APP_NAME>` a függvényalkalmazás nevét az Azure-ban.

## <a name="unit-testing"></a>Egység tesztelése

A Pythonban írt függvények a többi Python-kódhoz hasonlóan tesztelhetők szabványos tesztelési keretrendszerek használatával. A legtöbb kötés, lehetséges, hogy hozzon létre egy mock bemeneti objektum `azure.functions` létrehozásával egy példányát a megfelelő osztály a csomagból. Mivel [`azure.functions`](https://pypi.org/project/azure-functions/) a csomag nem érhető el azonnal, mindenképpen telepítse a `requirements.txt` fájlon keresztül a [csomagkezelés](#package-management) fenti szakaszában leírtak szerint. 

A következőkben például egy HTTP-aktivált függvény próbatesztje látható:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# __app__/HttpTrigger/__init__.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Íme egy másik példa, egy várólista által aktivált funkcióval:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```
## <a name="temporary-files"></a>Ideiglenes fájlok

A `tempfile.gettempdir()` metódus egy ideiglenes mappát `/tmp`ad vissza, amely Linux on . Az alkalmazás használhatja ezt a könyvtárat, hogy tárolja az ideiglenes fájlokat generált és használt a funkciók végrehajtása során. 

> [!IMPORTANT]
> Az ideiglenes könyvtárba írt fájlok nem garantáltan megmaradnak a meghívások között. A horizontális felskálázás során az ideiglenes fájlok nem lesznek megosztva a példányok között. 

A következő példa egy elnevezett ideiglenes`/tmp`fájlt hoz létre az ideiglenes könyvtárban ( ):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()   
   fp = tempfile.NamedTemporaryFile()     
   fp.write(b'Hello world!')              
   filesDirListInTemp = listdir(tempFilePath)     
```   

Javasoljuk, hogy a teszteket a projektmappától elkülönített mappában tartsa. Ez megakadályozza, hogy tesztkódot telepítsen az alkalmazással. 

## <a name="known-issues-and-faq"></a>Ismert problémák és gyakori kérdések

Az összes ismert problémát és szolgáltatáskérést a [GitHub-problémák](https://github.com/Azure/azure-functions-python-worker/issues) listája követi nyomon. Ha problémába ütközik, és nem találja a problémát a GitHubon, nyisson meg egy új problémát, és adja meg a probléma részletes leírását.

### <a name="cross-origin-resource-sharing"></a>Eltérő eredetű erőforrások megosztása

Az Azure Functions támogatja a források közötti erőforrás-megosztás (CORS). A CORS [a portálon](functions-how-to-use-azure-function-app-settings.md#cors) és az [Azure CLI-n](/cli/azure/functionapp/cors)keresztül van konfigurálva. A CORS engedélyezett eredetlista a függvényalkalmazás szintjén érvényes. Ha a CORS engedélyezve `Access-Control-Allow-Origin` van, a válaszok tartalmazzák a fejlécet. További információ: [Eltérő eredetű erőforrás-megosztás](functions-how-to-use-azure-function-app-settings.md#cors)

Az engedélyezett eredetlista [jelenleg nem támogatott](https://github.com/Azure/azure-functions-python-worker/issues/444) a Python-függvényalkalmazások. E miatt a korlátozás miatt kifejezetten be kell állítania a fejlécet a `Access-Control-Allow-Origin` HTTP-függvényekben, ahogy az a következő példában látható:

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

Győződjön meg arról, hogy a function.json t is frissíti a BEÁLLÍTÁSOK HTTP módszer ének támogatásához:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Ezt a HTTP-módszert használják a webböngészők az engedélyezett eredetlista egyeztetésére. 

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Az Azure Functions csomag API-dokumentációja](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions aktiválódik és kötéseket köt](functions-triggers-bindings.md)
* [Blob storage-kötések](functions-bindings-storage-blob.md)
* [HTTP- és Webhook-kötések](functions-bindings-http-webhook.md)
* [Várólista-tárolókötések](functions-bindings-storage-queue.md)
* [Időzítő eseményindító](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python

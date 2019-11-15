---
title: Python fejlesztői referenciája Azure Functions
description: Ismerje meg, hogyan fejlesztheti a függvényeket a Python használatával
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure functions, functions, Event Processing, dinamikus számítás, kiszolgáló nélküli architektúra, Python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 01d8560ee2752f21eb52c00f4c337d1dca59b8fb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082698"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python fejlesztői útmutató

Ez a cikk bemutatja, hogyan fejlesztheti Azure Functions a Python használatával. Az alábbi tartalom azt feltételezi, hogy már elolvasta a [Azure functions fejlesztői útmutatót](functions-reference.md). 

A Pythonban futó önálló függvények esetében tekintse meg a [Python függvények mintáit](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>A programozási modell

A Azure Functions egy olyan állapot nélküli metódust vár a Python-parancsfájlban, amely feldolgozza a bemenetet, és kimenetet hoz létre. Alapértelmezés szerint a futtatókörnyezet a metódust a `__init__.py` fájlban `main()` nevű globális metódusként kívánja megvalósítani. [Alternatív belépési pontot is megadhat](#alternate-entry-point).

Az eseményindítók és kötések adatai a Function *. JSON* fájlban megadott `name` tulajdonsággal vannak kötve a függvényhez. Az alábbi _function. JSON_ például egy `req`nevű HTTP-kérelem által aktivált egyszerű függvényt ír le:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

A `__init__.py` fájl a következő függvény kódját tartalmazza:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

a függvényben explicit módon deklarálhatja az attribútum típusát és a visszatérési típust is a Python típusú jegyzetek használatával. Ez segít a számos Python-kód-szerkesztő által biztosított IntelliSense-és automatikus kiegészítési funkciók használatában.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Használja az [Azure. functions. *](/python/api/azure-functions/azure.functions?view=azure-python) csomagban található Python-megjegyzéseket a bemenetek és kimenetek a metódusokhoz való kötéséhez.

## <a name="alternate-entry-point"></a>Másodlagos belépési pont

A függvények alapértelmezett viselkedését a *function. JSON* fájl `scriptFile` és `entryPoint` tulajdonságainak megadásával módosíthatja. Az alábbi _function. JSON_ például arra utasítja a futtatókörnyezetet, hogy a _Main.py_ -fájlban lévő `customentry()` metódust használja az Azure-függvény belépési pontjaként.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Mappa szerkezete

A Python functions projekt javasolt mappastruktúrát a következő példához hasonlóan néz ki:

```
 __app__
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 tests
```
A fő projekt mappája (\_\_alkalmazás\_\_) a következő fájlokat tartalmazhatja:

* *Local. Settings. JSON*: az Alkalmazásbeállítások és a kapcsolódási karakterláncok helyi futtatásakor való tárolásához használatos. Ez a fájl nem jelenik meg az Azure-ban. További információ: [Local. Settings. file](functions-run-local.md#local-settings-file).
* *követelmények. txt*: az Azure-ba való közzétételkor a rendszer által telepített csomagok listáját tartalmazza.
* a *Host. JSON*: olyan globális konfigurációs beállításokat tartalmaz, amelyek a Function alkalmazás összes funkcióját érintik. Ez a fájl közzé van téve az Azure-ban. Nem minden beállítás támogatott a helyi futtatásakor. További információ: [Host. JSON](functions-host-json.md).
* *funcignore*: (nem kötelező) deklarálja azokat a fájlokat, amelyek nem tudnak közzétenni az Azure-ban.
* *gitignore*: (nem kötelező) deklarálja a git-tárházból kizárt fájlokat, például a local. Settings. JSON fájlt.

Minden függvényhez tartozik a saját kód fájlja és a kötési konfigurációs fájl (function. JSON). 

A megosztott kódokat \_\_alkalmazás\_\_egy külön mappában kell tárolni. A következő szintaxissal hivatkozhat a SharedCode mappában található modulokra:

```python
from __app__.SharedCode import myFirstHelperFunction
```

A függvények helyi hivatkozásához a relatív importálási szintaxist a következőképpen használhatja:

```python
from . import example
```

Ha a projektet egy Azure-beli Function alkalmazásba helyezi üzembe, a *FunctionApp* mappa teljes tartalmát bele kell foglalni a csomagba, de a mappát nem. Javasoljuk, hogy a teszteket a Project mappától eltérő mappában tartsa karban, ebben a példában `tests`. Így a tesztelési kód üzembe helyezése az alkalmazással megtartható. További információ: [Unit Testing (egység tesztelése](#unit-testing)).

## <a name="triggers-and-inputs"></a>Eseményindítók és bemenetek

A bemenetek két kategóriára vannak osztva Azure Functions: aktiválja a bemenetet és a további bemenetet. Bár a `function.json` fájlban különböznek, a használat megegyezik a Python-kódban.  Az trigger és a bemeneti források kapcsolódási sztringje vagy titka a `local.settings.json` fájlban a helyileg futtatott értékekhez és az Azure-ban való futtatáskor az alkalmazás beállításaihoz rendelhető. 

A következő kód például a kettő közötti különbséget mutatja be:

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

A függvény meghívásakor a rendszer a HTTP-kérelmet `req`ként továbbítja a függvénynek. A rendszer beolvas egy bejegyzést az Azure _blob Storage az útvonal_ URL-címe alapján, és a függvény törzsében `obj`ként elérhetővé teszi.  Itt a megadott Storage-fiók a szolgáltatásban található, a Function alkalmazás által használt Storage-fiók.


## <a name="outputs"></a>Kimenetek

A kimenet a visszatérési értékben és a kimeneti paraméterekben is kifejezhető. Ha csak egy kimenet van, javasoljuk, hogy használja a visszatérési értéket. Több kimenet esetén a kimeneti paramétereket kell használnia.

Ha egy függvény visszatérési értékét egy kimeneti kötés értékeként szeretné használni, akkor a kötés `name` tulajdonságát a `function.json``$return` értékre kell állítani.

Több kimenet létrehozásához használja a [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) felület által biztosított `set()` metódust, hogy a kötéshez értéket rendeljen. A következő függvény például leküldheti az üzeneteket egy várólistába, és HTTP-választ is küldhet.

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

A Azure Functions futtatókörnyezet-naplózó hozzáférése a Function alkalmazás egyik gyökérszintű [`logging`](https://docs.python.org/3/library/logging.html#module-logging) kezelőjén keresztül érhető el. Ez a naplózó Application Insightshez van kötve, és lehetővé teszi a függvények végrehajtása során észlelt figyelmeztetések és hibák megjelölését.

Az alábbi példa egy tájékoztató üzenetet naplóz, ha a függvényt HTTP-eseményindítón keresztül hívja meg.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

További naplózási módszerek érhetők el, amelyek lehetővé teszik a konzolra való írást különböző nyomkövetési szinteken:

| Módszer                 | Leírás                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | KRITIKUS szintű üzenetet ír a gyökérszintű naplózó számára.  |
| **`error(_message_)`**   | A legfelső szintű naplózó üzenetbe írja a Level hibát.    |
| **`warning(_message_)`**    | Üzenet írása a root naplózó szintű FIGYELMEZTETÉSsel.  |
| **`info(_message_)`**    | A root naplózó szintű adatokat tartalmazó üzenetet ír.  |
| **`debug(_message_)`** | Egy szintű HIBAKERESÉSt tartalmazó üzenetet ír a root Logger-ben.  |

További információ a naplózásról: [Azure functions figyelése](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>HTTP-trigger és-kötések

A HTTP-trigger a function. Jon fájlban van definiálva. A kötés `name` meg kell egyeznie a függvény elnevezett paraméterével. Az előző példákban egy kötési nevet `req` használ a rendszer. Ez a paraméter egy [HttpRequest] objektum, és a rendszer egy [HttpResponse] objektumot ad vissza.

A [HttpRequest] objektumból lekérheti a kérések fejléceit, a lekérdezési paramétereket, az útvonal paramétereit és az üzenet törzsét. 

A következő példa a [Pythonhoz készült http trigger sablonból](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)származik. 

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

Ebben a függvényben a `name` lekérdezési paraméter értékét a [HttpRequest] objektum `params` paraméterében szerezheti be. A JSON-kódolású üzenet törzse a `get_json` metódus használatával olvasható. 

Hasonlóképpen beállíthatja a válaszüzenet `status_code`ét és `headers` a visszaadott [HttpResponse] objektumban.

## <a name="concurrency"></a>Egyidejűség

Alapértelmezés szerint a Python Runtime függvény egyszerre csak egy függvény hívását képes feldolgozni. Előfordulhat, hogy ez a párhuzamossági szint nem elegendő a következő feltételek közül egy vagy több esetében:

+ Egyszerre több meghívást próbál kezelni...
+ Nagy mennyiségű I/O-eseményt dolgoz fel.
+ Az alkalmazás I/O-kötésű.

Ezekben az esetekben a teljesítményt aszinkron módon, több nyelvi feldolgozói folyamat használatával növelheti.  

### <a name="async"></a>Aszinkron

Azt javasoljuk, hogy a `async def` utasítást használja, hogy a függvény aszinkron módon fusson.

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

Ha a `main()` függvény szinkron (a `async` minősítő nélkül), a függvény automatikusan egy `asyncio` szálban fut.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Több nyelvet használó munkavégző folyamat használata

Alapértelmezés szerint minden functions Host-példány egyetlen nyelvi munkavégző folyamattal rendelkezik. A gazdagép-példányok esetében azonban több nyelvi feldolgozói folyamat is támogatott. A függvények meghívásai ezután egyenletesen eloszthatók a nyelvi munkavégző folyamatok között. Ennek az értéknek a megváltoztatásához használja a [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) alkalmazás beállítását. 

## <a name="context"></a>Környezet

Ha egy függvény hívási környezetét szeretné lekérni a végrehajtás során, adja meg az [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) argumentumot az aláírásában. 

Például:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A [**környezeti**](/python/api/azure-functions/azure.functions.context?view=azure-python) osztály a következő karakterlánc-attribútumokkal rendelkezik:

`function_directory`  
Az a címtár, amelyben a függvény fut.

`function_name`  
A függvény neve.

`invocation_id`  
Az aktuális függvény meghívásának azonosítója.

## <a name="global-variables"></a>Globális változók

Nem garantált, hogy az alkalmazás állapota továbbra is megmarad a jövőbeli végrehajtásokhoz. Azonban a Azure Functions futtatókörnyezet gyakran ugyanazt a folyamatot használja ugyanazon alkalmazás több végrehajtásához. Egy költséges számítás eredményeinek gyorsítótárazásához globális változóként deklarálja. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Környezeti változók

A függvények területen az [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatási kapcsolatok karakterláncai környezeti változókként vannak kitéve a végrehajtás során. Ezeket a beállításokat a `import os` deklarálása, majd a `setting = os.environ["setting-name"]`használatával érheti el.

A következő példa beolvassa az [Alkalmazásbeállítások értékét](functions-how-to-use-azure-function-app-settings.md#settings)a `myAppSetting`nevű kulccsal:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Helyi fejlesztés esetén az Alkalmazásbeállítások a [Local. Settings. JSON fájlban maradnak](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Python-verzió 

A Azure Functions jelenleg a Python 3.6. x és 3.7. x (hivatalos CPython-eloszlások) használatát támogatja. Helyileg futtatva a futtatókörnyezet a rendelkezésre álló Python-verziót használja. Ha egy adott Python-verziót szeretne kérni a Function alkalmazás Azure-ban való létrehozásakor, használja a [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) parancs `--runtime-version` lehetőségét.  

## <a name="package-management"></a>Csomagkezelés

Amikor helyileg fejleszti a Azure Functions Core Tools vagy a Visual Studio Code-ot, adja hozzá a szükséges csomagok neveit és verzióit a `requirements.txt`-fájlhoz, és telepítse őket a `pip`használatával. 

Például az alábbi követelmények fájl és pip parancs használatával telepítheti a `requests` csomagot a PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Közzététel az Azure-ban

Ha készen áll a közzétételre, győződjön meg arról, hogy az összes nyilvánosan elérhető függőség szerepel a Project könyvtárának gyökérkönyvtárában található követelmények. txt fájlban. 

A közzétételből kizárt projektfájlok és mappák, beleértve a virtuális környezet mappáját is, a. funcignore fájlban vannak felsorolva.

A Python-projekt az Azure-ba való közzétételéhez három Build művelet támogatott:

+ Távoli Build: a függőségek a követelmények. txt fájl tartalmától függően távolról szerezhetők be. A [távoli Build](functions-deployment-technologies.md#remote-build) a javasolt Build módszer. A távoli az Azure-eszközök alapértelmezett Build-beállítása is. 
+ Helyi Build: a függőségek a követelmények. txt fájl tartalmától függően helyileg szerezhetők be. 
+ Egyéni függőségek: a projekt nem nyilvánosan elérhető csomagokat használ az eszközeink számára. (A Docker szükséges.)

A függőségek létrehozásához és a folyamatos kézbesítés (CD) rendszer használatával történő közzétételhez [használja az Azure-folyamatokat](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Távoli Build

Alapértelmezés szerint a Azure Functions Core Tools távoli buildet kér, amikor az alábbi, az [Azure functionapp Publishing](functions-run-local.md#publish) parancs használatával teszi közzé a Python-projektet az Azure-ban. 

```bash
func azure functionapp publish <APP_NAME>
```

Ne felejtse el lecserélni a `<APP_NAME>`t az Azure-beli Function alkalmazás nevével.

A [Visual Studio Code Azure functions-bővítménye](functions-create-first-function-vs-code.md#publish-the-project-to-azure) alapértelmezés szerint távoli buildet is igényel. 

### <a name="local-build"></a>Helyi Build

A távoli buildek használatának megakadályozása érdekében az alábbi, az [Azure functionapp publish](functions-run-local.md#publish) parancs használatával teheti közzé a helyi buildet. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Ne felejtse el lecserélni a `<APP_NAME>`t az Azure-beli Function alkalmazás nevével. 

A `--build local` lehetőség használatával a Project függőségei a követelmények. txt fájlból származnak, és ezek a függő csomagok letöltése és telepítése helyileg történik. A Project Files és a függőségek a helyi számítógépről az Azure-ba vannak telepítve. Ennek eredményeképpen egy nagyobb üzembehelyezési csomag tölthető fel az Azure-ba. Ha valamilyen okból kifolyólag, a követelmények. txt fájlhoz tartozó függőségek nem szerezhetők meg a Core Tools által, az egyéni függőségek lehetőséget kell használni a közzétételhez. 

### <a name="custom-dependencies"></a>Egyéni függőségek

Ha a projekt nem nyilvánosan elérhető csomagokat használ az eszközeink számára, elérhetővé teheti őket az alkalmazás számára, ha a \_\_alkalmazást\_\_/. python_packages könyvtárat. A közzététel előtt futtassa a következő parancsot a függőségek helyi telepítéséhez:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Egyéni függőségek használata esetén a `--no-build` közzétételi lehetőséget kell használnia, mivel már telepítette a függőségeket.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Ne felejtse el lecserélni a `<APP_NAME>`t az Azure-beli Function alkalmazás nevével.

## <a name="unit-testing"></a>Egység tesztelése

A Pythonban írt függvények a standard szintű tesztelési keretrendszerek használatával más Python-kódokhoz hasonlóan is vizsgálhatók. A legtöbb kötés esetében lehetséges, hogy létrehoz egy ál-bemeneti objektumot egy megfelelő osztály egy példányának létrehozásával a `azure.functions` csomagból. Mivel a [`azure.functions`](https://pypi.org/project/azure-functions/) csomag nem érhető el azonnal, a fenti [csomagkezelő](#package-management) részben leírtak szerint telepítse azt a `requirements.txt`-fájlon keresztül. 

Például a következő egy HTTP által aktivált függvény mintájának tesztelése:

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

Íme egy másik példa, amely egy üzenetsor által aktivált függvényt mutat be:

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

A `tempfile.gettempdir()` metódus egy ideiglenes mappát ad vissza, amely Linux rendszeren `/tmp`. Az alkalmazás ezt a könyvtárat használhatja a függvények által a végrehajtás során létrehozott és használt ideiglenes fájlok tárolására. 

> [!IMPORTANT]
> Az ideiglenes könyvtárba írt fájlok nem garantáltan megmaradnak a meghívások között. A Kibővítés során az ideiglenes fájlok nincsenek megosztva a példányok között. 

Az alábbi példa egy névvel ellátott ideiglenes fájlt hoz létre az ideiglenes könyvtárban (`/tmp`):

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

Javasoljuk, hogy a teszteket egy, a Project mappától eltérő mappában tartsa karban. Így a tesztelési kód üzembe helyezése az alkalmazással megtartható. 

## <a name="known-issues-and-faq"></a>Ismert problémák és gyakori kérdések

Az összes ismert probléma és szolgáltatás kérését a [GitHub-problémák](https://github.com/Azure/azure-functions-python-worker/issues) listája követheti nyomon. Ha probléma lép fel, és a GitHubon nem találja a problémát, nyisson meg egy új problémát, és adja meg a probléma részletes leírását.

### <a name="cross-origin-resource-sharing"></a>Eltérő eredetű erőforrások megosztása

A Azure Functions támogatja a több eredetű erőforrás-megosztást (CORS). A CORS a [portálon](functions-how-to-use-azure-function-app-settings.md#cors) és az [Azure CLI](/cli/azure/functionapp/cors)-n keresztül van konfigurálva. A CORS engedélyezett Origins listája a függvény alkalmazás szintjén érvényes. Ha a CORS engedélyezve van, a válaszok közé tartozik a `Access-Control-Allow-Origin` fejléc. További információ: [Eltérő eredetű erőforrás-megosztás](functions-how-to-use-azure-function-app-settings.md#cors)

Az engedélyezett Origins lista [jelenleg nem támogatott](https://github.com/Azure/azure-functions-python-worker/issues/444) a Python-függvények alkalmazásaiban. Ennek a korlátozásnak a miatt a HTTP-függvények `Access-Control-Allow-Origin` fejlécét kifejezetten a következő példában látható módon kell beállítania:

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

Győződjön meg arról, hogy a function. JSON fájlt is frissíti a beállítások HTTP-metódusának támogatásához:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Ezt a módszert a Chrome böngésző használja az engedélyezett Origins lista egyeztetéséhez. 

## <a name="next-steps"></a>Következő lépések

További információkért lásd a következőket:

* [Azure Functions csomag API-dokumentációja](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)
* [BLOB Storage-kötések](functions-bindings-storage-blob.md)
* [HTTP-és webhook-kötések](functions-bindings-http-webhook.md)
* [Üzenetsor-tárolási kötések](functions-bindings-storage-queue.md)
* [Időzítő eseményindító](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python

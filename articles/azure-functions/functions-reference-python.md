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
ms.openlocfilehash: 88d9ab0063b6cf2803332d7af50190c659b3e6fe
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207207"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python fejlesztői útmutató

Ez a cikk bemutatja, hogyan fejlesztheti Azure Functions a Python használatával. Az alábbi tartalom azt feltételezi, hogy már elolvasta a [Azure functions fejlesztői útmutatót](functions-reference.md). 

A Pythonban futó önálló függvények esetében tekintse meg a [Python függvények mintáit](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>A programozási modell

A Azure Functions egy olyan állapot nélküli metódust vár a Python-parancsfájlban, amely feldolgozza a bemenetet, és kimenetet hoz létre. Alapértelmezés szerint a futtatókörnyezet azt várja, hogy a metódus a fájlban megadott `main()` `__init__.py` globális metódusként legyen implementálva. [Alternatív belépési pontot](#alternate-entry-point)is megadhat.

Az eseményindítók és kötések adatai a Function `name` *. JSON* fájlban megadott tulajdonság használatával a metódus attribútumain keresztül vannak kötve. Az alábbi _function. JSON_ például a következő nevű `req`HTTP-kérelem által aktivált egyszerű függvényt írja le:

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

A függvények alapértelmezett viselkedését megváltoztathatja, ha a *function. JSON* fájlban `scriptFile` a `entryPoint` és a tulajdonságokat is megadja. Az alábbi _function. JSON_ például arra utasítja a futtatókörnyezetet, hogy az `customentry()` _Main.py_ -fájlban lévő metódust használja az Azure-függvény belépési pontjaként.

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

A Python functions projekthez tartozó mappastruktúrát a következő példához hasonlóan néz ki:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
```

Létezik egy megosztott [Host. JSON](functions-host-json.md) fájl, amely a Function alkalmazás konfigurálására használható. Minden függvényhez tartozik a saját kód fájlja és a kötési konfigurációs fájl (function. JSON). 

A megosztott kódokat külön mappában kell tárolni. A következő szintaxissal hivatkozhat a SharedCode mappában található modulokra:

```
from __app__.SharedCode import myFirstHelperFunction
```

Ha egy Function-projektet telepít az Azure-beli Function alkalmazásba, a *FunctionApp* mappa teljes tartalmát bele kell foglalni a csomagba, de a mappát nem.

## <a name="triggers-and-inputs"></a>Eseményindítók és bemenetek

A bemenetek két kategóriára vannak osztva Azure Functions: aktiválja a bemenetet és a további bemenetet. Bár a fájlban különböznek, `function.json` a használat megegyezik a Python-kóddal.  Az trigger és a bemeneti források kapcsolódási sztringje vagy titka a `local.settings.json` fájlban a helyi futtatásakor, valamint az Azure-ban való futtatáskor az alkalmazás beállításainak megfelelően képezhető le. 

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

A függvény meghívásakor a rendszer a HTTP-kérelmet a következőként továbbítja a `req`függvénynek:. A rendszer beolvas egy bejegyzést az Azure-Blob Storage az útvonal URL -címének azonosítója alapján, és a `obj` függvény törzsében elérhetővé teszi őket.  Itt a megadott Storage- `AzureWebJobsStorage` fiók a függvény alkalmazásban használt tárolási fiók.


## <a name="outputs"></a>Kimenetek

A kimenet a visszatérési értékben és a kimeneti paraméterekben is kifejezhető. Ha csak egy kimenet van, javasoljuk, hogy használja a visszatérési értéket. Több kimenet esetén a kimeneti paramétereket kell használnia.

Egy függvény visszatérési értékének egy kimeneti kötés `name` értékeként való használatához a kötés tulajdonságát a `$return` következő értékre kell beállítani: `function.json`.

Több kimenet létrehozásához használja `set()` a [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) csatoló által biztosított metódust, amellyel egy értéket rendelhet a kötéshez. A következő függvény például leküldheti az üzeneteket egy várólistába, és HTTP-választ is küldhet.

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

A Azure functions futtatókörnyezet-naplózó hozzáférése a Function alkalmazás [`logging`](https://docs.python.org/3/library/logging.html#module-logging) egyik gyökérszintű kezelőjén keresztül érhető el. Ez a naplózó Application Insightshez van kötve, és lehetővé teszi a függvények végrehajtása során észlelt figyelmeztetések és hibák megjelölését.

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

A HTTP-trigger a function. Jon fájlban van definiálva. `name` A kötésnek meg kell egyeznie a függvény elnevezett paraméterével. Az előző példákban egy kötési nevet `req` használunk. Ez a paraméter egy [HttpRequest] objektum, és a rendszer egy [HttpResponse] objektumot ad vissza.

A [HttpRequest] objektumból lekérheti a kérések fejléceit, a lekérdezési paramétereket, az útvonal paramétereit és az üzenet törzsét. 

A következő példa a Pythonhoz készült [http trigger sablonból](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)származik. 

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

Ebben a függvényben a `name` lekérdezési paraméter értékét a rendszer a [HttpRequest] objektum `params` paraméterében szerzi be. A JSON-kódolású üzenet törzse a `get_json` metódus használatával olvasható. 

Hasonlóképpen beállíthatja `status_code` a és `headers` a válaszüzenetet is a visszaadott [HttpResponse] objektumban.
                                                              
## <a name="async"></a>Aszinkron

Javasoljuk, hogy az `async def` utasítás használatával írja be az Azure-függvényt aszinkronként.

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

Ha a Main () függvény szinkron (nincs minősítő), automatikusan futtatja a függvényt egy `asyncio` szál-készletben.

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Környezet

Ha egy függvény hívási környezetét szeretné lekérni a végrehajtás során, [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) adja meg az argumentumot az aláírásában. 

Példa:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A [**környezeti**](/python/api/azure-functions/azure.functions.context?view=azure-python) osztály a következő módszerekkel rendelkezik:

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

A függvények területen az [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatási kapcsolatok karakterláncai környezeti változókként vannak kitéve a végrehajtás során. Ezek a beállítások deklarálva `import os` , `setting = os.environ["setting-name"]`majd a használatával érhetők el.

A következő példa beolvassa az [alkalmazás beállítását](functions-how-to-use-azure-function-app-settings.md#settings)a nevű `myAppSetting`kulccsal:

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

## <a name="python-version-and-package-management"></a>Python-verziók és-csomagok kezelése

Jelenleg a Azure Functions csak a Python 3.6. x (hivatalos CPython-eloszlás) használatát támogatja.

Amikor helyileg fejleszti a Azure functions Core Tools vagy a Visual Studio Code-ot, adja hozzá a szükséges csomagok neveit és `requirements.txt` verzióit a fájlhoz `pip`, és telepítse őket a használatával.

Például az alábbi követelmények fájl és pip parancs használatával telepítheti a `requests` csomagot a PyPI-ből.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Közzététel az Azure-ban

Ha készen áll a közzétételre, győződjön meg arról, hogy az összes függőség szerepel a Project könyvtárának gyökérkönyvtárában található *követelmények. txt* fájlban. A Azure Functions képes [távolról](functions-deployment-technologies.md#remote-build) felépíteni ezeket a függőségeket.

A közzétételből kizárt projektfájlok és mappák, beleértve a virtuális környezet mappáját is, a. funcignore fájlban vannak felsorolva.  

Az Azure-ba történő üzembe helyezéshez és a távoli buildek elvégzéséhez használja a következő parancsot:

```bash
func azure functionapp publish <app name> --build remote
```

Ha nem használ távoli buildet, és olyan csomagot használ, amely egy fordítót igényel, és nem támogatja számos, a Linux-kompatibilis kerekek PyPI való telepítését, az Azure-ba való helyi létrehozás nélküli közzététel sikertelen lesz a következő hiba miatt:

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Ha helyileg szeretné kiépíteni és konfigurálni a szükséges bináris fájlokat, [telepítse](https://docs.docker.com/install/) a Docker-t a helyi gépre, és futtassa a következő parancsot a [Azure functions Core Tools](functions-run-local.md#v2) (a) használatával történő közzétételhez. Ne felejtse `<app name>` el lecserélni a Function alkalmazás nevét az Azure-ban. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

A borítók alatt a központi eszközök a Docker használatával futtatják a [MCR.microsoft.com/Azure-functions/Python](https://hub.docker.com/r/microsoft/azure-functions/) -rendszerképet a helyi gépen lévő tárolóként. Ezzel a környezettel az Azure-ba történő végső üzembe helyezés előtt felépíti és telepíti a szükséges modulokat a forrás-elosztásból.

A függőségek létrehozásához és a folyamatos kézbesítés (CD) rendszer használatával történő közzétételhez [használja az Azure](functions-how-to-azure-devops.md)-folyamatokat. 

## <a name="unit-testing"></a>Egység tesztelése

A Pythonban írt függvények a standard szintű tesztelési keretrendszerek használatával más Python-kódokhoz hasonlóan is vizsgálhatók. A legtöbb kötés esetében lehetséges, hogy létrehoz egy modell típusú bemeneti objektumot úgy, hogy létrehoz egy megfelelő osztály egy példányát a `azure.functions` csomagból. Mivel a [`azure.functions`](https://pypi.org/project/azure-functions/) csomag nem érhető el azonnal, ne felejtse el telepíteni a `requirements.txt` fájlon keresztül, a fenti [Python-verzió és csomag kezelése](#python-version-and-package-management) részben leírtak szerint.

Például a következő egy HTTP által aktivált függvény mintájának tesztelése:

```json
{
  "scriptFile": "httpfunc.py",
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
# myapp/httpfunc.py
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
# myapp/test_httpfunc.py
import unittest

import azure.functions as func
from httpfunc import my_function


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

```python
# myapp/__init__.py
import azure.functions as func


def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


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

## <a name="known-issues-and-faq"></a>Ismert problémák és gyakori kérdések

Az összes ismert probléma és szolgáltatás kérését a [GitHub-problémák](https://github.com/Azure/azure-functions-python-worker/issues) listája követheti nyomon. Ha probléma lép fel, és a GitHubon nem találja a problémát, nyisson meg egy új problémát, és adja meg a probléma részletes leírását.

### <a name="cross-origin-resource-sharing"></a>Eltérő eredetű erőforrások megosztása

A Azure Functions támogatja a több eredetű erőforrás-megosztást (CORS). A CORS a Portálon és az [Azure CLI](/cli/azure/functionapp/cors) [-](functions-how-to-use-azure-function-app-settings.md#cors) n keresztül van konfigurálva. A CORS engedélyezett Origins listája a függvény alkalmazás szintjén érvényes. Ha a CORS engedélyezve van, a `Access-Control-Allow-Origin` válaszok tartalmazzák a fejlécet. További információ: [Eltérő eredetű erőforrás-megosztás](functions-how-to-use-azure-function-app-settings.md#cors)

Az engedélyezett Origins lista [jelenleg nem támogatott](https://github.com/Azure/azure-functions-python-worker/issues/444) a Python-függvények alkalmazásaiban. Ennek a korlátozásnak a miatt a http-függvények `Access-Control-Allow-Origin` fejlécét kifejezetten be kell állítania az alábbi példában látható módon:

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

## <a name="next-steps"></a>További lépések

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

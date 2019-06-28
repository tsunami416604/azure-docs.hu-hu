---
title: Python fejlesztői segédanyagok az Azure Functions szolgáltatáshoz
description: Megismerheti, hogyan hozhat létre a functions és a Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra, python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 249e5ac33b1420ada2cda45ea729471351f21adf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341996"
---
# <a name="azure-functions-python-developer-guide"></a>Az Azure Functions Python fejlesztői útmutatója

Ez a cikk bevezetést tapasztalatlan a Python használatával Azure Functions. Az alábbi tartalmat azt feltételezi, hogy Ön már elolvasta a [Azure Functions fejlesztői útmutató](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>A programozási modell

Egy Azure-függvényt egy állapot nélküli metódus az a Python-szkript, amely dolgozza fel a bemenetet és kimenetet kell lennie. Alapértelmezés szerint a futtatókörnyezet vár a metódus egy globális módszerként nevű végrehajtandó `main()` a a `__init__.py` fájlt.

Az alapértelmezett konfiguráció megadásával módosíthatja a `scriptFile` és `entryPoint` tulajdonságait a *function.json* fájl. Például a _function.json_ alábbi arra utasítja a modul használatához a `customentry()` metódus az a _main.py_ fájlt, az Azure-függvény belépési pontját.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Eseményindítók és kötések adatait a függvény segítségével módszert attribútumok keresztül van kötve a `name` meghatározott tulajdonság a *function.json* fájlt. Ha például a _function.json_ alábbi ismerteti egy egyszerű, HTTP-kérést nevű által aktivált függvényt `req`:

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

A `__init__.py` fájlt a következő függvény kódot tartalmazza:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Szükség esetén kihasználhatja az intellisense és a Kódszerkesztő által kínált automatikus kiegészítés szolgáltatásokkal, is deklarálja az attribútumtípusokat, és adja vissza a függvény típusa jegyzetek Python használatával. 

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Használja a Python-jegyzetek szerepel a [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) csomag bemeneti és kimeneti kötést létrehozni a módszereket.

## <a name="folder-structure"></a>gyökérmappa-szerkezetében

Egy Python-Functions-projektet a mappastruktúra a következőhöz hasonlóan néz ki:

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

Van egy megosztott [host.json](functions-host-json.md) fájlt, amely a függvényalkalmazás konfigurálása használható. Minden függvény rendelkezik a saját kód és kötési konfigurációs fájlt (function.json). 

Megosztott kód egy külön mappában kell tárolni. Modulok SharedCode mappában hivatkozni, használhatja a következő szintaxist:

```
from __app__.SharedCode import myFirstHelperFunction
```

A Functions-projekt telepítésekor a függvényalkalmazáshoz az Azure-ban, a teljes tartalmát a *FunctionApp* mappa szerepelnie kell a csomag, de nem maga a mappa.

## <a name="triggers-and-inputs"></a>Eseményindítók és a bemenetek

Bemenetei között meg vannak osztva az Azure Functions két kategóriába sorolhatók: triggerbemenete és további adatokat. Bár ezek eltérnek a `function.json` fájl (használat) megegyező Python-kódban.  Kapcsolati karakterláncok vagy a titkos kulcsokat a trigger és a bemeneti források szereplő értékek leképezése a `local.settings.json` fájlt a helyi futtatás során, és az alkalmazás beállításait, ha az Azure-ban futtatja. 

Ha például a következő kód bemutatja a különbség a kettő között:

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

Ha a függvény meghívása HTTP-kérelem a függvényt, átadott `req`. Egy bejegyzés az Azure Blob Storage alapján lehet beolvasni a _azonosító_ URL-útvonal és lehetőségként elérhetővé tegyen `obj` függvény törzsében.  Itt a storage-fiók megadva a kapcsolati karakterlánc található `AzureWebJobsStorage` Ez ugyanazt a tárfiókot a függvényalkalmazás által használt.


## <a name="outputs"></a>Kimenetek

Kimeneti visszatérési érték és a kimeneti paraméterek is lehet kifejezni. Ha csak egyetlen kimeneti, a visszaadott érték használatát javasoljuk. A több kimenetek kell használni a kimeneti paraméterek.

Használandó függvény visszatérési értéke egy kimeneti kötés értékét a `name` kell állítani a kötés tulajdonság `$return` a `function.json`.

Több kimeneteinek létrehozásához használja a `set()` metódus által biztosított a `azure.functions.Out` értéket rendel a kötési felületen. Például a következő függvényt egy üzenet le egy várólistába és elemkészlet is visszaadható HTTP-választ.

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

Az Azure Functions runtime naplózó elérését a legfelső szintű keresztül érhető el [ `logging` ](https://docs.python.org/3/library/logging.html#module-logging) kezelő a függvényalkalmazásban. A naplózó vannak kötve, az Application Insights, és lehetővé teszi, hogy jelző figyelmeztetéseket és hibákat észlelt a függvény végrehajtása során.

Az alábbi példa egy tájékoztató üzenet naplózza, ha a függvény meghívása HTTP-trigger használatával.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

További naplózás módszerek érhetők el, amelyek a konzol különböző szintű nyomkövetésekkel írt segítségével:

| Módszer                 | Leírás                                |
| ---------------------- | ------------------------------------------ |
| a naplózás. **kritikus (_üzenet_)**   | A kritikus szintű üzenetet ír a legfelső szintű naplózó meg.  |
| a naplózás. **hiba (_üzenet_)**   | Szolgáltatói hiba miatt üzenetet ír be a legfelső szintű naplózó.    |
| a naplózás. **figyelmeztetés (_üzenet_)**    | A legfelső szintű naplózó szintű figyelmeztető üzenet írása.  |
| a naplózás. **info (_üzenet_)**    | A legfelső szintű naplózó a szolgáltatói információval üzenetet ír.  |
| a naplózás. **hibakeresés (_üzenet_)** | A legfelső szintű naplózó szintű hibakeresési üzenet írása.  |

## <a name="async"></a>Az aszinkron

Azt javasoljuk, hogy az Azure-függvényét, egy aszinkron coroutine a ír a `async def` utasítást.

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

Ha a main() függvény szinkron (nincs `async` minősítő) automatikusan futtatjuk a függvény egy `asyncio` szálkészlet.

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Környezet

Egy függvény meghívási kontextusában első végrehajtása közben, adjon meg a `context` jeho signatura argumentumot. 

Példa:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A **környezet** osztály rendelkezik a következőkkel:

`function_directory`  
A könyvtár, amelyben a függvény fut-e.

`function_name`  
A függvény nevét.

`invocation_id`  
Az aktuális függvény meghívási azonosítója.

## <a name="global-variables"></a>A globális változók

Nem garantált, hogy az alkalmazás állapotát a későbbi végrehajtások megőrzi. Az Azure Functions runtime azonban gyakran több végrehajtások ugyanabból az alkalmazásból a ugyanezt az eljárást használ újra. Annak érdekében, hogy az eredményeket egy költséges számítási gyorsítótárazni, deklarálható mint egy globális változó. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="python-version-and-package-management"></a>Python-verzió és a csomagmegosztás felügyeleti

Jelenleg az Azure Functions csak támogatja Python 3.6.x (hivatalos CPython elosztás).

Amikor helyileg a az Azure Functions Core Tools vagy a Visual Studio Code használatával történő fejlesztéséhez, adja hozzá a nevét és a szükséges csomagok verzióit a `requirements.txt` fájlt, és telepítse őket használatával `pip`.

Például a következő követelmények fájl és a pip parancs segítségével telepítse a `requests` PyPI-csomagot.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Közzététel az Azure-bA

Amikor elkészült, közzétételére, győződjön meg arról, hogy az összes függősége az szerepel a *requirements.txt* fájlt, amely a projektkönyvtárba gyökérmappájában található. Ha egy csomagot, amely fordító szükséges hozzá, és nem támogatja a telepítést a PyPI manylinux-kompatibilis kerekek használ, a közzététel az Azure-bA sikertelen lesz, és a következő hiba: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Automatikusan hozhat létre és konfigurálja a szükséges bináris fájlokat [telepítheti a Dockert](https://docs.docker.com/install/) a helyi gépre, és futtassa a következő parancsot a közzétenni a [Azure Functions Core Tools](functions-run-local.md#v2) (func). Ne felejtse el kicserélni `<app name>` az Azure-ban a függvényalkalmazás nevére. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

A háttérben Core Tools használatával docker futtatása a [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) rendszerképet a helyi gépen tárolójaként. Ebben a környezetben használja, akkor Ezután létrehozhatja és forrás terjesztési, mielőtt végleges üzembe helyezés az Azure szolgáltatásra csomagolás őket az Azure a szükséges modulok telepítéséhez.

A függőségek létrehozása és közzététele a folyamatos továbbítás (CD) rendszert, használ [használata az Azure DevOps-folyamatok](https://docs.microsoft.com/azure/azure-functions/functions-how-to-azure-devops). 

## <a name="unit-testing"></a>Egységtesztelés

Pythonban írt funkciók tesztelhető legyen, mint más szokásos tesztelési keretrendszereket használó Python-kód. A legtöbb kötések, hozzon létre egy utánzatként funkcionáló bemeneti objektumot a megfelelő osztály egy példányát lehetőség a `azure.functions` csomagot.

Például a következő egy HTTP által aktivált függvény utánzatként funkcionáló tesztje:

```python
# myapp/__init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/my_function',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            'Hello, Test!',
        )
```

Íme egy másik példa, az üzenetsor által aktivált függvény:

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

Ismert problémák és a szolgáltatással kapcsolatos kéréseit a rendszer nyomon követi használatával [GitHub-problémák](https://github.com/Azure/azure-functions-python-worker/issues) listája. Ha problémába ütközik, és nem találja a problémát a Githubban, nyisson egy új problémát, és a hiba részletes leírását tartalmazza.

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)
* [BLOB storage-kötések](functions-bindings-storage-blob.md)
* [HTTP- és webhookkötések](functions-bindings-http-webhook.md)
* [Queue storage-kötések](functions-bindings-storage-queue.md)
* [Időzítő eseményindító](functions-bindings-timer.md)

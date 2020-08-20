---
title: Python fejlesztői referenciája Azure Functions
description: Ismerje meg, hogyan fejlesztheti a függvényeket a Python használatával
ms.topic: article
ms.date: 12/13/2019
ms.custom: devx-track-python
ms.openlocfilehash: f9b81a7263dc9a1bdae9fd881519ac734da2c6bc
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642197"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python fejlesztői útmutató

Ez a cikk bemutatja, hogyan fejlesztheti Azure Functions a Python használatával. Az alábbi tartalom azt feltételezi, hogy már elolvasta a [Azure functions fejlesztői útmutatót](functions-reference.md).

A Pythonban futó önálló függvények esetében tekintse meg a [Python függvények mintáit](/samples/browse/?products=azure-functions&languages=python).

## <a name="programming-model"></a>Programozási modell

A Azure Functions egy olyan állapot nélküli metódust vár a Python-parancsfájlban, amely feldolgozza a bemenetet, és kimenetet hoz létre. Alapértelmezés szerint a futtatókörnyezet azt várja, hogy a metódus a fájlban megadott globális metódusként legyen implementálva `main()` `__init__.py` . [Alternatív belépési pontot is megadhat](#alternate-entry-point).

Az eseményindítók és kötések adatai a metódus attribútumain keresztül a függvényhez vannak kötve a `name` *function.js* fájlban megadott tulajdonság használatával. Az alábbi  _function.js_ például egy, a nevű HTTP-kérelem által aktivált egyszerű függvényt ismertet `req` :

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

A definíció alapján a `__init__.py` függvény kódját tartalmazó fájl a következő példához hasonló lehet:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

A függvényben explicit módon deklarálhatja az attribútum típusát és a visszatérési típust is a Python típusú jegyzetek használatával. Ez segít a számos Python-kód-szerkesztő által biztosított IntelliSense-és automatikus kiegészítési funkciók használatában.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Használja az [Azure. functions. *](/python/api/azure-functions/azure.functions?view=azure-python) csomagban található Python-megjegyzéseket a bemenetek és kimenetek a metódusokhoz való kötéséhez.

## <a name="alternate-entry-point"></a>Másodlagos belépési pont

A függvények alapértelmezett viselkedését megváltoztathatja, ha a és a tulajdonságokat is `scriptFile` Megadja `entryPoint` a *function.js* fájljában. Az alábbi _function.js_ például azt jelzi, hogy a futtatókörnyezet a `customentry()` metódust használja a _Main.py_ fájlban, az Azure-függvény belépési pontja.

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
 | - Dockerfile
 tests
```
A fő projekt mappája ( \_ \_ alkalmazás \_ \_ ) a következő fájlokat tartalmazza:

* *local.settings.json*: az Alkalmazásbeállítások és a kapcsolódási karakterláncok helyi futtatásakor való tárolásához használatos. Ez a fájl nem jelenik meg az Azure-ban. További információ: [Local. Settings. file](functions-run-local.md#local-settings-file).
* *requirements.txt*: azon csomagok listáját tartalmazza, amelyeket a rendszeren az Azure-ba való közzétételkor telepít.
* *host.json*: olyan globális konfigurációs beállításokat tartalmaz, amelyek a Function alkalmazás összes funkcióját érintik. Ez a fájl közzé van téve az Azure-ban. Nem minden beállítás támogatott a helyi futtatásakor. További információ: [host.json](functions-host-json.md).
* *. funcignore*: (nem kötelező) deklarálja azokat a fájlokat, amelyek nem tudnak közzétenni az Azure-ban.
* *Docker*: (nem kötelező) a projekt [Egyéni tárolóban](functions-create-function-linux-custom-image.md)történő közzétételekor használatos.

Minden függvényhez tartozik a saját kód fájlja és a kötési konfigurációs fájl (function.js).

Ha a projektet egy Azure-beli Function alkalmazásba helyezi üzembe, a fő projekt (* \_ \_ alkalmazás \_ \_ *) mappájának teljes tartalmát bele kell foglalni a csomagba, de nem maga a mappa. Javasoljuk, hogy a teszteket a Project mappától eltérő mappában tartsa karban, ebben a példában `tests` . Így a tesztelési kód üzembe helyezése az alkalmazással megtartható. További információ: [Unit Testing (egység tesztelése](#unit-testing)).

## <a name="import-behavior"></a>Importálási viselkedés

A függvény kódjában lévő modulokat explicit relatív és abszolút referenciák használatával is importálhatja. A fent látható mappa szerkezete alapján a következő importálások működnek a Function file * \_ \_ app \_ \_ My \_ első \_ függvényében: \\ _ \_ init \_ \_ .* a (z)

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

A következő importálások *nem működnek* ugyanazon a fájlon belül:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

A megosztott kódokat az * \_ \_ alkalmazás \_ \_ *egy külön mappájába kell megőrizni. A *megosztott \_ kód* mappában található modulok hivatkozásait a következő szintaxissal végezheti el:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Eseményindítók és bemenetek

A bemenetek két kategóriára vannak osztva Azure Functions: aktiválja a bemenetet és a további bemenetet. Bár a fájlban különböznek `function.json` , a használat megegyezik a Python-kóddal.  Az trigger és a bemeneti források kapcsolódási sztringje vagy titka a `local.settings.json` fájlban a helyi futtatásakor, valamint az Azure-ban való futtatáskor az alkalmazás beállításainak megfelelően képezhető le.

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

A függvény meghívásakor a rendszer a HTTP-kérelmet a következőként továbbítja a függvénynek: `req` . A rendszer beolvas egy bejegyzést az Azure-Blob Storage az útvonal URL-címének _azonosítója_ alapján, és a `obj` függvény törzsében elérhetővé teszi őket.  Itt a megadott Storage-fiók a AzureWebJobsStorage alkalmazás-beállításban található, a Function alkalmazás által használt Storage-fiók.


## <a name="outputs"></a>Kimenetek

A kimenet a visszatérési értékben és a kimeneti paraméterekben is kifejezhető. Ha csak egy kimenet van, javasoljuk, hogy használja a visszatérési értéket. Több kimenet esetén a kimeneti paramétereket kell használnia.

Egy függvény visszatérési értékének egy kimeneti kötés értékeként való használatához a `name` kötés tulajdonságát a következő értékre kell beállítani `$return` : `function.json` .

Több kimenet létrehozásához használja a `set()` csatoló által biztosított metódust, [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) amellyel egy értéket rendelhet a kötéshez. A következő függvény például leküldheti az üzeneteket egy várólistába, és HTTP-választ is küldhet.

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

A Azure Functions futtatókörnyezet-naplózó hozzáférése a Function alkalmazás egyik gyökérszintű kezelőjén keresztül érhető el [`logging`](https://docs.python.org/3/library/logging.html#module-logging) . Ez a naplózó Application Insightshez van kötve, és lehetővé teszi a függvények végrehajtása során észlelt figyelmeztetések és hibák megjelölését.

Az alábbi példa egy tájékoztató üzenetet naplóz, ha a függvényt HTTP-eseményindítón keresztül hívja meg.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

További naplózási módszerek érhetők el, amelyek lehetővé teszik a konzolra való írást különböző nyomkövetési szinteken:

| Metódus                 | Leírás                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | KRITIKUS szintű üzenetet ír a gyökérszintű naplózó számára.  |
| **`error(_message_)`**   | A legfelső szintű naplózó üzenetbe írja a Level hibát.    |
| **`warning(_message_)`**    | Üzenet írása a root naplózó szintű FIGYELMEZTETÉSsel.  |
| **`info(_message_)`**    | A root naplózó szintű adatokat tartalmazó üzenetet ír.  |
| **`debug(_message_)`** | Egy szintű HIBAKERESÉSt tartalmazó üzenetet ír a root Logger-ben.  |

További információ a naplózásról: [Azure functions figyelése](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>HTTP-trigger és-kötések

A HTTP-trigger a fájl function.jsban van definiálva. A `name` kötésnek meg kell egyeznie a függvény elnevezett paraméterével.
Az előző példákban egy kötési nevet `req` használunk. Ez a paraméter egy [HttpRequest] objektum, és a rendszer egy [HttpResponse] objektumot ad vissza.

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

Ebben a függvényben a lekérdezési paraméter értékét a rendszer a `name` `params` [HttpRequest] objektum paraméterében szerzi be. A JSON-kódolású üzenet törzse a metódus használatával olvasható `get_json` .

Hasonlóképpen beállíthatja a és a `status_code` `headers` válaszüzenetet is a visszaadott [HttpResponse] objektumban.

## <a name="scaling-and-concurrency"></a>Skálázás és Egyidejűség

Alapértelmezés szerint a Azure Functions automatikusan figyeli az alkalmazás terhelését, és szükség esetén további gazdagép-példányokat hoz létre a Pythonhoz. A functions beépített (nem a felhasználó által konfigurálható) küszöbértékeket használ a különböző típusú triggerekhez, hogy eldöntse, mikor kell hozzáadnia a példányokat, például az üzenetek korát és a várólista méretét a QueueTrigger. További információ: [How the fogyasztás and Premium Plans Work](functions-scale.md#how-the-consumption-and-premium-plans-work).

Ez a skálázási viselkedés számos alkalmazás esetében elegendő. A következő jellemzők bármelyikével rendelkező alkalmazások esetében azonban nem lehet hatékonyan méretezni:

- Az alkalmazásnak számos egyidejű hívást kell kezelnie.
- Az alkalmazás nagy mennyiségű I/O-eseményt dolgoz fel.
- Az alkalmazás I/O-kötésű.

Ilyen esetekben az aszinkron minták és a több nyelvű munkavégző folyamat használatával tovább javíthatja a teljesítményt.

### <a name="async"></a>Aszinkron

Mivel a Python egy egyszálas futtatókörnyezet, a Pythonhoz tartozó gazdagép-példány egyszerre csak egy függvényt tud feldolgozni. A nagy mennyiségű I/O-eseményt feldolgozó alkalmazások és/vagy I/O-kötések esetén a függvények aszinkron futtatásával javítható a teljesítmény.

A függvények aszinkron futtatásához használja az `async def` utasítást, amely a függvényt a [asyncio](https://docs.python.org/3/library/asyncio.html) közvetlenül futtatja:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

A kulcsszó nélküli függvény `async` automatikusan fut egy asyncio szál-készletben:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Több nyelvet használó munkavégző folyamat használata

Alapértelmezés szerint minden functions Host-példány egyetlen nyelvi munkavégző folyamattal rendelkezik. A [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) alkalmazás beállításával növelheti a munkavégző folyamatok számát a gazdagépen (legfeljebb 10). Azure Functions ezt követően megpróbál egyenletesen terjeszteni egyidejű függvényeket a feldolgozók között.

A FUNCTIONS_WORKER_PROCESS_COUNT minden olyan gazdagépre vonatkozik, amelyet a functions hoz létre, amikor az alkalmazás az igények kielégítése érdekében felskálázást végez.

## <a name="context"></a>Környezet

Ha egy függvény hívási környezetét szeretné lekérni a végrehajtás során, adja [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) meg az argumentumot az aláírásában.

Például:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A [**környezeti**](/python/api/azure-functions/azure.functions.context?view=azure-python) osztály a következő karakterlánc-attribútumokkal rendelkezik:

`function_directory` Az a címtár, amelyben a függvény fut.

`function_name` A függvény neve.

`invocation_id` Az aktuális függvény meghívásának azonosítója.

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

A függvények területen az [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatási kapcsolatok karakterláncai környezeti változókként vannak kitéve a végrehajtás során. Ezek a beállítások deklarálva `import os` , majd a használatával érhetők el `setting = os.environ["setting-name"]` .

A következő példa beolvassa az [alkalmazás beállítását](functions-how-to-use-azure-function-app-settings.md#settings)a nevű kulccsal `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Helyi fejlesztés esetén az Alkalmazásbeállítások a [fájl local.settings.jsmaradnak](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Python-verzió

A Azure Functions a következő Python-verziókat támogatja:

| Függvények verziója | Python- <sup>*</sup> verziók |
| ----- | ----- |
| 3. x | 3,8<br/>3.7<br/>3,6 |
| 2. x | 3.7<br/>3,6 |

<sup>*</sup>Hivatalos CPython-disztribúciók

Ha egy adott Python-verziót szeretne kérni a Function alkalmazás Azure-ban való létrehozásakor, használja az `--runtime-version` [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) parancsot. A functions futásidejű verzióját a beállítás adja meg `--functions-version` . A Python verziója a Function alkalmazás létrehozásakor van beállítva, és nem módosítható.

Helyileg futtatva a futtatókörnyezet a rendelkezésre álló Python-verziót használja.

## <a name="package-management"></a>Csomagkezelés

Amikor helyileg fejleszti a Azure Functions Core Tools vagy a Visual Studio Code-ot, adja hozzá a szükséges csomagok neveit és verzióit a `requirements.txt` fájlhoz, és telepítse őket a használatával `pip` .

Például az alábbi követelmények fájl és pip parancs használatával telepítheti a `requests` csomagot a PyPI-ből.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Közzététel az Azure-ban

Ha készen áll a közzétételre, győződjön meg arról, hogy az összes nyilvánosan elérhető függőség megjelenik a requirements.txt fájlban, amely a projekt könyvtára gyökérkönyvtárában található.

A közzétételből kizárt projektfájlok és mappák, beleértve a virtuális környezet mappáját is, a. funcignore fájlban vannak felsorolva.

A Python-projektek Azure-ba való közzétételéhez három Build művelet támogatott: távoli Build, helyi Build és buildek egyéni függőségek használatával.

Az Azure-folyamatok használatával is felépítheti a függőségeket, és közzéteheti a folyamatos kézbesítés (CD) használatával. További információ: [folyamatos kézbesítés az Azure DevOps használatával](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Távoli Build

Távoli Build használata esetén a kiszolgálón visszaállított függőségek és a natív függőségek egyeznek az éles környezettel. Ez egy kisebb telepítési csomagot eredményez a feltöltéshez. Használjon távoli buildet Python-alkalmazások Windows rendszeren való fejlesztésekor. Ha a projektben egyéni függőségek vannak, a [távoli Build szolgáltatásban további index URL-cím is használható](#remote-build-with-extra-index-url).

A függőségek a requirements.txt fájl tartalmától függően távolról szerezhetők be. A [távoli Build](functions-deployment-technologies.md#remote-build) a javasolt Build módszer. Alapértelmezés szerint a Azure Functions Core Tools távoli buildet kér, amikor az alábbi, az [Azure functionapp Publishing](functions-run-local.md#publish) parancs használatával teszi közzé a Python-projektet az Azure-ban.

```bash
func azure functionapp publish <APP_NAME>
```

Ne felejtse el lecserélni a `<APP_NAME>` Function alkalmazás nevét az Azure-ban.

A [Visual Studio Code Azure functions-bővítménye](functions-create-first-function-vs-code.md#publish-the-project-to-azure) alapértelmezés szerint távoli buildet is igényel.

### <a name="local-build"></a>Helyi Build

A függőségek a requirements.txt fájl tartalma alapján szerezhetők be. A távoli buildek használatának megakadályozása érdekében az alábbi, az [Azure functionapp publish](functions-run-local.md#publish) parancs használatával teheti közzé a helyi buildet.

```command
func azure functionapp publish <APP_NAME> --build local
```

Ne felejtse el lecserélni a `<APP_NAME>` Function alkalmazás nevét az Azure-ban.

A beállítás használatával a rendszer `--build local` a requirements.txt fájlból olvassa be a projektek függőségeit, és a függő csomagokat a rendszer helyileg tölti le és telepíti. A Project Files és a függőségek a helyi számítógépről az Azure-ba vannak telepítve. Ennek eredményeképpen egy nagyobb üzembehelyezési csomag tölthető fel az Azure-ba. Ha valamilyen okból kifolyólag a requirements.txt fájlban lévő függőségek nem szerezhetők meg a Core Tools használatával, az egyéni függőségek lehetőséget kell használni a közzétételhez.

Helyi buildek használata nem ajánlott helyileg Windows rendszeren történő fejlesztéshez.

### <a name="custom-dependencies"></a>Egyéni függőségek

Ha a projekt függőségei nem találhatók a Python- [csomag indexében](https://pypi.org/), kétféleképpen lehet felépíteni a projektet. A Build metódus a projekt felépítésének módjától függ.

#### <a name="remote-build-with-extra-index-url"></a>Távoli Build extra index URL-címmel

Ha a csomagok elérhető egyéni csomag-indexből érhetők el, használjon távoli buildet. A közzététel előtt győződjön meg róla, hogy [létrehoz egy nevű alkalmazást](functions-how-to-use-azure-function-app-settings.md#settings) `PIP_EXTRA_INDEX_URL` . A beállítás értéke az egyéni csomag indexének URL-címe. Ha ezt a beállítást választja, a rendszer a távoli buildet a `pip install` kapcsoló használatával futtatja `--extra-index-url` . További információt a [Python pip telepítési dokumentációjában](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)talál.

Az alapszintű hitelesítési hitelesítő adatokat is használhatja a további csomagok indexének URL-címeivel. További információért lásd: [alapszintű hitelesítési hitelesítő adatok](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) a Python dokumentációjában.

#### <a name="install-local-packages"></a>Helyi csomagok telepítése

Ha a projekt nem nyilvánosan elérhető csomagokat használ az eszközeink számára, elérhetővé teheti őket az alkalmazásban, az \_ \_ app \_ \_ /. python_packages könyvtárba helyezve. A közzététel előtt futtassa a következő parancsot a függőségek helyi telepítéséhez:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Egyéni függőségek használatakor a közzétételi lehetőséget kell használnia `--no-build` , mivel már telepítette a függőségeket a projekt mappájába.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Ne felejtse el lecserélni a `<APP_NAME>` Function alkalmazás nevét az Azure-ban.

## <a name="unit-testing"></a>Egység tesztelése

A Pythonban írt függvények a standard szintű tesztelési keretrendszerek használatával más Python-kódokhoz hasonlóan is vizsgálhatók. A legtöbb kötés esetében lehetséges, hogy létrehoz egy modell típusú bemeneti objektumot úgy, hogy létrehoz egy megfelelő osztály egy példányát a `azure.functions` csomagból. Mivel a [`azure.functions`](https://pypi.org/project/azure-functions/) csomag nem érhető el azonnal, ne felejtse el telepíteni a `requirements.txt` fájlt a fenti [csomagkezelő](#package-management) című szakaszban leírtak szerint.

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

A `tempfile.gettempdir()` metódus egy ideiglenes mappát ad vissza, amely Linux rendszeren `/tmp` . Az alkalmazás ezt a könyvtárat használhatja a függvények által a végrehajtás során létrehozott és használt ideiglenes fájlok tárolására.

> [!IMPORTANT]
> Az ideiglenes könyvtárba írt fájlok nem garantáltan megmaradnak a meghívások között. A Kibővítés során az ideiglenes fájlok nincsenek megosztva a példányok között.

Az alábbi példa egy névvel ellátott ideiglenes fájlt hoz létre az ideiglenes könyvtárban ( `/tmp` ):

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

## <a name="preinstalled-libraries"></a>Előre telepített kódtárak

Van néhány könyvtár a Python functions futtatókörnyezettel.

### <a name="python-standard-library"></a>Python standard könyvtár

A Python standard Library tartalmazza a beépített Python-modulok listáját, amelyeket az egyes Python-disztribúciók szállítanak. A legtöbb ilyen függvénytár segítséget nyújt a rendszerfunkciók elérésében, például a fájl I/O-ban. Windows rendszereken ezek a kódtárak telepítve vannak a Python-val. A UNIX-alapú rendszereken a csomagokat a csomagok gyűjteményei adják meg.

A könyvtárak listájának részletes ismertetését az alábbi hivatkozásokon tekintheti meg:

* [Python 3,6 standard könyvtár](https://docs.python.org/3.6/library/)
* [Python 3,7 standard könyvtár](https://docs.python.org/3.7/library/)
* [Python 3,8 standard könyvtár](https://docs.python.org/3.8/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions Python Worker függőségei

A Python-feldolgozó függvények a kódtárak egy adott készletét igénylik. Ezeket a kódtárakat a függvények is használhatják, de nem a Python standard részét képezik. Ha a függvények bármelyik könyvtárra támaszkodnak, előfordulhat, hogy nem lesznek elérhetők a kódban, ha Azure Functionson kívül futnak. A [Setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) fájl **telepítés \_ szükséges** függőségeinek részletes listáját a telepítési feltételek című szakaszban találja.

> [!NOTE]
> Ha a Function alkalmazás requirements.txt tartalmaz egy `azure-functions-worker` bejegyzést, távolítsa el. A functions Worker szolgáltatást automatikusan Azure Functions platform felügyeli, és rendszeresen frissítjük új funkciókkal és hibajavításokkal. A munkavégző régi verziójának manuális telepítése requirements.txt váratlan problémákhoz vezethet.

### <a name="azure-functions-python-library"></a>Azure Functions Python-könyvtár

Minden Python Worker-frissítés tartalmazza a [Azure functions Python Library (Azure. functions)](https://github.com/Azure/azure-functions-python-library)új verzióját. Ez a megközelítés megkönnyíti a Python-függvény alkalmazásai folyamatos frissítését, mivel minden frissítés visszafelé kompatibilis. A könyvtár kiadásait tartalmazó lista az [Azure functions PyPi](https://pypi.org/project/azure-functions/#history)található.

A futásidejű függvénytár verzióját az Azure rögzíti, és requirements.txt nem bírálható felül. A `azure-functions` requirements.txt bejegyzése csak a kihelyezésre és a felhasználói tájékoztatásra szolgál.

A következő kód használatával követheti nyomon a Python functions könyvtárának aktuális verzióját a futtatókörnyezetben:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Futásidejű rendszerkönyvtárak

Az előre telepített rendszerkönyvtárak listáját a Python Worker Docker-lemezképekben kövesse az alábbi hivatkozásokon:

|  Függvények futtatókörnyezete  | Debian-verzió | Python-verziók |
|------------|------------|------------|
| 2-es verzió. x | Stretch  | [Python 3,6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| 3. x verzió | Buster | [Python 3,6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3,8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile) |

## <a name="cross-origin-resource-sharing"></a>Eltérő eredetű erőforrások megosztása

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

A CORS teljes mértékben támogatott a Python-függvények alkalmazásaiban.

## <a name="known-issues-and-faq"></a>Ismert problémák és gyakori kérdések

A következő lista a gyakori problémákkal kapcsolatos hibaelhárítási útmutatókat tartalmazza:

* [ModuleNotFoundError és ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [A "cygrpc" nem importálható](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Az összes ismert probléma és szolgáltatás kérését a [GitHub-problémák](https://github.com/Azure/azure-functions-python-worker/issues) listája követheti nyomon. Ha probléma lép fel, és a GitHubon nem találja a problémát, nyisson meg egy új problémát, és adja meg a probléma részletes leírását.

## <a name="next-steps"></a>Következő lépések

További információkat találhat az alábbi forrásokban:

* [Azure Functions csomag API-dokumentációja](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)
* [BLOB Storage-kötések](functions-bindings-storage-blob.md)
* [HTTP-és webhook-kötések](functions-bindings-http-webhook.md)
* [Üzenetsor-tárolási kötések](functions-bindings-storage-queue.md)
* [Időzítő-eseményindító](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python

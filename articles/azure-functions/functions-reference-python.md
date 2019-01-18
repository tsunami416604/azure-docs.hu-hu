---
title: Python fejlesztői segédanyagok az Azure Functions szolgáltatáshoz
description: Megismerheti, hogyan hozhat létre a functions és a Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra, python
ms.service: functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: dadb645067a0f6cac436d638a829ac4c0937bd60
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304377"
---
# <a name="azure-functions-python-developer-guide"></a>Az Azure Functions Python fejlesztői útmutatója

Ez a cikk bevezetést tapasztalatlan a Python használatával Azure Functions. Az alábbi tartalmat azt feltételezi, hogy Ön már elolvasta a [Azure Functions fejlesztői útmutató](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>A programozási modell

Egy Azure-függvényt egy állapot nélküli metódus az a Python-szkript, amely dolgozza fel a bemenetet és kimenetet kell lennie. Alapértelmezés szerint a futtatókörnyezet vár, ez egy globális módszerként nevű végrehajtandó `main()` a a `__init__.py` fájlt.

Az alapértelmezett konfiguráció megadásával módosíthatja a `scriptFile` és `entryPoint` tulajdonságait a `function.json` fájl. Például a _function.json_ alábbi arra utasítja a modul használatához a _customentry()_ metódus az a _main.py_ fájlt, az Azure-függvény belépési pontját.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Eseményindítók és kötések adatait a függvény segítségével módszert attribútumok keresztül van kötve a `name` meghatározott tulajdonság a `function.json` konfigurációs fájlt. Ha például a _function.json_ alábbi ismerteti egy egyszerű, HTTP-kérést nevű által aktivált függvényt `req`:

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

Igény szerint is deklarálja a paramétertípusok, és adja vissza a függvény típusa jegyzetek Python használatával. Ha például ugyanannak a függvénynek írható használatával jegyzetek, az alábbiak szerint:

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
 | - extensions.csproj
 | - bin
```

Van egy megosztott [host.json](functions-host-json.md) fájlt, amely a függvényalkalmazás konfigurálása használható. Minden függvény rendelkezik a saját kód és kötési konfigurációs fájlt (function.json). 

Megosztott kód egy külön mappában kell tárolni. Modulok SharedCode mappában hivatkozni, használhatja a következő szintaxist:

```
from ..SharedCode import myFirstHelperFunction
```

A Functions futtatókörnyezete által használt kötési bővítményeket vannak meghatározva a `extensions.csproj` fájlt, a tényleges függvénytárfájlok a `bin` mappát. Ha helyileg fejlesztésével, akkor meg kell [regisztrálja a kötési bővítményeket](functions-triggers-bindings.md#local-development-azure-functions-core-tools) Azure Functions Core Tools használatával. 

A függvényalkalmazáshoz az Azure Functions-projekt telepítésekor a FunctionApp mappa teljes tartalmát szerepelnie kell a csomag, de nem maga a mappa.

## <a name="inputs"></a>Bemenetek

Bemenetei között meg vannak osztva az Azure Functions két kategóriába sorolhatók: triggerbemenete és további adatokat. Bár ezek eltérnek a `function.json`, a használat értéke megegyezik a Python-kódban. Vegyük a következő kódrészlet például:

```json
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

```python
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Ha a függvény meghívása HTTP-kérelem a függvényt, átadott `req`. Egy bejegyzés az Azure Blob Storage alapján lehet beolvasni a _id_ URL-útvonal és lehetőségként elérhetővé tegyen `obj` függvény törzsében.

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

## <a name="importing-shared-code-into-a-function-module"></a>Megosztott kód importál egy függvénymodul

Python-modulok mellett függvény modulok közzétett importálni kell a relatív importálás szintaxis használatával:

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

Azt is megteheti megosztott kód üzembe a különálló csomag, közzéteszi a nyilvános és a egy privát PyPI példány, és adja meg azt a rendszeres függőségként.

## <a name="async"></a>Az aszinkron

Mivel csak egyetlen Python folyamat függvényalkalmazás tartozhat, mint egy aszinkron coroutine használata az Azure-függvény megvalósítása javasolt a `async def` utasítást.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

Ha a main() függvény szinkron (nincs `async` minősítő) azt automatikusan futtassa azt egy `asyncio` szálkészlet.

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

## <a name="python-version-and-package-management"></a>Python-verzió és a csomagmegosztás felügyeleti

Jelenleg az Azure Functions csak támogatja Python 3.6.x (hivatalos CPython elosztás).

Amikor helyileg a az Azure Functions Core Tools vagy a Visual Studio Code használatával történő fejlesztéséhez, adja hozzá a nevét és a szükséges csomagok verzióit a `requirements.txt` fájlt, és telepítse őket használatával `pip`.

Például a következő követelmények fájl és a pip parancs segítségével telepítse a `requests` PyPI-csomagot.

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

Amikor készen áll a közzétételre, győződjön meg arról, hogy az összes függősége az szerepel a `requirements.txt` a projektkönyvtárba gyökérmappájában található fájl. Sikeres végrehajtásához az Azure Functions, a követelmények fájlt tartalmaznia kell legalább a következő csomagokat:

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>Közzététel az Azure-bA

Ha egy csomagot, amely fordító szükséges hozzá, és nem támogatja a telepítést a PyPI manylinux-kompatibilis kerekek használ, a közzététel az Azure-bA sikertelen lesz, és a következő hiba: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Automatikusan hozhat létre és konfigurálja a szükséges bináris fájlokat [telepítheti a Dockert](https://docs.docker.com/install/) a helyi gépre, és futtassa a következő parancsot a közzétenni a [Azure Functions Core Tools](functions-run-local.md#v2) (func). Ne felejtse el kicserélni `<app name>` az Azure-ban a függvényalkalmazás nevére. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

A háttérben Core Tools használatával docker futtatása a [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) rendszerképet a helyi gépen tárolójaként. Ebben a környezetben használja, akkor Ezután létrehozhatja és forrás terjesztési, mielőtt végleges üzembe helyezés az Azure szolgáltatásra csomagolás őket az Azure a szükséges modulok telepítéséhez.

> [!NOTE]
> Core Tools (func) használ a PyInstaller program befagyasztani a felhasználó kód és a egy egyetlen önálló végrehajtható fájl futtatását az Azure-ban való függőségek. Ez a funkció jelenleg előzetes verzióban érhető el, és előfordulhat, hogy nem terjed ki az összes típusú Python-csomagokat. Ha nem sikerül a modulok importálásához, próbálja meg újra közzététele a `--no-bundler` lehetőséget. 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> Ha továbbra is problémákat tapasztal, vegye fel velünk a [nyissa meg a probléma](https://github.com/Azure/azure-functions-core-tools/issues/new) , beleértve a probléma leírását. 


A függőségek és közzétett egy folyamatos integrációs (CI) és a folyamatos továbbítás (CD) rendszert használ, használhatja egy [Azure folyamat](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=vsts) vagy [Travis CI egyéni parancsfájl](https://docs.travis-ci.com/user/deployment/script/). 

Példa `azure-pipelines.yml` parancsfájl a build és a közzétételi folyamat.
```yml
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'

- script: |
    set -e
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt-get install -y apt-transport-https
    echo "install Azure CLI..."
    sudo apt-get update && sudo apt-get install -y azure-cli
    npm i -g azure-functions-core-tools --unsafe-perm true
    echo "installing dotnet core"
    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 2.0
- script: |
    set -e
    az login --service-principal --username "$(APP_ID)" --password "$(PASSWORD)" --tenant "$(TENANT_ID)" 
    func settings add FUNCTIONS_WORKER_RUNTIME python
    func extensions install
    func azure functionapp publish $(APP_NAME) --build-native-deps
```

Példa `.travis.yaml` parancsfájl a build és a közzétételi folyamat.

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

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

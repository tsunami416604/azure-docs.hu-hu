---
title: Az első Python-függvény létrehozása az Azure-ban
description: Útmutató az első Python-függvény létrehozása az Azure-ban az Azure Functions Core Tools és az Azure CLI használatával.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 140fc870500054d0652b48bc602706398371fa8e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039058"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>Az első Python-függvény létrehozása az Azure-ban (előzetes verzió)

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Ez a rövid útmutató a cikk bemutatja, hogyan hozhat létre az első a az Azure CLI használatával [kiszolgáló nélküli](https://azure.com/serverless) Linux rendszeren futó Python függvényalkalmazásnak. A függvénykód helyben jön létre, és az [Azure Functions Core Tools](functions-run-local.md) segítségével lehet üzembe helyezni az Azure-ban. A függvényalkalmazást linuxon futó előzetes szempontjai kapcsolatos további információkért lásd: [Ez a cikk a Linux-függvények](https://aka.ms/funclinux).

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépeken követheti.

## <a name="prerequisites"></a>Előfeltételek

Hozhat létre, és helyben tesztelheti, meg kell:

+ Telepítés [Python 3.6-os](https://www.python.org/downloads/)

+ Telepítés [Azure Functions Core Tools](functions-run-local.md#v2) 2.2.70 verzió vagy újabb

Tehet közzé, és futtassa az Azure-ban:

+ Telepítse a [Azure CLI-vel]( /cli/azure/install-azure-cli) verzió 2.x-es vagy újabb.

+ Aktív Azure-előfizetésre van szüksége.
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Hozzon létre, és aktiválja a virtuális környezet

A Functions-projekt létrehozásához szükség, hogy a Python 3.6-os virtuális környezetben dolgozik. Futtassa a következő parancsokat, létrehozása és aktiválása nevű virtuális környezetet `.env`.

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>Egy helyi Functions-projekt létrehozása

Mostantól létrehozhat egy helyi Functions-projektet. Ez a könyvtár megegyezik egy Függvényalkalmazást az Azure-ban. Több függvényt, amely a helyi és üzemeltetési konfigurációja megegyező tartalmazhat.

A terminálablakban, vagy egy parancssorban futtassa a következő parancsot:

```bash
func init MyFunctionProj
```

Válasszon **python** , a kívánt modul.

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

A következő kimenet hasonló üzenet jelenik.

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

Egy új mappát _MyFunctionProj_ jön létre. A folytatáshoz lépjen ebbe a mappába.

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>Függvény létrehozása

Hozzon létre egy függvényt, futtassa a következő parancsot:

```bash
func new
```

Válasszon `HTTP Trigger` a sablont, és adja meg egy **függvénynév** , `HttpTrigger`.

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

A következő kimenet hasonló üzenet jelenik.

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

Egy nevű alárendelt mappát _HttpTrigger_ jön létre. Ez tartalmaz `__init__.py` vagyis az elsődleges parancsfájlt és `function.json` fájlt, amely az eseményindító és a függvény által használt kötéseket. A programozási modellel kapcsolatos további tudnivalókért olvassa el a [Azure Functions Python fejlesztői útmutató](functions-reference-python.md).

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Használja a következő parancsot a Functions gazdagép helyi futtatásához.

```bash
func host start
```

A Functions gazdagép indításakor, a HTTP-eseményindítóval aktivált függvény URL-CÍMÉT adja vissza. (Vegye figyelembe, hogy a teljes kimenet az olvashatóság érdekében csonkolva lettek.)

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %
...
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

Másolja az URL-címet, a függvény a kimenetből, és illessze be a böngésző címsorába. Az URL-címhez fűzze hozzá a `?name=<yourname>` lekérdezési sztringet, és hajtsa végre a kérelmet.

    http://localhost:7071/api/HttpTrigger?name=<yourname>

A következő képernyőképen látható a függvény válasza a böngészőből aktivált:

![test](./media/functions-create-first-function-python/function-test-local-browser.png)

Most már készen áll, hozzon létre egy Függvényalkalmazást és a többi szükséges erőforrást közzététele az Azure-bA.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Linux-függvényalkalmazás létrehozása az Azure-ban

A függvényalkalmazás szolgáltat környezetet a függvénykód végrehajtása. Lehetővé teszi, hogy egyszerűbb felügyelete, üzembe helyezéséhez és erőforrás-megosztás logikai egységbe csoportosítsa a függvényeket. Hozzon létre egy **Linux rendszeren futó Python függvényalkalmazásnak** használatával a [az functionapp létrehozása](/cli/azure/functionapp#az_functionapp_create) parancsot.

Futtassa a következő parancsot egy egyedi függvényalkalmazás-nevet helyén használatával a `<app_name>` helyőrző és a tárfiók nevét `<storage_name>`. Az `<app_name>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, ezért az Azure összes alkalmazásában csak egyszer használhatja.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> Ha már létezik `myResourceGroup` nevű erőforráscsoport egy olyan App Service-alkalmazáshoz, amelynek nem a Linux az alapja, egy másik erőforráscsoportot kell használnia. Egy erőforráscsoporton belül nem lehet Windows- és Linux-alkalmazásokat is üzemeltetni.  

A függvényalkalmazás létrehozása után a következő üzenet jelenik meg:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Most már készen áll a helyi functions-projekt közzététele a függvényalkalmazáshoz az Azure-ban.

## <a name="deploy-the-function-app-project-to-azure"></a>A függvényalkalmazás projektjének üzembe helyezése az Azure-ban

Az Azure Functions Core Tools használatával, futtassa a következő parancsot. Cserélje le `<app_name>` az előző lépésben az alkalmazás nevére.

```bash
func azure functionapp publish <app_name>
```

Látni fogja például a következő kimenetet, amely az olvashatóság érdekében csonkolva lettek.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

Tudjon meg többet a Python használatával Azure-függvények.

> [!div class="nextstepaction"]
> [Az Azure Functions Python fejlesztői útmutató](functions-reference-python.md)
> [Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)

---
title: Az első függvény létrehozása az Azure CLI használatával
description: Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure CLI és az Azure Functions Core Tools használatával.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: ef5459b2b31b67afe187612ffc1ab079a5045a8c
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114910"
---
# <a name="create-your-first-function-from-the-command-line"></a>Az első függvény létrehozása parancssorból

Ez a rövid útmutató lépésről lépésre bemutatja, hogyan hozhatja létre az első függvényét a parancssorból vagy a terminálból. Az Azure CLI használatával létrehoz egy függvényalkalmazást, amely az a [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) infrastruktúra lesz, amelyen a függvény működni fog. A függvénykód-projekt egy függvényből lett létrehozva az [Azure Functions Core Tools](functions-run-local.md) használatával, ennek a használatával történik a függvényalkalmazás-projekt Azure-beli üzembe helyezése is.

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépen is követheti.

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához az alábbiakkal kell rendelkeznie:

+ Telepítse az [Azure Core Tools 2.x verzióját](functions-run-local.md#v2).

+ Telepítse az [Azure CLI-t]( /cli/azure/install-azure-cli). Ehhez a cikkhez az Azure CLI 2.0-ás vagy újabb verziója szükséges. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Használhatja az [Azure Cloud Shellt](https://shell.azure.com/bash) is.

+ Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>A helyi függvényalkalmazás-projekt létrehozása

Futtassa a következő parancsot a parancssorból, hogy létrehozzon az aktuális helyi könyvtár `MyFunctionProj` mappájába egy függvényalkalmazás-projektet. A `MyFunctionProj` mappában egy GitHub-adattár is létrejön.

```bash
func init MyFunctionProj
```

Amikor a rendszer kéri, a nyíl billentyűk segítségével válasszon egy feldolgozói futtatókörnyezetet a következő nyelvek közül:

+ `dotnet`: létrehoz egy .NET osztálytárprojektet (.csproj).
+ `node`: létrehoz egy JavaScript-projektet.

A parancs végrehajtásakor a következő kimenethez hasonlót fog látni:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

## <a name="create-a-function"></a>Függvény létrehozása

A következő parancs megnyitja az új projektet, és létrehoz egy HTTP-triggert alkalmazó függvényt `MyHtpTrigger` néven.

```bash
cd MyFunctionProj
func new --name MyHttpTrigger --template "HttpTrigger"
```

A parancs végrehajtásakor a következő kimenethez hasonlót fog látni, amely egy JavaScript-függvény:

```output
Writing C:\functions\MyFunctionProj\MyHttpTrigger\index.js
Writing C:\functions\MyFunctionProj\MyHttpTrigger\sample.dat
Writing C:\functions\MyFunctionProj\MyHttpTrigger\function.json
```

## <a name="edit-the-function"></a>A függvény szerkesztése

Alapértelmezés szerint a sablon létrehoz egy függvényt, amelynek szüksége van egy függvénykulcsra a kérések küldésekor. Hogy könnyebben lehessen tesztelni a függvényt az Azure-ban, módosítania kell a függvényt, hogy engedélyezze a névtelen hozzáférést. Ennek a módja a Functions-projekt nyelvétől függ.

### <a name="c"></a>C\#

Nyissa meg az új függvényét, vagyis a MyHttpTrigger.cs kódfájlt, módosítsa a függvénydefinícióban az **AuthorizationLevel** (engedélyezési szint) attribútumot `anonymous` értékre, majd mentse a változtatásokat.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

Nyissa meg az új függvényét, vagyis a function.json fájlt egy szerkesztőprogramban, módosítsa a **bindings.httpTrigger** részen az **authLevel** értékét `anonymous` értékre, majd mentse a változtatásokat.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
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
```

Így már anélkül hívhatja meg a függvényt az Azure-ban, hogy meg kellene adnia a függvénykulcsot. Helyi futtatás esetén sosem kell megadni a függvénykulcsot.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A következő parancs elindítja a függvényalkalmazást. Az alkalmazás ugyanazt az Azure Functions-futtatókörnyezetet használja a futáshoz, amelyik az Azure-ban van.

```bash
func host start --build
```

A `--build` beállítás a C#-projektek fordításához szükséges. JavaScript-projekteknél ez a beállítás nem szükséges.

A Functions-gazdagép az indulásakor a következőhöz hasonló kimenetet írja ki, amelyet a könnyebb olvashatóság érdekében lerövidítettünk:

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

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Másolja ki a `HTTPTrigger` függvény URL-címét a futtatókörnyezetének kimenetéből, majd illessze be a böngészője címsorába. Az URL-címhez fűzze hozzá a `?name=<yourname>` lekérdezési sztringet, és hajtsa végre a kérelmet. Az alábbiakban látható a böngészőben a GET kérelemre a helyi függvény által visszaadott válasz:

![Helyi tesztelés a böngészőben](./media/functions-create-first-azure-function-azure-cli/functions-test-local-browser.png)

Most, hogy már futtatta a függvényt helyben, létrehozhatja az Azure-ban a függvényalkalmazást és az egyéb szükséges erőforrásokat.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás szolgáltat környezetet a függvénykód kiszolgáló nélküli végrehajtásához. Lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében. Hozzon létre egy függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#az-functionapp-create) parancs használatával. 

A következő parancsban a `<app_name>` helyőrző helyett írjon be egy egyedi függvényalkalmazás-nevet, a `<storage_name>` helyőrző helyett pedig a tárfiók nevét. Az `<app_name>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. A _deployment-source-url_ paraméter egy GitHubon található mintaadattár, amely tartalmaz egy „Hello World” HTTP-eseményindító által aktivált függvényt.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

A _consumption-plan-location_ paraméter beállítása azt jelenti, hogy a függvényalkalmazás egy Használatalapú futtatási csomagban fut. Ebben a kiszolgáló nélküli csomagban az erőforrások hozzáadása a függvények igényei szerint dinamikusan történik, és csak akkor kell fizetnie, amikor a függvények futnak. További információ: [A megfelelő szolgáltatási csomag kiválasztása](functions-scale.md).

A függvény létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

## <a name="configure-the-function-app"></a>A függvényalkalmazás konfigurálása

A 2.x verziójú Core Tools a 2.x verziójú Azure Functions-futtatókörnyezet sablonjai használatával hozza létre a projekteket. Ezért ellenőrizze, hogy az Azure a 2.x verziójú futtatókörnyezetet használja. Ha `~2` értékre állítja a `FUNCTIONS_WORKER_RUNTIME` alkalmazásbeállítás, azzal a legújabb 2.x verzióra rögzíti a függvényalkalmazást. Adja meg az alkalmazásbeállításokat az [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) paranccsal.

A következő Azure CLI-parancsban az <app_name> a függvényalkalmazás neve.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~2
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

---
title: Az Azure Storage, a Visual Studio Code csatlakoztathatják a functionst
description: Ismerje meg, hogy a függvények csatlakozni a Visual Studio Code egy Azure Storage-üzenetsor kimeneti kötés hozzáadása.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: jeconnoc
ms.openlocfilehash: b207064f691391af2c180c7a6ab03e42ed79fcb6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450995"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Az Azure Storage, a Visual Studio Code csatlakoztathatják a functionst

Az Azure Functions lehetővé teszi a saját integrációs kód írása nélkül az Azure-szolgáltatások és más erőforrások csatlakoztathatják a functionst. Ezek *kötések*, amelyek képviselik, bemeneti és kimeneti is deklarált belül a függvény definícióját. A függvény kötések származó adatok megadott paraméterekként. Egy trigger egy speciális típusú bemeneti kötést. Függvény csak egy eseményindító tartozik, míg képes rendelkezik több bemeneti és kimeneti kötéseit. További tudnivalókért lásd: [Azure Functions eseményindítók és kötések fogalmak](functions-triggers-bindings.md).

Ez a cikk bemutatja, hogyan használható a Visual Studio Code való csatlakozáshoz a létrehozott függvény a [előző rövid útmutatóban a cikk](functions-create-first-function-vs-code.md) az Azure Storage. A kimeneti kötést, adja hozzá ezt a funkciót az a HTTP-kérelemben szereplő adatokat ír az Azure Queue storage-üzenet. 

A legtöbb kötések egy tárolt kapcsolati karakterlánccal, amely függvények a kötött szolgáltatáshoz való hozzáféréshez szükséges. Könnyebben, használhatja a Storage-fiók, amely létrehozta a függvényalkalmazást. Ez a fiók a kapcsolat már tárolták a nevű beállításhoz alkalmazás `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk a Kezdés előtt az alábbi követelményeknek kell megfelelnie:

* Telepítse a [Azure Storage-bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
* Telepítés [az Azure Storage Explorer](https://storageexplorer.com/). Storage Explorer egy olyan eszköz, a kimeneti kötés által létrehozott üzenetsori üzenetek vizsgálatához fogja használni. Storage Explorer macOS, Windows és Linux-alapú operációs rendszerek támogatott.
* Telepítés [.NET Core parancssori felületi eszközöket](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) (C# csak-projektek).
* Hajtsa végre a [a Visual Studio Code rövid útmutató 1. rész](functions-create-first-function-vs-code.md). 

Ez a cikk azt feltételezi, hogy már bejelentkezett az Azure-előfizetéshez a Visual Studio Code-ból. A futó bejelentkezhet `Azure: Sign In` a parancskatalógus. 

## <a name="download-the-function-app-settings"></a>Töltse le a függvényalkalmazás-beállításokat

Az a [előző rövid útmutatóban a cikk](functions-create-first-function-vs-code.md), egy függvényalkalmazást az Azure-ban a szükséges tárfiók együtt létrehozott. Ez a fiók kapcsolati karakterláncára lesz biztonságosan tárolva alkalmazás beállításai az Azure-ban. Ebben a cikkben írt üzenetek ugyanazzal a fiókkal az üzenetsor-tárolóba. Csatlakozhat a tárfiókhoz a függvény helyi futtatás során, a beállítások töltse le a local.settings.json fájlhoz. 

1. Nyomja le az F1 billentyűt a parancskatalógus megnyitásához, majd keresse meg és futtassa a parancsot `Azure Functions: Download Remote Settings....`. 

1. Válassza ki az előző cikkben létrehozott függvényalkalmazás. Válassza ki **Igen, az összeset** felülírja a meglévő helyi beállításokat. 

    > [!IMPORTANT]  
    > Mert titkos adatait, a local.settings.json fájllal soha nem közzétételre, és ki van zárva a verziókövetés tartalmazza.

1. Másolja az értéket `AzureWebJobsStorage`, azaz a tárolási fiók kapcsolati karakterlánc értékét a kulcsot. Ez a kapcsolat segítségével győződjön meg arról, hogy a kimeneti kötés megfelelően működik-e.

## <a name="register-binding-extensions"></a>Kötési bővítmények regisztrálása

Egy üzenetsor-tároló kimeneti kötését használja, mivel a tárolási kötések bővítmény telepítve van a projekt futtatása előtt kell rendelkeznie. 

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# osztálytár

HTTP- és időzítő eseményindító kivételével kötések bővítménycsomagok vannak implementálva. Futtassa a következő [dotnet-csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) paranccsal a terminálablakban a Storage-kiterjesztési csomag hozzáadása a projekthez.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Most a-tároló kimeneti kötését a projekthez is hozzáadhat.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A függvények, minden típusú kötés van szükség, egy `direction`, `type`, és a egy egyedi `name` a function.json fájlban kell definiálni. Ezek az attribútumok határoz meg módja a függvényalkalmazás nyelvének függ.

### <a name="javascript"></a>JavaScript

Közvetlenül a function.json fájlban megadott kötés attribútumok. A kötés típusától függően szükség lehet további tulajdonságok. A [üzenetsor kimeneti konfigurációs](functions-bindings-storage-queue.md#output---configuration) bemutatja egy Azure Storage-üzenetsor kötés szükséges mezőket. A bővítmény megkönnyíti a function.json fájlt adhat hozzá kötéseket. 

A kötés létrehozásához kattintson a jobb gombbal (Ctrl + kattintás macOS rendszeren) a `function.json` a HttpTrigger-mappában fájlt, és válassza a **kötés hozzáadása...** . Kövesse az utasításokat az új kötést a következő kötés tulajdonságainak megadása:

| Kérdés | Érték | Leírás |
| -------- | ----- | ----------- |
| **Válassza ki a kötés iránya** | `out` | A kötés egy kimeneti kötés. |
| **Kötés irányú kiválasztása...** | `Azure Queue Storage` | A kötés egy Azure Storage-üzenetsor kötést. |
| **Ebben a kódban a kötés azonosítására használt név** | `msg` | A kötési paraméter a kódban hivatkozott azonosító név. |
| **Az üzenetet küld az üzenetsorba** | `outqueue` | Az üzenetsorba író a kötés neve. Ha a *queueName* nem létezik, a kötést hoz létre, azt először használ. |
| **Válassza ki a "local.setting.json" beállítás** | `AzureWebJobsStorage` | Egy tárfiók kapcsolati karakterláncát tartalmazó alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás tartalmazza a tárfiók, függvényalkalmazás létrehozott kapcsolati karakterláncára. |

A kötés adnak hozzá a `bindings` tömb a function.json fájlban, amely most az alábbihoz hasonlóan kell kinéznie:

```json
{
   ...

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
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="c-class-library"></a>C\# osztálytár

Az egy C# hordozhatóosztálytár-projektjének, a függvény metódus attribútumokat kötelező a kötések vannak meghatározva. A function.json fájlban majd ezek az attribútumok alapján automatikusan létrehozott.

Nyissa meg a HttpTrigger.cs projekt fájlt, és adja hozzá a következő `using` utasítást:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Adja hozzá a következő paramétert a `Run` metódusdefiníciót:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

A `msg` paraméter egy `ICollector<T>` típusát, amely egy kimeneti kötés, ha a függvény írt üzenetek gyűjteményét befejeződik. Ebben az esetben a kimenete nevű üzenetsor-tárolóba `outqueue`. A tárfiók kapcsolati karakterlánca állítja be a `StorageAccountAttribute`. Ez az attribútum azt jelzi, hogy a beállítás, amely tartalmazza a Tárfiók kapcsolati sztringje, és az osztályt, a metódus vagy a paraméter szintjén alkalmazható. Nincs megadva ebben az esetben `StorageAccountAttribute` , mert már az alapértelmezett tárfiók.

A Futtatás metódusdefiníciót most a következőhöz hasonlóan kell kinéznie:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

Miután a kötés van definiálva, használhatja a `name` a kötés hozzá a függvényaláíráshoz attribútumaként. Kimeneti kötés használatával nem kell az Azure Storage SDK kódot használja a hitelesítéshez, üzenetsor-hivatkozás beszerzése vagy írásáért. A Functions futtatókörnyezete és üzenetsorának kimeneti kötést, hajtsa végre ezeket a feladatokat.

### <a name="javascript"></a>JavaScript

Használó kód hozzáadása a `msg` kimeneti kötés objektumot a `context.bindings` üzenetsori üzenetek létrehozásához. Ezt a kódot a `context.res` utasítás elé írja be.

```javascript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + 
(req.query.name || req.body.name);
```

Ezen a ponton a függvényt kell hasonlítania:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " + 
        (req.query.name || req.body.name);
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```

### <a name="c"></a>C\#

Használó kód hozzáadása a `msg` kimeneti kötés objektumot üzenetsori üzenetek létrehozásához. Adja hozzá ezt a kódot, mielőtt a metódus adja vissza.

```cs
if (!string.IsNullOrEmpty(name))
{
    // Add a message to the output collection.
    msg.Add(string.Format("Name passed to the function: {0}", name));
}
```

Ezen a ponton a függvényt kell hasonlítania:

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

A Functions futtatókörnyezete egy **outqueue** nevű új üzenetsort hoz létre a tárfiókjában a kimeneti kötés első használatakor. Storage Explorer használatával fogjuk győződjön meg arról, hogy a várólista létrehozása az új üzenet együtt.

### <a name="connect-storage-explorer-to-your-account"></a>A Storage Explorer csatlakoztatása a fiókjához

Kihagyhatja ezt a szakaszt, ha már telepítve van az Azure Storage Explorert, és csatlakoztatta az Azure-fiókjával.

1. Futtassa a [Azure Storage Explorer] eszköze, a bal oldalon található csatlakozási ikonra, válassza ki és **vegyen fel egy fiókot**.

    ![Adjon hozzá egy Azure-fiókját a Microsoft Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. Az a **Connect** párbeszédpanelen válassza ki **Azure-fiók hozzáadása**, válassza ki a **Azure-környezet**, és válassza ki **jelentkezzen be a...** . 

    ![Jelentkezzen be az Azure-fiókjába](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Miután sikeresen bejelentkezik a fiókjába, az Azure-előfizetést a fiókjához társított összes megjelenik.

### <a name="examine-the-output-queue"></a>A kimeneti üzenetsor vizsgálata

1. A Visual Studio Code, nyomja le az F1 billentyűt, nyissa meg a parancskatalógust, majd keresse meg és futtassa a parancsot `Azure Storage: Open in Storage Explorer` , és válassza ki a Tárfiók nevét. A storage-fiók megnyitása az Azure Storage Explorerben.  

1. Bontsa ki az **Üzenetsorok** csomópontot, majd válassza ki az **outqueue** nevű üzenetsort. 

   Az üzenetsor tartalmazza az üzenetet, amelyet az üzenetsor kimeneti kötése létrehozott a HTTP által aktivált függvény futtatásakor. Ha a függvény az alapértelmezett hívta `name` értékét *Azure*, az üzenetsorban található üzenet van *a függvénynek átadott név: Azure*.

    ![Azure Storage Explorerben megjelenő üzenetsori üzenet](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Futtassa a függvényt, újabb kérés küldése a, és a egy új üzenet jelenik meg az üzenetsorban.  

Most, a frissített függvény alkalmazás az Azure-ban való ismételt közzétételéhez ideje.

## <a name="redeploy-and-test-the-updated-app"></a>Ismételt üzembe helyezése és a frissített alkalmazás tesztelése

1. A Visual Studio Code-nyomja le az F1 billentyűt a parancskatalógus megnyitásához. A parancskatalógus keresése és kiválasztása `Azure Functions: Deploy to function app...`.

1. Válassza ki a függvényalkalmazást, amelyet az első cikk. Válassza a projekthez, hogy ugyanaz az alkalmazás újbóli üzembe helyezés, mert **telepítés** fájlok felülírása vonatkozó figyelmeztetést elvetéséhez.

1. Üzembe helyezés befejezése után újra használhatja a cURL vagy a böngésző az újratelepített függvény teszteléséhez. Mint korábban, fűzze hozzá a lekérdezési karakterlánc `&name=<yourname>` az URL-címhez, akkor az alábbi példában látható módon:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Újra [tekintheti meg az üzenetet a tárolási üzenetsort](#examine-the-output-queue) ellenőrizheti a kimeneti kötés újra az üzenetsor új üzenetet állít elő.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az *erőforrások* kifejezés az Azure-ban például függvényalkalmazásokra, függvényekre vagy tárfiókokra utal. Az erőforrások *erőforráscsoportokba* vannak csoportosítva, és az adott csoport törlésével a benne lévő összes erőforrás törölhető.

E rövid útmutatók elvégzéséhez erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. Ha már nincs szüksége ezekre az erőforrásokra, a következőképpen törölheti őket:

1. A Visual Studio Code-nyomja le az F1 billentyűt a parancskatalógus megnyitásához. A parancskatalógus keresése és kiválasztása `Azure Functions: Open in portal`.

1. Válassza ki a függvényalkalmazást, és nyomja le az Enter billentyűt. A függvényalkalmazás oldaláról való megnyitásakor a [az Azure portal](https://portal.azure.com).

1. Az a **áttekintése** lapra, válassza ki a megnevezett hivatkozás alatt **erőforráscsoport**.

    ![Válassza ki az erőforráscsoportot, amelyet törölni kíván a függvényalkalmazás oldaláról.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. Az **Erőforráscsoport** oldalon tekintse át a csoportban lévő erőforrások listáját, és győződjön meg arról, hogy a törölni kívánt elemek szerepelnek benne.
 
1. Válassza az **Erőforráscsoport törlése** elemet, majd kövesse az utasításokat.

   A törlés eltarthat néhány percig. Amint a művelet befejeződött, néhány másodpercre egy értesítés jelenik meg. Az értesítést úgy is megtekintheti, ha kiválasztja a harang ikont az oldal tetején.

## <a name="next-steps"></a>További lépések

Hogy frissítette a HTTP által aktivált függvényt az adatok írása az üzenetsor-tárolóba. Függvények fejlesztése kapcsolatos további információkért lásd: [fejlesztése az Azure Functions Visual Studio Code használatával](functions-develop-vs-code.md).

Ezután engedélyeznie kell Application Insights-figyelést a függvényalkalmazás számára:

> [!div class="nextstepaction"]
> [Az Application Insights-integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/

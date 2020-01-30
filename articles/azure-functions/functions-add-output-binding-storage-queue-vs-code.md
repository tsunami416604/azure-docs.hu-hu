---
title: Függvények összekapcsolhatók az Azure Storage-ba a Visual Studio Code használatával
description: Megtudhatja, hogyan adhat hozzá kimeneti kötést a függvények Azure Storage-várólistákhoz való összekapcsolásához a Visual Studio Code használatával.
ms.date: 06/25/2019
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 5b7d7be7854a216b7cb7b610ea6d51fdc496a93f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845655"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Függvények összekapcsolhatók az Azure Storage-ba a Visual Studio Code használatával

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Ez a cikk bemutatja, hogyan használható a Visual Studio Code az előző rövid útmutató [cikkében](functions-create-first-function-vs-code.md) létrehozott függvény Azure Storage-ba való összekapcsolásához. Az ehhez a függvényhez hozzáadott kimeneti kötés adatokat ír a HTTP-kérelemből egy Azure üzenetsor-tárolási várólistán lévő üzenetbe. 

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a függvények a kötött szolgáltatás eléréséhez használnak. A könnyebb kezelhetőség érdekében használja a Function alkalmazással létrehozott Storage-fiókot. A fiókhoz való kapcsolódás már egy `AzureWebJobsStorage`nevű alkalmazás-beállításban van tárolva.  

## <a name="prerequisites"></a>Előfeltételek

A cikk elindítása előtt a következő követelményeknek kell megfelelnie:

* Telepítse az [Azure Storage-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)-hoz.

* Telepítse a [Azure Storage Explorer](https://storageexplorer.com/). Storage Explorer egy eszköz, amellyel megvizsgálhatja a kimeneti kötés által létrehozott üzenetsor-üzeneteket. A Storage Explorer macOS-, Windows-és Linux-alapú operációs rendszereken támogatott.

::: zone pivot="programming-language-csharp"
* [A .net Core parancssori felülete eszközök](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)telepítése.
::: zone-end

* Hajtsa végre a [Visual Studio Code](functions-create-first-function-vs-code.md)rövid útmutatójának 1. részében ismertetett lépéseket. 

Ez a cikk azt feltételezi, hogy már be van jelentkezve az Azure-előfizetésbe a Visual Studio Code-ból. `Azure: Sign In` futtatásával a parancssorból is bejelentkezhet. 

## <a name="download-the-function-app-settings"></a>A függvény alkalmazás beállításainak letöltése

Az [előző](functions-create-first-function-vs-code.md)rövid útmutatóban létrehozott egy Function alkalmazást az Azure-ban a szükséges Storage-fiókkal együtt. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ebben a cikkben egy fiókba írja az üzeneteket egy tárolási várólistába. Ha a funkciót helyileg futtatja, a Storage-fiókhoz való csatlakozáshoz le kell töltenie az Alkalmazásbeállítások a local. Settings. JSON fájlra. 

1. Nyomja le az F1 billentyűt a parancs palettájának megnyitásához, majd keresse meg és futtassa a parancsot `Azure Functions: Download Remote Settings....`. 

1. Válassza ki az előző cikkben létrehozott Function alkalmazást. A meglévő helyi beállítások felülírásához válassza az **Igen** lehetőséget. 

    > [!IMPORTANT]  
    > Mivel titkokat tartalmaz, a local. Settings. JSON fájl soha nem lesz közzétéve, és ki van zárva a verziókövetés alól.

1. Másolja a `AzureWebJobsStorage`értéket, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánc-értékének kulcsa. Ezzel a kapcsolattal ellenőrizheti, hogy a kimeneti kötés a várt módon működik-e.

## <a name="register-binding-extensions"></a>Kötési bővítmények regisztrálása

Mivel a várólista-tároló kimeneti kötését használja, a projekt futtatása előtt telepítenie kell a Storage-kötések bővítményt. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

A HTTP-és időzítő-eseményindítók kivételével a kötések kiterjesztési csomagként vannak implementálva. Futtassa a következő [DotNet-csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) parancsot a terminál ablakban a tárolási bővítmény csomagjának a projekthez való hozzáadásához.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

::: zone-end

Most hozzáadhatja a tárolási kimeneti kötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A functions szolgáltatásban minden típusú kötéshez szükség van egy `direction`, `type`ra és egy egyedi `name`ra, amelyet a function. JSON fájlban kell meghatározni. Az attribútumok definiálásának módja a Function alkalmazás nyelvétől függ.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

A kötés meghatározása után a kötés `name` használhatja a függvény aláírása attribútumként való eléréséhez. Kimeneti kötés használatával nem szükséges az Azure Storage SDK-kód használata hitelesítéshez, üzenetsor-hivatkozás beszerzése vagy az adatírás. A functions futtatókörnyezet és a várólista kimeneti kötése elvégzi ezeket a feladatokat.

::: zone pivot="programming-language-javascript"

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

::: zone-end

::: zone pivot="programming-language-typescript"

Vegyen fel egy olyan kódot, amely a `msg` kimeneti kötési objektumot használja `context.bindings` egy üzenetsor-üzenet létrehozásához. Adja hozzá ezt a kódot a `context.res` utasítás előtt.

```typescript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + name;
```

Ezen a ponton a függvénynek a következőképpen kell kinéznie:

```javascript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " + name; 
        // Send a "hello" response.
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

export default httpTrigger;
```

::: zone-end

::: zone pivot="programming-language-powershell"

Vegyen fel olyan kódot, amely a `Push-OutputBinding` parancsmagot használja, hogy szöveget írjon a várólistához a `msg` kimeneti kötés használatával. Adja hozzá ezt a kódot, mielőtt beállítja az OK állapotot a `if` utasításban.

```powershell
# Write the $name value to the queue.
$outputMsg = "Name passed to the function: $name"
Push-OutputBinding -name msg -Value $outputMsg
```

Ezen a ponton a függvénynek a következőképpen kell kinéznie:

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    # Write the $name value to the queue.
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

A **rendszer létrehoz** egy új üzenetsor-várólistát a Storage-fiókban a functions futtatókörnyezetben a kimeneti kötés első használatakor. A Storage Explorer segítségével ellenőrizheti, hogy a várólista létrejött-e az új üzenettel együtt.

### <a name="connect-storage-explorer-to-your-account"></a>A Storage Explorer csatlakoztatása a fiókjához

Hagyja ki ezt a szakaszt, ha már telepítette Azure Storage Explorer és csatlakoztatta azt az Azure-fiókjához.

1. Futtassa az [Azure Storage Explorer] eszközt, válassza a bal oldali csatlakozási ikont, majd válassza a **fiók hozzáadása**lehetőséget.

    ![Azure-fiók hozzáadása a Microsoft Azure Storage Explorerhoz](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. A **kapcsolat** párbeszédpanelen válassza az **Azure-fiók hozzáadása**lehetőséget, válassza ki az **Azure-környezetet**, és válassza a **Bejelentkezés**lehetőséget. 

    ![Jelentkezzen be az Azure-fiókjába](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Miután sikeresen bejelentkezett a fiókjába, megjelenik a fiókjához társított összes Azure-előfizetés.

### <a name="examine-the-output-queue"></a>A kimeneti üzenetsor vizsgálata

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs palettájának megnyitásához, majd keresse meg és futtassa a parancsot `Azure Storage: Open in Storage Explorer` majd válassza ki a Storage-fiók nevét. A Storage-fiók a Azure Storage Explorerban nyílik meg.  

1. Bontsa ki az **Üzenetsorok** csomópontot, majd válassza ki az **outqueue** nevű üzenetsort. 

   Az üzenetsor tartalmazza az üzenetet, amelyet az üzenetsor kimeneti kötése létrehozott a HTTP által aktivált függvény futtatásakor. Ha az alapértelmezett *Azure*`name` értékkel hívta meg a függvényt, az üzenetsorban található üzenet a következő lesz: *A függvénynek átadott név: Azure*.

    ![Üzenetsor-üzenet látható Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Futtassa újra a függvényt, küldjön egy másik kérést, és megjelenik egy új üzenet a várólistában.  

Itt az ideje, hogy újra közzé lehessen tenni a frissített Function alkalmazást az Azure-ban.

## <a name="redeploy-and-verify-the-updated-app"></a>A frissített alkalmazás újbóli üzembe helyezése és ellenőrzése

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki `Azure Functions: Deploy to function app...`.

1. Válassza ki az első cikkben létrehozott Function alkalmazást. Mivel a projekt ugyanarra az alkalmazásba való újbóli üzembe helyezését végzi, válassza a **telepítés** lehetőséget a fájlok felülírásával kapcsolatos figyelmeztetés elvetéséhez.

1. Az üzembe helyezés befejezése után a cURL vagy egy böngésző használatával tesztelheti az újratelepített függvényt. Ahogy korábban is, fűzze hozzá a lekérdezési karakterláncot `&name=<yourname>` az URL-címhez, ahogy az alábbi példában is látható:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Ismét [tekintse meg az üzenetet a Storage-várólistán](#examine-the-output-queue) annak ellenőrzéséhez, hogy a kimeneti kötés ismét létrehoz egy új üzenetet a várólistában.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az *erőforrások* kifejezés az Azure-ban például függvényalkalmazásokra, függvényekre vagy tárfiókokra utal. Az erőforrások *erőforráscsoportokba* vannak csoportosítva, és az adott csoport törlésével a benne lévő összes erőforrás törölhető.

E rövid útmutatók elvégzéséhez erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. Ha már nincs szüksége ezekre az erőforrásokra, a következőképpen törölheti őket:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Következő lépések

Frissítette a HTTP által aktivált függvényt az adattárolási várólistába való íráshoz. Ezután további információt a függvények a Visual Studio Code használatával történő fejlesztéséről a következő témakörben olvashat:

> [!div class="nextstepaction"]
> [Azure Functions fejlesztése a Visual Studio Code használatával](functions-develop-vs-code.md)

[Azure Storage Explorer]: https://storageexplorer.com/

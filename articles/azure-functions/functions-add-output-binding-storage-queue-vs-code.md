---
title: Függvények összekapcsolhatók az Azure Storage-ba a Visual Studio Code használatával
description: Megtudhatja, hogyan adhat hozzá kimeneti kötést a függvények Azure Storage-várólistákhoz való összekapcsolásához a Visual Studio Code használatával.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: jeconnoc
ms.openlocfilehash: 40a912a94dc61342c04528e902bb0e084546904d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592815"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Függvények összekapcsolhatók az Azure Storage-ba a Visual Studio Code használatával

Azure Functions lehetővé teszi a függvények Azure-szolgáltatásokhoz és más erőforrásokhoz való összekapcsolását anélkül, hogy saját integrációs kódot kellene írnia. Ezek a kötések, amelyek a bemeneti és a kimeneti adatokat jelölik, a függvény definíciójában vannak deklarálva. A kötések adatait paraméterként a függvény kapja meg. Az trigger egy speciális típusú bemeneti kötés. Habár egy függvénynek csak egy triggere van, több bemeneti és kimeneti kötés is lehet. További információ: [Azure functions triggerek és kötések fogalmai](functions-triggers-bindings.md).

Ez a cikk bemutatja, hogyan használható a Visual Studio Code az előző rövid útmutató [cikkében](functions-create-first-function-vs-code.md) létrehozott függvény Azure Storage-ba való összekapcsolásához. Az ehhez a függvényhez hozzáadott kimeneti kötés adatokat ír a HTTP-kérelemből egy Azure üzenetsor-tárolási várólistán lévő üzenetbe. 

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a függvények a kötött szolgáltatás eléréséhez használnak. A könnyebb kezelhetőség érdekében használja a Function alkalmazással létrehozott Storage-fiókot. A fiókhoz való kapcsolódás már egy nevű `AzureWebJobsStorage`alkalmazás-beállításban van tárolva.  

## <a name="prerequisites"></a>Előfeltételek

A cikk elindítása előtt a következő követelményeknek kell megfelelnie:

* Telepítse az [Azure Storage-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)-hoz.
* Telepítse a [Azure Storage Explorer](https://storageexplorer.com/). Storage Explorer egy eszköz, amellyel megvizsgálhatja a kimeneti kötés által létrehozott üzenetsor-üzeneteket. A Storage Explorer macOS-, Windows-és Linux-alapú operációs rendszereken támogatott.
* [A .net Core parancssori felülete eszközök](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) telepítése (C# csak projektek).
* Hajtsa végre a [Visual Studio Code](functions-create-first-function-vs-code.md)rövid útmutatójának 1. részében ismertetett lépéseket. 

Ez a cikk azt feltételezi, hogy már be van jelentkezve az Azure-előfizetésbe a Visual Studio Code-ból. A parancs futtatásával `Azure: Sign In` a parancssorból is bejelentkezhet. 

## <a name="download-the-function-app-settings"></a>A függvény alkalmazás beállításainak letöltése

Az [előző](functions-create-first-function-vs-code.md)rövid útmutatóban létrehozott egy Function alkalmazást az Azure-ban a szükséges Storage-fiókkal együtt. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ebben a cikkben egy fiókba írja az üzeneteket egy tárolási várólistába. Ha a funkciót helyileg futtatja, a Storage-fiókhoz való csatlakozáshoz le kell töltenie az Alkalmazásbeállítások a local. Settings. JSON fájlra. 

1. Nyomja le az F1 billentyűt a parancs palettájának megnyitásához, majd keresse meg és `Azure Functions: Download Remote Settings....`futtassa a parancsot. 

1. Válassza ki az előző cikkben létrehozott Function alkalmazást. A meglévő helyi beállítások felülírásához válassza az **Igen** lehetőséget. 

    > [!IMPORTANT]  
    > Mivel titkokat tartalmaz, a local. Settings. JSON fájl soha nem lesz közzétéve, és ki van zárva a verziókövetés alól.

1. Másolja ki az `AzureWebJobsStorage`értéket, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánc-értékének kulcsa. Ezzel a kapcsolattal ellenőrizheti, hogy a kimeneti kötés a várt módon működik-e.

## <a name="register-binding-extensions"></a>Kötési bővítmények regisztrálása

Mivel a várólista-tároló kimeneti kötését használja, a projekt futtatása előtt telepítenie kell a Storage-kötések bővítményt. 

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# osztály könyvtára

A HTTP-és időzítő-eseményindítók kivételével a kötések kiterjesztési csomagként vannak implementálva. Futtassa a következő [DotNet-csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) parancsot a terminál ablakban a tárolási bővítmény csomagjának a projekthez való hozzáadásához.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Most hozzáadhatja a tárolási kimeneti kötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A functions esetében minden típusú kötéshez a `direction`, `type`, és a Function `name` . JSON fájlban definiált egyedi azonosító szükséges. Az attribútumok definiálásának módja a Function alkalmazás nyelvétől függ.

### <a name="javascript"></a>JavaScript

A kötési attribútumok közvetlenül a function. JSON fájlban vannak definiálva. A kötési típustól függően szükség lehet további tulajdonságokra. A [várólista kimeneti konfigurációja](functions-bindings-storage-queue.md#output---configuration) leírja az Azure Storage-várólista kötéséhez szükséges mezőket. A bővítmény megkönnyíti a kötések hozzáadását a function. JSON fájlhoz. 

Kötés létrehozásához kattintson a jobb gombbal (CTRL + kattintás MacOS-re) a `function.json` HttpTrigger mappában található fájlra, majd válassza a **kötés hozzáadása...** lehetőséget. Kövesse az utasításokat a következő kötési tulajdonságok definiálásához az új kötéshez:

| Kérdés | Value | Leírás |
| -------- | ----- | ----------- |
| **Kötési irány kiválasztása** | `out` | A kötés kimeneti kötés. |
| **Kötés kijelölése iránysal...** | `Azure Queue Storage` | A kötés egy Azure Storage-várólista kötése. |
| **A kódban a kötés azonosítására használt név** | `msg` | A kódban hivatkozott kötési paramétert azonosító név. |
| **Az az üzenetsor, amelybe az üzenet el lesz küldve** | `outqueue` | Annak a sornak a neve, amelyet a kötés ír. Ha a *queueName* nem létezik, a kötés létrehozza az első használatkor. |
| **Válassza a beállítás a következőből: "local. Setting. JSON"** | `AzureWebJobsStorage` | A Storage-fiókhoz tartozó kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás tartalmazza a Function alkalmazással létrehozott Storage-fiókhoz tartozó kapcsolatok karakterláncát. |

A függvény egy kötést ad `bindings` hozzá a tömbhöz a function. JSON fájlban, amelynek most az alábbi példához hasonlóan kell kinéznie:

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

### <a name="c-class-library"></a>C\# osztály könyvtára

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

A kötés meghatározása után a kötést használhatja `name` a függvény aláírása attribútumként való eléréséhez. Kimeneti kötés használatával nem szükséges az Azure Storage SDK-kód használata hitelesítéshez, üzenetsor-hivatkozás beszerzése vagy az adatírás. A functions futtatókörnyezet és a várólista kimeneti kötése elvégzi ezeket a feladatokat.

### <a name="javascript"></a>JavaScript

Hozzon létre egy olyan `msg` kódot, amely a `context.bindings` kimeneti kötési objektumot használja az üzenetsor létrehozásához. Ezt a kódot a `context.res` utasítás elé írja be.

```javascript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + 
(req.query.name || req.body.name);
```

Ezen a ponton a függvénynek a következőképpen kell kinéznie:

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

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

A rendszer létrehoz egy  új üzenetsor-várólistát a Storage-fiókban a functions futtatókörnyezetben a kimeneti kötés első használatakor. A Storage Explorer segítségével ellenőrizheti, hogy a várólista létrejött-e az új üzenettel együtt.

### <a name="connect-storage-explorer-to-your-account"></a>A Storage Explorer csatlakoztatása a fiókjához

Hagyja ki ezt a szakaszt, ha már telepítette Azure Storage Explorer és csatlakoztatta azt az Azure-fiókjához.

1. Futtassa az [Azure Storage Explorer] eszközt, válassza a bal oldali csatlakozási ikont, majd válassza a **fiók hozzáadása**lehetőséget.

    ![Azure-fiók hozzáadása a Microsoft Azure Storage Explorerhoz](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. A **kapcsolat** párbeszédpanelen válassza az **Azure-fiók hozzáadása**lehetőséget, válassza ki az **Azure-környezetet**, és válassza a **Bejelentkezés**lehetőséget. 

    ![Jelentkezzen be az Azure-fiókjába](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Miután sikeresen bejelentkezett a fiókjába, megjelenik a fiókjához társított összes Azure-előfizetés.

### <a name="examine-the-output-queue"></a>A kimeneti üzenetsor vizsgálata

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs paletta megnyitásához, majd keresse meg és `Azure Storage: Open in Storage Explorer` futtassa a parancsot, és válassza ki a Storage-fiók nevét. A Storage-fiók a Azure Storage Explorerban nyílik meg.  

1. Bontsa ki az **Üzenetsorok** csomópontot, majd válassza ki az **outqueue** nevű üzenetsort. 

   Az üzenetsor tartalmazza az üzenetet, amelyet az üzenetsor kimeneti kötése létrehozott a HTTP által aktivált függvény futtatásakor. Ha a függvényt az `name` *Azure*alapértelmezett értékével hívja meg, a várólista-üzenet a *következő függvénynek lesz átadva: Azure*-ban.

    ![Üzenetsor-üzenet látható Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Futtassa újra a függvényt, küldjön egy másik kérést, és megjelenik egy új üzenet a várólistában.  

Itt az ideje, hogy újra közzé lehessen tenni a frissített Function alkalmazást az Azure-ban.

## <a name="redeploy-and-verify-the-updated-app"></a>A frissített alkalmazás újbóli üzembe helyezése és ellenőrzése

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki `Azure Functions: Deploy to function app...`a következőt:.

1. Válassza ki az első cikkben létrehozott Function alkalmazást. Mivel a projekt ugyanarra az alkalmazásba való újbóli üzembe helyezését végzi, válassza a **telepítés** lehetőséget a fájlok felülírásával kapcsolatos figyelmeztetés elvetéséhez.

1. Az üzembe helyezés befejezése után a cURL vagy egy böngésző használatával tesztelheti az újratelepített függvényt. Ahogy korábban is, fűzze hozzá a `&name=<yourname>` lekérdezési karakterláncot az URL-címhez, az alábbi példában látható módon:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Ismét [tekintse meg az üzenetet a Storage-várólistán](#examine-the-output-queue) annak ellenőrzéséhez, hogy a kimeneti kötés ismét létrehoz egy új üzenetet a várólistában.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az *erőforrások* kifejezés az Azure-ban például függvényalkalmazásokra, függvényekre vagy tárfiókokra utal. Az erőforrások *erőforráscsoportokba* vannak csoportosítva, és az adott csoport törlésével a benne lévő összes erőforrás törölhető.

E rövid útmutatók elvégzéséhez erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. Ha már nincs szüksége ezekre az erőforrásokra, a következőképpen törölheti őket:

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki `Azure Functions: Open in portal`a következőt:.

1. Válassza ki a Function alkalmazást, és nyomja le az ENTER billentyűt. Megnyílik a Function app (alkalmazás) lap a [Azure Portal](https://portal.azure.com).

1. Az **Áttekintés** lapon válassza ki az elnevezett hivatkozást az **erőforráscsoport**területen.

    ![Válassza ki az erőforráscsoportot, amelyet törölni kíván a függvényalkalmazás oldaláról.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. Az **Erőforráscsoport** oldalon tekintse át a csoportban lévő erőforrások listáját, és győződjön meg arról, hogy a törölni kívánt elemek szerepelnek benne.
 
1. Válassza az **Erőforráscsoport törlése** elemet, majd kövesse az utasításokat.

   A törlés eltarthat néhány percig. Amint a művelet befejeződött, néhány másodpercre egy értesítés jelenik meg. Az értesítést úgy is megtekintheti, ha kiválasztja a harang ikont az oldal tetején.

## <a name="next-steps"></a>További lépések

Frissítette a HTTP által aktivált függvényt az adattárolási várólistába való íráshoz. További információ a függvények fejlesztéséről: [Azure functions fejlesztése a Visual Studio Code használatával](functions-develop-vs-code.md).

Ezután engedélyezze Application Insights figyelését a Function alkalmazáshoz:

> [!div class="nextstepaction"]
> [Application Insights integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/

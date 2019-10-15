---
title: Függvények összekapcsolhatók az Azure Storage-ba a Visual Studio Code használatával
description: Megtudhatja, hogyan adhat hozzá kimeneti kötést a függvények Azure Storage-várólistákhoz való összekapcsolásához a Visual Studio Code használatával.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: 951e48e591f490ea6321329352fd798fea58855d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329702"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Függvények összekapcsolhatók az Azure Storage-ba a Visual Studio Code használatával

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Ez a cikk bemutatja, hogyan használható a Visual Studio Code az előző rövid útmutató [cikkében](functions-create-first-function-vs-code.md) létrehozott függvény Azure Storage-ba való összekapcsolásához. Az ehhez a függvényhez hozzáadott kimeneti kötés adatokat ír a HTTP-kérelemből egy Azure üzenetsor-tárolási várólistán lévő üzenetbe. 

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a függvények a kötött szolgáltatás eléréséhez használnak. A könnyebb kezelhetőség érdekében használja a Function alkalmazással létrehozott Storage-fiókot. A fiókhoz való kapcsolódás már egy `AzureWebJobsStorage` nevű alkalmazás-beállításban van tárolva.  

## <a name="prerequisites"></a>Előfeltételek

A cikk elindítása előtt a következő követelményeknek kell megfelelnie:

* Telepítse az [Azure Storage-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)-hoz.
* Telepítse a [Azure Storage Explorer](https://storageexplorer.com/). Storage Explorer egy eszköz, amellyel megvizsgálhatja a kimeneti kötés által létrehozott üzenetsor-üzeneteket. A Storage Explorer macOS-, Windows-és Linux-alapú operációs rendszereken támogatott.
* [A .net Core parancssori felülete eszközök](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) telepítése (C# csak projektek).
* Hajtsa végre a [Visual Studio Code](functions-create-first-function-vs-code.md)rövid útmutatójának 1. részében ismertetett lépéseket. 

Ez a cikk azt feltételezi, hogy már be van jelentkezve az Azure-előfizetésbe a Visual Studio Code-ból. A parancssorból a `Azure: Sign In` futtatásával jelentkezhet be. 

## <a name="download-the-function-app-settings"></a>A függvény alkalmazás beállításainak letöltése

Az [előző](functions-create-first-function-vs-code.md)rövid útmutatóban létrehozott egy Function alkalmazást az Azure-ban a szükséges Storage-fiókkal együtt. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ebben a cikkben egy fiókba írja az üzeneteket egy tárolási várólistába. Ha a funkciót helyileg futtatja, a Storage-fiókhoz való csatlakozáshoz le kell töltenie az Alkalmazásbeállítások a local. Settings. JSON fájlra. 

1. Nyomja le az F1 billentyűt a parancs palettájának megnyitásához, majd keresse meg és futtassa a következő parancsot: `Azure Functions: Download Remote Settings....`. 

1. Válassza ki az előző cikkben létrehozott Function alkalmazást. A meglévő helyi beállítások felülírásához válassza az **Igen** lehetőséget. 

    > [!IMPORTANT]  
    > Mivel titkokat tartalmaz, a local. Settings. JSON fájl soha nem lesz közzétéve, és ki van zárva a verziókövetés alól.

1. Másolja a `AzureWebJobsStorage` értéket, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánc-értékének kulcsa. Ezzel a kapcsolattal ellenőrizheti, hogy a kimeneti kötés a várt módon működik-e.

## <a name="register-binding-extensions"></a>Kötési bővítmények regisztrálása

Mivel a várólista-tároló kimeneti kötését használja, a projekt futtatása előtt telepítenie kell a Storage-kötések bővítményt. 

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

A HTTP-és időzítő-eseményindítók kivételével a kötések kiterjesztési csomagként vannak implementálva. Futtassa a következő [DotNet-csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) parancsot a terminál ablakban a tárolási bővítmény csomagjának a projekthez való hozzáadásához.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```
---
Most hozzáadhatja a tárolási kimeneti kötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A functions esetében minden típusú kötéshez `direction`, `type` és egy egyedi `name` szükséges, amely a function. JSON fájlban adható meg. Az attribútumok definiálásának módja a Function alkalmazás nyelvétől függ.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

A kötés meghatározása után a kötés `name` értékkel férhet hozzá a függvény aláírása attribútumként. Kimeneti kötés használatával nem szükséges az Azure Storage SDK-kód használata hitelesítéshez, üzenetsor-hivatkozás beszerzése vagy az adatírás. A functions futtatókörnyezet és a várólista kimeneti kötése elvégzi ezeket a feladatokat.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

A **rendszer létrehoz** egy új üzenetsor-várólistát a Storage-fiókban a functions futtatókörnyezetben a kimeneti kötés első használatakor. A Storage Explorer segítségével ellenőrizheti, hogy a várólista létrejött-e az új üzenettel együtt.

### <a name="connect-storage-explorer-to-your-account"></a>A Storage Explorer csatlakoztatása a fiókjához

Hagyja ki ezt a szakaszt, ha már telepítette Azure Storage Explorer és csatlakoztatta azt az Azure-fiókjához.

1. Futtassa az [Azure Storage Explorer] eszközt, válassza a bal oldali csatlakozási ikont, majd válassza a **fiók hozzáadása**lehetőséget.

    ![Azure-fiók hozzáadása a Microsoft Azure Storage Explorerhoz](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. A **kapcsolat** párbeszédpanelen válassza az **Azure-fiók hozzáadása**lehetőséget, válassza ki az **Azure-környezetet**, és válassza a **Bejelentkezés**lehetőséget. 

    ![Jelentkezzen be az Azure-fiókjába](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Miután sikeresen bejelentkezett a fiókjába, megjelenik a fiókjához társított összes Azure-előfizetés.

### <a name="examine-the-output-queue"></a>A kimeneti üzenetsor vizsgálata

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs palettájának megnyitásához, majd keresse meg és futtassa a `Azure Storage: Open in Storage Explorer` parancsot, és válassza ki a Storage-fiók nevét. A Storage-fiók a Azure Storage Explorerban nyílik meg.  

1. Bontsa ki az **Üzenetsorok** csomópontot, majd válassza ki az **outqueue** nevű üzenetsort. 

   Az üzenetsor tartalmazza az üzenetet, amelyet az üzenetsor kimeneti kötése létrehozott a HTTP által aktivált függvény futtatásakor. Ha az alapértelmezett *Azure* `name` értékkel hívta meg a függvényt, az üzenetsorban található üzenet a következő lesz: *A függvénynek átadott név: Azure*.

    ![Üzenetsor-üzenet látható Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Futtassa újra a függvényt, küldjön egy másik kérést, és megjelenik egy új üzenet a várólistában.  

Itt az ideje, hogy újra közzé lehessen tenni a frissített Function alkalmazást az Azure-ban.

## <a name="redeploy-and-verify-the-updated-app"></a>A frissített alkalmazás újbóli üzembe helyezése és ellenőrzése

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki a `Azure Functions: Deploy to function app...` értéket.

1. Válassza ki az első cikkben létrehozott Function alkalmazást. Mivel a projekt ugyanarra az alkalmazásba való újbóli üzembe helyezését végzi, válassza a **telepítés** lehetőséget a fájlok felülírásával kapcsolatos figyelmeztetés elvetéséhez.

1. Az üzembe helyezés befejezése után a cURL vagy egy böngésző használatával tesztelheti az újratelepített függvényt. Ahogy korábban is, fűzze hozzá a `&name=<yourname>` lekérdezési karakterláncot az URL-címhez, ahogy az alábbi példában is látható:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Ismét [tekintse meg az üzenetet a Storage-várólistán](#examine-the-output-queue) annak ellenőrzéséhez, hogy a kimeneti kötés ismét létrehoz egy új üzenetet a várólistában.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az *erőforrások* kifejezés az Azure-ban például függvényalkalmazásokra, függvényekre vagy tárfiókokra utal. Az erőforrások *erőforráscsoportokba* vannak csoportosítva, és az adott csoport törlésével a benne lévő összes erőforrás törölhető.

E rövid útmutatók elvégzéséhez erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. Ha már nincs szüksége ezekre az erőforrásokra, a következőképpen törölheti őket:

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki a `Azure Functions: Open in portal` értéket.

1. Válassza ki a Function alkalmazást, és nyomja le az ENTER billentyűt. Megnyílik a Function app (alkalmazás) lap a [Azure Portal](https://portal.azure.com).

1. Az **Áttekintés** lapon válassza ki az elnevezett hivatkozást az **erőforráscsoport**területen.

    ![Válassza ki az erőforráscsoportot, amelyet törölni kíván a függvényalkalmazás oldaláról.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. Az **Erőforráscsoport** oldalon tekintse át a csoportban lévő erőforrások listáját, és győződjön meg arról, hogy a törölni kívánt elemek szerepelnek benne.
 
1. Válassza az **Erőforráscsoport törlése** elemet, majd kövesse az utasításokat.

   A törlés eltarthat néhány percig. Amint a művelet befejeződött, néhány másodpercre egy értesítés jelenik meg. Az értesítést úgy is megtekintheti, ha kiválasztja a harang ikont az oldal tetején.

## <a name="next-steps"></a>Következő lépések

Frissítette a HTTP által aktivált függvényt az adattárolási várólistába való íráshoz. További információ a függvények fejlesztéséről: [Azure functions fejlesztése a Visual Studio Code használatával](functions-develop-vs-code.md).

Ezután engedélyezze Application Insights figyelését a Function alkalmazáshoz:

> [!div class="nextstepaction"]
> [Application Insights-integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/

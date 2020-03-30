---
title: Tartós függvények létrehozása az Azure Portal használatával
description: Ismerje meg, hogyan telepítheti a Durable Functions bővítményt az Azure Functions portálfejlesztéshez.
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: azfuncdf
ms.openlocfilehash: 0060088acb100036c094406e01d0d736a4af88eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769642"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Tartós függvények létrehozása az Azure Portal használatával

Az Azure Functions [tartós függvények](durable-functions-overview.md) bővítménye a [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)nuGet csomagban található. Ezt a bővítményt telepíteni kell a függvényalkalmazásban. Ez a cikk bemutatja, hogyan telepítheti ezt a csomagot, hogy tartós függvények az Azure Portalon.

> [!NOTE]
> 
> * Ha a C# függvényben tartós funkciókat fejleszt, inkább a [Visual Studio 2019 fejlesztését](durable-functions-create-first-csharp.md)érdemes figyelembe venni.
> * Ha javascriptben tartós függvényeket fejleszt, inkább a [Visual Studio Code fejlesztését](./quickstart-js-vscode.md)érdemes figyelembe venni.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Bármely függvény végrehajtásának üzemeltetéséhez rendelkeznie kell egy függvényalkalmazással. A függvényalkalmazás lehetővé teszi a függvények logikai egységként történő csoportosítását az erőforrások egyszerűbb kezelése, üzembe helyezése, méretezése és megosztása érdekében. Létrehozhat .NET vagy JavaScript alkalmazást.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Alapértelmezés szerint a létrehozott függvényalkalmazás az Azure Functions futásidejű 2.x-es verzióját használja. A Durable Functions bővítmény az Azure Functions futásidejű C#-ban 1.x és 2.x-es verzióján, valamint a JavaScript 2.x-es verzióján is működik. A sablonok azonban csak akkor érhetők el, ha a futásidejű 2.x-es verzióját célozza meg, függetlenül a választott nyelvtől.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>A tartós funkciókat ellátó npm csomag telepítése (csak JavaScript esetén)

Ha JavaScript durable functions-et hoz létre, telepítenie kell az [ `durable-functions` npm csomagot.](https://www.npmjs.com/package/durable-functions)

1. Válassza ki a függvényalkalmazás nevét, majd a **Platform Features**, majd **a Speciális eszközök (Kudu)** lehetőséget.

   ![Funkciók platform funkciók válassza Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. A Kudu konzolon belül válassza a **Debug konzol,** majd a **CMD**lehetőséget.

   ![Kudu hibakeresési konzol](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. A függvényalkalmazás fájlkönyvtár-struktúrájának meg kell jelennie. Navigáljon a `site/wwwroot` mappához. Innen úgy tölthet fel `package.json` egy fájlt, hogy behúzza a fájlkönyvtár ablakába. A `package.json` minta az alábbiakban található:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Kudu feltöltés package.json](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Miután `package.json` feltöltötte a `npm install` parancsot, futtassa a parancsot a Kudu távoli végrehajtási konzolról.

   ![Kudu fuss npm telepítés](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Orchestrator függvény létrehozása

1. Bontsa ki a **+** függvényalkalmazást, és kattintson a **Funkciók**gomb ra. Ha ez az első függvény a függvényalkalmazásban, válassza a **Portálba épített**, majd a **Folytatás** lehetőséget. Ha nem ez az első, folytassa a harmadik lépéssel.

   ![Függvények gyors létrehozásának oldala az Azure Portalon](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Válassza ki a **További sablonok**, majd a **Befejezés és sablonok megtekintése** lehetőséget.

    ![Függvények rövid útmutatója - további sablonok kiválasztása](./media/durable-functions-create-portal/add-first-function.png)

1. A keresőmezőbe írja `durable` be a kívánt parancsot, és válassza a **Durable Functions HTTP starter** sablont.

1. Amikor a rendszer kéri, válassza **a Telepítés** lehetőséget az Azure DurableTask bővítmény és a függvényalkalmazásban lévő függőségek telepítéséhez. A bővítményt csak egyszer kell telepíteni az adott függvényalkalmazáshoz. A telepítést követően válassza a **Folytatás** gombot.

    ![Kötési bővítmények telepítése](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. A telepítés befejezése után nevezze `HttpStart` el az új függvényt, és válassza a **Létrehozás gombot.** A létrehozott függvény a vezénylés elindításához használatos.

1. Hozzon létre egy másik függvényt a függvényalkalmazásban, ezúttal a **Durable Functions Orchestrator** sablon használatával. Nevezze el az `HelloSequence`új vezénylési függvényt.

1. Hozzon létre `Hello` egy harmadik függvényt, amelyet a **Tartós függvények tevékenység sablonnal** neveznek el.

## <a name="test-the-durable-function-orchestration"></a>A tartós függvény vezénylésének tesztelése

1. Lépjen vissza a **HttpStart** függvényhez, válassza **a</> függvény URL-címének bemásolása** és az URL **másolása lehetőséget.** Ezzel az URL-címmel indíthatja el a **HelloSequence** függvényt.

1. A HTTP-eszköz, például a Postman vagy a cURL használatával POST-kérelmet küldhet a másolt URL-címre. A következő példa egy cURL parancs, amely POST-kérelmet küld a tartós függvénynek:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    Ebben a `{your-function-app-name}` példában a tartomány, amely a függvényalkalmazás neve. A válaszüzenet URI-végpontok készletét fogja tartalmazni, amelyek a végrehajtás monitorozásához és szabályozásához használhatók fel hasonlóan a következő példához:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Hívja `statusQueryGetUri` meg a végpont URI-ját, és láthatja a tartós függvény aktuális állapotát, amely a következő példához hasonlóan nézhet ki:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Folytassa a `statusQueryGetUri` végpont hívását, amíg az állapot **befejezettre**nem változik, és a következő példához hasonló választ lát:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Az első tartós függvény most már működik az Azure-ban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a tartós függvények gyakori mintáiról](durable-functions-overview.md#application-patterns)

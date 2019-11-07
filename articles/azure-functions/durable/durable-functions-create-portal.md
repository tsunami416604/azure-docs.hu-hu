---
title: Durable Functions létrehozása a Azure Portal használatával
description: Megtudhatja, hogyan telepítheti a Azure Functions Durable Functions bővítményét a portál fejlesztéséhez.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 238969b320608c08491c3d3e85870d57f41c1589
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614980"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Durable Functions létrehozása a Azure Portal használatával

A Azure Functions [Durable functions](durable-functions-overview.md) -bővítménye a [Microsoft. Azure. Webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)NuGet csomagban található. Ezt a bővítményt telepíteni kell a Function alkalmazásban. Ez a cikk bemutatja, hogyan telepítheti a csomagot úgy, hogy tartós funkciókat fejlesszen ki a Azure Portalban.

> [!NOTE]
> 
> * Ha tartós funkciókat fejleszt a alkalmazásban C#, érdemes megfontolnia a [Visual Studio 2019 fejlesztését](durable-functions-create-first-csharp.md).
> * Ha a JavaScriptben tartós funkciókat fejleszt, érdemes megfontolnia a [Visual Studio Code-fejlesztést](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

A függvények végrehajtásának üzemeltetéséhez Function-alkalmazás szükséges. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében. Létrehozhat .NET-vagy JavaScript-alkalmazást is.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Alapértelmezés szerint a létrehozott Function alkalmazás a Azure Functions futtatókörnyezet 2. x verzióját használja. A Durable Functions bővítmény a (z) és a (z) Azure Functions futtatókörnyezetének 1. x és C#2. x verziójában is működik a JavaScriptben, és a 2. x verzió. A sablonok azonban csak akkor érhetők el, ha a kiválasztott nyelvtől függetlenül megcélozzák a futtatókörnyezet 2. x verzióját.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>A tartós függvények NPM-csomagjának telepítése (csak JavaScript)

Ha JavaScript-Durable Functions hoz létre, akkor telepítenie kell a [`durable-functions` NPM csomagot](https://www.npmjs.com/package/durable-functions).

1. Válassza ki a függvény alkalmazásának nevét, majd a **platform funkcióit**, majd a **speciális eszközöket (kudu)** .

   ![Functions platform-funkciók kudu kiválasztása](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. A kudu-konzolon válassza a **Debug konzol** , majd a **cmd parancsot**.

   ![Kudu hibakeresési konzol](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. A Function alkalmazás fájljának könyvtár-struktúrájának meg kell jelennie. Navigáljon a `site/wwwroot` mappához. Innen feltölthet egy `package.json` fájlt úgy, hogy áthúzza a fájl könyvtár ablakba. A minta `package.json` a következő:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Kudu feltöltési csomag. JSON](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. A `package.json` feltöltése után futtassa a `npm install` parancsot a kudu távoli végrehajtási konzolról.

   ![Kudu NPM-telepítés futtatása](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Orchestrator függvény létrehozása

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények+ elem melletti**  gombra. Ha ez az első függvény a függvényalkalmazásban, válassza a **Portálba épített**, majd a **Folytatás** lehetőséget. Ha nem ez az első, folytassa a harmadik lépéssel.

   ![Függvények gyors létrehozásának oldala az Azure Portalon](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Válassza ki a **További sablonok**, majd a **Befejezés és sablonok megtekintése** lehetőséget.

    ![Függvények rövid útmutatója - további sablonok kiválasztása](./media/durable-functions-create-portal/add-first-function.png)

1. A keresőmezőbe írja be a `durable` kifejezést, majd válassza a **DURABLE FUNCTIONS http-indító** sablont.

1. Ha a rendszer kéri, válassza a **telepítés** lehetőséget az Azure DurableTask bővítmény bármely függőségének telepítéséhez a Function alkalmazásban. Az Add Function alkalmazáshoz csak egyszer kell telepítenie a bővítményt. A telepítést követően válassza a **Folytatás** gombot.

    ![Kötési bővítmények telepítése](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. A telepítés befejezése után nevezze el az új függvényt `HttpStart` majd válassza a **Létrehozás**lehetőséget. A létrehozott függvény az előkészítés elindítására szolgál.

1. Hozzon létre egy másik függvényt a Function alkalmazásban, ezúttal a **Durable functions Orchestrator** sablon használatával. Nevezze el az új összehangoló függvényt `HelloSequence`.

1. Hozzon létre egy `Hello` nevű harmadik függvényt a **Durable functions Activity** sablon használatával.

## <a name="test-the-durable-function-orchestration"></a>A tartós függvények előkészítésének tesztelése

1. Lépjen vissza a **HttpStart** függvényhez, válassza a **</> a függvény URL-címének beolvasása** lehetőséget, és **másolja** az URL-címet. Ezt az URL-címet használhatja a **HelloSequence** függvény elindításához.

1. Használjon olyan HTTP-eszközt, mint a Poster vagy a cURL, hogy POST-kérést küldjön a másolt URL-címre. A következő példa egy olyan cURL-parancs, amely POST-kérést küld a tartós függvénynek:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    Ebben a példában a `{your-function-app-name}` az a tartomány, amely a Function alkalmazás neve. A válaszüzenet olyan URI-végpontokat tartalmaz, amelyek segítségével figyelheti és kezelheti a végrehajtást, ami a következő példához hasonlóan néz ki:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Hívja meg az `statusQueryGetUri` Endpoint URI-t, és láthatja a tartós függvény aktuális állapotát, ami a következő példához hasonló lehet:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Folytassa a `statusQueryGetUri` végpont meghívását mindaddig, amíg az állapot be nem **fejeződik**, és az alábbi példához hasonló választ láthat:

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

Az első tartós funkció most már működik az Azure-ban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók a tartós függvények gyakori mintái](durable-functions-overview.md#application-patterns)

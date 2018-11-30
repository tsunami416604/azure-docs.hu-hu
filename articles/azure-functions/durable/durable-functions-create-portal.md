---
title: Hozzon létre Durable Functions az Azure portal használatával
description: Ismerje meg, hogyan telepítheti a Durable Functions bővítmény az Azure Functions fejlesztői portál.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: acbba991e6dcce56fad7f27c45f85214cc8fc707
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642304"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Hozzon létre Durable Functions az Azure portal használatával

A [Durable Functions](durable-functions-overview.md) kiterjesztése az Azure Functions szolgáltatáshoz van megadva a NuGet-csomagot a [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Ezzel a bővítménnyel kell telepíteni a függvényalkalmazásban. Ez a cikk bemutatja, hogyan kell telepítenie a csomagot, így durable functions az Azure Portalon is fejleszthet.

>[!NOTE]
>
>* Ha a tartós függvények fejleszt C#, Ehelyett érdemes [Visual Studio 2017 fejlesztői](durable-functions-create-first-csharp.md).
* Tartós függvények használata Javascriptben fejleszt, Ehelyett érdemes **Visual Studio Code fejlesztési**.
>
>Tartós függvények létrehozása az JavaScript használatával még nem támogatott a portálon. Használja helyette a Visual Studio Code-ot.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a bármely függvény végrehajtása. A függvényalkalmazás a függvények egyszerűbb felügyelete, üzembe helyezéséhez és erőforrás-megosztás logikai egységbe csoportosítsa a teszi lehetővé. Létre kell hoznia egy C# függvényalkalmazást, mivel a JavaScript-sablonok még nem támogatottak a Durable Functions.  

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Alapértelmezés szerint a telepített verzióját használja a létrehozott függvényalkalmazás 2.x verzióját az Azure Functions futtatókörnyezettel. Mindkét verziót működik a Durable Functions bővítmény 1.x és a 2.x verzióját az Azure Functions futtatókörnyezettel. Azonban sablonok esetén csak érhetők el verzió célzó 2.x verziójú futtatókörnyezet.

## <a name="create-an-orchestrator-function"></a>Az orchestrator-függvény létrehozása

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Ha ez az első függvény a függvényalkalmazásban, válassza a **Portálba épített**, majd a **Folytatás** lehetőséget. Ha nem ez az első, folytassa a harmadik lépéssel.

   ![Függvények gyors létrehozásának oldala az Azure Portalon](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Válassza ki a **További sablonok**, majd a **Befejezés és sablonok megtekintése** lehetőséget.

    ![Függvények rövid útmutatója - további sablonok kiválasztása](./media/durable-functions-create-portal/add-first-function.png)

1. A keresőmezőbe írja be a `durable` majd válassza a **Durable Functions – HTTP-alapkészlet** sablont.

1. Amikor a rendszer kéri, válassza ki a **telepítése** függőségek az Azure DurableTask bővítmény telepítéséhez a függvényalkalmazáshoz. Csak ki kell telepíteni a bővítményt, egyszer a függvényalkalmazás számára. A telepítést követően válassza a **Folytatás** gombot.

    ![Kötési bővítmények telepítése](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. A telepítés befejezése után az új függvény neve `HttpStart` válassza **létrehozás**. Indítsa el a vezénylés a létrehozott függvény segítségével.

1. A függvényalkalmazás, egy másik függvény létrehozása használatával most a **Durable Functions-vezénylő** sablont. Adjon nevet az új vezénylési függvénynek `HelloSequence`.

1. Hozzon létre egy harmadik függvényt nevű `Hello` használatával a **Durable Functions-tevékenység** sablont.

## <a name="test-the-durable-function-orchestration"></a>A tartós függvény vezénylési tesztelése

1. Lépjen vissza a **HttpStart** működik, válassza a **<> / Get függvény URL-Címének** és **másolási** az URL-címet. Az URL-címet szeretne használni a **HelloSequence** függvény.

1. Egy HTTP-eszköz, például a Postman vagy a cURL használatával küldünk egy POST kérést másolt URL-CÍMÉT. Az alábbi példa a cURL-parancsot, amely egy POST kérést küld a tartós függvény:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    Ebben a példában `{your-function-app-name}` az a tartomány, a függvényalkalmazás neve. A válaszüzenet tartalmaz URI végpontok, amelyek segítségével figyelheti és kezelheti a végrehajtási, amely a következő példához hasonlóan néz ki:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Hívja a `statusQueryGetUri` végpont URI-t, és tekintse meg az aktuális állapotát a tartós függvény, amely ebben a példában hasonló lehet:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Továbbra is hívása a `statusQueryGetUri` végpont addig, amíg a állapota **befejezve**, és a egy válasz az alábbi példához hasonlóan jelenik: 

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

Az első tartós függvény működik-e már az Azure-ban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tartós függvény előforduló általános minták megismeréséhez](durable-functions-overview.md)
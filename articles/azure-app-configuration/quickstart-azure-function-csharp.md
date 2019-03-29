---
title: 'Gyors útmutató: Azure-alkalmazás konfigurálása az Azure Functions használatával |} A Microsoft Docs'
description: A Azure-alkalmazások konfigurálása az Azure Functions gyors üzembe helyezés.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Azure Functions
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 9b0c48b3a3fb3a1b4e4fbe94a368297823a86778
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579580"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Gyors útmutató: Alkalmazások konfigurálása az Azure-függvény létrehozása

Az Azure App konfigurálása felügyelt konfigurációs szolgáltatása az Azure-ban. Könnyedén tárolhatja, és a egy helyet, amely választja el az alkalmazás beállításait kezelheti a kódból használhatja azt. Ez a rövid útmutató bemutatja, hogyan belefoglalhatja a szolgáltatást az Azure-függvény. 

Bármely Kódszerkesztő segítségével ebben a rövid útmutatóban található lépések elvégzése. [A Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, macOS és Linux platformokon az érhető el.

![A rövid útmutató teljes helyi](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató elvégzéséhez telepítse [Visual Studio 2017](https://visualstudio.microsoft.com/vs). Győződjön meg arról, hogy a **Azure-fejlesztési** számítási feladatot is telepítve van. Is telepítheti a [legújabb Azure Functions tools](../azure-functions/functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Hozzon létre egy alkalmazást a konfigurációs adattároló

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Egy alkalmazás a konfigurációs adattároló csatlakozni

1. Kattintson jobb gombbal a projektre, és válassza ki **NuGet-csomagok kezelése**. Az a **Tallózás** lapon, keresése és a következő NuGet-csomagok hozzáadása a projekthez. Ha nem látja őket, válassza ki a **előzetes verzió** jelölőnégyzetet.

    ```
    Microsoft.Extensions.Configuration.AzureAppConfiguration 1.0.0 preview or later
    ```

2. Nyissa meg *Function1.cs*, és vegyen fel egy hivatkozást az alkalmazás konfigurációs .NET Core konfigurációszolgáltatót.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Frissítés a `Run` metódus meghívásával használhatja az Alkalmazáskonfigurációt `builder.AddAzureAppConfiguration()`.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        var config = builder.Build();
        string message = config["TestApp:Settings:Message"];
        message = message ?? req.Query["message"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        message = message ?? data?.message;

        return message != null
            ? (ActionResult) new OkObjectResult(message)
            : new BadRequestObjectResult("Please pass a message from a configuration store, on the query string or in the request body");
    }
    ```

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

1. Nevű környezeti változó értéke **ConnectionString**, és állítsa be az alkalmazás a konfigurációs adattárolónál a hozzáférési kulcsot. Ha használja a Windows-parancssort, futtassa a következő parancsot, és a parancssort, a módosítás érvénybe léptetéséhez indítsa újra:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell használatakor a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Ha macOS vagy Linux használ, futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a kérelmet, töltse le és telepítse a Visual studióból **Azure Functions Core (CLI)** eszközök. Emellett szüksége lehet olyan érvényes tűzfalkivétel engedélyezéséhez, hogy az eszközöket képes kezelni a HTTP-kérelmekre.

3. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![A rövid útmutató függvény hibakeresése a VS-ben](./media/quickstarts/function-visual-studio-debugging.png)

4. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az alábbi képen látható a böngészőben a helyi GET kérelemre a függvény által visszaadott válasz.

    ![Helyi rövid függvény indítása](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs adattároló, és az egy Azure-függvényt használta azt. Ismerje meg, hogyan használhatja az Alkalmazáskonfigurációt, folytassa a következő oktatóanyag azt mutatja be a hitelesítést.

> [!div class="nextstepaction"]
> [Az Azure-erőforrások integráció felügyelt identitásokból](./integrate-azure-managed-service-identity.md)

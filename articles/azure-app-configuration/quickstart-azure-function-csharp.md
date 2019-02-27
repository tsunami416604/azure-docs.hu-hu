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
ms.openlocfilehash: a6ed644334977f13107b12f955b1fbf015f731d1
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884887"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Gyors útmutató: Alkalmazások konfigurálása az Azure-függvény létrehozása

Az Azure App konfigurálása felügyelt konfigurációs szolgáltatása az Azure-ban. Ez lehetővé teszi, hogy könnyedén tárolhatja és kezelheti a kódot egy helyet, amely választja el az alkalmazás beállításait. Ez a rövid útmutató bemutatja, hogyan belefoglalhatja a szolgáltatást az Azure-függvény. 

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. A [Visual Studio Code](https://code.visualstudio.com/) például jó választás lehet, és Windows, macOS és Linux platformokon is használható.

![Rövid útmutató – teljes helyi](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítse [Visual Studio 2017](https://visualstudio.microsoft.com/vs) (és ellenőrizze, hogy a **Azure-fejlesztési** számítási feladatot is telepítve van) és a [legújabb Azure Functions tools](../azure-functions/functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Hozzon létre egy alkalmazást a konfigurációs adattároló

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-app-configuration-store"></a>Csatlakozhat az alkalmazás a konfigurációs adattároló

1. Nyissa meg *Function1.cs* , és adjon hozzá egy hivatkozást az alkalmazás konfigurációs .NET Core-konfigurációszolgáltatót.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

2. Frissítés a `Run` metódus meghívásával használhatja az Alkalmazáskonfigurációt `builder.AddAzureAppConfiguration()`.

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

1. Nevű környezeti változó értéke **ConnectionString** , és állítsa be az alkalmazás a konfigurációs adattárolónál a hozzáférési kulcsot. Ha Windows-parancssort használja, hajtsa végre a következő parancsot, és a parancssort, a módosítás érvénybe léptetéséhez indítsa újra:

        setx ConnectionString "Endpoint=<service_endpoint>;Id=<store_id>;Secret=<secret_key>="

    Ha a Windows Powershellt használ, hajtsa végre a következő parancsot:

        $Env:ConnectionString = "Endpoint=<service_endpoint>;Id=<store_id>;Secret=<secret_key>="

    Ha macOS vagy Linux használ, hajtsa végre a következő parancsot:

        export ConnectionString='Endpoint=<service_endpoint>;Id=<store_id>;Secret=<secret_key>='

2. A függvény teszteléséhez nyomja le a **F5**. Ha a rendszer kéri, fogadja el a kérelmet, töltse le és telepítse a Visual studióból **Azure Functions Core (CLI)** eszközök. Lehet, hogy egy tűzfalkivételt is engedélyeznie kell, hogy az eszközök kezelhessék a HTTP-kéréseket.

3. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![A rövid útmutató függvény hibakeresése a VS-ben](./media/quickstarts/function-visual-studio-debugging.png)

4. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az alábbiakban látható a böngészőben a helyi GET kérelemre a függvény által visszaadott válasz:

    ![Helyi rövid függvény indítása](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy új alkalmazás-konfigurációs adattároló létrehozta és a egy Azure-függvényt használta azt. Alkalmazáskonfiguráció használatával kapcsolatos további információkért folytassa a következő oktatóanyag azt mutatja be a hitelesítést.

> [!div class="nextstepaction"]
> [Az Azure-erőforrások integráció felügyelt identitásokból](./integrate-azure-managed-service-identity.md)

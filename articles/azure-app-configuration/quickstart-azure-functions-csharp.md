---
title: Gyors útmutató az Azure-alkalmazások konfigurálásához a Azure Functionskal | Microsoft Docs
description: Útmutató az Azure-alkalmazások konfigurálásához a Azure Functions használatával.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 268e6c5a999244eb643990143d1102d129b7af68
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310056"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Gyors útmutató: Azure Functions-alkalmazás létrehozása az Azure-alkalmazás konfigurálásával

Ebben a rövid útmutatóban beépíti az Azure app Configuration szolgáltatást egy Azure Functions alkalmazásba, hogy központilag központosítsa az összes Alkalmazásbeállítások tárolását és kezelését.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) az **Azure-fejlesztési** számítási feladattal.
- [Eszközök Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Configuration Explorer** >  **+ Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

    | Jelmagyarázat | Value (Díj) |
    |---|---|
    | TestApp: beállítások: üzenet | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

## <a name="create-a-functions-app"></a>Functions-alkalmazás létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**lehetőséget. A **Tallózás** lapon keresse meg és adja hozzá a következő NuGet-csomagokat a projekthez. Ha nem találja őket, jelölje be az **előzetes verzió belefoglalása** jelölőnégyzetet.

    ```
    Microsoft.Extensions.Configuration.AzureAppConfiguration 3.0.0-preview-010550001-251 or later
    ```

2. Nyissa meg a *Function1.cs*, és adja hozzá a .net Core konfiguráció és az alkalmazás konfigurációs szolgáltatójának névtereit.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```
3. Adjon hozzá egy `Configuration` nevű `static` tulajdonságot a `IConfiguration`egy egyedi példányának létrehozásához. Ezután vegyen fel egy `static` konstruktort az alkalmazás-konfigurációhoz való kapcsolódáshoz `AddAzureAppConfiguration()`meghívásával. Ez az alkalmazás indításakor automatikusan betöltődik a konfigurációba. Ugyanezt a konfigurációs példányt fogjuk használni az összes functions híváshoz később.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```
4. Frissítse a `Run` metódust a konfiguráció értékeinek olvasásához.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

    ```CLI
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```
    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Nyomja le az F5 billentyűt a függvény teszteléséhez. Ha a rendszer kéri, fogadja el a Visual Studiótól érkező kérést **Azure functions Core (CLI)** eszközök letöltéséhez és telepítéséhez. Előfordulhat, hogy egy tűzfal-kivételt is engedélyeznie kell, hogy az eszközök kezelni tudják a HTTP-kérelmeket.

3. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Gyors üzembe helyezési funkció hibakeresése a VS-ben](./media/quickstarts/function-visual-studio-debugging.png)

4. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az alábbi képen a böngészőben a függvény által visszaadott helyi GET kérelemre adott válasz látható.

    ![A Gyorsindítás funkció helyi elindítása](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és azt egy Azure Functions alkalmazással használta az [alkalmazás-konfigurációs szolgáltatón](https://go.microsoft.com/fwlink/?linkid=2074664)keresztül. Ha szeretné megtudni, hogyan konfigurálhatja a Azure Functions alkalmazást a konfigurációs beállítások dinamikus frissítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-azure-functions-csharp.md)

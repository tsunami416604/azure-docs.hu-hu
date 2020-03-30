---
title: Az Azure App konfigurációjának rövid útmutatója az Azure Functions funkcióival | Microsoft dokumentumok
description: Rövid útmutató az Azure App Configuration és az Azure Functions használatához.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 2f6efdad7ab0685e58d2edd73bc36b758e8dbae2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245497"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Rövid útmutató: Azure Functions alkalmazás létrehozása az Azure App konfigurációjával

Ebben a rövid útmutatóban az Azure App Konfigurációs szolgáltatás egy Azure Functions alkalmazás központosíthatja a tárolás és az alkalmazás beállításainak kezelése a kódtól elkülönítve.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) az **Azure fejlesztési** munkaterheléssel.
- [Az Azure Functions eszközei](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Alkalmazáskonfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. A következő kulcsérték-párok hozzáadásához válassza a **Configuration Explorer** > + Create**Key-value (Kulcsérték** **létrehozása)** > lehetőséget:

    | Kulcs | Érték |
    |---|---|
    | TestApp:Beállítások:Üzenet | Az Azure App konfigurációjából származó adatok |

    Egyelőre hagyja üresen a **Címke** és **a Tartalomtípus** mezőt.

7. Kattintson az **Alkalmaz** gombra.

## <a name="create-a-functions-app"></a>Functions alkalmazás létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Csatlakozás alkalmazáskonfigurációs tárolóhoz

1. Kattintson a jobb gombbal a projektre, és válassza **a NuGet csomagok kezelése parancsot.** A **Tallózás** lapon keresse `Microsoft.Extensions.Configuration.AzureAppConfiguration` meg és adja hozzá a NuGet csomagot a projekthez. Ha nem találja, jelölje be az **Előzetes kiadás belefoglalása** jelölőnégyzetet.

2. Nyissa *meg a Function1.cs,* és adja hozzá a .NET Core konfiguráció és az alkalmazáskonfigurációs szolgáltató névtereit.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Adjon `static` hozzá `Configuration` egy nevű tulajdonságot a `IConfiguration`singleton példányának létrehozásához. Ezután `static` adjon hozzá egy konstruktot, hogy csatlakozzon az alkalmazás konfigurációjához a hívással. `AddAzureAppConfiguration()` Ez egyszer betölti a konfigurációt az alkalmazás indításakor. Ugyanaz a konfigurációs példány lesz később az összes Függvényhíváshoz.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Frissítse `Run` a metódust, hogy értékeket olvasson be a konfigurációból.

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

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazáskonfigurációs tároló hozzáférési kulcsára. Ha a Windows parancssorát használja, futtassa a következő parancsot, és indítsa újra a parancssort a módosítás érvénybe léptetéséhez:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Ha windows PowerShellt használ, futtassa a következő parancsot:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    MacOS vagy Linux használata esetén futtassa a következő parancsot:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. A funkció teszteléséhez nyomja meg az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az **Azure Functions Core (CLI)** eszközök letöltésére és telepítésére. Előfordulhat, hogy engedélyeznie kell egy tűzfalkivételt is, hogy az eszközök kezelni tudják a HTTP-kérelmeket.

3. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Gyorsindítási függvény hibakeresése a VS-ben](./media/quickstarts/function-visual-studio-debugging.png)

4. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az alábbi képen a böngészőben a függvény által visszaadott helyi GET-kérésre adott válasz látható.

    ![A Quickstart függvény helyi indítása](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazáskonfigurációs áruházat, és használta azt egy Azure Functions alkalmazással az [alkalmazáskonfigurációs szolgáltatón](https://go.microsoft.com/fwlink/?linkid=2074664)keresztül. Ha meg szeretné tudni, hogyan konfigurálhatja az Azure Functions alkalmazást a konfigurációs beállítások dinamikus frissítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-azure-functions-csharp.md)

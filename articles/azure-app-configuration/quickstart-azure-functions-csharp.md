---
title: Gyors útmutató az Azure-alkalmazások konfigurálásához a Azure Functionskal | Microsoft Docs
description: Ebben a rövid útmutatóban egy Azure Functions alkalmazást hoz végre az Azure app Configuration és a C# használatával. Hozzon létre és kapcsolódjon egy alkalmazás-konfigurációs tárolóhoz. A függvény helyi tesztelése.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: lcozzens
ms.openlocfilehash: b62bf900e3e7859437b10b7b45801ee0dc575282
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767778"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Gyors útmutató: Azure Functions-alkalmazás létrehozása az Azure-alkalmazás konfigurálásával

Ebben a rövid útmutatóban beépíti az Azure app Configuration szolgáltatást egy Azure Functions alkalmazásba, hogy központilag központosítsa az összes Alkalmazásbeállítások tárolását és kezelését.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) az **Azure-fejlesztési** számítási feladattal.
- [Eszközök Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **Configuration Explorer**  >  **+ Create**  >  A következő kulcs-érték párok hozzáadásához válassza a Configuration Explorer +**kulcs létrehozása-érték** elemet:

    | Kulcs | Érték |
    |---|---|
    | TestApp: beállítások: üzenet | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

8. Kattintson az **Alkalmaz** gombra.

## <a name="create-a-functions-app"></a>Functions-alkalmazás létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**lehetőséget. A **Tallózás** lapon keresse meg és adja hozzá a `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet csomagot a projekthez. Ha nem találja, jelölje be az **előzetes verzió belefoglalása** jelölőnégyzetet.

2. Nyissa meg a *Function1.cs*, és adja hozzá a .net Core konfiguráció és az alkalmazás konfigurációs szolgáltatójának névtereit.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Adjon hozzá egy `static` nevű tulajdonságot a (z `Configuration` ) egyedi példányának létrehozásához `IConfiguration` . Ezután adjon hozzá egy `static` konstruktort az alkalmazás konfigurációjához való kapcsolódáshoz a hívásával `AddAzureAppConfiguration()` . Ez az alkalmazás indításakor automatikusan betöltődik a konfigurációba. Ugyanezt a konfigurációs példányt fogjuk használni az összes functions híváshoz később.

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

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

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

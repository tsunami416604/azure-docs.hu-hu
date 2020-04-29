---
title: Oktatóanyag az Azure-alkalmazások konfigurációjának dinamikus konfigurációjának használatához egy Azure Functions alkalmazásban | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan lehet dinamikusan frissíteni a Azure Functions-alkalmazások konfigurációs információit
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ba70d5f186c1424b2019716ab7a87aeae85f8913
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74185449"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Oktatóanyag: dinamikus konfiguráció használata egy Azure Functions alkalmazásban

Az App Configuration .NET Standard konfigurációs szolgáltatója támogatja a gyorsítótárazást és a konfiguráció frissítését dinamikusan vezérelt alkalmazási tevékenységgel. Ez az oktatóanyag bemutatja, hogyan valósítható meg a dinamikus konfigurációs frissítések a kódban. Ez a rövid útmutatókban bemutatott Azure Functions alkalmazásra épül. Mielőtt továbblépne, először [hozzon létre egy Azure functions-alkalmazást az Azure app Configuration szolgáltatással](./quickstart-azure-functions-csharp.md) .

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a Azure Functions alkalmazást, hogy frissítse a konfigurációját az alkalmazás konfigurációs tárolójának változásaira válaszul.
> * Adja meg a legújabb konfigurációt a Azure Functions-hívásokhoz.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) az **Azure-fejlesztési** számítási feladattal
- [Eszközök Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- [Azure functions-alkalmazás létrehozása az Azure-alkalmazás konfigurálásával –](./quickstart-azure-functions-csharp.md) gyors üzembe helyezési útmutató

## <a name="reload-data-from-app-configuration"></a>Adatok újratöltése az alkalmazás konfigurációjától

1. Nyissa meg a *Function1.cs*. `static` A `Configuration`tulajdonság mellett vegyen fel egy `static` új tulajdonságot `ConfigurationRefresher` , `IConfigurationRefresher` amely megtartja, hogy a rendszer a függvények hívásakor a konfigurációs frissítéseket a későbbiekben fogja használni.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Frissítse a konstruktort, és használja `ConfigureRefresh` a metódust az alkalmazás konfigurációs tárolójából frissíteni kívánt beállítás megadásához. A `IConfigurationRefresher` rendszer egy példányt kér le a `GetRefresher` metódus használatával. Szükség esetén a konfigurációs gyorsítótár lejárati idejének időablakát is módosítjuk az alapértelmezett 30 másodperctől számított 1 percre.

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. Frissítse a `Run` metódust és a jelet a konfiguráció frissítéséhez `Refresh` a függvények hívásának elején található metódus használatával. Ez nem lesz-op, ha a gyorsítótár lejárati ideje nem érhető el. Távolítsa `await` el az operátort, ha azt szeretné, hogy a konfiguráció a blokkolás nélkül frissüljön.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.Refresh();

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studiótól érkező kérést **Azure functions Core (CLI)** eszközök letöltéséhez és telepítéséhez. Előfordulhat, hogy egy tűzfal-kivételt is engedélyeznie kell, hogy az eszközök kezelni tudják a HTTP-kérelmeket.

3. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Gyors üzembe helyezési funkció hibakeresése a VS-ben](./media/quickstarts/function-visual-studio-debugging.png)

4. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az alábbi képen a böngészőben a függvény által visszaadott helyi GET kérelemre adott válasz látható.

    ![A Gyorsindítás funkció helyi elindítása](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás**lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

6. Válassza a **Configuration Explorer**lehetőséget, és frissítse a következő kulcs értékeit:

    | Kulcs | Érték |
    |---|---|
    | TestApp: beállítások: üzenet | Adatok az Azure-alkalmazás konfigurációjában – frissítve |

7. Frissítse a böngészőt néhányszor. Ha a gyorsítótárazott beállítás egy perc elteltével lejár, az oldal megjeleníti a függvények a frissített értékkel való hívásának válaszát.

    ![A gyors üzembe helyezési funkció helyi frissítése](./media/quickstarts/dotnet-core-function-refresh-local.png)

Az oktatóanyagban használt példa kód letölthető az [alkalmazás-konfiguráció GitHub](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction) -tárházból

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte a Azure Functions alkalmazást, hogy dinamikusan frissítse a konfigurációs beállításokat az alkalmazás konfigurációjában. Ha meg szeretné tudni, hogyan használható az Azure felügyelt identitása az alkalmazás-konfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)

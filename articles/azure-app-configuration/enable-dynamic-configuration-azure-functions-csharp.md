---
title: Oktatóanyag az Azure App Configuration dinamikus konfigurációjának azure Functions alkalmazásában való használatához | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan frissítheti dinamikusan az Azure Functions-alkalmazások konfigurációs adatait
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74185449"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Oktatóanyag: Dinamikus konfiguráció használata egy Azure Functions alkalmazásban

Az Alkalmazáskonfiguráció .NET Standard konfigurációszolgáltató támogatja a gyorsítótárazást és a dinamikusan vezérelt konfigurációt az alkalmazástevékenység által vezérelve. Ez az oktatóanyag bemutatja, hogyan valósíthatja meg a dinamikus konfigurációs frissítéseket a kódban. A rövid útmutatókban bevezetett Azure Functions alkalmazásra épül. Mielőtt folytatna, először fejezze be [az Azure-függvények létrehozása alkalmazást az Azure App Configuration alkalmazással.](./quickstart-azure-functions-csharp.md)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be az Azure Functions alkalmazást, hogy frissítse a konfigurációját az alkalmazáskonfigurációs áruház változásaira válaszul.
> * Adja be a legújabb konfigurációt az Azure Functions-hívások.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) az **Azure fejlesztési** munkaterhelésével
- [Az Azure Functions eszközei](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- A gyorsindítás befejezése [Azure-függvényalkalmazás létrehozása az Azure App konfigurációjával](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Adatok újratöltése az alkalmazáskonfigurációból

1. Nyissa *meg Function1.cs.* A `static` tulajdonságon `Configuration`kívül adjon `static` hozzá `ConfigurationRefresher` egy új tulajdonságot, `IConfigurationRefresher` amely megtartja a konfigurációs frissítések jelzéséhez használt singleton példányt a Functions későbbi hívásai során.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Frissítse a konstruktot, és a `ConfigureRefresh` módszerrel adja meg az alkalmazáskonfigurációs tárolóból frissítendő beállítást. A rendszer `IConfigurationRefresher` metódussal `GetRefresher` olvassa be a rendszer egy példányát. Opcionálisan a konfigurációs gyorsítótár lejárati idejét is módosítjuk 1 percre az alapértelmezett 30 másodpercről.

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

3. Frissítse `Run` a metódust és a `Refresh` jelet a konfiguráció frissítéséhez a Függvényhívás elején lévő módszerrel. Ez nem lesz operatív, ha a gyorsítótár lejárati időablak nem éri el. Távolítsa `await` el az operátort, ha azt szeretné, hogy a konfiguráció torlaszoló dulakozó nélkül frissüljön.

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

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. Ha a Windows parancssorát használja, futtassa a következő parancsot, és indítsa újra a parancssort a módosítás érvénybe léptetéséhez:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha windows PowerShellt használ, futtassa a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    MacOS vagy Linux használata esetén futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az **Azure Functions Core (CLI)** eszközök letöltésére és telepítésére. Előfordulhat, hogy engedélyeznie kell egy tűzfalkivételt is, hogy az eszközök kezelni tudják a HTTP-kérelmeket.

3. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Gyorsindítási függvény hibakeresése a VS-ben](./media/quickstarts/function-visual-studio-debugging.png)

4. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az alábbi képen a böngészőben a függvény által visszaadott helyi GET-kérésre adott válasz látható.

    ![A Quickstart függvény helyi indítása](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza az **Összes erőforrás**lehetőséget, és válassza ki a rövid útmutatóban létrehozott Alkalmazáskonfigurációs tároló példányt.

6. Válassza a **Configuration Explorer**lehetőséget, és frissítse a következő kulcs értékeit:

    | Kulcs | Érték |
    |---|---|
    | TestApp:Beállítások:Üzenet | Az Azure App konfigurációjából származó adatok – frissítve |

7. Frissítse a böngészőt néhányszor. Ha a gyorsítótárazott beállítás egy perc múlva lejár, a lapon a Függvények hívásának válasza frissített értékkel jelenik meg.

    ![A gyorsindításfunkció helyi frissítése](./media/quickstarts/dotnet-core-function-refresh-local.png)

Az oktatóanyagban használt példakód letölthető [az alkalmazáskonfigurációs GitHub-tárból](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte az Azure Functions alkalmazás dinamikusan frissíti a konfigurációs beállításokat az alkalmazáskonfigurációból. Ha meg szeretné tudni, hogyan használhatja az Azure felügyelt identitást az alkalmazáskonfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitásintegráció](./howto-integrate-azure-managed-service-identity.md)

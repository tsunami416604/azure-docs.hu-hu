---
title: Útmutató a szolgáltatás-jelzők hozzáadásához a Azure Functionshoz | Microsoft Docs
description: Ebben a rövid útmutatóban a Azure Functions az Azure-alkalmazás konfigurációjában található szolgáltatás-jelzőket használja, és a függvényt helyileg tesztelheti.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: 5542dca6d9b1ab18cf1b9b93e20fa64da0eb1dd4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "91000965"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Gyors útmutató: szolgáltatás-jelzők hozzáadása Azure Functions-alkalmazáshoz

Ebben a rövid útmutatóban egy Azure Functions alkalmazásban hozza létre a szolgáltatások felügyeletének megvalósítását az Azure app Configuration használatával. Az alkalmazás konfigurációs szolgáltatásával központilag tárolhatja az összes funkció jelzőjét, és szabályozhatja az állapotukat. 

A .NET-szolgáltatások felügyeleti könyvtárai kiterjesztik a keretrendszert a Feature Flag támogatásával. Ezek a kódtárak a .NET konfigurációs rendszerére épülnek. Integrálva vannak az alkalmazások konfigurációjával a .NET-konfigurációs szolgáltatón keresztül.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) az **Azure-fejlesztési** számítási feladattal.
- [Eszközök Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **funkció-kezelő**  >  **+ Hozzáadás** elemet a szolgáltatáshoz tartozó jelző hozzáadásához `Beta` .

    > [!div class="mx-imgBorder"]
    > ![A szolgáltatás jelölő engedélyezése Beta néven](media/add-beta-feature-flag.png)

    `label`Most hagyja és `Description` meghatározatlan maradjon.

7. Válassza az **alkalmaz** lehetőséget az új funkció jelző mentéséhez.

## <a name="create-a-functions-app"></a>Functions-alkalmazás létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**lehetőséget. A **Tallózás** lapon keresse meg és adja hozzá a következő NuGet-csomagokat a projekthez. Ellenőrizze, `Microsoft.Extensions.DependencyInjection` hogy a legutóbbi stabil builden van-e. 

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration
    Microsoft.FeatureManagement
    ```


1. Nyissa meg a *Function1.cs*, és adja hozzá a csomagok névtereit.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.DependencyInjection;
    ```

1. Adja hozzá az `Function1` alábbi statikus konstruktort az Azure-alkalmazás konfigurációs szolgáltatójának rendszerindításához. Ezután vegyen fel két `static` tagot, egy nevű mezőt, amely létrehoz egy egyedi `ServiceProvider` példányt `ServiceProvider` , valamint egy nevű tulajdonságot, amely `Function1` `FeatureManager` létrehoz egy különálló példányt `IFeatureManager` . Ezután kapcsolódjon az alkalmazás konfigurálásához a `Function1` hívásával `AddAzureAppConfiguration()` . Ez a folyamat betölti a konfigurációt az alkalmazás indításakor. Ugyanezt a konfigurációs példányt fogjuk használni az összes functions híváshoz később. 

    ```csharp
        // Implements IDisposable, cached for life time of function
        private static ServiceProvider ServiceProvider; 

        static Function1()
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            var services = new ServiceCollection();                                                                             
            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            ServiceProvider = services.BuildServiceProvider(); 
        }

        private static IFeatureManager FeatureManager => ServiceProvider.GetRequiredService<IFeatureManager>();
    ```

1. Frissítse a `Run` metódust a megjelenített üzenet értékének módosításához a szolgáltatás jelzője állapotától függően.

    ```csharp
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
                [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
                ILogger log)
            {
                string message = await FeatureManager.IsEnabledAsync("Beta")
                     ? "The Feature Flag 'Beta' is turned ON"
                     : "The Feature Flag 'Beta' is turned OFF";
                
                return (ActionResult)new OkObjectResult(message); 
            }
    ```

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

1. Állítson be egy **ConnectionString**nevű környezeti változót, ahol az érték az alkalmazás konfigurációs tárolójában korábban lekért elérési kulcs a **hozzáférési kulcsok**területen. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

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

1. Nyomja le az F5 billentyűt a függvény teszteléséhez. Ha a rendszer kéri, fogadja el a Visual Studiótól érkező kérést **Azure functions Core (CLI)** eszközök letöltéséhez és telepítéséhez. Előfordulhat, hogy egy tűzfal-kivételt is engedélyeznie kell, hogy az eszközök kezelni tudják a HTTP-kérelmeket.

1. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Gyors üzembe helyezési funkció hibakeresése a VS-ben](./media/quickstarts/function-visual-studio-debugging.png)

1. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az alábbi képen látható, hogy a funkció jelzője `Beta` le van tiltva. 

    ![A gyors üzembe helyezés funkció jelzője letiltva](./media/quickstarts/functions-launch-ff-disabled.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás**lehetőséget, majd válassza ki a létrehozott app Configuration Store-példányt.

1. Válassza a **szolgáltatásvezérlő**lehetőséget, majd módosítsa a **bétaverzió** állapotát **a be**értékre.

1. Térjen vissza a parancssorba, és szakítsa meg a futó folyamatot a következő lenyomásával: `Ctrl-C` .  Indítsa újra az alkalmazást az F5 billentyű lenyomásával. 

1. Másolja a függvény URL-címét a Azure Functions Runtime kimenetéről ugyanazzal a folyamattal, mint a 3. lépéssel. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. A böngésző válaszának úgy kell megjelennie, hogy a funkció jelzője `Beta` be legyen kapcsolva, ahogy az alábbi képen is látható.
 
    ![A rövid útmutató funkció funkciójának jelzője engedélyezve](./media/quickstarts/functions-launch-ff-enabled.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a szolgáltatás jelölőjét, és azt egy Azure Functions alkalmazással használta az [alkalmazás-konfigurációs szolgáltatón](https://go.microsoft.com/fwlink/?linkid=2074664)keresztül.

- További információ a [szolgáltatások kezeléséről](./concept-feature-management.md).
- [Szolgáltatás-jelzők kezelése](./manage-feature-flags.md).
- [Dinamikus konfiguráció használata egy Azure Functions alkalmazásban](./enable-dynamic-configuration-azure-functions-csharp.md)

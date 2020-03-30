---
title: Rövid útmutató a szolgáltatásjelzők .NET Framework alkalmazásokhoz való hozzáadásához | Microsoft Dokumentumok | Microsoft dokumentumok
description: Rövid útmutató a funkciójelzők .NET Framework-alkalmazásokhoz való hozzáadásához és kezeléséhez az Azure App konfigurációjában
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 5ea9749c07aadc7037e753160e9b053992bebae2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77619321"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Rövid útmutató: Szolgáltatásjelzők hozzáadása .

Ebben a rövid útmutatóban az Azure App Configuration alkalmazást beépítheti egy .NET Framework alkalmazásba a szolgáltatáskezelés teljes körű megvalósításának létrehozásához. Az Alkalmazáskonfigurációs szolgáltatás segítségével központilag tárolhatja az összes funkciójelzőt, és szabályozhatja azok állapotát. 

A . Ezek a könyvtárak a .NET konfigurációs rendszerre épülnek. Zökkenőmentesen integrálhatók az alkalmazáskonfigurációval a .NET konfigurációszolgáltatón keresztül.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET keretrendszer 4.8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Alkalmazáskonfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. A **Szolgáltatáskezelő** > **+Hozzáadás** lehetőséget `Beta`választva hozzáadhat egy szolgáltatásjelzőt.

    > [!div class="mx-imgBorder"]
    > ![Béta nevű szolgáltatásjelző engedélyezése](media/add-beta-feature-flag.png)

    Egyelőre hagyja `label` meg határozatlanul.

## <a name="create-a-net-console-app"></a>.NET konzolalkalmazás létrehozása

1. Indítsa el a Visual Studio alkalmazást, és válassza az**Új** > **projekt** **fájlja** > lehetőséget.

1. Az **Új projekt létrehozása**csoportban szűrje a **Konzolprojekt** típusát, és kattintson a **Console App (.NET Framework)** elemre. Kattintson a **Tovább** gombra.

1. Az **Új projekt konfigurálása**mezőbe írja be a projekt nevét. A **Keretrendszer**csoportban válassza a **.NET Framework 4.8** vagy újabb lehetőséget. Kattintson **a Létrehozás gombra.**

## <a name="connect-to-an-app-configuration-store"></a>Csatlakozás alkalmazáskonfigurációs tárolóhoz

1. Kattintson a jobb gombbal a projektre, és válassza **a NuGet csomagok kezelése parancsot.** A **Tallózás** lapon keresse meg és adja hozzá a következő NuGet csomagokat a projekthez. Ha nem találja őket, jelölje be az **Előzetes kiadás belefoglalása** jelölőnégyzetet.

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Nyissa *meg Program.cs,* és adja hozzá a következő állításokat:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    ```

1. Frissítse `Main` a metódust az alkalmazáskonfigurációhoz `UseFeatureFlags` való csatlakozáshoz, és adja meg a lehetőséget, hogy a szolgáltatásjelzők beolvasása megtörténhessen. Ezután jelenítsen `Beta` meg egy üzenetet, ha a szolgáltatásjelző engedélyezve van.

    ```csharp
        public static async Task Main(string[] args)
        {         
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            IServiceCollection services = new ServiceCollection();

            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            using (ServiceProvider serviceProvider = services.BuildServiceProvider())
            {
                IFeatureManager featureManager = serviceProvider.GetRequiredService<IFeatureManager>();

                if (await featureManager.IsEnabledAsync("Beta"))
                {
                    Console.WriteLine("Welcome to the beta!");
                }
            }

            Console.WriteLine("Hello World!");
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString** nevű környezeti változót az alkalmazáskonfigurációs tároló kapcsolati karakterláncára. Ha a Windows parancssorát használja, futtassa a következő parancsot:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha windows PowerShellt használ, futtassa a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. A Visual Studio újraindítása a módosítás érvénybe léptetéséhez. 

1. A konzolalkalmazás létrehozásához és futtatásához nyomja le a Ctrl+ F5 billentyűkombinációt.

    ![Alkalmazás funkciójelzővel](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy szolgáltatásjelzőt az Alkalmazás konfigurációjában, és használta azt egy . Ha meg szeretné tudni, hogyan frissítheti dinamikusan a szolgáltatásjelzőket és más konfigurációs értékeket az alkalmazás újraindítása nélkül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-dotnet.md)

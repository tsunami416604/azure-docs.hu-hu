---
title: Az Azure App konfigurációjának rövid útmutatója a . Microsoft dokumentumok
description: Rövid útmutató az Azure App Configuration és a .NET Framework alkalmazások használatához
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 1c56088a2c51c50c7f9cf1ff1e790d580fdb08d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245395"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Rövid útmutató: .NET Framework alkalmazás létrehozása az Azure App konfigurációjával

Ebben a rövid útmutatóban az Azure App Configuration alkalmazást egy .

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET keretrendszer 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Alkalmazáskonfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Configuration Explorer** > **Kulcsérték** **létrehozása** > lehetőséget a következő kulcsérték-párok hozzáadásához:

    | Kulcs | Érték |
    |---|---|
    | TestApp:Beállítások:Üzenet | Az Azure App konfigurációjából származó adatok |

    Egyelőre hagyja üresen a **Címke** és **a Tartalomtípus** mezőt.

7. Kattintson az **Alkalmaz** gombra.

## <a name="create-a-net-console-app"></a>.NET konzolalkalmazás létrehozása

1. Indítsa el a Visual Studio alkalmazást, és válassza az**Új** > **projekt** **fájlja** > lehetőséget.

1. Az **Új projekt létrehozása**csoportban szűrje a **Konzolprojekt** típusát, és kattintson a **Console App (.NET Framework)** elemre. Válassza a **Tovább lehetőséget.**

1. Az **Új projekt konfigurálása**mezőbe írja be a projekt nevét. A **Keretrendszer**csoportban válassza a **.NET Framework 4.7.1** vagy újabb lehetőséget. Kattintson a **Létrehozás** gombra.

## <a name="connect-to-an-app-configuration-store"></a>Csatlakozás alkalmazáskonfigurációs tárolóhoz

1. Kattintson a jobb gombbal a projektre, és válassza **a NuGet csomagok kezelése parancsot.** A **Tallózás** lapon keresse meg és adja hozzá a következő NuGet csomagokat a projekthez. Ha nem találja őket, jelölje be az **Előzetes kiadás belefoglalása** jelölőnégyzetet.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Frissítse a projekt *App.config* fájlját az alábbiak szerint:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   Az Alkalmazáskonfigurációs tároló kapcsolati karakterláncát `ConnectionString`a rendszer a környezeti változóból olvassa be. Adja `Environment` hozzá a `MyConfigStore` konfigurációszerkesztőt `configBuilders` a `appSettings` szakasz tulajdonsága előtt.

1. Nyissa *meg a Program.cs,* és frissítse a metódust az `Main` Alkalmazáskonfiguráció használatához a hívással. `ConfigurationManager`

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString** nevű környezeti változót az alkalmazáskonfigurációs tároló kapcsolati karakterláncára. Ha a Windows parancssorát használja, futtassa a következő parancsot:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Ha windows PowerShellt használ, futtassa a következő parancsot:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. A Visual Studio újraindítása a módosítás érvénybe léptetéséhez. A konzolalkalmazás létrehozásához és futtatásához nyomja le a Ctrl+ F5 billentyűkombinációt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új App Configuration Store-t, és használta azt egy .NET Framework konzolalkalmazással. Az `AppSettings` alkalmazás `ConfigurationManager` indítása után nem változik az értéke. Az Alkalmazáskonfiguráció .NET Standard konfigurációs szolgáltató könyvtára azonban használható a .NET Framework alkalmazásban is. Ha meg szeretné tudni, hogyan engedélyezheti a .NET Framework alkalmazás dinamikus annektását a konfigurációs beállítások dinamikus frissítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-dotnet.md)

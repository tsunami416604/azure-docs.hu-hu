---
title: Gyors útmutató az Azure-alkalmazások konfigurálásához a .NET Core használatával | Microsoft Docs
description: Gyors útmutató az Azure-alkalmazások konfigurációjának .NET Core-alkalmazásokkal való használatához
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: f27ad43fabbba92f97a4035b00f72a8a4af4cc5c
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500217"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Gyors útmutató: .NET Core-alkalmazás létrehozása az alkalmazás konfigurációjával

Ebben a rövid útmutatóban az Azure-alkalmazások konfigurációját egy .NET Core Console-alkalmazásba helyezi el, hogy központilag központosítsa az alkalmazások beállításait a kódból elkülönítve.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [.Net Core SDK](https://dotnet.microsoft.com/download) – a [Azure Cloud Shell](https://shell.azure.com)is elérhető.

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Configuration Explorer** > **Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

    | Paraméter | Érték |
    |---|---|
    | TestApp:Settings:Message | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

## <a name="create-a-net-core-console-app"></a>.NET Core Console-alkalmazás létrehozása

A [.net Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) használatával hozzon létre egy új .net Core Console alkalmazás-projektet. A a .NET Core parancssori felülete a Visual Studióban való használatának előnye, hogy a Windows, macOS és Linux platformokon is elérhető.  Másik lehetőségként használhatja a [Azure Cloud Shell](https://shell.azure.com)elérhető előtelepített eszközöket.

1. Hozzon létre egy új mappát a projekthez.

2. Az új mappában a következő parancs futtatásával hozzon létre egy új ASP.NET Core Console alkalmazás-projektet:

    ```CLI
        dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Adja hozzá a `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet-csomagra mutató hivatkozást a következő parancs futtatásával:

    ```CLI
        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Futtassa a következő parancsot a projekt csomagjainak visszaállításához:

    ```CLI
        dotnet restore
    ```

3. Nyissa meg a *program.cs*, és adjon hozzá egy hivatkozást a .net Core app Configuration Provider szolgáltatáshoz.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. A `builder.AddAzureAppConfiguration()` metódus meghívásával frissítse a `Main` metódust az alkalmazás konfigurációjának használatához.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. A parancssorban futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

    ```CLI
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Futtassa a következő parancsot a konzol alkalmazás létrehozásához:

    ```CLI
        dotnet build
    ```

3. A létrehozás sikeres befejezése után futtassa a következő parancsot az alkalmazás helyi futtatásához:

    ```CLI
        dotnet run
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és azt egy .NET Core Console-alkalmazással használta az [alkalmazás-konfigurációs szolgáltatón](https://go.microsoft.com/fwlink/?linkid=2074664)keresztül. Ha szeretné megtudni, hogyan konfigurálhatja a .NET Core-alkalmazást a konfigurációs beállítások dinamikus frissítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-dotnet-core.md)

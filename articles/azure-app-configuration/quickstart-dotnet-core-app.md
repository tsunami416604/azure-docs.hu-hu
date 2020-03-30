---
title: Az Azure App konfigurációjának rövid útmutatója a .NET Core használatával | Microsoft dokumentumok
description: Rövid útmutató az Azure App Configuration és a .NET Core alkalmazások használatához
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 420d9b48013f5f6debe588667fe1cc0390517e66
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245378"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Rövid útmutató: .NET Core alkalmazás létrehozása alkalmazáskonfigurációval

Ebben a rövid útmutatóban az Azure App Configuration alkalmazást egy .NET Core konzolalkalmazásba építi be, hogy központosítsa az alkalmazásbeállítások tárolását és felügyeletét a kódtól elkülönítve.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download) – az [Azure Cloud Shellben](https://shell.azure.com)is elérhető.

## <a name="create-an-app-configuration-store"></a>Alkalmazáskonfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Configuration Explorer** > **Kulcsérték** **létrehozása** > lehetőséget a következő kulcsérték-párok hozzáadásához:

    | Kulcs | Érték |
    |---|---|
    | TestApp:Beállítások:Üzenet | Az Azure App konfigurációjából származó adatok |

    Egyelőre hagyja üresen a **Címke** és **a Tartalomtípus** mezőt.

7. Kattintson az **Alkalmaz** gombra.

## <a name="create-a-net-core-console-app"></a>.NET Core konzolalkalmazás létrehozása

A [.NET Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) segítségével új .NET Core konzolalkalmazás-projektet hozhat létre. A .NET Core CLI használatának előnye a Visual Studio-val szemben, hogy windowsos, macOS és Linux platformokon is elérhető.  Másik lehetőségként használja az Azure [Cloud Shellben](https://shell.azure.com)elérhető előtelepített eszközöket.

1. Hozzon létre egy új mappát a projekthez.

2. Az új mappában futtassa a következő parancsot egy új ASP.NET Core konzolalkalmazás-projekt létrehozásához:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Csatlakozás alkalmazáskonfigurációs tárolóhoz

1. Adjon hozzá hivatkozást a `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet csomaghoz a következő parancs futtatásával:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. A következő parancs futtatásával állítsa vissza a projekt csomagjait:

    ```dotnetcli
    dotnet restore
    ```

3. Nyissa *meg a Program.cs,* és adjon hozzá hivatkozást a .NET Core alkalmazáskonfigurációs szolgáltatóhoz.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Frissítse `Main` a metódust az alkalmazáskonfiguráció használatához a `builder.AddAzureAppConfiguration()` metódus hívásával.

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

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazáskonfigurációs tároló hozzáférési kulcsára. A parancssorból futtassa a következő parancsot:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Ha windows PowerShellt használ, futtassa a következő parancsot:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    MacOS vagy Linux használata esetén futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

    A módosítás érvénybe lépésének engedélyezéséhez indítsa újra a parancssort. Nyomtassa ki a környezeti változó értékét, hogy ellenőrizze, hogy megfelelően van-e beállítva.

2. A konzolalkalmazás létrehozásához futtassa a következő parancsot:

    ```dotnetcli
    dotnet build
    ```

3. A sikeres build befejezése után futtassa a következő parancsot az alkalmazás helyi futtatásához:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új App Configuration Store-t, és használta azt egy .NET Core konzolalkalmazással az [Alkalmazáskonfiguráció szolgáltatón](https://go.microsoft.com/fwlink/?linkid=2074664)keresztül. Ha meg szeretné tudni, hogyan konfigurálhatja a .NET Core alkalmazást a konfigurációs beállítások dinamikus frissítésére, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-dotnet-core.md)

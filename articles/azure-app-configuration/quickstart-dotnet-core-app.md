---
title: Gyors útmutató az Azure-alkalmazások konfigurálásához a .NET Core használatával | Microsoft Docs
description: Gyors útmutató az Azure-alkalmazások konfigurációjának .NET Core-alkalmazásokkal való használatához
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 99a42102b30c91b7f1bba06cfaae7662b408b784
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326569"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Gyors útmutató: .NET Core-alkalmazás létrehozása az alkalmazás konfigurációjával

Ebben a rövid útmutatóban az Azure-alkalmazások konfigurációját egy .NET Core Console-alkalmazásba helyezi el, hogy központilag központosítsa az alkalmazások beállításait a kódból elkülönítve.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Configuration Explorer** >  **+ Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

    | Kulcs | Value |
    |---|---|
    | TestApp:Settings:Message | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

## <a name="create-a-net-core-console-app"></a>.NET Core Console-alkalmazás létrehozása

A [.net Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) használatával hozzon létre egy új .net Core Console alkalmazás-projektet. A a .NET Core parancssori felülete a Visual Studióban való használatának előnye, hogy a Windows, macOS és Linux platformokon is elérhető.

1. Hozzon létre egy új mappát a projekthez.

2. Az új mappában a következő parancs futtatásával hozzon létre egy új ASP.NET Core Console alkalmazás-projektet:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Adja hozzá a `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet-csomagra mutató hivatkozást a következő parancs futtatásával:

        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009200001-7

2. Futtassa a következő parancsot a projekt csomagjainak visszaállításához:

        dotnet restore

3. Nyissa meg a *program.cs*, és adjon hozzá egy hivatkozást a .net Core app Configuration Provider szolgáltatáshoz.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Frissítse a `Main` metódust az alkalmazás konfigurációjának használatára a `builder.AddAzureAppConfiguration()` metódus meghívásával.

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

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Futtassa a következő parancsot a konzol alkalmazás létrehozásához:

        dotnet build

3. A létrehozás sikeres befejezése után futtassa a következő parancsot az alkalmazás helyi futtatásához:

        dotnet run

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és azt egy .NET Core Console-alkalmazással használta az [alkalmazás-konfigurációs szolgáltatón](https://go.microsoft.com/fwlink/?linkid=2074664)keresztül. Ha többet szeretne megtudni az alkalmazások konfigurációjának használatáról, folytassa a következő oktatóanyaggal, amely bemutatja a hitelesítést.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)

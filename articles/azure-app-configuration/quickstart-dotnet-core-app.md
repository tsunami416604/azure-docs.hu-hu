---
title: 'Gyors útmutató: .NET Core használatával Azure-alkalmazás konfigurációja |} A Microsoft Docs'
description: Az Azure-alkalmazások konfigurálása használata a .NET Core-alkalmazások gyors üzembe helyezés
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
ms.openlocfilehash: 5501e92b9a9d977f74bf4ed028b3cd3de4e56133
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225382"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Gyors útmutató: Hozzon létre egy .NET Core-alkalmazást az alkalmazások konfigurálása

Az Azure App konfigurálása felügyelt konfigurációs szolgáltatása az Azure-ban. Könnyedén tárolhatja, és a egy helyet, amely választja el az alkalmazás beállításait kezelheti a kódból használhatja azt. Ez a rövid útmutató bemutatja, hogyan belefoglalhatja a szolgáltatást egy .NET Core-konzolalkalmazást az.

Bármely Kódszerkesztő segítségével ebben a rövid útmutatóban található lépések elvégzése. [A Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, macOS és Linux platformokon az érhető el.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató elvégzéséhez telepítse a [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Hozzon létre egy alkalmazást a konfigurációs adattároló

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-core-console-app"></a>Hozzon létre egy .NET Core-Konzolalkalmazás

Használja a [.NET Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) , hozzon létre egy új .NET Core console app projektet. A .NET Core parancssori felület a Visual Studio-val előnye, hogy legyen elérhető a Windows, macOS és Linux rendszerek között.

1. Hozzon létre egy új mappát a projekthez.

2. Az új mappára futtassa a következő parancsot egy új ASP.NET Core MVC webalkalmazás-projektet létrehozása:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Egy alkalmazás a konfigurációs adattároló csatlakozni

1. Vegyen fel egy hivatkozást a `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet-csomagot a következő parancs futtatásával:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Futtassa a következő parancsot a projekt-csomagok visszaállítására szolgáló:

        dotnet restore

3. Nyissa meg *Program.cs*, és frissítse a `Main` metódus meghívásával használhatja az Alkalmazáskonfigurációt az `builder.AddAzureAppConfiguration()` metódust.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Hozhat létre, és az alkalmazás helyileg történő futtatása

1. Nevű környezeti változó értéke **ConnectionString**, és állítsa be az alkalmazás a konfigurációs adattárolónál a hozzáférési kulcsot. Ha használja a Windows-parancssort, futtassa a következő parancsot, és a parancssort, a módosítás érvénybe léptetéséhez indítsa újra:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell használatakor a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Ha macOS vagy Linux használ, futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Futtassa a konzolalkalmazást hozhat létre a következő parancsot:

        dotnet build

3. A build sikeres befejezése után futtassa a következő parancsot az alkalmazás helyi futtatásához:

        dotnet run

    ![A rövid útmutató: alkalmazás futtatása](./media/quickstarts/dotnet-core-app-run.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs adattároló és a egy .NET Core-konzolalkalmazást az használta. Ismerje meg, hogyan használhatja az Alkalmazáskonfigurációt, folytassa a következő oktatóanyag azt mutatja be a hitelesítést.

> [!div class="nextstepaction"]
> [Az Azure-erőforrások integráció felügyelt identitásokból](./integrate-azure-managed-service-identity.md)

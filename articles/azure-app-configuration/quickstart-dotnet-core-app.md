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
ms.openlocfilehash: cd4115aaeec15d14d48dcb71cbdc75212c6dc2db
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960672"
---
# <a name="quickstart-create-an-net-core-app-with-app-configuration"></a>Gyors útmutató: Hozzon létre egy .NET Core-alkalmazást az alkalmazások konfigurálása

Az Azure App konfigurálása felügyelt konfigurációs szolgáltatása az Azure-ban. Ez lehetővé teszi, hogy könnyedén tárolhatja és kezelheti a kódot egy helyet, amely választja el az alkalmazás beállításait. Ez a rövid útmutató bemutatja, hogyan belefoglalhatja a szolgáltatást egy .NET Core-konzolalkalmazást az.

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. A [Visual Studio Code](https://code.visualstudio.com/) például jó választás lehet, és Windows, macOS és Linux platformokon is használható.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítse a [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Hozzon létre egy alkalmazást a konfigurációs adattároló

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-core-console-app"></a>Hozzon létre egy .NET Core-Konzolalkalmazás

Használhatja a [.NET Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) hozhat létre egy új .NET Core Konzolalkalmazás-projektet. A .NET Core CLI előnye a Visual Studióval szemben az, hogy Windows, macOS és Linux platformokon is használható.

1. Hozzon létre egy új mappát a projekthez.

2. Az új mappában hozzon létre egy új ASP.NET Core MVC webalkalmazás-projektet a következő paranccsal:

        dotnet new console

## <a name="connect-to-app-configuration-store"></a>Csatlakozhat az alkalmazás a konfigurációs adattároló

1. Adjon hozzá egy, a `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet-csomagra mutató hivatkozást a következő parancs futtatásával:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. A projekt csomagjainak visszaállításához futtassa a következő parancsot.

        dotnet restore

3. Nyissa meg *Program.cs* és frissítheti a `Main` metódus meghívásával használhatja az Alkalmazáskonfigurációt az `builder.AddAzureAppConfiguration()` metódust.

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

1. Nevű környezeti változó értéke **ConnectionString** , és állítsa be az alkalmazás a konfigurációs adattárolónál a hozzáférési kulcsot. Ha Windows-parancssort használja, hajtsa végre a következő parancsot, és a parancssort, a módosítás érvénybe léptetéséhez indítsa újra:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha a Windows Powershellt használ, hajtsa végre a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Ha macOS vagy Linux használ, hajtsa végre a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Futtassa a konzolalkalmazást hozhat létre a következő parancsot:

        dotnet build

3. A build sikeres befejeződése után hajtsa végre a következő parancsot az alkalmazás helyi futtatásához:

        dotnet run

    ![A rövid útmutató: alkalmazás futtatása](./media/quickstarts/dotnet-core-app-run.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban egy új alkalmazás-konfigurációs adattároló létrehozta és egy .NET Core-konzolalkalmazást az használta azt. Alkalmazáskonfiguráció használatával kapcsolatos további információkért folytassa a következő oktatóanyag azt mutatja be a hitelesítést.

> [!div class="nextstepaction"]
> [Az Azure-erőforrások integráció felügyelt identitásokból](./integrate-azure-managed-service-identity.md)

---
title: 'Gyors útmutató: Azure-alkalmazás konfigurációja .NET-keretrendszerrel |} A Microsoft Docs'
description: Gyors üzembe helyezés az Azure-alkalmazások konfigurálása használata a .NET-keretrendszer-alkalmazásokkal
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 551b884f032eaba3f052fcb7571ba907038152ff
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226844"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Gyors útmutató: Hozzon létre egy .NET keretrendszerre épülő alkalmazás az Azure-alkalmazás konfigurációja

Az Azure App konfigurálása felügyelt konfigurációs szolgáltatása az Azure-ban. Könnyedén tárolhatja, és a egy helyet, amely választja el az alkalmazás beállításait kezelheti a kódból használhatja azt. Ez a rövid útmutató bemutatja, hogyan belefoglalhatja a szolgáltatást az asztali Windows .NET-keretrendszer-alapú konzolalkalmazást.

![A rövid útmutató teljes helyi](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató elvégzéséhez telepítse [Visual Studio 2017](https://visualstudio.microsoft.com/vs) és [.NET-keretrendszer 4.7.1](https://dotnet.microsoft.com/download) vagy újabb, ha még nem tette.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Hozzon létre egy alkalmazást a konfigurációs adattároló

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Hozzon létre egy .NET-Konzolalkalmazás

1. Indítsa el a Visual Studiót, és válassza ki **fájl** > **új** > **projekt**.

2. A **új projekt**válassza **telepített** > **Visual C#**   >  **Windows asztali**. Válassza ki **Console App (.NET Framework)**, és adja meg a projekt nevét. Válassza ki **.NET-keretrendszer 4.7.1** vagy felfelé, és válassza ki **OK**.

## <a name="connect-to-an-app-configuration-store"></a>Egy alkalmazás a konfigurációs adattároló csatlakozni

1. Kattintson jobb gombbal a projektre, és válassza ki **NuGet-csomagok kezelése**. Az a **Tallózás** lapon, keresése és a következő NuGet-csomagok hozzáadása a projekthez. Ha nem látja őket, válassza ki a **előzetes verzió** jelölőnégyzetet.
    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Frissítés a *App.config* fájlt a projekt az alábbiak szerint:

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

   Az alkalmazás konfigurációs tár a kapcsolati karakterláncot a környezeti változóból olvasható `ConnectionString`. Adja hozzá a `Environment` konfigurációs builder előtt a `MyConfigStore` a a `configBuilders` tulajdonságát a `appSettings` szakaszban.

3. Nyissa meg *Program.cs*, és frissítse a `Main` metódus meghívásával használhatja az Alkalmazáskonfigurációt `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Hozhat létre, és az alkalmazás helyileg történő futtatása

1. Nevű környezeti változó értéke **ConnectionString** az alkalmazás a konfigurációs adattárolónál a kapcsolati karakterláncot. Ha a Windows-parancssort használja, futtassa a következő parancsot:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell használatakor a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Indítsa újra a Visual Studióban, hogy a módosítás érvénybe léptetéséhez. Nyomja le a Ctrl + F5 billentyűkombinációt hozhat létre és futtassa a konzolalkalmazást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs adattároló, és a .NET-keretrendszer konzolalkalmazást használta. Ismerje meg, hogyan használhatja az Alkalmazáskonfigurációt, folytassa a következő oktatóanyag azt mutatja be a hitelesítést.

> [!div class="nextstepaction"]
> [Az Azure-erőforrások integráció felügyelt identitásokból](./integrate-azure-managed-service-identity.md)

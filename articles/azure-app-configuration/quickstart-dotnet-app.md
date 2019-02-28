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
ms.openlocfilehash: b5e41b1f9ee982b8ff8c86232f715d5dab705cd6
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962162"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Gyors útmutató: Hozzon létre egy .NET keretrendszerre épülő alkalmazás az Azure-alkalmazás konfigurációja

Az Azure App konfigurálása felügyelt konfigurációs szolgáltatása az Azure-ban. Ez lehetővé teszi, hogy könnyedén tárolhatja és kezelheti a kódot egy helyet, amely választja el az alkalmazás beállításait. Ez a rövid útmutató bemutatja, hogyan belefoglalhatja a szolgáltatást az asztali Windows .NET-keretrendszer-alapú konzolalkalmazást.

![Rövid útmutató – teljes helyi](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítse [Visual Studio 2017](https://visualstudio.microsoft.com/vs) és [.NET-keretrendszer 4.7.1](https://dotnet.microsoft.com/download) vagy újabb, ha még nem tette.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Hozzon létre egy alkalmazást a konfigurációs adattároló

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Hozzon létre egy .NET-Konzolalkalmazás

1. Indítsa el a Visual Studiót, és válassza ki **fájl** > **új** > **projekt**.

2. Az a **új projekt** párbeszédablakban válassza **telepített**, bontsa ki a **Visual C#**   >  **Windows asztali**, kiválasztása **Console App (.NET Framework)**, adjon meg egy **neve** válassza ki a projekthez, **.NET-keretrendszer 4.7.1** vagy beállítása, és kattintson a **OK**.

## <a name="connect-to-app-configuration-store"></a>Csatlakozhat az alkalmazás a konfigurációs adattároló

1. Kattintson jobb gombbal a projektre, és válassza ki **NuGet-csomagok kezelése...** . Az a **Tallózás** lapon, keresése és a projekthez adja hozzá az alábbi NuGet-csomagok (Ellenőrizze a **előzetes verzió** jelölőnégyzetet, ha nem látja őket).
    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Frissítés a *App.config* fájlt a projekt a következőképpen:

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

   Adja vegye figyelembe, hogy fog kell olvasásakor, az alkalmazás a konfigurációs adattárolónál a kapcsolati karakterláncot a környezeti változó, `ConnectionString`, fontos, hogy adja hozzá a `Environment` konfigurációs builder előtt a `MyConfigStore` a a `configBuilders` tulajdonságát a `appSettings` szakaszban.

3. Nyissa meg *Program.cs* és frissítheti a `Main` metódus meghívásával használhatja az Alkalmazáskonfigurációt `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Hozhat létre, és az alkalmazás helyileg történő futtatása

1. Nevű környezeti változó értéke **ConnectionString** az alkalmazás a konfigurációs adattárolónál a kapcsolati karakterláncot. Ha Windows-parancssort használja, hajtsa végre a következő parancsot:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha a Windows Powershellt használ, hajtsa végre a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Indítsa újra a módosítás érvénybe lépéséhez, majd nyomja le a Visual Studio **Ctrl + F5** a billentyűzeten hozhat létre és futtassa a konzolalkalmazást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy új alkalmazás-konfigurációs adattároló létrehozta és a .NET-keretrendszer konzolalkalmazást használta azt. Alkalmazáskonfiguráció használatával kapcsolatos további információkért folytassa a következő oktatóanyag azt mutatja be a hitelesítést.

> [!div class="nextstepaction"]
> [Az Azure-erőforrások integráció felügyelt identitásokból](./integrate-azure-managed-service-identity.md)

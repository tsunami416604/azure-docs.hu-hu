---
title: Gyors útmutató az Azure-alkalmazások konfigurálásához a .NET-keretrendszerrel | Microsoft Docs
description: Gyors útmutató az Azure-alkalmazások konfigurációjának a .NET-keretrendszer alkalmazásaival való használatához
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
ms.date: 10/09/2019
ms.author: lcozzens
ms.openlocfilehash: f1fd1c21789c272bf8deae72ef64f2bbb1c6b3cb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707438"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Gyors útmutató: .NET-keretrendszerbeli alkalmazás létrehozása az Azure app Configuration használatával

Ebben a rövid útmutatóban az Azure-alkalmazások konfigurációját egy .NET-keretrendszer alapú konzolos alkalmazásba helyezi el, amely központosítja az alkalmazás-beállítások a kódból való elkülönítését és kezelését.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET-keretrendszer 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Configuration Explorer** >  **+ Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

    | Jelmagyarázat | Value (Díj) |
    |---|---|
    | TestApp: beállítások: üzenet | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

## <a name="create-a-net-console-app"></a>.NET-konzol alkalmazás létrehozása

1. Indítsa el a Visual studiót, és válassza a **fájl** > **új** > **projekt**lehetőséget.

1. A **create a New Project (új projekt létrehozása**) területen szűrje a **konzol** projekt típusát, és kattintson a **Console app (.NET-keretrendszer)** elemre. Kattintson a **Tovább** gombra.

1. Az **új projekt konfigurálása**területen adja meg a projekt nevét. A **keretrendszer**területen válassza a **.NET-keretrendszer 4.7.1** vagy újabb lehetőséget. Kattintson a  **Create** (Létrehozás) gombra.

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**lehetőséget. A **Tallózás** lapon keresse meg és adja hozzá a következő NuGet-csomagokat a projekthez. Ha nem találja őket, jelölje be az **előzetes verzió belefoglalása** jelölőnégyzetet.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Frissítse a projekt *app. config* fájlját a következőképpen:

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

   Az alkalmazás konfigurációs tárolójának a `ConnectionString`környezeti változóból való beolvasása. Adja hozzá a `Environment` Configuration Builder-t, mielőtt a `MyConfigStore` a `appSettings` szakasz `configBuilders` tulajdonságában.

1. Nyissa meg a *program.cs*, és frissítse a `Main` metódust az alkalmazás konfigurációjának használatára a `ConfigurationManager`meghívásával.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString** nevű környezeti változót az alkalmazás konfigurációs tárolójának kapcsolati karakterláncára. Ha Windows-parancssort használ, futtassa a következő parancsot:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Indítsa újra a Visual studiót, hogy a módosítás érvénybe lépjen. A konzol alkalmazás létrehozásához és futtatásához nyomja le a CTRL + F5 billentyűkombinációt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és felhasználta azt egy .NET Framework Console alkalmazással. A `ConfigurationManager` `AppSettings` értéke nem változik az alkalmazás elindítása után. Az App Configuration .NET Standard konfigurációs szolgáltatói könyvtára azonban .NET-keretrendszerbeli alkalmazásokban is használható. Ha szeretné megtudni, hogyan engedélyezheti a .NET-keretrendszer alkalmazását a konfigurációs beállítások dinamikus frissítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-dotnet.md)

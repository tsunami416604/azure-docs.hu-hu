---
title: Gyors útmutató az Azure-alkalmazások konfigurálásához a .NET-keretrendszerrel | Microsoft Docs
description: Gyors útmutató az Azure-alkalmazások konfigurációjának a .NET-keretrendszer alkalmazásaival való használatához
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
ms.openlocfilehash: 8aa8c8132220965d55097c4fed8ba1b2e9501301
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326525"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Gyors útmutató: .NET-keretrendszerbeli alkalmazás létrehozása az Azure app Configuration használatával

Ebben a rövid útmutatóban az Azure-alkalmazások konfigurációját egy .NET-keretrendszer alapú konzolos alkalmazásba helyezi el, amely központosítja az alkalmazás-beállítások a kódból való elkülönítését és kezelését.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET-keretrendszer 4.7.1](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Configuration Explorer** >  **+ Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

    | Kulcs | Value |
    |---|---|
    | TestApp:Settings:Message | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

## <a name="create-a-net-console-app"></a>.NET-konzol alkalmazás létrehozása

1. Indítsa el a Visual studiót, és válassza a **fájl** > **új** > **projekt**lehetőséget.

2. Az **új projekt**területen válassza a **telepített** > **C#Visual**   > Windows Desktop elemet. Válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget, és adja meg a projekt nevét. Válassza a **.NET-keretrendszer 4.7.1** vagy a fel lehetőséget, majd kattintson **az OK gombra**.

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**lehetőséget. A **Tallózás** lapon keresse meg és adja hozzá a következő NuGet-csomagokat a projekthez. Ha nem találja őket, jelölje be az **előzetes verzió** belefoglalása jelölőnégyzetet.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Frissítse a projekt *app. config* fájlját a következőképpen:

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

   Az alkalmazás konfigurációs tárolójának a környezeti változóból `ConnectionString`való beolvasása a kapcsolatok karakterlánca. Adja hozzá `Environment` a Configuration Builder `MyConfigStore` `configBuilders` -taszakasztulajdonságához.`appSettings`

3. Nyissa meg a *program.cs*, `Main` és frissítse a metódust az alkalmazás `ConfigurationManager`konfigurációjának meghívásával való használatára.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString** nevű környezeti változót az alkalmazás konfigurációs tárolójának kapcsolati karakterláncára. Ha Windows-parancssort használ, futtassa a következő parancsot:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Indítsa újra a Visual studiót, hogy a módosítás érvénybe lépjen. A konzol alkalmazás létrehozásához és futtatásához nyomja le a CTRL + F5 billentyűkombinációt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és felhasználta azt egy .NET Framework Console alkalmazással. Ha többet szeretne megtudni az alkalmazások konfigurációjának használatáról, folytassa a következő oktatóanyaggal, amely bemutatja a hitelesítést.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)

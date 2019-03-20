---
title: A folyamatos integrációt és teljesítést folyamat integrálása az Azure-alkalmazások konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy konfigurációs fájl használatával adatokat az Azure-alkalmazások konfigurálása során a folyamatos integrációt és teljesítést
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: cb9fe6dc234c317daa5eabec01812324e7c81663
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224328"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integráció CI-/CD-folyamattal

Javíthatja az alkalmazás nem tudja elérni az Azure-alkalmazások konfigurálása a távoli lehetőségét szembeni rugalmasságát. Ehhez a csomag aktuális konfigurációs adatok fájlba, hogy az alkalmazás és a betöltött helyben telepített az indítás során. Ez a megközelítés biztosítja, hogy az alkalmazás alapértelmezett beállítás értéke van legalább. Ezek az értékek felülírja, az alkalmazás a konfigurációs adattároló újabb módosításainak elérhetővé válik.

Használatával a [exportálása](./howto-import-export-data.md#export-data) függvényt az Azure-alkalmazások konfigurálása, automatizálhatja egyetlen fájlként aktuális konfigurációs adatok lekérését jelenti. Ezután egy build és a központi telepítési lépés a folyamatos integráció és folyamatos üzembe helyezés (CI/CD) folyamatokat ágyazhat be ezt a fájlt.

Az alábbi példa bemutatja, hogyan foglalhat bele az Alkalmazáskonfigurációt az adatok egy buildet. lépés: a webalkalmazás a rövid útmutatók rendszerben bevezetett. A folytatás előtt Befejezés [ASP.NET Core-alkalmazás létrehozása az alkalmazás konfigurációs](./quickstart-aspnet-core-app.md) első.

Bármely Kódszerkesztő segítségével ebben a rövid útmutatóban található lépések elvégzése. [A Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, macOS és Linux platformokon az érhető el.

## <a name="prerequisites"></a>Előfeltételek

Ha helyileg hoz létre, töltse le és telepítse a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Ha még nem tette.

Ehhez egy felhő-összeállítási, az Azure DevOps például ellenőrizze, hogy a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a buildelési rendszer van telepítve.

## <a name="export-an-app-configuration-store"></a>Egy alkalmazás a konfigurációs adattároló exportálása

1. Nyissa meg a *.csproj* fájlt, és adja hozzá a következő szkriptet:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Adja hozzá a *ConnectionString* társított konfigurációs alkalmazásáruházában környezeti változóban.

2. Nyissa meg a Program.cs, és frissítse a `CreateWebHostBuilder` metódus meghívásával az exportált JSON-fájl használata a `config.AddJsonFile()` metódust.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

## <a name="build-and-run-the-app-locally"></a>Hozhat létre, és az alkalmazás helyileg történő futtatása

1. Nevű környezeti változó értéke **ConnectionString**, és állítsa be az alkalmazás a konfigurációs adattárolónál a hozzáférési kulcsot. Ha használja a Windows-parancssort, futtassa a következő parancsot, és a parancssort, a módosítás érvénybe léptetéséhez indítsa újra:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell használatakor a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Ha macOS vagy Linux használ, futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Az alkalmazás létrehozása a .NET Core-CLI-vel, a következő parancsot az parancs-rendszerhéjba:

        dotnet build

3. A build sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

        dotnet run

4. Nyisson meg egy böngészőablakot, és nyissa meg `http://localhost:5000`, azaz a helyileg üzemeltetett webes alkalmazás alapértelmezett URL-CÍMÉT.

    ![A rövid útmutató alkalmazások indítása helyi](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>További lépések

* [Az Azure-erőforrások integráció felügyelt identitásokból](./integrate-azure-managed-service-identity.md)

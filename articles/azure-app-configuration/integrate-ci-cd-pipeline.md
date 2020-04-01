---
title: Az Azure App Configuration integrálása folyamatos integrációs és kézbesítési folyamat használatával
description: Ismerje meg a folyamatos integráció és kézbesítés megvalósítását az Azure App Konfigurációjával
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: lcozzens
ms.openlocfilehash: c744557471a9b37bd620bb9195bdb709c24649ab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77047289"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integráció CI-/CD-folyamattal

Ez a cikk bemutatja, hogyan használhatja az Azure App Configuration szolgáltatásból származó adatokat folyamatos integrációs és folyamatos üzembe helyezési rendszerben.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Alkalmazáskonfiguráció használata az Azure DevOps-folyamatban

Ha rendelkezik egy Azure DevOps-folyamattal, lehívhatja a kulcsértékeket az Alkalmazáskonfigurációból, és feladatváltozóként állíthatja be őket. Az [Azure App Konfigurációs DevOps-bővítmény](https://go.microsoft.com/fwlink/?linkid=2091063) egy kiegészítő modul, amely ezt a funkciót biztosítja. Kövesse az utasításokat, hogy a bővítményt egy build- vagy kiadási feladatütemezésben használja.

## <a name="deploy-app-configuration-data-with-your-application"></a>Alkalmazáskonfigurációs adatok központi telepítése az alkalmazással

Előfordulhat, hogy az alkalmazás nem fut, ha az Azure App konfigurációjától függ, és nem tudja elérni. Növelje az alkalmazás rugalmasságát azáltal, hogy a konfigurációs adatokat egy olyan fájlba csomagolja, amely az alkalmazással együtt van telepítve, és az alkalmazás indításakor helyileg van betöltve. Ez a megközelítés garantálja, hogy az alkalmazás alapértelmezett beállítási értékekkel rendelkezik indításkor. Ezeket az értékeket felülírja az alkalmazáskonfigurációs tárolóban végrehajtott újabb módosítások, ha az elérhetővé válik.

Az Azure App Configuration [exportálása](./howto-import-export-data.md#export-data) funkcióhasználatával automatizálhatja az aktuális konfigurációs adatok egyetlen fájlként történő beolvasásának folyamatát. Ezután beágyazhatja ezt a fájlt egy build vagy üzembe helyezési lépésbe a folyamatos integrációés a folyamatos üzembe helyezési (CI/CD) folyamatba.

A következő példa bemutatja, hogyan veheti fel az alkalmazáskonfigurációs adatokat a rövid útmutatókban bevezetett webalkalmazás buildelési lépéseként. Mielőtt folytatna, először fejezze be [a ASP.NET Core alkalmazás létrehozása az alkalmazás konfigurációjával.](./quickstart-aspnet-core-app.md)

Bármelyik kódszerkesztőt használhatja az oktatóanyag lépéseihez. [Visual Studio Code](https://code.visualstudio.com/) egy kiváló lehetőség elérhető a Windows, macOS, és Linux platformokon.

### <a name="prerequisites"></a>Előfeltételek

Ha helyileg hoz létre, töltse le és telepítse az [Azure CLI-t,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ha még nem tette meg.

Felhőalapú build elvégezéséhez például az Azure DevOps segítségével győződjön meg arról, hogy az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) telepítve van a buildrendszerben.

### <a name="export-an-app-configuration-store"></a>Alkalmazáskonfigurációs tároló exportálása

1. Nyissa meg a *.csproj* fájlt, és adja hozzá a következő parancsfájlt:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Nyissa *meg Program.cs,* és frissítse a `CreateWebHostBuilder` metódust az exportált JSON-fájl használatára a `config.AddJsonFile()` metódus hívásával.  Adja `System.Reflection` hozzá a névteret is.

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

### <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazáskonfigurációs tároló hozzáférési kulcsára. 
    Ha a Windows parancssorát használja, futtassa a következő parancsot, és indítsa újra a parancssort a módosítás érvénybe léptetéséhez:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha windows PowerShellt használ, futtassa a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    MacOS vagy Linux használata esetén futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Ha az alkalmazást a .NET Core CLI segítségével szeretné felépíteni, futtassa a következő parancsot a parancshéjban:

        dotnet build

3. A sikeres build befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

        dotnet run

4. Nyisson meg egy `http://localhost:5000`böngészőablakot, és lépjen a gombra, amely a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe.

    ![A gyorsindítási alkalmazás helyi indítása](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban exportálta az Azure App konfigurációs adatait egy központi telepítési folyamatban való használathoz. Ha többet szeretne megtudni az alkalmazáskonfiguráció használatáról, folytassa az Azure CLI-mintákkal.

> [!div class="nextstepaction"]
> [Felügyelt identitásintegráció](./howto-integrate-azure-managed-service-identity.md)

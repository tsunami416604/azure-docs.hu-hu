---
title: Az Azure-alkalmazások konfigurációjának integrálása folyamatos integráció és kézbesítési folyamat használatával
description: Ismerje meg, hogyan implementálhatja a folyamatos integrációt és teljesítést az Azure app Configuration használatával
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 01/30/2020
ms.author: lcozzens
ms.openlocfilehash: f0d1e57c7e212fefc6e17a8170e3b4537b190f60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88211638"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integráció CI-/CD-folyamattal

Ez a cikk azt ismerteti, hogyan használhatók az Azure-alkalmazások konfigurációjában tárolt adatok egy folyamatos integrációs és folyamatos üzembe helyezési rendszeren.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Az alkalmazások konfigurációjának használata az Azure DevOps-folyamatában

Ha rendelkezik Azure DevOps-folyamattal, beolvashatja a kulcs-értékeket az alkalmazás konfigurációjában, és beállíthatja feladatként változóként. Az [Azure app Configuration DevOps bővítmény](https://go.microsoft.com/fwlink/?linkid=2091063) egy kiegészítő modul, amely biztosítja ezt a funkciót. Kövesse az utasításait, hogy a bővítményt felépítési vagy kiadási Feladatsorban használja.

## <a name="deploy-app-configuration-data-with-your-application"></a>Alkalmazások konfigurációs adatai üzembe helyezése az alkalmazással

Előfordulhat, hogy az alkalmazás nem futtatható, ha az az Azure-alkalmazás konfigurációjától függ, és nem tudja elérni. Növelje az alkalmazás rugalmasságát úgy, hogy a konfigurációs adatait becsomagolja egy, az alkalmazással telepített fájlba, és az alkalmazás indításakor helyileg betölti. Ez a módszer garantálja, hogy az alkalmazás alapértékeit az indításkor állítja be. Ezeket az értékeket az alkalmazás konfigurációs tárolójának bármely újabb változása felülírja, ha az elérhető.

Az Azure-alkalmazások konfigurációjának [Exportálás](./howto-import-export-data.md#export-data) funkciója segítségével automatizálhatja az aktuális konfigurációs adatok egyetlen fájlként történő beolvasásának folyamatát. Ezt a fájlt a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD) folyamatában is beágyazhatja a Build vagy az üzembe helyezési lépésbe.

Az alábbi példa bemutatja, hogyan lehet az alkalmazás konfigurációs információit felépíteni a gyors útmutatókban bemutatott webalkalmazáshoz. A folytatás előtt fejezze be a [ASP.net Core alkalmazás létrehozása az alkalmazás-konfigurációval](./quickstart-aspnet-core-app.md) először.

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, MacOS és Linux platformokon.

### <a name="prerequisites"></a>Előfeltételek

Ha helyileg épít, töltse le és telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -t, ha még nem tette meg.

A felhőalapú buildek létrehozásához az Azure DevOps például ellenőrizze, hogy az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) telepítve van-e a Build rendszerbe.

### <a name="export-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló exportálása

1. Nyissa meg a *. csproj* fájlt, és adja hozzá a következő parancsfájlt:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Nyissa meg a *program.cs*, és frissítse a `CreateWebHostBuilder` metódust az exportált JSON-fájl használatára a metódus meghívásával `config.AddJsonFile()` .  Adja hozzá a `System.Reflection` névteret is.

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

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. 
    Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, futtassa a következő parancsot a parancs-rendszerhéjban:

    ```console
     dotnet build
    ```

3. A létrehozás sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```console
     dotnet run
    ```

4. Nyisson meg egy böngészőablakot, és nyissa meg a következőt `http://localhost:5000` :, amely a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe.

    ![Gyorsindítás alkalmazás elindítása helyi](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban exportálta az Azure-alkalmazás konfigurációs szolgáltatásait egy központi telepítési folyamat során. Ha többet szeretne megtudni az alkalmazások konfigurációjának használatáról, folytassa az Azure CLI-mintákkal.

> [!div class="nextstepaction"]
> [Azure CLI](https://docs.microsoft.com/cli/azure/appconfig?view=azure-cli-latest)

---
title: 'Oktatóanyag: integrálás folyamatos integrációs és szállítási folyamattal'
titleSuffix: Azure App Configuration
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhatja ki a konfigurációs fájlokat az Azure-alkalmazások konfigurációjában tárolt adathasználattal a folyamatos integráció és a kézbesítés során
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: cd40b52c20a3cafdbbeef093b574d44b9163c7b2
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899391"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integráció CI-/CD-folyamattal

Ez a cikk az Azure-alkalmazások konfigurálásának különböző módjait ismerteti folyamatos integrációs és folyamatos üzembe helyezési rendszer esetén.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Az alkalmazások konfigurációjának használata az Azure DevOps-folyamatában

Ha rendelkezik Azure DevOps-folyamattal, beolvashatja a kulcs-értékeket az alkalmazás konfigurációjában, és beállíthatja feladatként változóként. Az [Azure app Configuration DevOps bővítmény](https://go.microsoft.com/fwlink/?linkid=2091063) egy kiegészítő modul, amely biztosítja ezt a funkciót. Egyszerűen kövesse az utasításokat, hogy a bővítményt felépítési vagy kiadási Feladatsorban használja.

## <a name="deploy-app-configuration-data-with-your-application"></a>Alkalmazások konfigurációs adatai üzembe helyezése az alkalmazással

Előfordulhat, hogy az alkalmazás nem futtatható, ha az az Azure-alkalmazás konfigurációjától függ, és nem tudja elérni. Javíthatja az alkalmazás rugalmasságát, hogy az ilyen eseményekkel foglalkozzon, azonban valószínűtlen, hogy ez megtörténik. Ehhez csomagolja ki az aktuális konfigurációs adatfájlt egy olyan fájlba, amely az alkalmazással együtt lett telepítve, és helyileg töltődik be az indítás során. Ez a módszer garantálja, hogy az alkalmazásnak legalább az alapértelmezett értékeket kell beállítania. Ezeket az értékeket az alkalmazás konfigurációs tárolójának bármely újabb változása felülírja, ha az elérhető.

Az Azure-alkalmazások konfigurációjának [Exportálás](./howto-import-export-data.md#export-data) funkciója segítségével automatizálhatja az aktuális konfigurációs adatok egyetlen fájlként történő beolvasásának folyamatát. Ezt követően ágyazza be ezt a fájlt a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD) folyamatának összeállítási vagy telepítési lépéseként.

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

    Adja hozzá az alkalmazás konfigurációs tárolójához társított *ConnectionString* környezeti változóként.

2. Nyissa meg a *program.cs*, és frissítse a `CreateWebHostBuilder` metódust az exportált JSON-fájl használatára az `config.AddJsonFile()` metódus meghívásával.

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

1. Állítson be egy **ConnectionString**nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, futtassa a következő parancsot a parancs-rendszerhéjban:

        dotnet build

3. A létrehozás sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

        dotnet run

4. Nyisson meg egy böngészőablakot, és lépjen a `http://localhost:5000`elemre, amely a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe.

    ![Gyorsindítás alkalmazás elindítása helyi](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban exportálta az Azure-alkalmazás konfigurációs szolgáltatásait egy központi telepítési folyamat során. Ha többet szeretne megtudni az alkalmazások konfigurációjának használatáról, folytassa az Azure CLI-mintákkal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)

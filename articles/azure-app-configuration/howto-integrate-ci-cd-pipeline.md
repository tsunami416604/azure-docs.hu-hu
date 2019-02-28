---
title: Alkalmazások konfigurálása az Azure segítségével folyamatos integrációt és teljesítést folyamat integrálása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy konfigurációs fájl adatokat használó Azure-alkalmazások konfigurálása során a folyamatos integrációt és teljesítést
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
ms.openlocfilehash: 7b2e919bc46810e8478956675ffeb1cb0542da85
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957368"
---
# <a name="integrate-with-a-cicd-pipeline"></a>A CI/CD-folyamat integrálása

Az alkalmazás nem tudja elérni az Azure-alkalmazások konfigurálása a távoli lehetőségét szembeni rugalmasságát javítása érdekében egy fájlba, amelyet az alkalmazással együtt, és helyben betöltött az indítás során kell csomag aktuális konfigurációs adatok . Ez a megközelítés biztosítja, hogy az alkalmazás legalább lesz alapértelmezett beállítás értékeit. Ezek az értékek felülírja az alkalmazás a konfigurációs adattárolónál az újabb módosításokat elérhetővé válik.

Használatával a [ **exportálása** ](./howto-import-export-data.md#export-data) függvényt az Azure-alkalmazások konfigurálása, automatizálhatja egyetlen fájlként aktuális konfigurációs adatok lekérését jelenti. A build és a központi telepítési lépés a folyamatos integráció és folyamatos üzembe helyezés majd beágyazhatja ezt a fájlt.

Az alábbi példa bemutatja, hogyan foglalhat bele az Alkalmazáskonfigurációt az adatok egy buildet. lépés: a webalkalmazás a rövid útmutatók rendszerben bevezetett. Teljes [ASP.NET Core-alkalmazás létrehozása az alkalmazás konfigurációs](./quickstart-aspnet-core-app.md) első, a folytatás előtt.

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. A [Visual Studio Code](https://code.visualstudio.com/) például jó választás lehet, és Windows, macOS és Linux platformokon is használható.

## <a name="prerequisites"></a>Előfeltételek

Ha helyileg hoz létre, töltse le és telepítse [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Ha még nem tette.

Ha szeretne egy felhő-összeállítási, az Azure DevOps például ellenőrizze, hogy [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a buildelési rendszer van telepítve.

## <a name="export-app-configuration-store"></a>Alkalmazásáruház-konfiguráció exportálása

1. Nyissa meg a *.csproj* fájlt, és adja hozzá a következő:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    A *ConnectionString* kapcsolódó az alkalmazáskonfigurációt az üzlet szerint kell kiosztani, egy környezeti változó.

2. Nyissa meg *Program.cs* és frissítheti a `CreateWebHostBuilder` metódus meghívásával az exportált json-fájl használata a `config.AddJsonFile()` metódust.

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

1. Nevű környezeti változó értéke **ConnectionString** , és állítsa be az alkalmazás a konfigurációs adattárolónál a hozzáférési kulcsot. Ha Windows-parancssort használja, hajtsa végre a következő parancsot, és a parancssort, a módosítás érvénybe léptetéséhez indítsa újra:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Ha a Windows Powershellt használ, hajtsa végre a következő parancsot:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Ha macOS vagy Linux használ, hajtsa végre a következő parancsot:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Ha az alkalmazást a .NET Core CLI használatával szeretné létrehozni, futtassa a következő parancsot a parancsfelületen:

        dotnet build

3. Ha a létrehozás sikeresen befejeződött, hajtsa végre a következő parancsot a webalkalmazás helyben való futtatásához:

        dotnet run

4. Egy böngészőablakot, és navigáljon a `http://localhost:5000`, azaz a helyileg üzemeltetett webes alkalmazás alapértelmezett URL-CÍMÉT.

    ![A rövid útmutató alkalmazások indítása helyi](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>További lépések

* [Az Azure-erőforrások integráció felügyelt identitásokból](./integrate-azure-managed-service-identity.md)

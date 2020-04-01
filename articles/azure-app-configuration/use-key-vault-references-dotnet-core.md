---
title: Oktatóanyag az Azure App konfigurációs kulcstárolójának hivatkozásaihoz egy ASP.NET Core alkalmazásban | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure App Configuration Key Vault-referenciáit egy ASP.NET Core alkalmazásból
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: eceb4a9d4e0cc84166280f30b094b82088f53a4a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79475306"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Oktatóanyag: Key Vault-hivatkozások használata egy ASP.NET Core alkalmazásban

Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure App Konfigurációs szolgáltatás az Azure Key Vault használatával. Az alkalmazáskonfiguráció és a Key Vault a legtöbb alkalmazástelepítésben egymás mellett használt kiegészítő szolgáltatások.

Az Alkalmazáskonfiguráció segít a szolgáltatások együttes használatában a Key Vaultban tárolt értékeket tartalmazó kulcsok létrehozásával. Amikor az Alkalmazás konfigurációja ilyen kulcsokat hoz létre, a Key Vault-értékek URI-it tárolja, nem pedig magukat az értékeket.

Az alkalmazás az alkalmazáskonfigurációs ügyfélszolgáltató segítségével olvassa be a Key Vault-hivatkozásokat, ugyanúgy, mint az alkalmazáskonfigurációban tárolt többi kulcs esetében. Ebben az esetben az alkalmazáskonfigurációban tárolt értékek olyan URI-k, amelyek a Key Vault ban lévő értékekre hivatkoznak. Ezek nem key vault-értékek vagy hitelesítő adatok. Mivel az ügyfélszolgáltató felismeri a kulcsokat key vault-hivatkozásként, a Key Vault segítségével lekéri az értékaikat.

Az alkalmazás felelős az alkalmazáskonfiguráció és a Key Vault megfelelő hitelesítésért. A két szolgáltatás nem kommunikál közvetlenül.

Ez az oktatóanyag bemutatja, hogyan valósíthatja meg a Key Vault-hivatkozásokat a kódban. A rövid útmutatókban bevezetett webalkalmazásra épül. Mielőtt folytatna, először fejezze be [a ASP.NET Core alkalmazás létrehozása az alkalmazás konfigurációjával.](./quickstart-aspnet-core-app.md)

Bármelyik kódszerkesztőt használhatja az oktatóanyag lépéseihez. A [Visual Studio Code](https://code.visualstudio.com/) például egy platformfüggetlen kódszerkesztő, amely a Windows, a macOS és a Linux operációs rendszerekhez érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy alkalmazáskonfigurációs kulcsot, amely a Key Vaultban tárolt értékre hivatkozik.
> * A kulcs értékének elérése egy ASP.NET Core webalkalmazásból.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt telepítse a [.NET Core SDK -t.](https://dotnet.microsoft.com/download)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Tároló létrehozása

1. Válassza az **Erőforrás létrehozása** lehetőséget az Azure Portal bal felső sarkában:

    ![Kimenet a kulcstartó létrehozása után](./media/quickstarts/search-services.png)
1. A keresőmezőbe írja be a Key Vault ( Key Vault ) **mezőbe.**
1. Az eredmények listájában válassza a bal oldali **kulcstartók** lehetőséget.
1. A **kulcstartókban**válassza **a Hozzáadás**lehetőséget.
1. A key **vault létrehozása**jobb oldalán adja meg a következő információkat:
    - **Az előfizetés** kiválasztásához válassza az Előfizetés lehetőséget.
    - Az **Erőforráscsoport**csoportban válassza **az Új létrehozása** lehetőséget, és adjon meg egy erőforráscsoport nevét.
    - A **Key Vault neve,** egyedi nevet kell megadni. Ebben az oktatóanyagban adja meg **a Contoso-vault2**értéket.
    - A **Régió** legördülő listában válasszon egy helyet.
1. Hagyja a többi **Key Vault-beállítás létrehozása** az alapértelmezett értékekkel.
1. Kattintson a **Létrehozás** gombra.

Ezen a ponton az Azure-fiók az egyetlen, amely jogosult az új tároló eléréséhez.

![Kimenet a kulcstartó létrehozása után](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Ha titkos kulcsot szeretne hozzáadni a tárolóhoz, csak néhány további lépést kell tennie. Ebben az esetben adjon hozzá egy üzenetet, amely segítségével tesztelheti a Key Vault beolvasása. Az üzenet neve **Üzenet**, és a "Hello from Key Vault" értéket tárolja benne.

1. A Key Vault tulajdonságlapjain válassza a **Titkok**lehetőséget.
1. Válassza **a Létrehozás/importálás**lehetőséget.
1. A **Titkos kapcsolat ablaktáblában** adja meg a következő értékeket:
    - **Feltöltési beállítások**: Írja be **a kézi értéket**.
    - **Név**: Üzenet **beírása**.
    - **Érték**: Adja meg **a Hello a Key Vault**.
1. Hagyja a másik **hozzon létre egy titkos** tulajdonságokat az alapértelmezett értékekkel.
1. Kattintson a **Létrehozás** gombra.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Kulcstartóhivatkozás hozzáadása az alkalmazás konfigurációjához

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza az **Összes erőforrás**lehetőséget, majd válassza ki a rövid útmutatóban létrehozott Alkalmazáskonfigurációs tároló példányt.

1. Válassza a **Configuration Explorer**lehetőséget.

1. Válassza a + Key vault **hivatkozásának** > **Key vault reference**lehetőséget, majd adja meg a következő értékeket:
    - **Kulcs**: Válassza a **TestApp:Settings:KeyVaultMessage lehetőséget.**
    - **Címke**: Hagyja üresen ezt az értéket.
    - **Előfizetés**, **Erőforrás csoport**és **kulcstartó:** Adja meg az előző szakaszban létrehozott key vaultban megadott értékeknek megfelelő értékeket.
    - **Titkos:** Válassza ki az előző szakaszban létrehozott **Üzenet** titkos kulcsot.

## <a name="connect-to-key-vault"></a>Csatlakozás a Key Vaulthoz

1. Ebben az oktatóanyagban egy egyszerű szolgáltatása a Key Vault hitelesítéséhez. Az egyszerű szolgáltatás létrehozásához használja az Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Ez a művelet kulcs-/értékpárok sorozatát adja vissza:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Futtassa a következő parancsot, hogy az egyszerű szolgáltatás hozzáférjen a kulcstartóhoz:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Környezeti változók hozzáadása az *ügyfélazonosító*, *az ügyféltitkos*és *a tenantId*értékeinek tárolásához.

    #### <a name="windows-command-prompt"></a>[Windows parancssor](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Ezek a Key Vault hitelesítő adatok csak az alkalmazáson belül. Az alkalmazás közvetlenül a Key Vault ezekkel a hitelesítő adatokkal hitelesíti magát. Ezek soha nem kerülnek átadásra az App Configuration szolgáltatás.

1. Indítsa újra a terminált az új környezeti változók betöltéséhez.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>A kód frissítése key vault-hivatkozás használatához

1. Adjon hozzá hivatkozást a szükséges NuGet csomagokra a következő parancs futtatásával:

    ```dotnetcli
    dotnet add package Microsoft.Azure.KeyVault
    dotnet add package Azure.Identity
    ```

1. Nyissa *meg Program.cs,* és adjon hivatkozásokat a következő szükséges csomagokhoz:

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Azure.Identity;
    ```

1. Frissítse `CreateWebHostBuilder` a metódust az alkalmazáskonfiguráció használatához a `config.AddAzureAppConfiguration` metódus hívásával. Adja `ConfigureKeyVault` meg a lehetőséget, és adja át a megfelelő hitelesítő adatokat a Key Vault.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. Amikor inicializálta a kapcsolatot az alkalmazás konfigurációjával, `ConfigureKeyVault` a metódus hívásával beállította a kapcsolatot a Key Vault-tal. Az inicializálás után a Key Vault-hivatkozások értékeit ugyanúgy érheti el, mint a normál alkalmazáskonfigurációs kulcsok értékeit.

    Ha működés közben szeretné látni ezt a folyamatot, nyissa meg az *Index.cshtml fájlt* a Nézetek**kezdőmappájában.** **Views** >  Cserélje le a tartalmát a következő kódra:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    A Key Vault-hivatkozás **TestApp:Settings:KeyVaultMessage** értékét ugyanúgy érheti el, mint a **TestApp:Settings:Message**konfigurációs értékét.

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a .NET Core CLI segítségével szeretné felépíteni, futtassa a következő parancsot a parancshéjban:

    ```dotnetcli
    dotnet build
    ```

1. A létrehozás befejezése után a következő paranccsal helyileg futtassa a webalkalmazást:

    ```dotnetcli
    dotnet run
    ```

1. Nyisson meg egy böngészőablakot, és nyissa meg `http://localhost:5000`a , a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címét.

    ![Gyors útmutató a helyi alkalmazás indítása](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy alkalmazáskonfigurációs kulcsot, amely a Key Vaultban tárolt értékre hivatkozik. Ha meg szeretné tudni, hogyan adhat hozzá egy Azure által felügyelt szolgáltatásidentitást, amely egyszerűsíti az alkalmazáskonfigurációhoz és a Key Vaulthoz való hozzáférést, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitásintegráció](./howto-integrate-azure-managed-service-identity.md)

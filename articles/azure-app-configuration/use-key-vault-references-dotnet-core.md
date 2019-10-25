---
title: Oktatóanyag az Azure-alkalmazások konfigurációjának használatára Key Vault hivatkozások egy ASP.NET Core alkalmazásban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure app Configuration Key Vault hivatkozásait egy ASP.NET Core alkalmazásból
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
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 277333cbca5a31fdc08ae943d2ff61c35d2c9310
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802361"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Oktatóanyag: Key Vault referenciák használata ASP.NET Core alkalmazásban

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure app Configuration szolgáltatást a Azure Key Vaultekkel együtt. Az alkalmazások konfigurálása és Key Vault a legtöbb alkalmazás-telepítésben egymás mellett használt kiegészítő szolgáltatások.

Az alkalmazás konfigurálása a szolgáltatások közös használatát segíti a Key Vaultban tárolt értékeket hivatkozó kulcsok létrehozásával. Ha az alkalmazás konfigurációja ilyen kulcsokat hoz létre, akkor az értékek helyett a Key Vault értékek URI-azonosítóit tárolja.

Az alkalmazás az alkalmazás konfigurációs ügyfelének használatával kéri le Key Vault hivatkozásokat, ugyanúgy, mint az alkalmazás konfigurációjában tárolt egyéb kulcsok esetében. Ebben az esetben az alkalmazás konfigurációjában tárolt értékek URI-k, amelyek a Key Vault értékeire hivatkoznak. Nem Key Vault értékeket vagy hitelesítő adatokat. Mivel az ügyfél Key Vault hivatkozásként ismeri fel a kulcsokat, az Key Vault használatával kéri le az értékeket.

Az alkalmazás felelős azért, hogy megfelelően hitelesítse az alkalmazások konfigurációját és a Key Vault. A két szolgáltatás nem kommunikál közvetlenül.

Ez az oktatóanyag bemutatja, hogyan implementálhatja Key Vault hivatkozásait a kódban. A szolgáltatás a gyors útmutatókban bemutatott webalkalmazásra épül. A folytatás előtt fejezze be a [ASP.net Core alkalmazás létrehozása az alkalmazás-konfigurációval](./quickstart-aspnet-core-app.md) először.

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) például egy platformfüggetlen Kódszerkesztő, amely a Windows, MacOS és Linux operációs rendszerekhez érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy olyan alkalmazás-konfigurációs kulcsot, amely Key Vaultban tárolt értékre hivatkozik.
> * A kulcs értékének elérése egy ASP.NET Core webalkalmazásból.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt telepítse a [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Tároló létrehozása

1. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában:

    ![A Key Vault létrehozása után a kimenet befejeződött](./media/quickstarts/search-services.png)
1. A keresőmezőbe írja be a **Key Vault**kifejezést.
1. Az eredmények listából válassza a bal oldali **kulcstartók** lehetőséget.
1. A **kulcstartók**területen válassza a **Hozzáadás**lehetőséget.
1. A **Key Vault létrehozása**jobb oldalán adja meg a következő információkat:
    - Válassza ki az **előfizetést az előfizetés** kiválasztásához.
    - Az **erőforráscsoport**területen válassza az **új létrehozása** elemet, és adjon meg egy erőforráscsoport-nevet.
    - A **Key Vault neve mezőben**egyedi nevet kell megadni. Ebben az oktatóanyagban írja be a **contoso-vault2**.
    - A **régió** legördülő listából válassza ki a kívánt helyet.
1. Hagyja meg a többi **create Key Vault** -beállítást az alapértelmezett értékekkel.
1. Kattintson a **Létrehozás** gombra.

Ezen a ponton az Azure-fiókja az egyetlen jogosult az új tároló elérésére.

![A Key Vault létrehozása után a kimenet befejeződött](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Ha titkos kulcsot szeretne hozzáadni a tárolóhoz, néhány további lépést kell elvégeznie. Ebben az esetben adjon hozzá egy üzenetet, amely segítségével tesztelheti Key Vault lekérését. Az üzenet neve **üzenet**, és a "Hello from Key Vault" értéket tárolja.

1. A Key Vault tulajdonságok oldalain válassza a **titkok**elemet.
1. Válassza a **készítés/importálás**lehetőséget.
1. A **titkos kulcs létrehozása** panelen adja meg a következő értékeket:
    - **Feltöltési beállítások**: adja meg a **manuális**értéket.
    - **Név**: írja be az **üzenetet**.
    - **Érték**: adja meg **a Hello értéket Key Vault**.
1. Hagyja a másik **titkos** tulajdonságot az alapértelmezett értékekkel.
1. Kattintson a **Létrehozás** gombra.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Key Vault-hivatkozás hozzáadása az alkalmazás konfigurációjához

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). Válassza a **minden erőforrás**lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

1. Válassza a **Configuration Explorer**lehetőséget.

1. Válassza a + > **Key Vault-hivatkozás** **létrehozása** lehetőséget, majd adja meg a következő értékeket:
    - **Kulcs**: válassza a **TestApp: Settings: KeyVaultMessage**elemet.
    - **Címke**: hagyja üresen ezt az értéket.
    - **Előfizetés**, **erőforráscsoport**és **Key Vault**: adja meg az előző szakaszban létrehozott kulcstartóban szereplőknek megfelelő értékeket.
    - **Titkos**kód: válassza ki az előző szakaszban létrehozott **üzenet** titkos nevét.

## <a name="connect-to-key-vault"></a>Kapcsolódás Key Vaulthoz

1. Ebben az oktatóanyagban egy egyszerű szolgáltatásnevet használ a Key Vault való hitelesítéshez. Az egyszerű szolgáltatás létrehozásához használja az Azure CLI az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    A művelet a kulcs/érték párok sorozatát adja vissza:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. A következő parancs futtatásával engedélyezheti, hogy az egyszerű szolgáltatásnév hozzáférjen a kulcstartóhoz:

    ```
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. A következő parancsokban adja hozzá a titkokat a *clientId* és a *ClientSecret* helyett a Secrets Managerhez. A parancsoknak ugyanabban a könyvtárban kell futniuk, mint a *. csproj* fájlt.

    ```
    dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>
    dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>
    ```

> [!NOTE]
> Ezeket a Key Vault hitelesítő adatokat csak az alkalmazáson belül használja a rendszer. Az alkalmazás közvetlenül a hitelesítő adatokkal Key Vault. Ezeket a rendszer soha nem továbbítja az alkalmazás konfigurációs szolgáltatásának.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>A kód frissítése Key Vault-hivatkozás használatára

1. Nyissa meg a *program.cs*, és adja hozzá a következő szükséges csomagokra mutató hivatkozásokat:

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. A `config.AddAzureAppConfiguration` metódus meghívásával frissítse a `CreateWebHostBuilder` metódust az alkalmazás konfigurációjának használatához. Adja meg a `UseAzureKeyVault` lehetőséget egy új `KeyVaultClient`-hivatkozás átadásához a Key Vault.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. Amikor inicializálta az alkalmazás-konfigurációhoz való csatlakozást, átadta a `KeyVaultClient` hivatkozást a `UseAzureKeyVault` metódusnak. Az inicializálás után a Key Vault hivatkozások értékeit ugyanúgy érheti el, mint a normál alkalmazás-konfigurációs kulcsok értékeit.

    A folyamat működés közbeni megtekintéséhez nyissa meg az *index. cshtml* mappát a **views** > **kezdőlapján** . Cserélje le a tartalmát a következő kódra:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    A Key Vault Reference **TestApp: Settings: KeyVaultMessage** értéket ugyanúgy érheti el, mint a **TestApp: Settings: üzenet**konfigurációs értékeként.

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, futtassa a következő parancsot a parancs-rendszerhéjban:

    ```
    dotnet build
    ```

1. A Build befejezése után a következő parancs használatával helyileg futtathatja a webalkalmazást:

    ```
    dotnet run
    ```

1. Nyisson meg egy böngészőablakot, és lépjen a `http://localhost:5000` lapra, amely a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe.

    ![Gyorsindítás – helyi alkalmazás elindítása](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy alkalmazás-konfigurációs kulcsot, amely a Key Vaultban tárolt értékre hivatkozik. A következő oktatóanyagban megismerheti, hogyan adhat hozzá olyan Azure által felügyelt szolgáltatás-identitást, amely megkönnyíti az alkalmazás-konfiguráció és a Key Vault elérését.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)

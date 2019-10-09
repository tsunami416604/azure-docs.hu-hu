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
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035857"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Oktatóanyag: Key Vault referenciák használata ASP.NET Core alkalmazásban

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure app Configuration szolgáltatást a Azure Key Vaultkal együtt. Ezek kiegészítő szolgáltatások, amelyek a legtöbb alkalmazás-telepítésben egymás mellett lesznek felhasználva. A közös használat érdekében az alkalmazás konfigurációja lehetővé teszi, hogy olyan kulcsokat hozzon létre, amelyek a Key Vaultban tárolt értékekre hivatkoznak. Ha ezt teszi, az alkalmazás konfigurációja az URI-t a Key Vault értékre tárolja, nem pedig magát az értéket. Az alkalmazás lekéri a kulcs értékét az alkalmazás konfigurációs ügyfelének használatával, ugyanúgy, mint bármely más, az alkalmazás konfigurációjában tárolt kulcshoz. Az ügyfél-szolgáltató felismeri Key Vault hivatkozásként, és a Key Vault kéri az érték lekérését. Az alkalmazás felelős azért, hogy megfelelően hitelesítse az alkalmazások konfigurációját és a Key Vault. A két szolgáltatás nem kommunikál közvetlenül.

Ebből az oktatóanyagból megtudhatja, hogyan implementálhatja Key Vault hivatkozásait a kódban. A szolgáltatás a gyors útmutatókban bemutatott webalkalmazásra épül. A folytatás előtt fejezze be a [ASP.net Core alkalmazás létrehozása az alkalmazás-konfigurációval](./quickstart-aspnet-core-app.md) először.

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) egy kiváló lehetőség, amely a Windows, MacOS és Linux platformokon érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Olyan alkalmazás-konfigurációs kulcs létrehozása, amely a Key Vaultban tárolt értékre hivatkozik
> * A kulcs értékének elérése egy ASP.NET Core webalkalmazásból

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez telepítse a [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Tároló létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra

    ![Kimenet a Key Vault létrehozási parancsának befejeződése után](./media/quickstarts/search-services.png)
2. A keresőmezőbe írja be a **Key Vault** kifejezést.
3. Az eredmények listájában válassza a **Key Vault** lehetőséget.
4. A Key Vault szakaszban kattintson a **Létrehozás** gombra.
5. A **Kulcstartó létrehozása** szakaszban adja meg a következő információkat:
    - **Név**: Egyedi nevet kell megadni. Ebben a rövid útmutatóban a **contoso-vault2-** t használjuk. 
    - **Előfizetés**: Válasszon egy előfizetést.
    - Az **erőforráscsoport**területen válassza az **új létrehozása** elemet, és adjon meg egy erőforráscsoport-nevet.
    - A **Hely** legördülő menüből válassza ki a helyet.
    - A többi beállítást hagyja az alapértelmezett értéken.
6. A fenti adatok megadása után válassza a **Létrehozás** elemet.

Ezen a ponton az Azure-fiókja az egyetlen jogosult az új tároló elérésére.

![Kimenet a Key Vault létrehozási parancsának befejeződése után](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Titkos kód a tárolóhoz való hozzáadásához csak néhány további lépést kell végrehajtania. Ebben az esetben egy üzenet jelenik meg, amely segítségével tesztelheti Key Vault lekérését. Az üzenet neve **üzenet** , és a Hello értéket tároljuk **Key Vaultban** .

1. A Key Vault tulajdonságok lapon válassza a **titkok**elemet.
1. Kattintson a **Létrehozás/Importálás** gombra.
1. A **Titkos kód létrehozása** képernyőn válassza az alábbi értékeket:
    - **Feltöltési beállítások**: Kézi.
    - **Név**: Message
    - **Érték**: Hello Key Vault
    - A többi értéket hagyja az alapértelmezett értéken. Kattintson a **Create** (Létrehozás) gombra.

## <a name="add-a-key-vault-reference-to-app-config"></a>Key Vault-hivatkozás hozzáadása az alkalmazás-konfigurációhoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás**lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

1. Kattintson a **Configuration Explorer** elemre.

1. Kattintson a **+ létrehozás** > **Key Vault-hivatkozás** elemre, és válassza ki a következő értékeket:
    - **Kulcs**: TestApp: beállítások: KeyVaultMessage
    - **Címke**: Hagyja üresen
    - **Előfizetés**, **erőforráscsoport**, **kulcstartó**: Válassza ki az előző szakaszban létrehozott Key Vault megfelelő beállításokat.
    - **Titkos**kód: Válassza ki az előző szakaszban létrehozott **üzenethez** tartozó titkos kulcsot.

## <a name="connect-to-key-vault"></a>Kapcsolódás Key Vaulthoz

1. Ebben az oktatóanyagban egy egyszerű szolgáltatásnevet fog használni a kulcstartóhoz való hitelesítéshez. Az egyszerű szolgáltatás létrehozásához használja az Azure CLI az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    A művelet a kulcs/érték párok sorozatát fogja visszaadni. 

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

1. A következő parancs futtatásával engedélyezheti az egyszerű szolgáltatásnév számára a kulcstartó elérését:

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. Adja hozzá a Secrets Managerhez a *clientId* és a *clientSecret* titkait. Ezeket a parancsokat ugyanabban a könyvtárban kell végrehajtani, mint a *. csproj* fájlt.

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> Ezeket a Key Vault hitelesítő adatokat csak az alkalmazáson belül használja a rendszer. Az alkalmazás közvetlenül a hitelesítő adatokkal Key Vault. Ezeket a rendszer soha nem továbbítja az alkalmazás konfigurációs szolgáltatásának.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>A kód frissítése Key Vault-hivatkozás használatára

1. Nyissa meg a *program.cs*, és adja hozzá a szükséges csomagokra mutató hivatkozásokat.

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. A `config.AddAzureAppConfiguration()` metódus meghívásával frissítse a `CreateWebHostBuilder` metódust az alkalmazás konfigurációjának használatához. Adja meg a `UseAzureKeyVault` kapcsolót, és adjon meg egy új `KeyVaultClient` hivatkozást a Key Vault.

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

1. Miután átadta a *KeyVaultClient* hivatkozását az `UseAzureKeyVault` metódusnak az alkalmazás-konfigurációhoz való csatlakozás inicializálásakor, a Key Vault-hivatkozások értékeit ugyanúgy érheti el, mint a normál alkalmazás-konfigurációs kulcsok értékeit. Ha szeretné megtekinteni a folyamatot működés közben, nyissa meg az *index. cshtml* mappát a views > Home könyvtárban. Cserélje le a tartalmát a következő kódra:

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

    A Key Vault Reference *TestApp: Settings: KeyVaultMessage* értéket ugyanúgy érheti el, mint a *TestApp: Settings: Message*

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, futtassa a következő parancsot a parancs-rendszerhéjban:

        dotnet build

2. A létrehozás sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

        dotnet run

3. Nyisson meg egy böngészőablakot, és lépjen a `http://localhost:5000` lapra, amely a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe.

    ![Gyorsindítás alkalmazás elindítása helyi](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure által felügyelt szolgáltatás-identitást adott hozzá, amellyel egyszerűbbé válik az alkalmazások konfigurációjának elérése, és javítható a hitelesítő adatok kezelése az alkalmazáshoz. Ha többet szeretne megtudni az alkalmazások konfigurációjának használatáról, folytassa az Azure CLI-mintákkal.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)

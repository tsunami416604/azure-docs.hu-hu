---
title: A titkos alkalmazás beállításainak biztonságos mentése webalkalmazásokhoz – Azure Key Vault | Microsoft Docs
description: Titkos alkalmazások beállításainak, például az Azure-beli hitelesítő adatoknak vagy harmadik féltől származó API-kulcsok biztonságos mentése a ASP.NET Core Key Vault Provider, a felhasználói titok vagy a .NET 4.7.1 konfigurációs építők használatával
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: cawa
ms.openlocfilehash: bcacd5d2ed9e325383ec7ae75002ae0a6213111c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81429758"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Titkos alkalmazás-beállítások biztonságos mentése webalkalmazásokhoz

## <a name="overview"></a>Áttekintés
Ez a cikk azt ismerteti, hogyan lehet biztonságosan menteni a titkos alkalmazások konfigurációs beállításait az Azure-alkalmazásokhoz.

Az összes webalkalmazás-konfigurációs beállítást a rendszer a konfigurációs fájlokba (például Web.config) menti. Ezzel a gyakorlattal ellenőrizheti a titkos beállításokat, például a Felhőbeli hitelesítő adatokat a nyilvános forráskódú vezérlő rendszerekhez, például a GitHubhoz. Eközben nehéz lehet követni a legjobb biztonsági gyakorlatot, mert a forráskód módosításához és a fejlesztési beállítások újrakonfigurálásához szükséges terhek is megváltozhatnak.

Annak érdekében, hogy a fejlesztési folyamat biztonságos legyen, az eszközök és a keretrendszer kódtárai úgy jönnek létre, hogy az alkalmazás titkos beállításait biztonságosan, minimális vagy forráskód-módosítás nélkül mentse.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET és .NET Core-alkalmazások

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Titkos beállítások mentése a felhasználói titkos tárolóban, amely a verziókövetés mappáján kívül esik
Ha gyors prototípust végez, vagy nem rendelkezik internet-hozzáféréssel, kezdje a titkos beállítások áthelyezését a verziókövetés mappáján kívül a felhasználói titkos tárolóba. A felhasználói titkos tároló egy, a User Profiler mappában mentett fájl, ezért a rendszer nem ellenőrzi, hogy nincsenek-e bejelentkezve a forrás vezérlőelembe. Az alábbi ábra bemutatja, hogyan működik a [felhasználói titok](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) .

![A felhasználói titok a forrás vezérlőelemen kívül tartja a titkos beállításokat](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Ha a .NET Core Console alkalmazást futtatja, akkor a Key Vault használatával biztonságosan mentheti a titkot.

### <a name="save-secret-settings-in-azure-key-vault"></a>Titkos beállítások mentése a Azure Key Vaultban
Ha projektet fejleszt, és a forráskódot biztonságosan kell megosztania, használja a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Hozzon létre egy Key Vault az Azure-előfizetésében. Töltse ki az összes kötelező mezőt a felhasználói felületen, és kattintson a panel alján található *Létrehozás* gombra.

    ![Azure Key Vault létrehozása](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. A Key Vaulthoz való hozzáférés biztosítása Önnek és a csoport tagjainak. Ha nagyméretű csapattal rendelkezik, létrehozhat egy [Azure Active Directory csoportot](../../active-directory/active-directory-groups-create-azure-portal.md) , és hozzáadhatja a biztonsági csoportnak a Key Vaulthoz való hozzáférését. A *titkos engedélyek* legördülő menüben tekintse meg a *beolvasás* és *Listázás* a *titkos felügyeleti műveletekben*részt.
Ha már létrehozta a webalkalmazást, adja meg a webalkalmazáshoz való hozzáférést a Key Vault számára, hogy az alkalmazás beállításaiban és fájljaiban ne tárolja titkos konfigurációját. Keresse meg a webalkalmazás nevét, és adja hozzá a felhasználók hozzáférésének megadásához.

    ![Key Vault hozzáférési szabályzat hozzáadása](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Adja hozzá a titkot, hogy Key Vault a Azure Portal. A beágyazott konfigurációs beállításoknál cserélje le a ":" és a "--" kifejezésre, hogy a Key Vault titkos név érvényes legyen. a (z) ":" nem szerepelhet Key Vault titkos kód nevében.

    ![Key Vault titkos kód hozzáadása](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > A Visual Studio 2017 V 15,6 előtt javasoljuk, hogy telepítse a Visual studióhoz készült Azure Services-alapú hitelesítési bővítményt. Ez azonban már elavult, mivel a funkció integrálva van a Visual Studióban. Ezért ha a Visual Studio 2017 egy régebbi verzióját használja, javasoljuk, hogy frissítsen legalább VS 2017 15,6-ra vagy akár úgy, hogy ezt a funkciót natív módon használhassa, és a Key-vaultot a Visual Studio bejelentkezési identitásának használatával is elérheti.
    >

4. Adja hozzá a következő NuGet-csomagokat a projekthez:

    ```
    Microsoft.Azure.KeyVault
    Microsoft.Azure.Services.AppAuthentication
    Microsoft.Extensions.Configuration.AzureKeyVault
    ```
5. Adja hozzá a következő kódot a Program.cs fájlhoz:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                        keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. Adja hozzá a Key Vault URL-címét launchsettings.jsfájlhoz. A környezeti változó neve *KEYVAULT_ENDPOINT* a 6. lépésben hozzáadott kódban van meghatározva.

    ![Key Vault URL-cím hozzáadása projekt környezeti változóként](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. A projekt hibakeresésének megkezdése. A futtatásának sikeresnek kell lennie.

## <a name="aspnet-and-net-applications"></a>ASP.NET és .NET-alkalmazások

A .NET 4.7.1 támogatja a Key Vault és a titkos konfigurációs építőket, amelyek biztosítják, hogy a titkokat a verziókövetés mappán kívül is át lehessen helyezni a kód módosítása nélkül.
A folytatáshoz [töltse le a .net-4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) , és telepítse át az alkalmazást, ha a .NET-keretrendszer régebbi verzióját használja.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Titkos beállítások mentése a forrás-ellenőrzési mappán kívüli titkos fájlba
Ha gyors prototípust ír, és nem szeretné kiépíteni az Azure-erőforrásokat, folytassa ezt a lehetőséget.

1. Telepítse a következő NuGet-csomagot a projektbe
    ```
    Microsoft.Configuration.ConfigurationBuilders.Base
    ```

2. Hozzon létre egy, a következőhöz hasonló fájlt. Mentse azt a projekt mappáján kívüli helyre.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Adja meg a titkos fájlt, hogy az Web.config fájlban lévő Configuration Builder legyen. Ezt a szakaszt a *appSettings* szakasz előtt helyezze el.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. A appSettings szakasz megadásával a titkos konfigurációs szerkesztőt használja. Ellenőrizze, hogy van-e bejegyzés a titkos beállításhoz egy dummy értékkel.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Az alkalmazás hibakeresése. A futtatásának sikeresnek kell lennie.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Titkos beállítások mentése egy Azure Key Vaultban
A projekthez tartozó Key Vault konfigurálásához kövesse az ASP.NET Core szakasz utasításait.

1. Telepítse a következő NuGet-csomagot a projektbe
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Adja meg a Web.config Key Vault Configuration Builder-t. Ezt a szakaszt a *appSettings* szakasz előtt helyezze el. Cserélje le a *vaultName* nevet a Key Vault nevére, ha a Key Vault nyilvános Azure-ban van, vagy ha szuverén felhőt használ, a teljes URI-t használja.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3. A appSettings szakasz megadásával a Key Vault Configuration Builder használatával. Győződjön meg arról, hogy a titkos beállításnak van-e bejegyzése egy dummy értékkel.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. A projekt hibakeresésének megkezdése. A futtatásának sikeresnek kell lennie.

---
title: Titkos Alkalmazásbeállítások webalkalmazás biztonságosan mentése |} Microsoft Docs
description: Hogyan biztonságosan mentése titkos alkalmazás beállításait, például Azure hitelesítő adatok vagy harmadik féltől származó API használatával ASP.NET alapvető Key Vault Provider, a felhasználó titkos kulcs vagy a .NET 4.7.1 konfigurációs rendszerépítők
services: visualstudio
documentationcenter: ''
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: ''
ms.workload: web, azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cawa
ms.openlocfilehash: eee882dc52ffc37067916dac29324efe0bc95b03
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Titkos Alkalmazásbeállítások webalkalmazás biztonságosan mentése

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti, hogyan biztonságosan az Azure-alkalmazások titkos alkalmazás konfigurációs beállításainak mentése.

Hagyományosan az összes webes alkalmazás konfigurációs beállítások Web.config például konfigurációs fájlokban lesznek mentve. Ez az eljárás vezet a titkos beállításai, például a felhő hitelesítő adatokat nyilvános forráskódú ellenőrzési rendszerek, például a Github ellenőrzése. Eközben lehet nehéz hajtsa végre a biztonsági szempontból ajánlott a Forráskód módosítása és fejlesztési újrakonfigurálnia többletterhelés miatt.

Győződjön meg arról, hogy biztonságos fejlesztési folyamat, hogy tooling és a keretrendszer titkos Alkalmazásbeállítások biztonságosan a minimális és a nem az adatforrás-kód módosítása mentése jönnek létre.

## <a name="aspnet-and-net-core-applications"></a>Az ASP.NET és a .NET core alkalmazások

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Forrásmappa vezérlő kívül esik a felhasználó titkos tárolójában titkos beállításainak mentése
Ha egy gyors prototípus végez, vagy nem rendelkezik internet-hozzáféréssel, kezdje a vezérlő forrásmappa kívül a titkos beállítások felhasználói titkos kulcsot tároló áthelyezését. Titkos felhasználókhoz tartozó tárolóban, a titkos kulcsok nem ellenőrzi verziókövetési rendszerrel felhasználói Profilkészítő mappában mentett fájlt. A következő ábra bemutatja, hogyan [felhasználói titkos](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio#SecretManager) működik.

![Felhasználói titkos tartja a verziókövetési kívül titkos beállítások](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Ha .NET core console alkalmazást futtat, a Key Vault segítségével biztonságos helyen mentse a titkos kulcs.

### <a name="save-secret-settings-in-azure-key-vault"></a>Az Azure Key Vault titkos beállításainak mentése
Ha egy csapatprojekt fejleszt, és meg szeretné osztani a forráskód biztonságosan, [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. A kulcstároló létrehozása az Azure-előfizetéshez. A felhasználói felület, majd kattintson az összes kötelező mezőt kitöltötte *létrehozása* a panel alján

    ![Az Azure Key Vault létrehozása](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Engedélyezze a Key Vault való hozzáférést, és a csoport tagjai. Ha egy nagy csapat, létrehozhat egy [Azure Active Directory-csoport](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal) , és adja hozzá a biztonsági csoport elérheti a Kulcstárolóba. A a *titkos engedélyek* legördülő menüben ellenőrzése *beolvasása* és *lista* alatt *titkos felügyeleti műveletek*.

    ![Kulcstároló hozzáférési házirend hozzáadása](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. A titkos kód hozzáadása Key Vault Azure-portál. A beágyazott konfigurációs beállításokhoz, cserélje le a ":" a "--", a Key Vault titkos neve érvénytelen. ":" kell lennie a kulcstároló nevében titkos nem engedélyezett.

    ![Key Vault titkos kulcs hozzáadása](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

4. Telepítse a [a Visual Studio Azure Services hitelesítési kiterjesztés](https://go.microsoft.com/fwlink/?linkid=862354). A bővítmény keresztül alkalmazás által elérhető Key Vault bejelentkezési identitás Visual Studio használatával.

5. A következő NuGet-csomagok hozzáadása a projekthez:

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
6. Adja hozzá a következő kódot a Program.cs fájlban:

    ```csharp
    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
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
                    .UseStartup<Startup>()
                    .Build();

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
7. A Key Vault URL-cím hozzáadását launchsettings.json fájl. A környezeti változó neve *KEYVAULT_ENDPOINT* a 6. lépésben felvett kód van definiálva.

    ![Adja hozzá a projekt környezeti változó Key Vault URL-címe](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. A projekt a hibakeresés elindításához. Sikeresen fusson.

## <a name="aspnet-and-net-applications"></a>Az ASP.NET és a .NET-alkalmazások

.NET 4.7.1 támogatja a Key Vault és a titkos kulcs konfigurációs rendszerépítők, amely biztosítja, hogy a titkos kulcsok áthelyezhető kívül forrásmappa ellenőrző kód módosítása nélkül.
A folytatáshoz [.NET 4.7.1 letöltése](https://www.microsoft.com/download/details.aspx?id=56115) és telepíthetők át az alkalmazást, ha a .NET-keretrendszer régebbi verzióját használja.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Forrásmappa vezérlő kívül esik a titkos fájlban titkos beállításainak mentése
Ha egy gyors prototípus írása, és nem szeretné telepíteni az Azure-erőforrások, nyissa meg ezt a lehetőséget.

1. Telepítse a következő NuGet-csomagot a projekthez
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic.1.0.0-alpha1.nupkg
    ```

2. Hasonló a következő fájl létrehozása. Mentse a projektmappa kívül a location alatt.

    ```xml

       <root>
              <secrets ver="1.0">
                     <secret name="secret1" value="foo_one" />
                        <secret name="secret2" value="foo_two" />
                       </secrets>
      </root>
      ```

3. Adja meg a titkos fájl konfigurációs szerkesztő a Web.config fájlban. Ez a szakasz előtt helyezze *appSettings* szakasz.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Adja meg az appSettings szakaszt használ a titkos konfigurációs szerkesztő. Győződjön meg arról, hogy minden olyan bejegyzést, a titkos beállításához, üres értékű.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Az alkalmazás hibakeresését. Sikeresen fusson.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Egy Azure Key Vault a titkos beállításainak mentése
Az ASP.NET core szakaszából a kulcstároló, a projekt konfigurálásához kövesse az utasításokat.

1. Telepítse a következő NuGet-csomagot a projekthez
```
Microsoft.Configuration.ConfigurationBuilders.UserSecrets.1.0.0-preview2.nupkg
```

2. Adja meg a Key Vault konfigurációs builder a Web.config fájlban. Ez a szakasz előtt helyezze *appSettings* szakasz. Cserélje le *vaultName* kell lennie a Key Vault name, ha a Key Vault szuverén felhő használata a nyilvános Azure és a teljes URI-címe van.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="KeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3.  Adja meg az appSettings szakaszt használ a Key Vault konfigurációs jelentéskészítő. Győződjön meg arról, hogy minden olyan bejegyzést, a titkos beállításához, üres értékű.

    ```xml
    <appSettings configBuilders="AzureKeyVault">
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
        <add key="secret" value="" />
    </appSettings>
    ```

4. A projekt a hibakeresés elindításához. Sikeresen fusson.

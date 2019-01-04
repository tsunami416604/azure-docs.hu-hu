---
title: Biztonságosan a webalkalmazás – Azure Key Vault titkos alkalmazás beállításainak mentése folyamatban van |} A Microsoft Docs
description: Hogyan biztonságosan mentése például az Azure-beli hitelesítő vagy külső API titkos Alkalmazásbeállítások kulcsok használata az ASP.NET core Key Vault Provider, a felhasználó titkos kulcsot, illetve a .NET 4.7.1 konfigurációs kapcsolat építői
services: visualstudio
documentationcenter: ''
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: ''
ms.workload: web, azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: cawa
ms.openlocfilehash: 5ed0f861e17c646a66d9fa636e17ce0ba9c7c91a
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999152"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Biztonságosan a webalkalmazás titkos alkalmazás beállításainak mentése

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan biztonságosan az Azure-alkalmazások titkos alkalmazás konfigurációs beállításainak mentése.

Hagyományosan az összes webes alkalmazás konfigurációs beállítások konfigurációs fájlok, például a Web.config lesznek mentve. Ez az eljárás vezet a Felhőhöz tartozó hitelesítő adatok, amilyen a GitHub nyilvános verziókövetési rendszerekben, például a titkos beállítások ellenőrzése. Mindeközben annak oka az lehet nehezen hajtsa végre a biztonsági szempontból ajánlott a többletterhelés forráskódjának módosítása, és konfigurálja újra a fejlesztői beállítások miatt.

Ahhoz, hogy a fejlesztési folyamatban a biztonságos, azokat az eszközöket és keretrendszer kódtárakat menteni a titkos Alkalmazásbeállítások biztonságosan, minimális konfigurációval vagy nem az adatforrás-kód módosítása jönnek létre.

## <a name="aspnet-and-net-core-applications"></a>Az ASP.NET és a .NET core-alkalmazások

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Felhasználói titkos kulcs tárolójában, amely kívül esik a vezérlő forrásmappa titkos beállítások mentése
Ha egy gyors prototípust végez, vagy nem rendelkezik internet-hozzáféréssel, kezdje a vezérlő forrásmappa kívül a titkos beállítások áthelyezése a felhasználói titkos tároló. Felhasználói Titkoskód-tárolót egy olyan fájl, mentett felhasználói profiler mappában, így a titkos kulcsok nincs bejelentkezve a forráskezelőhöz. Az alábbi diagram bemutatja, hogyan [felhasználói titkos](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio#SecretManager) működik.

![Felhasználói titkos tartja a verziókövetés kívül titkos beállításai](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Ha a .NET core-konzolalkalmazást futtatja, a Key Vault használatával biztonságosan menteni a titkos kód.

### <a name="save-secret-settings-in-azure-key-vault"></a>Az Azure Key Vaultban titkos beállítások mentése
Ha fejleszt egy projektet, és biztonságosan megosztani a forráskódot, használjon [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Hozzon létre egy Key Vaultot az Azure-előfizetésében. A felhasználói felület, majd az összes kötelező mezőt kitöltötte *létrehozás* a panel alján lévő

    ![Az Azure Key Vault létrehozása](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. A támogatás a Key vault eléréséhez, és a munkacsoportja többi tagjával. Ha egy nagy csapat, létrehozhat egy [Azure Active Directory-csoport](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal) , és adja hozzá a Key Vault a biztonsági csoport hozzáférése. Az a *titkos kód engedélyei* legördülő listában, ellenőrizze *első* és *lista* alatt *titkos műveletek*.

    ![A Key Vault hozzáférési szabályzat hozzáadása](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. A titkos kód hozzáadása a Key Vault az Azure Portalon. Beágyazott konfigurációs beállításainak megadásához cserélje le ":" a "--& gt", a Key Vault titkos név érvényes. ":" nem engedélyezett a titkos be egy Key Vaultot nevét kell.

    ![Adja hozzá a Key Vault titkos kulcsából](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

4. Telepítse a [Visual Studióhoz készült Azure-szolgáltatások hitelesítési bővítmény](https://go.microsoft.com/fwlink/?linkid=862354). Ez a bővítmény keresztül az alkalmazás hozzáférhessen a Key Vault használatával a Visual Studio bejelentkezési identitás.

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
7. A Key Vault URL-címe hozzá launchsettings.json fájlt. A környezeti változó neve *KEYVAULT_ENDPOINT* a 6. lépésben hozzáadott kód van definiálva.

    ![Key Vault URL-cím hozzáadása a projekt környezeti változó](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Hibakeresés a projektben. Sikeresen fusson.

## <a name="aspnet-and-net-applications"></a>Az ASP.NET és a .NET-alkalmazások

.NET 4.7.1 támogatja a Key Vaultot és a titkos kulcs konfigurációs sikerei, amely biztosítja, hogy a titkos kulcsokat is áthelyezhetők kívül forrásmappa ellenőrző kódot sem kell módosítani.
A folytatáshoz [töltse le a .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) és áttelepítheti a alkalmazást, ha a .NET-keretrendszer régebbi verzióját használja.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>A titkos kód fájlját, amely kívül esik a forrásmappa vezérlőelem a titkos beállítások mentése
Ha egy gyors prototípust írása, és nem szeretne Azure-erőforrások kiosztásához, nyissa meg ezt a lehetőséget választja.

1. Telepítse a következő NuGet-csomagot a projekthez
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic
    ```

2. Hozzon létre egy fájlt, amely hasonlít az alábbi. Mentse a fájlt a projektmappa fájllistájának kívüli helyre.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Adja meg a titkos kód fájlját kell egy konfigurációs builder a Web.config fájlban. Ez a szakasz előtt helyezze *appSettings* szakaszban.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Adja meg az appSettings szakaszt a titkos konfigurációs builder használ. Ellenőrizze, hogy minden bejegyzés a titkos beállítás üres értékkel.

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

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Az Azure Key vaultban titkos beállítások mentése
Az ASP.NET core szakaszban egy Key Vaultot a projekt konfigurálásához kövesse az utasításokat.

1. Telepítse a következő NuGet-csomagot a projekthez
```
Microsoft.Configuration.ConfigurationBuilders.UserSecrets
```

2. Adja meg a Key Vault konfigurációs builder a Web.config fájlban. Ez a szakasz előtt helyezze *appSettings* szakaszban. Cserélje le *vaultName* kell lennie a Key Vault-e a Key Vault az Azure nyilvános vagy teljes URI szuverén felhő használata.

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
3.  Adja meg az appSettings szakaszt a Key Vault konfigurációs builder használ. Ellenőrizze, hogy minden bejegyzés a titkos beállítás üres értékkel.

    ```xml
    <appSettings configBuilders="AzureKeyVault">
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
        <add key="secret" value="" />
    </appSettings>
    ```

4. Hibakeresés a projektben. Sikeresen fusson.

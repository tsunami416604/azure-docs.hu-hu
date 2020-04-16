---
title: Titkos alkalmazásbeállítások biztonságos mentése egy webalkalmazáshoz – Azure Key Vault | Microsoft dokumentumok
description: Titkos alkalmazásbeállítások, például Azure-hitelesítő adatok vagy harmadik féltől származó API-kulcsok biztonságos mentése ASP.NET alapvető Key Vault-szolgáltató, felhasználói titok vagy a .NET 4.7.1 konfigurációkészítők használatával
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429758"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Titkos alkalmazásbeállítások biztonságos mentése egy webalkalmazáshoz

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti, hogyan biztonságosan mentheti a titkos alkalmazáskonfigurációs beállításokat az Azure-alkalmazásokhoz.

Hagyományosan minden webalkalmazás-konfigurációs beállítás a konfigurációs fájlokba , például a Web.config fájlba kerül. Ez a gyakorlat a titkos beállítások, például a felhőbeli hitelesítő adatok nyilvános forrásvezérlő rendszerek, például a GitHub ellenőrzése. Eközben nehéz lehet követni a biztonság ajánlott eljárás, mert a terhelés szükséges a forráskód módosítása és a fejlesztési beállítások újrakonfigurálása.

Annak érdekében, hogy a fejlesztési folyamat biztonságos legyen, eszközkészítő és keretkódtárak jönnek létre az alkalmazástitkos beállítások minimális vagy semmilyen forráskód-módosítással történő biztonságos mentéséhez.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET és .NET Core alkalmazások

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Titkos beállítások mentése a forrásvezérlő mappán kívüli Felhasználótitkos tárolóban
Ha csinálsz egy gyors prototípus, vagy ha nincs internet-hozzáféréssel, kezdje mozog a titkos beállításokat kívül forrásvezérlő mappát User Secret tárolni. A User Secret tároló a felhasználói profilozó mappájába mentett fájl, így a rendszer nem ad be titkos kulcsokat a forrásvezérlőbe. Az alábbi ábra bemutatja, hogyan működik [a felhasználói titkos kulcsot.](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio)

![A User Secret a titkos beállításokat a forrásellenőrzésen kívül tartja](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Ha a .NET core konzolalkalmazást futtatja, a Key Vault segítségével mentse biztonságosan a titkos kulcsot.

### <a name="save-secret-settings-in-azure-key-vault"></a>Titkos beállítások mentése az Azure Key Vaultban
Ha projektet fejleszt, és a forráskódot biztonságosan kell megosztania, használja az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)ot.

1. Hozzon létre egy Key Vault az Azure-előfizetésben. Töltse ki az összes szükséges mezőt a felhasználói felületen, és kattintson a panel alján található *Létrehozás* gombra.

    ![Azure Key Vault létrehozása](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Hozzáférést biztosíthat Önnek és a csapattagjainak a Key Vaulthoz. Ha nagy csapat, létrehozhat egy [Azure Active Directory-csoportot,](../../active-directory/active-directory-groups-create-azure-portal.md) és adja hozzá, hogy a biztonsági csoport hozzáférést a Key Vault. A *Titkos engedélyek* legördülő listában jelölje be a *Bekerülési* és *lista jelölőnégyzetet* a *Titkos kezelési műveletek csoportban.*
Ha már létrehozta a webalkalmazást, adjon hozzáférést a webalkalmazásnak a Key Vaulthoz, hogy a titkos konfiguráció tanak a titkos konfiguráció tvagy a fájlokban való tárolása nélkül is hozzáférhessen a key vaulthoz. Keresse meg a webalkalmazást a neve alapján, és adja hozzá ugyanúgy, ahogy a felhasználóknak hozzáférést biztosít.

    ![Kulcstartó hozzáférési házirendjének hozzáadása](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Adja hozzá a titkos kulcsot a Key Vault az Azure Portalon. Egymásba ágyazott konfigurációs beállítások esetén cserélje le a ':'-t a '--' helyett, hogy a Key Vault titkos neve érvényes. A ':' nem lehet key vault-titok nevében.

    ![Kulcstartó titkos kulcsának hozzáadása](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > A Visual Studio 2017 V15.6 előtt az Azure Services hitelesítési bővítményének telepítését javasoljuk a Visual Studio számára. De ez elavult most, mint a funkció integrálva van a Visual Studio . Ezért ha a Visual Studio 2017 egy régebbi verzióját használja, javasoljuk, hogy legalább a VS 2017 15.6-os vagy újabb verziójára frissítsen, hogy ezt a funkciót natív módon használhassa, és a Visual Studio bejelentkezési identitásának használatával hozzáférjen a key-vaulthoz.
    >

4. Adja hozzá a következő NuGet csomagokat a projekthez:

    ```
    Microsoft.Azure.KeyVault
    Microsoft.Azure.Services.AppAuthentication
    Microsoft.Extensions.Configuration.AzureKeyVault
    ```
5. Adja hozzá a következő kódot Program.cs fájlhoz:

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
6. Adja hozzá a Key Vault URL-címét a launchsettings.json fájlhoz. A környezeti változó *KEYVAULT_ENDPOINT nevét* a 6.

    ![A Key Vault URL-címének hozzáadása projektkörnyezeti változóként](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. Indítsa el a projekt hibakeresését. Sikeresen kell futnia.

## <a name="aspnet-and-net-applications"></a>ASP.NET és .NET alkalmazások

A .NET 4.7.1 támogatja a Key Vault és a Titkos konfiguráció készítőit, így a titkos kulcsok kódmódosítás nélkül áthelyezhetők a forrásvezérlő mappán kívülre.
A folytatáshoz [töltse le a .NET 4.7.1-es verziót,](https://www.microsoft.com/download/details.aspx?id=56115) és telepítse át az alkalmazást, ha az a .NET keretrendszer régebbi verzióját használja.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Titkos beállítások mentése a forrásvezérlő mappán kívüli titkos fájlba
Ha egy gyors prototípust ír, és nem szeretne Azure-erőforrásokat kiépíteni, folytassa ezzel a lehetőséggel.

1. Telepítse a következő NuGet csomagot a projektbe
    ```
    Microsoft.Configuration.ConfigurationBuilders.Base
    ```

2. Hozzon létre egy hasonló fájlt, amely hasonlít a következőhöz. Mentse a projektmappán kívüli helyre.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Adja meg, hogy a titkos fájl konfigurációszerkesztő legyen a Web.config fájlban. Helyezze ezt a szakaszt *az appBeállítások* szakasz elé.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Adja meg appSettings szakasz a titkos konfiguráció szerkesztője. Győződjön meg arról, hogy van egy bejegyzés a titkos beállításhoz, amelynek üres értéke van.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Hibakeresés az alkalmazás. Sikeresen kell futnia.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Titkos beállítások mentése Azure Key Vaultban
Kövesse ASP.NET alapszakasz utasításait a projekt key vault-konfigurálásához.

1. Telepítse a következő NuGet csomagot a projektbe
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Kulcstartó konfigurációszerkesztőjének definiálása a Web.config fájlban. Helyezze ezt a szakaszt *az appBeállítások* szakasz elé. Cserélje *le a VaultName-t* a Key Vault nevére, ha a Key Vault nyilvános Azure-ban van, vagy teljes URI-t, ha A Sovereign cloud használata.

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
3. Adja meg appSettings szakasz a Key Vault konfigurációszerkesztője. Győződjön meg arról, hogy a titkos beállításhoz van-e próbaértékkel rendelkező bejegyzés.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Indítsa el a projekt hibakeresését. Sikeresen kell futnia.

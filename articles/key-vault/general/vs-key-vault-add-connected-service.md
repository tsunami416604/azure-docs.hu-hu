---
title: Key Vault-támogatás hozzáadása a ASP.NET projekthez a Visual Studio – Azure Key Vault | Microsoft dokumentumok
description: Az oktatóanyag segítségével megtudhatja, hogyan adhat hozzá Key Vault-támogatást egy ASP.NET vagy ASP.NET Core webalkalmazáshoz.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 68f8607db25b70ea5e6bfe21a7d6e002aeb127d6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429771"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Kulcstároló hozzáadása a webalkalmazáshoz a Visual Studio Connected Services használatával

Ebben az oktatóanyagban megtudhatja, hogyan adhat hozzá egyszerűen mindent, amire szüksége van az Azure Key Vault használatával a webes projektek titkos kulcsainak kezeléséhez a Visual Studióban, függetlenül attól, hogy ASP.NET Core-t vagy bármilyen ASP.NET projektet használ. A Visual Studio Csatlakoztatott szolgáltatások szolgáltatásával beállíthatja, hogy a Visual Studio automatikusan hozzáadja az Azure-beli Key Vaulthoz való csatlakozáshoz szükséges összes NuGet csomagot és konfigurációs beállítást.

A Csatlakoztatott szolgáltatások által a key vault engedélyezését lehetővé tevő projektben végrehajtott módosításokról a [Key Vault csatlakoztatott szolgáltatás – Mi történt a ASP.NET 4.7.1-es projekttel](#how-your-aspnet-framework-project-is-modified) vagy a [Key Vault Connected Service - Mi történt a ASP.NET Core projekttel?](#how-your-aspnet-core-project-is-modified)

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztráljon egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)
- **Visual Studio 2019 16.3-as** vagy újabb verziója, vagy a **Visual Studio 2017 15.7-es verziója** a **webfejlesztési** munkaterhelés telepítésével. [Ezt innen töltheti le](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- A Visual Studio 2017-es ASP.NET (nem Core) esetében a . Telepítésükhöz indítsa el a Visual Studio Installer programot, válassza a **Modify**( Módosítás ) lehetőséget, majd válassza az Egyes összetevők lehetőséget , majd a jobb oldalon **bontsa**ki **a ASP.NET és a webfejlesztést,** és válassza a **.NET Framework 4.7.1 Fejlesztőeszközök lehetőséget.**
- Egy ASP.NET 4.7.1-es vagy újabb verzió, illetve ASP.NET Core 2.0 vagy újabb webes projekt megnyílik.

## <a name="add-key-vault-support-to-your-project"></a>A Key Vault támogatás hozzáadása a projekthez

Mielőtt elkezdené, győződjön meg arról, hogy be van jelentkezve a Visual Studióba. Jelentkezzen be ugyanazzal a fiókkal, amelyet az Azure-előfizetéséhez használ. Ezután nyisson meg egy ASP.NET 4.7.1-es vagy újabb verziót, vagy ASP.NET Core 2.0 webes projektet, és hajtsa végre az alábbi lépéseket:

1. A **Megoldáskezelőben**kattintson a jobb gombbal arra a projektre, amelyhez hozzá szeretné adni a Key Vault-támogatást, és válassza a Csatlakoztatott szolgáltatás **hozzáadása parancsot.** > **Connected Service**
   Megjelenik a Connected Service (Csatlakoztatott szolgáltatás) lap a projekthez adható szolgáltatásokkal.
1. Az elérhető szolgáltatások menüjében válassza a **Biztonságos titkok az Azure Key Vault-tal**lehetőséget.

   ![Válassza a "Biztonságos titkok az Azure Key Vaultmal" lehetőséget](../media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Válassza ki a használni kívánt előfizetést, majd válasszon egy új vagy meglévő Key Vault.Select the subscription you want to use, and then choose a new or existing Key Vault. Ha az új Key Vault ot választja, egy **Szerkesztési** hivatkozás jelenik meg. Válassza ki az új Key Vault konfigurálásához.

   ![Válassza ki előfizetését.](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. Az **Azure Key Vault szerkesztése**mezőbe írja be a Key Vaulthoz használni kívánt nevet.

1. Jelöljön ki egy meglévő **erőforráscsoportot,** vagy válasszon egy automatikusan létrehozott egyedi névvel rendelkező új csoportot.  Ha egy másik nevű új csoportot szeretne létrehozni, használhatja az [Azure Portalt,](https://portal.azure.com)majd zárja be a lapot, és indítsa újra az erőforráscsoportok listájának újratöltéséhez.
1. Válassza ki azt a **helyet,** ahol a Key Vaultot létre szeretné hozni. Ha a webalkalmazás az Azure-ban van üzemeltetve, válassza ki azt a régiót, ahol a webalkalmazás az optimális teljesítmény érdekében található.
1. Válasszon **egy tarifacsomagot**. További információt a [Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).
1. A konfigurációs beállítások elfogadásához válassza az **OK gombot.**
1. Miután kiválasztott egy meglévő Key Vaultot, vagy konfigurált egy új Key Vaultot, a Visual Studio **Azure Key Vault** lapján válassza a **Hozzáadás** lehetőséget a csatlakoztatott szolgáltatás hozzáadásához.
1. Válassza ki a **Key Vaultban tárolt titkos kulcsok kezelése** hivatkozást a Key Vault titkos **kulcsokkal** lap megnyitásához. Ha bezárta a lapot vagy a projektet, az [Azure Portalon](https://portal.azure.com) a **Minden szolgáltatás** lehetőséget választva, majd a **Biztonság**csoportban válassza a **Key Vault**lehetőséget, majd válassza ki a Key Vaultot.
1. A létrehozott Key Vault csoportban válassza a **Titkok**, majd a **Létrehozás/importálás lehetőséget.**

   ![Titkos fájl létrehozása/importálása](../media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Írjon be egy titkos kulcsot, például *a MySecret* értéket, és adjon neki karakterlánc-értéket tesztként, majd válassza a **Létrehozás** gombot.

   ![Titkos kulcs létrehozása](../media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (nem kötelező) Adja meg egy másik titkot, de ezúttal tedd egy kategóriába elnevezésével *Secrets--MySecret*. Ez a szintaxis egy "Titkos kulcsok" kategóriát ad meg, amely egy titkos "Sajattitkos kulcsot" tartalmaz.

Most már kódolva férhet hozzá a titkaihoz. A következő lépések attól függően eltérőek, hogy ASP.NET 4.7.1-es vagy ASP.NET Core-t használja.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Hozzáférhet a titkos kulcsokhoz kódban (ASP.NET Core)

1. A Megoldáskezelőben kattintson a jobb gombbal a projektre, és válassza **a NuGet-csomagok kezelése parancsot.** A **Tallózás** lapon keresse meg és telepítse a következő két NuGet csomagot: [a Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és a .NET Core 2 esetében adja hozzá a [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) vagy a .NET Core 3 csomagot, adja hozzá a[Microsoft.Azure.KeyVault.Core értéket.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core)

1. A .NET Core 2 `Program.cs` esetében válassza `BuildWebHost` a lapot, és módosítsa a Program osztály definícióját a következőre:

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
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
               }
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   A .

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. Ezután nyissa meg az egyik lapozófájlt, például *Index.cshtml.cs,* és írja be a következő kódot:
   1. Az irányelv `Microsoft.Extensions.Configuration` segítségével adjon meg egy hivatkozást:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Adja hozzá a konfigurációs változót.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. Adja hozzá ezt a konstruktotort, vagy cserélje le a meglévő konstruktot erre:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Frissítse a(z) `OnGet` metódust. Frissítse az itt látható helyőrző értéket a fenti parancsokban létrehozott titkos névvel.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Az érték futásidőben való megerősítéséhez `ViewData["Message"]` adjon hozzá kódot a *.cshtml* fájlhoz, hogy megjelenjen a titkos kód egy üzenetben.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Az alkalmazás helyi futtatásával ellenőrizheti, hogy a titkos kulcs sikeresen beszerezhető-e a Key Vaultból.

## <a name="access-your-secrets-aspnet"></a>Hozzáférés a titkok (ASP.NET)

Beállíthatja a konfigurációt úgy, hogy a web.config fájl `appSettings` nak van egy dummy értéke az elemben, amelyet a valós érték helyettesít futásidőben. Ezt az `ConfigurationManager.AppSettings` adatstruktúrán keresztül érheti el.

1. A web.config fájl szerkesztése.  Keresse meg az appSettings címkét, adjon hozzá egy attribútumot `configBuilders="AzureKeyVault"`, és adjon hozzá egy sort:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. A `About` metódus szerkesztése *a HomeController.cs*alkalmazásban a megerősítési érték megjelenítéséhez.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Futtassa az alkalmazást helyileg a hibakereső alatt, váltson a **Beet lapra,** és ellenőrizze, hogy a Key Vault értéke megjelenik-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a Key Vault és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az ERŐFORRÁSCSOPORT NEVE MEZŐBE ÍRJA BE AZ **ERŐFORRÁSCSOPORT NEVÉT,** és válassza a **Törlés**lehetőséget.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Key Vault egy másik Microsoft-fiókon fut, mint amelyet a Visual Studióba bejelentkezett (például a Key Vault a munkahelyi fiókjában fut, de a Visual Studio a saját fiókját használja), hibaüzenet jelenik meg a Program.cs fájlban, amely a Visual Studio nem tud hozzáférni a Key Vaulthoz. A probléma megoldása:

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com) és nyissa meg a Key Vaultot.

1. Válassza **az Access-házirendek**, majd **a Hozzáférési házirend hozzáadása**lehetőséget, és válassza ki azt a fiókot, amelybe be van jelentkezve.

1. A Visual Studióban válassza a **Fájlfiók** > **beállításai lehetőséget.**
Válassza **a Fiók hozzáadása** lehetőséget a Minden **fiók** szakaszban. Jelentkezzen be azzal a fiókkal, amelyet a hozzáférési szabályzat a felhasználójaként választott.

1. Válassza **az Eszközök** > **beállításai**lehetőséget, és keresse meg az Azure **Service Authentication**lehetőséget. Ezután válassza ki a Visual Studio-hoz hozzáadott fiókot.

Most, amikor hibakeresést az alkalmazás, a Visual Studio csatlakozik a fiókhoz, ahol a Key Vault található.

## <a name="how-your-aspnet-core-project-is-modified"></a>A ASP.NET Core projekt módosítása

Ez a szakasz azonosítja a ASP.NET projekt pontos módosításait, amikor a Visual Studio használatával hozzáadja a Key Vault csatlakoztatott szolgáltatást.

### <a name="added-references-for-aspnet-core"></a>További hivatkozások a ASP.NET Core-hoz

Hatással van a projektfájl .NET hivatkozásaira és a NuGet csomaghivatkozásokra.

| Típus | Referencia |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Hozzáadott fájlok ASP.NET Core

- `ConnectedService.json`hozzáadott, amely rögzíti a csatlakoztatott szolgáltatóval, verzióval és a dokumentáció valamerre vonatkozó információkat.

### <a name="project-file-changes-for-aspnet-core"></a>A ASP.NET Core projektfájl-módosításai

- Hozzáadva a Connected `ConnectedServices.json` Services ItemGroup és a fájl.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.json változások ASP.NET Core

- A következő környezeti változóbejegyzéseket adta hozzá mind az IIS Express profilhoz, mind a webes projekt nevének megfelelő profilhoz:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Változások az Azure for ASP.NET Core-on

- Létrehozott egy erőforráscsoportot (vagy egy meglévőt használt).
- Létrehozott egy key vaultot a megadott erőforráscsoportban.

## <a name="how-your-aspnet-framework-project-is-modified"></a>A ASP.NET Framework projekt módosítása

Ez a szakasz azonosítja a ASP.NET projekt pontos módosításait, amikor a Visual Studio használatával hozzáadja a Key Vault csatlakoztatott szolgáltatást.

### <a name="added-references-for-aspnet-framework"></a>További hivatkozások ASP.NET keretrendszerhez

Hatással van a projektfájl `packages.config` .NET hivatkozásaira és (NuGet hivatkozásokra).

| Típus | Referencia |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Hozzáadott fájlok ASP.NET keretrendszerhez

- `ConnectedService.json`amely rögzít néhány információt a csatlakoztatott szolgáltatóról, verziószámról és egy hivatkozást a dokumentációról.

### <a name="project-file-changes-for-aspnet-framework"></a>A ASP.NET Keretrendszer projektfájljainak módosításai

- Hozzáadva a Connected Services ItemGroup és a ConnectedServices.json fájl.
- Hivatkozások a Hozzáadott hivatkozások szakaszban ismertetett .NET [szerelvényekre.](#added-references-for-aspnet-framework)

### <a name="webconfig-or-appconfig-changes"></a>web.config vagy app.config változások

- Hozzáadva a következő konfigurációs bejegyzések:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>Változások az Azure for ASP.NET Framework-en

- Létrehozott egy erőforráscsoportot (vagy egy meglévőt használt).
- Létrehozott egy key vaultot a megadott erőforráscsoportban.

## <a name="next-steps"></a>További lépések

Ha követte ezt az oktatóanyagot, a Key Vault-engedélyek a saját Azure-előfizetéssel való futtatásra vannak beállítva, de ez nem feltétlenül kívánatos egy éles környezetben. Felügyelt identitást hozhat létre az alkalmazás Key Vault-hozzáféréskezeléséhez. Lásd: [Kulcstartó hitelesítésének biztosítása felügyelt identitással.](/azure/key-vault/managed-identity)

A Key Vault fejlesztői útmutatójának elolvasásával többet is megtudhat a [Key Vault fejlesztői útmutatójáról.](developers-guide.md)

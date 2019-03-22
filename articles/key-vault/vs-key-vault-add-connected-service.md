---
title: A Key Vault-támogatás hozzáadása az ASP.NET-projektben, Visual Studio használatával – az Azure Key Vault |} A Microsoft Docs
description: Ez az oktatóanyag segítségével elsajátíthatja a Key Vault támogatásának hozzáadása egy ASP.NET- vagy ASP.NET Core-webalkalmazás.
services: key-vault
author: ghogen
manager: jillfra
ms.prod: visual-studio
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: ghogen
ms.openlocfilehash: d95bd114be712953b79ef5afbb0915173f6de26c
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339278"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Key Vault hozzáadása a webalkalmazás a Visual Studio csatlakoztatott szolgáltatásai segítségével

Ebben az oktatóanyagban, megtudhatja, hogyan adhat hozzá, amit az Azure Key Vaulttal a webes projekt a Visual Studióban, a titkos kulcsok kezeléséhez, hogy az ASP.NET Core vagy bármilyen típusú ASP.NET-projekt indításához. A csatlakoztatott szolgáltatás használatával a Visual Studióban, akkor is automatikusan adja hozzá a NuGet-csomagok és a konfigurációs beállításokat kell csatlakozni a Key Vault az Azure-ban a Visual Studio. 

A módosításokat, hogy csatlakoztatott szolgáltatásai lehetővé teszi a projekt a Key Vault engedélyezése a részletekért lásd: [Key Vault csatlakoztatott szolgáltatás – Mi történt a saját ASP.NET 4.7.1 projekt](#how-your-aspnet-framework-project-is-modified) vagy [Key Vault csatlakoztatott szolgáltatás – Mi történt ASP.NET Core-projektemmel](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
- **A Visual Studio 2019** vagy **Visual Studio 2017 verzió 15.7** az a **webfejlesztés** számítási feladattal. [Ezt innen töltheti le](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- ASP.NET (és nem alapvető) a Visual Studio 2017 kell a .NET-keretrendszer 4.7.1 vagy újabb fejlesztői eszközök, amelyek alapértelmezés szerint nincs telepítve. A telepítéshez indítsa el a Visual Studio telepítőjét, válassza a **módosítás**, és válassza a **az egyes összetevők**, majd a jobb oldalon bontsa ki a **ASP.NET és webfejlesztési**, és válassza a **4.7.1 .NET-keretrendszer fejlesztői eszközök**.
- Az ASP.NET 4.7.1 vagy újabb, vagy az ASP.NET Core 2.0 webes projekt megnyitása.

## <a name="add-key-vault-support-to-your-project"></a>Key Vault támogatásának hozzáadása a projekthez

1. A **Megoldáskezelőben** válassza az **Add** > **Connected Service** (Hozzáadás, Csatlakoztatott szolgáltatás) lehetőséget.
   Megjelenik a Connected Service (Csatlakoztatott szolgáltatás) lap a projekthez adható szolgáltatásokkal.
1. Elérhető szolgáltatások menüjében válassza **biztonságos titkos kulcsok az Azure Key Vault**.

   !["Az Azure Key Vault biztonságos titkoskulcs" kiválasztása](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Ha bejelentkezett a Visual Studióba, és rendelkezik a fiókjához társított Azure-előfizetéssel, egy lap jelenik meg, amely az előfizetéseit tartalmazza egy legördülő listában. Győződjön meg arról, hogy be van jelentkezve a Visual studióba, és ugyanazt a fiókot használja az Azure-előfizetését, hogy a fiók be van jelentkezve a következővel.

1. Válassza ki az előfizetést szeretné használni, és kattintson az új vagy meglévő kulcstároló, vagy válassza a Szerkesztés hivatkozásra módosíthatja az automatikusan létrehozott nevet.

   ![Válassza ki előfizetését.](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Írja be a Key vault használni kívánt nevét.

   ![Nevezze át a Key Vaultban, és válasszon ki egy erőforráscsoportot](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat egy automatikusan létrehozott egyedi névvel.  Ha szeretne létrehozni egy új csoportot egy másik névvel, használhatja a [az Azure Portal](https://portal.azure.com), majd zárja be a lapot, és töltse be újra az erőforráscsoportok listájának újraindítás.
1. Válassza ki a régiót, amelyben létrehozza a Key Vaultban. Ha webalkalmazása az Azure-ban üzemeltetett, válassza ki a régiót, amelyben az optimális teljesítmény webes alkalmazást.
1. Válasszon ki egy díjszabási modellt. További információkért lásd: [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/).
1. Kattintson az OK gombra, fogadja el a konfigurációs beállításokkal.
1. Válasszon **Hozzáadás** a Key Vault létrehozása. A létrehozási folyamat meghiúsulhat, ha úgy dönt, hogy olyan nevet, amely már használták.  Ha ez történik, használja a **szerkesztése** hivatkozás nevezze át a Key Vaultban, és próbálkozzon újra.

   ![Csatlakoztatott szolgáltatás hozzáadása a projekthez](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Ezután adjon hozzá egy titkos kulcsot a Key vaultban az Azure-ban. A portálon jó helyen lekéréséhez kattintson a kezelés titkos kulcsok a Key vaultban tárolt mutató hivatkozás. Ha már bezárta a lap, vagy a projekthez, navigálhat a a [az Azure portal](https://portal.azure.com) kiválasztásával **minden szolgáltatás**alatt **biztonsági**, válassza a **Key Vault**, majd válassza ki a létrehozott Key Vault.

   ![Navigálás a portálon](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. A kulcs a Key Vault szakaszban létrehozott tároló, válassza a **titkok**, majd **létrehozás/importálás**.

   ![Titkos kulcs létrehozás/importálás](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Adja meg a titkos kulcs, például a "MySecret" és a teszteléshez használhat bármilyen karakterlánc típusú értéket adjon meg hozzá, majd válassza a **létrehozás** gombra.

   ![Titkos kulcs létrehozása](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (nem kötelező) Adjon meg egy másik titkos, de ezúttal helyezzük el egy kategória szerint elnevezése "Titkos – MySecret". Ez a szintaxis adja meg a "Titok" kategória, amely tartalmazza a titkos kulcs "MySecret."
 
Most a kód a titkos kulcsokat is elérheti. A következő lépések eltérnek attól függően, hogy a ASP.NET 4.7.1- vagy ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>A titkos kód eléréséhez

1. A két nuget-csomagok telepítéséhez [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-kódtárakat.

2. Nyissa meg a Program.cs fájlt, és frissítse a kódot az alábbira: 
   ```
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

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
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

3. Ezután nyissa meg a About.cshtml.cs fájlt, és írja be az alábbi kódot:
   1. Ez Microsoft.Extensions.Configuration hivatkozást tartalmaz a using utasítást:

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. Ez a konstruktor adja hozzá:

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Frissítse a OnGet módot. Frissítse az itt látható a fenti parancsok létrehozott titkos nevű helyőrző értékét.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Az alkalmazás helyi futtatásához keresse meg a névjegy lapra. A beolvasott titkos érték kell megjelennie.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="how-your-aspnet-core-project-is-modified"></a>Hogyan módosul, az ASP.NET Core-projektet

Ez a szakasz azonosítja a pontos változások ASP.NET-projekt hozzáadása a Key Vault csatlakoztatott szolgáltatás Visual Studio használatával.

### <a name="added-references"></a>A hozzáadott referenciák

A projekt fájl .NET-referenciák és az alkalmazáscsomag-hivatkozások NuGet van hatással.

| Typo | Leírások |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files"></a>Hozzáadott fájlok

- ConnectedService.json hozzáadva, amely rögzíti a csatlakoztatott szolgáltatás szolgáltató, a verzió és a egy hivatkozás a dokumentációs némi információt.

### <a name="project-file-changes"></a>Projekt fájlmódosítás

- A csatlakoztatott szolgáltatások ItemGroup és ConnectedServices.json fájl megtörtént.

### <a name="launchsettingsjson-changes"></a>launchsettings.JSON módosítások

- Adja hozzá a következő környezeti változó bejegyzéseket az IIS Express profil, mind a profilt, amely megegyezik a webes projekt neve:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure"></a>Változások az Azure-ban

- Létrehozott egy erőforráscsoportot (vagy egy meglévő használja).
- A megadott erőforráscsoportban létrehozott egy kulcstartót.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Hogyan módosul, az ASP.NET keretrendszer projekt

Ez a szakasz azonosítja a pontos változások ASP.NET-projekt hozzáadása a Key Vault csatlakoztatott szolgáltatás Visual Studio használatával.

### <a name="added-references"></a>A hozzáadott referenciák

Hatással van a .NET projekt fájlhivatkozásokat és `packages.config` (NuGet-hivatkozás).

| Typo | Leírások |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files"></a>Hozzáadott fájlok

- ConnectedService.json ad hozzá, amely rögzíti a csatlakoztatott szolgáltatás szolgáltató, verzióját és a egy hivatkozást a dokumentációban némi információt.

### <a name="project-file-changes"></a>Projekt fájlmódosítás

- A csatlakoztatott szolgáltatások ItemGroup és ConnectedServices.json fájl megtörtént.
- A .NET-szerelvények ismertetett mutató hivatkozásokat a [hozzá hivatkozásokat](#added-references) szakaszban.

### <a name="webconfig-or-appconfig-changes"></a>Web.config vagy az App.config fájlt módosítások

- Adja hozzá a következő konfigurációs bejegyzéseket:

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

### <a name="changes-on-azure"></a>Változások az Azure-ban

- Létrehozott egy erőforráscsoportot (vagy egy meglévő használja).
- A megadott erőforráscsoportban létrehozott egy kulcstartót.

## <a name="next-steps"></a>További lépések

További információ a Key Vault fejlesztői olvassa el a [Key Vault fejlesztői útmutató](key-vault-developers-guide.md)
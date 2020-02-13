---
title: MVC-projekt módosításai az Azure AD-hez való csatlakozáskor
description: Leírja, hogy mi történik az MVC-projekttel, amikor az Azure AD-hoz csatlakozik a Visual Studio csatlakoztatott szolgáltatásaival
author: ghogen
manager: jillfra
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 0f426e7a87204acd4be6529f7dd11a954f23491a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159471"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt az MVC-projekttel (Visual Studio Azure Active Directory csatlakoztatott szolgáltatás)?

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-dotnet-getting-started.md)
> - [mi történt](vs-active-directory-dotnet-what-happened.md)

Ez a cikk az am ASP.NET MVC-projektben végzett pontos módosításokat ismerteti, amikor a [Azure Active Directory csatlakoztatott szolgáltatást a Visual Studióval](vs-active-directory-add-connected-service.md)adja hozzá.

További információ a csatlakoztatott szolgáltatás használatáról: [első lépések](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Hozzáadott referenciák

Hatással van a projektfájl *. NET referenciái) és a `packages.config`ra (NuGet-referenciák).

| Típus | Referencia |
| --- | --- |
| NET NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| NET NuGet | Microsoft.Owin |
| NET NuGet | Microsoft.Owin.Host.SystemWeb |
| NET NuGet | Microsoft.Owin.Security |
| NET NuGet | Microsoft.Owin.Security.Cookies |
| NET NuGet | Microsoft.Owin.Security.OpenIdConnect |
| NET NuGet | Owin |
| .NET        | System.IdentityModel |
| NET NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

További hivatkozások ha a címtár- **adatok olvasása** lehetőséget választotta:

| Típus | Referencia |
| --- | --- |
| NET NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (csak Visual Studio 2015) |
| NET NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| NET NuGet | Microsoft.Data.Edm |
| NET NuGet | Microsoft.Data.OData |
| NET NuGet | Microsoft.Data.Services.Client |
| NET NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms (csak Visual Studio 2015) |
| NET NuGet | System.Spatial |

A következő hivatkozások törlődnek (csak ASP.NET 4 projekt, a Visual Studio 2015-as verziójában):

| Típus | Referencia |
| --- | --- |
| NET NuGet | Microsoft.AspNet.Identity.Core |
| NET NuGet | Microsoft.AspNet.Identity.EntityFramework |
| NET NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Projektfájl módosításai

- Állítsa a tulajdonságot `IISExpressSSLPort` egy külön számra.
- Állítsa a tulajdonságot a 0 értékre `WebProject_DirectoryAccessLevelKey`, vagy 1 értéket, ha a **Címtáradatok olvasása** lehetőséget választotta.
- Állítsa a `IISUrl` tulajdonságot `https://localhost:<port>/`, ahol a `<port>` megfelel a `IISExpressSSLPort` értéknek.

## <a name="webconfig-or-appconfig-changes"></a>a web. config vagy az app. config módosítása

- A következő konfigurációs bejegyzések lettek hozzáadva:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- `System.IdentityModel.Tokens.Jwt` és `Microsoft.IdentityModel.Protocol.Extensions``<runtime><assemblyBinding>` csomópontjában `<dependentAssembly>` elemek lettek hozzáadva.

További változtatások a **Címtáradatok olvasása** lehetőség bejelölésével:

- A következő konfigurációs bejegyzést adta hozzá a `<appSettings>`:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- A következő elemeket adta hozzá a `<configuration>`; a Project-MDF-file és a Project-Catalog-ID értékek a következőnél változhatnak:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- A `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`és `Microsoft.Data.OData``<runtime><assemblyBinding>` csomópontjában `<dependentAssembly>` elemek lettek hozzáadva.

## <a name="code-changes-and-additions"></a>Kód módosításai és kiegészítései

- A `[Authorize]` attribútum hozzáadva a `Controllers/HomeController.cs`hoz és bármely más meglévő vezérlőhöz.

- Hozzáadott egy hitelesítési indítási osztályt (`App_Start/Startup.Auth.cs`), amely az Azure AD-hitelesítés indítási logikáját tartalmazza. Ha a **Címtáradatok olvasása** lehetőséget választotta, ez a fájl a OAuth-kód fogadására és a hozzáférési tokenek cseréjére szolgáló kódot is tartalmaz.

- Egy vezérlő osztályt (`Controllers/AccountController.cs`) adott hozzá, amely `SignIn` és `SignOut` metódusokat tartalmaz.

- Egy részleges nézet, `Views/Shared/_LoginPartial.cshtml`, amely a `SignIn` és a `SignOut`művelet hivatkozását tartalmazza.

- Részleges nézet, `Views/Account/SignoutCallback.cshtml`, amely HTML-kódot tartalmaz a kijelentkezési KEZELŐFELÜLEThez.

- Frissítette a `Startup.Configuration` metódust, hogy tartalmazza a `ConfigureAuth(app)` meghívását, ha az osztály már létezik; egyéb esetben olyan `Startup` osztályt adott hozzá, amely a metódus meghívását is tartalmazza.

- Hozzáadott `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) vagy `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), amely azokat az információkat tartalmazza, amelyeket a Visual Studio használ a csatlakoztatott szolgáltatás hozzáadásának nyomon követésére.

- Ha bejelölte a **Címtáradatok olvasása** lehetőséget, a rendszer hozzáadta `Models/ADALTokenCache.cs` és `Models/ApplicationDbContext.cs` a jogkivonat-gyorsítótárazás támogatásához. További vezérlőt és nézetet is hozzáadott a felhasználói profil adatainak az Azure Graph API-kkal való elérésének szemléltetéséhez: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`és `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Fájl biztonsági mentése (Visual Studio 2015)

A csatlakoztatott szolgáltatás hozzáadásakor a Visual Studio 2015 biztonsági mentést készít a módosított és eltávolított fájlokról. A rendszer az összes érintett fájlt menti a `Backup/AzureAD`mappába. A Visual Studio 2017-es és újabb verziói nem hoznak létre biztonsági másolatokat.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Változások az Azure-ban

- Létrehozott egy Azure AD-alkalmazást abban a tartományban, amelyet a csatlakoztatott szolgáltatás hozzáadásakor kiválasztott.
- Frissítette az alkalmazást, hogy tartalmazza a **címtár-adatolvasási** engedélyt, ha ez a beállítás be van jelölve.

[További információ a Azure Active Directoryról](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Következő lépések

- [Hitelesítési forgatókönyvek Azure Active Directory](authentication-scenarios.md)
- [Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](quickstart-v2-aspnet-webapp.md)

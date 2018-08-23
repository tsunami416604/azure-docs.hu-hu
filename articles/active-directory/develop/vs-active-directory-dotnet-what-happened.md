---
title: Azure ad-ben való csatlakozáskor MVC projekt végrehajtott módosítások
description: Leírja, mi történik az MVC projekthez, amikor a Visual Studio kapcsolódó szolgáltatásaival használatával az Azure AD connect
services: active-directory
author: ghogen
manager: douge
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: f5ce147e61566ba75532103b4f17460e8029da12
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058733"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt az MVC-projektemmel (a Visual Studio Azure Active Directory csatlakoztatott szolgáltatás)?

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-dotnet-getting-started.md)
> - [mi történt](vs-active-directory-dotnet-what-happened.md)

Ez a cikk azonosítja az ASP.NET MVC projekt hozzáadásakor am pontos módosításait a [Azure Active Directory csatlakoztatott szolgáltatás Visual studióval](vs-active-directory-add-connected-service.md).

A csatlakoztatott szolgáltatás való használatáról információkért lásd: [bevezetés](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>A hozzáadott referenciák

A projekt fájlhivatkozásokat *.NET befolyásolja) és `packages.config` (NuGet-hivatkozás).

| Típus | Leírások |
| --- | --- |
| .NET; NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.Cookies |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Owin |
| .NET        | System.IdentityModel |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

További hivatkozások, ha bejelölte a **címtáradatok olvasása** lehetőséget:

| Típus | Leírások |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (Visual Studio 2015-ös csak) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (Visual Studio 2015-ös csak) |
| .NET; NuGet | System.Spatial |

A következő hivatkozásokat el lesznek távolítva (ASP.NET 4-projektek, mint a Visual Studio 2015):

| Típus | Leírások |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Projekt fájlmódosítás

- A tulajdonság értéke `IISExpressSSLPort` egyedi szám.
- A tulajdonság értéke `WebProject_DirectoryAccessLevelKey` 0 vagy 1, ha bejelölte a **címtáradatok olvasása** lehetőséget.
- A tulajdonság értéke `IISUrl` való `https://localhost:<port>/` ahol `<port>` megegyezik a `IISExpressSSLPort` értéket.

## <a name="webconfig-or-appconfig-changes"></a>Web.config vagy az App.config fájlt módosítások

- Adja hozzá a következő konfigurációs bejegyzéseket:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Hozzáadott `<dependentAssembly>` elemek alatt a `<runtime><assemblyBinding>` csomópont `System.IdentityModel.Tokens.Jwt` és `Microsoft.IdentityModel.Protocol.Extensions`.

További módosításokat, ha bejelölte a **címtáradatok olvasása** lehetőséget:

- A következő konfigurációs bejegyzés alatt hozzáadott `<appSettings>`:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Hozzáadva a következő elemek alatt `<configuration>`; a project-mdf- és a katalógus-projektazonosító változhat a értékei:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
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

- Hozzáadott `<dependentAssembly>` elemek alatt a `<runtime><assemblyBinding>` csomópont `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`, és `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Kód módosításairól és kínálatáról

- Hozzáadja a `[Authorize]` attribútumot `Controllers/HomeController.cs` és más, meglévő tartományvezérlőkön.

- Hozzáadva egy hitelesítést indítási osztályt `App_Start/Startup.Auth.cs`, amely tartalmazza az Azure AD-hitelesítés ügyfélindítási logikája. Ha bejelölte a **címtáradatok olvasása** beállítást, ez a fájl is tartalmazza kódot, hogy megkapja az OAuth-kódot, és azt exchange-hozzáférési jogkivonatot.

- Hozzáadva, amely vezérlőosztály `Controllers/AccountController.cs`, tartalmazó `SignIn` és `SignOut` módszereket.

- Egy részleges nézetet, hozzáadott `Views/Shared/_LoginPartial.cshtml`, egy művelet hivatkozására tartalmazó `SignIn` és `SignOut`.

- Egy részleges nézetet, hozzáadott `Views/Account/SignoutCallback.cshtml`, HTML tartalmazó kijelentkezés felhasználói felület.

- Frissítve a `Startup.Configuration` metódus hívását tartalmazza `ConfigureAuth(app)` Ha már van ilyen osztály; ellenkező esetben hozzáadja egy `Startup` osztály, amely tartalmazza a meghívja a metódust.

- Hozzáadott `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) vagy `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), amely információkat tartalmaz, amely a Visual Studio segítségével nyomon követheti a csatlakoztatott szolgáltatás.

- Ha bejelölte a **címtáradatok olvasása** hozzáadva, a beállítás `Models/ADALTokenCache.cs` és `Models/ApplicationDbContext.cs` token-gyorsítótárazási támogatásához. Kiegészült egy további tartományvezérlő és a nézet elérésére felhasználói profil adatait az Azure graph API-k használatával mutatja be: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`, és `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Biztonsági mentés (Visual Studio 2015)

A csatlakoztatott szolgáltatás hozzáadása, ha Visual Studio 2015 eltávolított és a módosított fájlok mentését. A mappa összes érintett fájlok lesznek mentve `Backup/AzureAD`. A Visual Studio 2017 nem hoz létre a biztonsági mentéseket.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Változások az Azure-ban

- Létrehozott egy Azure AD-alkalmazást a tartományban, amikor a csatlakoztatott szolgáltatás hozzáadása a kiválasztott.
- Az alkalmazás frissítése a **címtáradatok olvasása** engedélyt, ha ezt a lehetőséget választotta.

[További információ az Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>További lépések

- [Hitelesítési forgatókönyvek az Azure Active Directory](authentication-scenarios.md)
- [Jelentkezzen be a Microsoft ASP.NET-webalkalmazás hozzáadása](quickstart-v1-aspnet-webapp.md)

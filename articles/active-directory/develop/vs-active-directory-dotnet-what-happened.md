---
title: Változások a MVC projekt csatlakoztatása az Azure AD |} Microsoft Docs
description: Ismerteti, mi történik a MVC projekt, amikor csatlakozik a Visual Studio szolgáltatásainak a használatával az Azure AD connect
services: active-directory
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: 1925a32ce5745673c08af3f9cfe63090d4adfa23
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt a MVC projekt (a Visual Studio Azure Active Directory szolgáltatás csatlakozik)?

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-dotnet-getting-started.md)
> - [mi történt](vs-active-directory-dotnet-what-happened.md)

Ez a cikk azonosítja a pontos am ASP.NET MVC projekt hozzáadása során végzett módosításokat a [Azure Active Directory szolgáltatást a Visual Studio használatával csatlakoztatott](vs-active-directory-add-connected-service.md).

A csatlakoztatott szolgáltatás munkavégzés további információkért lásd: [bevezetés](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>A hozzáadott hivatkozások

A projekt fájlhivatkozások *.NET befolyásolja) és `packages.config` (NuGet-hivatkozás).

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
| .NET        | EntityFramework.SqlServer (Visual Studio 2015-öt csak) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (Visual Studio 2015-öt csak) |
| .NET; NuGet | System.Spatial |

A következő hivatkozásokat eltávolításuk (ASP.NET 4 projektek csak, mint a Visual Studio 2015-öt):

| Típus | Leírások |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Projekt fájlváltozások

- Állítsa be a tulajdonságot `IISExpressSSLPort` egy különálló számára.
- Állítsa be a tulajdonságot `WebProject_DirectoryAccessLevelKey` 0 vagy 1, ha bejelölte a **címtáradatok olvasása** lehetőséget.
- Állítsa be a tulajdonságot `IISUrl` való `https://localhost:<port>/` ahol `<port>` megegyezik a `IISExpressSSLPort` érték.

## <a name="webconfig-or-appconfig-changes"></a>Web.config vagy az App.config fájlt módosítások

- A következő konfigurációs bejegyzések felvételekor:

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

- A következő elemeket a hozzáadott `<configuration>`; az mdf-projektfájlt és katalógus-projektazonosítónak változnak vonatkozó értékeket:

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

## <a name="code-changes-and-additions"></a>Kód módosításait és kiegészítéseit

- Hozzáadva a `[Authorize]` attribútumot `Controllers/HomeController.cs` és más meglévő vezérlők.

- Egy hitelesítési indítási osztály hozzáadott `App_Start/Startup.Auth.cs`, az Azure AD-alapú hitelesítés ügyfélindítási logikája tartalmazó. Ha bejelölte a **címtáradatok olvasása** beállítást, a fájl is tartalmaz kódot, hogy megkapja az OAuth-kódot, és az exchange-hozzáférési tokent.

- A vezérlő osztályhoz hozzáadott `Controllers/AccountController.cs`, tartalmazó `SignIn` és `SignOut` módszerek.

- Részleges nézet hozzáadott `Views/Shared/_LoginPartial.cshtml`, egy művelet hivatkozására, a tartalmazó `SignIn` és `SignOut`.

- Részleges nézet hozzáadott `Views/Account/SignoutCallback.cshtml`, HTML tartalmazó kijelentkezés felhasználói felülete.

- Frissítve a `Startup.Configuration` metódus hívását tartalmazza `ConfigureAuth(app)` Ha az osztály már létezik; ellenkező esetben hozzá egy `Startup` osztály, amely tartalmazza az meghívja a metódust.

- Hozzáadott `Connected Services/AzureAD/ConnectedService.json` (a Visual Studio 2017) vagy `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), a Visual Studio segítségével nyomon követheti a csatlakoztatott szolgáltatás hozzáadása tartalmazó adatokat.

- Ha bejelölte a **címtáradatok olvasása** beállítás hozzáadott `Models/ADALTokenCache.cs` és `Models/ApplicationDbContext.cs` token-gyorsítótárazási támogatásához. Adott egy további tartományvezérlő és a nézet elérése során felhasználói profillal kapcsolatos információk az Azure graph API-k segítségével mutatja be: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`, és `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>A fájlok biztonsági mentése (a Visual Studio 2015)

A csatlakoztatott szolgáltatás hozzáadásakor Visual Studio 2015 fájlokról megváltozott és eltávolítása. A mappa minden érintett fájlok lesznek mentve `Backup/AzureAD`. A Visual Studio 2017 nem hoz létre a biztonsági mentéseket.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Változások az Azure-on

- Amikor a csatlakoztatott szolgáltatás hozzáadása a kiválasztott tartományban létre egy Azure AD-alkalmazást.
- Frissítve az alkalmazásnak, hogy tartalmazzák a **címtáradatok olvasása** engedélyt, ha ezt a beállítást választotta.

[További tudnivalók az Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory hitelesítési forgatókönyvei](active-directory-authentication-scenarios.md)
- [Bejelentkezés a Microsoft ASP.NET webalkalmazás hozzáadása](guidedsetups/active-directory-aspnetwebapp-v1.md)

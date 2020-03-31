---
title: MVC-projekt módosításai, amikor csatlakozik az Azure AD-hez
description: A cikk ismerteti, hogy mi történik az MVC-projekttel, amikor a Visual Studio csatlakoztatott szolgáltatásaival csatlakozik az Azure AD-hez
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
ms.openlocfilehash: ac38adba4ca0d978dd48a546bed1b1faf4fe40fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036959"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt az MVC-projekttel (Visual Studio Azure Active Directoryhoz csatlakoztatott szolgáltatás)?

> [!div class="op_single_selector"]
> - [Bevezetés](vs-active-directory-dotnet-getting-started.md)
> - [mi történt](vs-active-directory-dotnet-what-happened.md)

Ez a cikk azonosítja a ASP.NET MVC-projekt pontos módosításait az [Azure Active Directoryhoz csatlakoztatott szolgáltatás Visual Studio használatával történő hozzáadásakor.](vs-active-directory-add-connected-service.md)

A csatlakoztatott szolgáltatás sal kapcsolatos tudnivalókat az [Első lépések című témakörben talál.](vs-active-directory-dotnet-getting-started.md)

## <a name="added-references"></a>Hozzáadott hivatkozások

Hatással van a projektfájlra (*.NET-hivatkozások) és `packages.config` a (NuGet hivatkozásokra).

| Típus | Referencia |
| --- | --- |
| .NET; NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Biztonság |
| .NET; NuGet | Microsoft.Owin.Security.Cookies |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Owin között |
| .NET        | System.IdentityModell |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

További hivatkozások, ha a **Címtáradatok olvasása** beállítást választotta:

| Típus | Referencia |
| --- | --- |
| .NET; NuGet | EntityFramework (Entitáskeret) |
| .NET        | EntityFramework.SqlServer (csak Visual Studio 2015 esetén) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Ügyfél |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (csak Visual Studio 2015 esetén) |
| .NET; NuGet | System.Spatial |

A rendszer eltávolítja a következő hivatkozásokat (csak ASP.NET 4 projektet, mint a Visual Studio 2015-ben):

| Típus | Referencia |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Projektfájl-módosítások

- Állítsa a `IISExpressSSLPort` tulajdonságot külön számra.
- Állítsa a `WebProject_DirectoryAccessLevelKey` tulajdonságot 0-ra, vagy 1-re, ha a **Címtáradatok olvasása** beállítást választotta.
- Állítsa a `IISUrl` `https://localhost:<port>/` tulajdonságot arra a pontra, ahol `<port>` megegyezik az `IISExpressSSLPort` értékkel.

## <a name="webconfig-or-appconfig-changes"></a>web.config vagy app.config változások

- Hozzáadva a következő konfigurációs bejegyzések:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Hozzáadott `<dependentAssembly>` elemek `<runtime><assemblyBinding>` a csomópont `System.IdentityModel.Tokens.Jwt` `Microsoft.IdentityModel.Protocol.Extensions`alatt és a hoz.

További módosítások, ha a **Címtáradatok olvasása** lehetőséget választotta:

- Hozzáadva a következő `<appSettings>`konfigurációs bejegyzés:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Hozzáadva a `<configuration>`következő elemek a következő elemek közül; a projekt-mdf-fájl és a projekt-katalógus-azonosító értékei eltérőek lehetnek:

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

- A `<dependentAssembly>` csomópont `<runtime><assemblyBinding>` alá hozzáadott `Microsoft.Data.Services.Client` `Microsoft.Data.Edm`elemek `Microsoft.Data.OData`a és a.

## <a name="code-changes-and-additions"></a>Kódmódosítások és -kiegészítések

- Hozzáadva `[Authorize]` az `Controllers/HomeController.cs` attribútumot és minden más meglévő vezérlőhöz.

- Hozzáadott egy hitelesítési `App_Start/Startup.Auth.cs`indítási osztály, , amely tartalmazza az Azure AD-hitelesítés indítási logikáját. Ha a **Címtáradatok olvasása** beállítást választotta, ez a fájl egy OAuth-kód fogadására és hozzáférési jogkivonatra cserélésre szolgáló kódot is tartalmaz.

- Hozzáadott egy `Controllers/AccountController.cs`vezérlőosztályt, `SignIn` `SignOut` a tartalmazó és metódusokat.

- Részleges nézet hozzáadva, `Views/Shared/_LoginPartial.cshtml`amely a `SignIn` program `SignOut`hoz és művelethivatkozást tartalmaz.

- Hozzáadott egy részleges `Views/Account/SignoutCallback.cshtml`nézetet, amely HTML-t tartalmaz a kijelentkezési felhasználói felülethez.

- Frissítve `Startup.Configuration` a metódus, `ConfigureAuth(app)` hogy az tartalmazza a hívást, ha az osztály már létezett; más különben olyan osztályt `Startup` ad hozzá, amely tartalmazza a metódus hívásait.

- Hozzáadva `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) vagy `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), amely olyan információkat tartalmaz, amelyeket a Visual Studio a csatlakoztatott szolgáltatás hozzáadásának nyomon követésére használ.

- Ha a **Címtáradatok olvasása** beállítást választotta, adja hozzá, `Models/ADALTokenCache.cs` és `Models/ApplicationDbContext.cs` támogassa a tokengyorsítótárazást. További vezérlőt és nézetet is hozzáadott a felhasználói profil `Controllers/UserProfileController.cs`adatainak az Azure graph API-k használatával történő elérésének szemléltetésére: , `Views/UserProfile/Index.cshtml`és`Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Fájl biztonsági mentése (Visual Studio 2015)

A csatlakoztatott szolgáltatás hozzáadásakor a Visual Studio 2015 biztonsági másolatot tesz a módosított fájlokról, és eltávolítja azokat. Az összes érintett fájl `Backup/AzureAD`a mappába kerül. A Visual Studio 2017-es és újabb verziói nem hoznak létre biztonsági mentéseket.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Változások az Azure-ban

- Létrehozott egy Azure AD-alkalmazást a csatlakoztatott szolgáltatás hozzáadásakor kiválasztott tartományban.
- Frissítve az alkalmazás, hogy tartalmazza a **Címtáradatok olvasása** engedélyt, ha ez a beállítás be van jelölve.

[További információ az Azure Active Directoryról.](https://azure.microsoft.com/services/active-directory/)

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory hitelesítési forgatókönyvei](authentication-scenarios.md)
- [Bejelentkezés hozzáadása a Microsofttal egy ASP.NET webalkalmazáshoz](quickstart-v2-aspnet-webapp.md)

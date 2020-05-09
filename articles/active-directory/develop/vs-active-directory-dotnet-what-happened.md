---
title: MVC-projekt módosításai az Azure AD-hez való csatlakozáskor
description: Leírja, hogy mi történik az MVC-projekttel, amikor az Azure AD-hoz csatlakozik a Visual Studio csatlakoztatott szolgáltatásaival
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: d42d905bf35c015213e76bc50c4bc339a5c4a062
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886109"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt az MVC-projekttel (Visual Studio Azure Active Directory csatlakoztatott szolgáltatás)?

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-dotnet-getting-started.md)
> - [mi történt](vs-active-directory-dotnet-what-happened.md)

Ez a cikk egy ASP.NET MVC-projekt pontos módosításait azonosítja, amikor a [Azure Active Directory csatlakoztatott szolgáltatást a Visual Studióval](vs-active-directory-add-connected-service.md)adja hozzá.

További információ a csatlakoztatott szolgáltatás használatáról: [első lépések](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Hozzáadott referenciák

Hatással van a projektfájl (*. NET-referenciák `packages.config` ) és a (NuGet-hivatkozások) elemre.

| Típus | Referencia |
| --- | --- |
| NET NuGet | Microsoft. IdentityModel. Protocol. Extensions |
| NET NuGet | Microsoft. Owin |
| NET NuGet | Microsoft.Owin.Host.SystemWeb |
| NET NuGet | Microsoft. Owin. Security |
| NET NuGet | Microsoft.Owin.Security.Cookies |
| NET NuGet | Microsoft.Owin.Security.OpenIdConnect |
| NET NuGet | Owin |
| .NET        | System. IdentityModel |
| NET NuGet | System. IdentityModel. tokens. JWT |
| .NET        | System.Runtime.Serialization |

További hivatkozások ha a címtár- **adatok olvasása** lehetőséget választotta:

| Típus | Referencia |
| --- | --- |
| NET NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (csak Visual Studio 2015) |
| NET NuGet | Microsoft. Azure. ActiveDirectory. GraphClient |
| NET NuGet | Microsoft. EDM |
| NET NuGet | Microsoft. OData |
| NET NuGet | Microsoft. Services. Services. Client |
| NET NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms (csak Visual Studio 2015) |
| NET NuGet | System. térbeli |

A következő hivatkozások törlődnek (csak ASP.NET 4 projekt, a Visual Studio 2015-as verziójában):

| Típus | Referencia |
| --- | --- |
| NET NuGet | Microsoft. AspNet. Identity. Core |
| NET NuGet | Microsoft. AspNet. Identity. EntityFramework |
| NET NuGet | Microsoft. AspNet. Identity. Owin |

## <a name="project-file-changes"></a>Projektfájl módosításai

- Állítsa a tulajdonságot `IISExpressSSLPort` egy külön számra.
- Állítsa a tulajdonságot `WebProject_DirectoryAccessLevelKey` 0 vagy 1 értékre, ha a **Címtáradatok olvasása** lehetőséget választotta.
- Állítsa be a `IISUrl` tulajdonságot `<port>` , hogy `IISExpressSSLPort` `https://localhost:<port>/` a hol egyezzen az értékkel.

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

- A `<dependentAssembly>` `System.IdentityModel.Tokens.Jwt` és `Microsoft.IdentityModel.Protocol.Extensions`a `<runtime><assemblyBinding>` csomópont alatt hozzáadott elemek.

További változtatások a **Címtáradatok olvasása** lehetőség bejelölésével:

- Az alábbi konfigurációs bejegyzést adta hozzá `<appSettings>`a következőhöz:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- A következő elemeket adta hozzá `<configuration>`a alá: a Project-MDF-file és a Project-Catalog-ID értékek a következőnél változhatnak:

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

- Hozzáadott `<dependentAssembly>` elemek a `<runtime><assemblyBinding>` csomópont alatt a `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`és `Microsoft.Data.OData`rendszerhez.

## <a name="code-changes-and-additions"></a>Kód módosításai és kiegészítései

- Az attribútum `[Authorize]` hozzá lett `Controllers/HomeController.cs` adva a és más meglévő vezérlőkhöz.

- Egy hitelesítési indítási osztály `App_Start/Startup.Auth.cs`lett hozzáadva, amely az Azure ad-hitelesítés indítási logikáját tartalmazza. Ha a **Címtáradatok olvasása** lehetőséget választotta, ez a fájl a OAuth-kód fogadására és a hozzáférési tokenek cseréjére szolgáló kódot is tartalmaz.

- Hozzá lett adva egy vezérlő `Controllers/AccountController.cs`osztály, `SignIn` amely `SignOut` tartalmazza a és a metódusokat.

- Egy részleges nézet `Views/Shared/_LoginPartial.cshtml`lett hozzáadva, amely a `SignIn` és `SignOut`a műveleti hivatkozását tartalmazza.

- A rendszer részleges nézetet adott `Views/Account/SignoutCallback.cshtml`hozzá, amely HTML-kódot tartalmaz a kijelentkezési kezelőfelülethez.

- Frissítette `Startup.Configuration` a metódust, hogy tartalmazza `ConfigureAuth(app)` a hívást, hogy az osztály már létezik; egyéb esetben olyan `Startup` osztályt adott hozzá, amely a metódus meghívását is tartalmazza.

- Hozzáadva `Connected Services/AzureAD/ConnectedService.json` (visual Studio 2017) `Service References/Azure AD/ConnectedService.json` vagy (Visual Studio 2015), amely azokat az információkat tartalmazza, amelyeket a Visual Studio használ a csatlakoztatott szolgáltatás hozzáadásának nyomon követésére.

- Ha a **Címtáradatok olvasása** lehetőséget választotta, a rendszer `Models/ADALTokenCache.cs` hozzáadta és `Models/ApplicationDbContext.cs` a jogkivonat-gyorsítótárazást is támogatja. További vezérlőt is hozzáadhat, és megtekintheti a felhasználói profil adatainak az Azure Graph API- `Controllers/UserProfileController.cs`kkal `Views/UserProfile/Index.cshtml`való elérésének szemléltetését:, és`Views/UserProfile/Relogin.cshtml`

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

## <a name="next-steps"></a>További lépések

- [Hitelesítési forgatókönyvek Azure Active Directory](authentication-scenarios.md)
- [Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](quickstart-v2-aspnet-webapp.md)

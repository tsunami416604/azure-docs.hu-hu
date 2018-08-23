---
title: Azure ad-ben való csatlakozáskor a WebAPI-projekt végrehajtott módosítások
description: Leírja, mi történik a WebAPI-projekthez, amikor a Visual Studio segítségével az Azure AD connect
services: active-directory
author: ghogen
manager: douge
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 04732d6541fd6132360d4c235b35979c70772922
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054305"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt a WebAPI-projektemmel (a Visual Studio Azure Active Directory csatlakoztatott szolgáltatás)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-webapi-getting-started.md)
> - [mi történt](vs-active-directory-webapi-what-happened.md)

Ez a cikk azonosítja a pontos módosításait ASP.NET WebAPI egyoldalas alkalmazást az ASP.NET és ASP.NET az Azure API-projektek hozzáadásakor a [Azure Active Directory csatlakoztatott szolgáltatás Visual studióval](vs-active-directory-add-connected-service.md). Az ASP.NET az Azure Mobile Service projekteket a Visual Studio 2015 is vonatkozik.

A csatlakoztatott szolgáltatás való használatáról információkért lásd: [bevezetés](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>A hozzáadott referenciák

A projekt fájlhivatkozásokat *.NET befolyásolja) és `packages.config` (NuGet-hivatkozás).

| Típus | Leírások |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(A visual Studio 2015-ös csak) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Csak a Visual Studio 2017: is hozzá a következő bejegyzés alatt `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Hozzáadott `<dependentAssembly>` elemek alatt a `<runtime><assemblyBinding>` csomópont `System.IdentityModel.Tokens.Jwt`.

- Ha bejelölte a **címtáradatok olvasása** beállítást, a következő konfigurációs bejegyzés alatt hozzáadott `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Kód módosításairól és kínálatáról

- Hozzáadja a `[Authorize]` attribútumot `Controllers/ValueController.cs` és más, meglévő tartományvezérlőkön.

- Hozzáadva egy hitelesítést indítási osztályt `App_Start/Startup.Auth.cs`, az Azure AD-hitelesítés ügyfélindítási logikája tartalmazó vagy módosítani azt. Ha bejelölte a **címtáradatok olvasása** beállítást, ez a fájl is tartalmazza kódot, hogy megkapja az OAuth-kódot, és azt exchange-hozzáférési jogkivonatot.

- (A visual Studio 2015-höz, az ASP.NET 4 csak alkalmazás) Eltávolított `App_Start/IdentityConfig.cs` , és hozzá `Controllers/AccountController.cs`, `Models/IdentityModel.cs`, és `Providers/ApplicationAuthProvider.cs`.

- Hozzáadott `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) vagy `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), amely információkat tartalmaz, amely a Visual Studio segítségével nyomon követheti a csatlakoztatott szolgáltatás.

### <a name="file-backup-visual-studio-2015"></a>Biztonsági mentés (Visual Studio 2015)

A csatlakoztatott szolgáltatás hozzáadása, ha Visual Studio 2015 eltávolított és a módosított fájlok mentését. A mappa összes érintett fájlok lesznek mentve `Backup/AzureAD`. A Visual Studio 2017 nem hoz létre a biztonsági mentéseket.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Változások az Azure-ban

- Létrehozott egy Azure AD-alkalmazást a tartományban, amikor a csatlakoztatott szolgáltatás hozzáadása a kiválasztott.
- Az alkalmazás frissítése a **címtáradatok olvasása** engedélyt, ha ezt a lehetőséget választotta.

[További információ az Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>További lépések

- [Hitelesítési forgatókönyvek az Azure Active Directory](authentication-scenarios.md)
- [Jelentkezzen be a Microsoft ASP.NET-webalkalmazás hozzáadása](quickstart-v1-aspnet-webapp.md)
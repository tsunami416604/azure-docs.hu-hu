---
title: Változások a WebAPI projekt csatlakoztatása az Azure AD
description: Ismerteti, mi történik a WebAPI-projektet a Visual Studio segítségével az Azure AD connect
services: active-directory
author: ghogen
manager: douge
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: b8303ccf6fc96e8d0708c9e414fdf511e0cf2fdf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31785161"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt a WebAPI project (a Visual Studio Azure Active Directory szolgáltatás csatlakozik)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-webapi-getting-started.md)
> - [mi történt](vs-active-directory-webapi-what-happened.md)

Ez a cikk azonosítja a pontos módosításainak ASP.NET WebAPI, ASP.NET egyoldalas alkalmazások és ASP.NET Azure API-projektek hozzáadásakor a [Azure Active Directory szolgáltatást a Visual Studio használatával csatlakoztatott](vs-active-directory-add-connected-service.md). Az ASP.NET Azure Mobile Service projektek a Visual Studio 2015 is vonatkozik.

A csatlakoztatott szolgáltatás munkavégzés további információkért lásd: [bevezetés](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>A hozzáadott hivatkozások

A projekt fájlhivatkozások *.NET befolyásolja) és `packages.config` (NuGet-hivatkozás).

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
| .NET        | EntityFramework.SqlServer (Visual Studio 2015-öt csak) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(A visual Studio 2015 csak) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Csak a Visual Studio 2017: is hozzá a következő bejegyzés alatt `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Hozzáadott `<dependentAssembly>` elemek alatt a `<runtime><assemblyBinding>` csomópont `System.IdentityModel.Tokens.Jwt`.

- Ha bejelölte a **címtáradatok olvasása** lehetőségre, a következő konfigurációs bejegyzés alatt hozzáadott `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Kód módosításait és kiegészítéseit

- Hozzáadva a `[Authorize]` attribútumot `Controllers/ValueController.cs` és más meglévő vezérlők.

- Egy hitelesítési indítási osztály hozzáadott `App_Start/Startup.Auth.cs`, az Azure AD-alapú hitelesítés ügyfélindítási logikája tartalmazó vagy módosítani azt. Ha bejelölte a **címtáradatok olvasása** beállítást, a fájl is tartalmaz kódot, hogy megkapja az OAuth-kódot, és az exchange-hozzáférési tokent.

- (A visual Studio 2015 ASP.NET 4 csak az alkalmazás) Eltávolított `App_Start/IdentityConfig.cs` és hozzáadott `Controllers/AccountController.cs`, `Models/IdentityModel.cs`, és `Providers/ApplicationAuthProvider.cs`.

- Hozzáadott `Connected Services/AzureAD/ConnectedService.json` (a Visual Studio 2017) vagy `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), a Visual Studio segítségével nyomon követheti a csatlakoztatott szolgáltatás hozzáadása tartalmazó adatokat.

### <a name="file-backup-visual-studio-2015"></a>A fájlok biztonsági mentése (a Visual Studio 2015)

A csatlakoztatott szolgáltatás hozzáadásakor Visual Studio 2015 fájlokról megváltozott és eltávolítása. A mappa minden érintett fájlok lesznek mentve `Backup/AzureAD`. A Visual Studio 2017 nem hoz létre a biztonsági mentéseket.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Változások az Azure-on

- Amikor a csatlakoztatott szolgáltatás hozzáadása a kiválasztott tartományban létre egy Azure AD-alkalmazást.
- Frissítve az alkalmazásnak, hogy tartalmazzák a **címtáradatok olvasása** engedélyt, ha ezt a beállítást választotta.

[További tudnivalók az Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory hitelesítési forgatókönyvei](active-directory-authentication-scenarios.md)
- [Bejelentkezés a Microsoft ASP.NET webalkalmazás hozzáadása](guidedsetups/active-directory-aspnetwebapp-v1.md)
---
title: A WebAPI-projekteken végrehajtott módosítások, amikor az Azure AD-hez csatlakoznak
description: A webAPI-projekttel kapcsolatos tudnivalók, amikor a Visual Studio használatával csatlakozik az Azure AD-hez
author: ghogen
manager: jillfra
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 595a89ca58e970a9c886d0b6c2dd05aecd1411ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159386"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt a WebAPI-projektemmel (Visual Studio Azure Active Directoryhoz csatlakoztatott szolgáltatás)

> [!div class="op_single_selector"]
> - [Bevezetés](vs-active-directory-webapi-getting-started.md)
> - [mi történt](vs-active-directory-webapi-what-happened.md)

Ez a cikk azonosítja a webAPI- ASP.NET és ASP.NET egyoldalas alkalmazáson és ASP.NET Azure API-projekteken végrehajtott pontos módosításokat az [Azure Active Directoryhoz csatlakoztatott szolgáltatás Visual Studio használatával történő hozzáadásakor.](vs-active-directory-add-connected-service.md) A Visual Studio 2015-ben az ASP.NET Azure Mobile Service-projektekre is vonatkozik.

A csatlakoztatott szolgáltatás sal kapcsolatos tudnivalókat az [Első lépések című témakörben talál.](vs-active-directory-webapi-getting-started.md)

## <a name="added-references"></a>Hozzáadott hivatkozások

Hatással van a projektfájl *.NET hivatkozásaira) és `packages.config` (NuGet hivatkozások).

| Típus | Referencia |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Biztonság |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin között |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Csak Visual Studio 2015 esetén) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Csak a Visual Studio 2017-ben: A következő bejegyzést is hozzáadta `<appSettings>`a "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Hozzáadott `<dependentAssembly>` elemek `<runtime><assemblyBinding>` a csomópont `System.IdentityModel.Tokens.Jwt`alatt.

- Ha a **Címtáradatok olvasása** lehetőséget választotta, `<appSettings>`a következő konfigurációs bejegyzést adta hozzá a következő csoportba:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Kódmódosítások és -kiegészítések

- Hozzáadva `[Authorize]` az `Controllers/ValueController.cs` attribútumot és minden más meglévő vezérlőhöz.

- Hozzáadott egy hitelesítési `App_Start/Startup.Auth.cs`indítási osztály, amely tartalmazza az Azure AD-hitelesítés indítási logikáját, vagy ennek megfelelően módosította. Ha a **Címtáradatok olvasása** beállítást választotta, ez a fájl egy OAuth-kód fogadására és hozzáférési jogkivonatra cserélésre szolgáló kódot is tartalmaz.

- (Visual Studio 2015 csak ASP.NET 4 alkalmazással) Eltávolítva `App_Start/IdentityConfig.cs` `Controllers/AccountController.cs`és `Models/IdentityModel.cs`hozzáadva , és `Providers/ApplicationAuthProvider.cs`.

- Hozzáadva `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) vagy `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), amely olyan információkat tartalmaz, amelyeket a Visual Studio a csatlakoztatott szolgáltatás hozzáadásának nyomon követésére használ.

### <a name="file-backup-visual-studio-2015"></a>Fájl biztonsági mentése (Visual Studio 2015)

A csatlakoztatott szolgáltatás hozzáadásakor a Visual Studio 2015 biztonsági másolatot tesz a módosított fájlokról, és eltávolítja azokat. Az összes érintett fájl `Backup/AzureAD`a mappába kerül. A Visual Studio 2017 nem hoz létre biztonsági mentéseket.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Változások az Azure-ban

- Létrehozott egy Azure AD-alkalmazást a csatlakoztatott szolgáltatás hozzáadásakor kiválasztott tartományban.
- Frissítve az alkalmazás, hogy tartalmazza a **Címtáradatok olvasása** engedélyt, ha ez a beállítás be van jelölve.

[További információ az Azure Active Directoryról.](https://azure.microsoft.com/services/active-directory/)

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory hitelesítési forgatókönyvei](authentication-scenarios.md)
- [Bejelentkezés hozzáadása a Microsofttal egy ASP.NET webalkalmazáshoz](quickstart-v2-aspnet-webapp.md)

---
title: WebAPI-projekt módosításai az Azure AD-hez való csatlakozáskor
description: Leírja, hogy mi történik a WebAPI-projekttel, amikor az Azure AD-hez csatlakozik a Visual Studióval
services: active-directory
author: ghogen
manager: douge
ms.service: active-directory
ms.subservice: develop
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19a36ca4b194d8031b4a263a092ecb52be74cdd0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324277"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt a WebAPI-projekttel (Visual Studio Azure Active Directory csatlakoztatott szolgáltatás)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-webapi-getting-started.md)
> - [mi történt](vs-active-directory-webapi-what-happened.md)

Ez a cikk a ASP.NET WebAPI, a ASP.NET egyoldalas alkalmazás és a ASP.NET Azure API-projektek pontos módosításait mutatja be, amikor a [Visual Studio használatával hozzáadja a Azure Active Directory csatlakoztatott szolgáltatást](vs-active-directory-add-connected-service.md). A Visual Studio 2015 ASP.NET Azure Mobile Service-projektjeire is vonatkozik.

További információ a csatlakoztatott szolgáltatás használatáról: [első lépések](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Hozzáadott referenciák

Hatással van a (*. net-referenciák `packages.config` ) projektfájl és a (NuGet-hivatkozások) fájlra.

| Type | Hivatkozás |
| --- | --- |
| NET NuGet | Microsoft.Owin |
| NET NuGet | Microsoft.Owin.Host.SystemWeb |
| NET NuGet | Microsoft.Owin.Security |
| NET NuGet | Microsoft.Owin.Security.ActiveDirectory |
| NET NuGet | Microsoft.Owin.Security.Jwt |
| NET NuGet | Microsoft.Owin.Security.OAuth |
| NET NuGet | Owin |
| NET NuGet | System.IdentityModel.Tokens.Jwt |

További hivatkozások ha a címtár- **adatok olvasása** lehetőséget választotta:

| Type | Hivatkozás |
| --- | --- |
| NET NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (csak Visual Studio 2015) |
| NET NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| NET NuGet | Microsoft.Data.Edm |
| NET NuGet | Microsoft.Data.OData |
| NET NuGet | Microsoft.Data.Services.Client |
| NET NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Csak Visual Studio 2015) |
| NET NuGet | System.Spatial |

A következő hivatkozások törlődnek (csak ASP.NET 4 projekt, a Visual Studio 2015-as verziójában):

| Type | Hivatkozás |
| --- | --- |
| NET NuGet | Microsoft.AspNet.Identity.Core |
| NET NuGet | Microsoft.AspNet.Identity.EntityFramework |
| NET NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Projektfájl módosításai

- Állítsa a tulajdonságot `IISExpressSSLPort` egy külön számra.
- Állítsa a tulajdonságot `WebProject_DirectoryAccessLevelKey` 0 vagy 1 értékre, ha a **Címtáradatok olvasása** lehetőséget választotta.
- Állítsa be a `IISUrl` tulajdonságot `<port>` , hogy `IISExpressSSLPort` `https://localhost:<port>/` a hol egyezzen az értékkel.

## <a name="webconfig-or-appconfig-changes"></a>a web. config vagy az app. config módosítása

- A következő konfigurációs bejegyzések lettek hozzáadva:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Csak Visual Studio 2017 esetén: A következő bejegyzést `<appSettings>`is hozzáadta a "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Hozzáadott `<dependentAssembly>` elemek a `<runtime><assemblyBinding>` csomópont alatt a `System.IdentityModel.Tokens.Jwt`következőhöz:.

- Ha a címtárbeli **adat olvasása** lehetőséget választotta, a következő konfigurációs bejegyzést `<appSettings>`adta hozzá a következőhöz:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Kód módosításai és kiegészítései

- `[Authorize]` Az`Controllers/ValueController.cs` attribútum hozzá lett adva a és más meglévő vezérlőkhöz.

- Egy hitelesítési indítási osztályt `App_Start/Startup.Auth.cs`adott hozzá, amely az Azure ad-hitelesítés indítási logikáját tartalmazza, vagy ennek megfelelően módosította. Ha a **Címtáradatok olvasása** lehetőséget választotta, ez a fájl a OAuth-kód fogadására és a hozzáférési tokenek cseréjére szolgáló kódot is tartalmaz.

- (Visual Studio 2015 csak ASP.NET 4 alkalmazással) Eltávolítva `App_Start/IdentityConfig.cs` és `Controllers/AccountController.cs` `Providers/ApplicationAuthProvider.cs`hozzáadva:, és. `Models/IdentityModel.cs`

- Hozzáadva `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) `Service References/Azure AD/ConnectedService.json` vagy (Visual Studio 2015), amely azokat az információkat tartalmazza, amelyeket a Visual Studio használ a csatlakoztatott szolgáltatás hozzáadásának nyomon követésére.

### <a name="file-backup-visual-studio-2015"></a>Fájl biztonsági mentése (Visual Studio 2015)

A csatlakoztatott szolgáltatás hozzáadásakor a Visual Studio 2015 biztonsági mentést készít a módosított és eltávolított fájlokról. A rendszer az összes érintett fájlt menti a `Backup/AzureAD`mappába. A Visual Studio 2017 nem hoz létre biztonsági másolatokat.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Változások az Azure-ban

- Létrehozott egy Azure AD-alkalmazást abban a tartományban, amelyet a csatlakoztatott szolgáltatás hozzáadásakor kiválasztott.
- Frissítette az alkalmazást, hogy tartalmazza a **címtár** -adatolvasási engedélyt, ha ez a beállítás be van jelölve.

[További információ a Azure Active Directoryról](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>További lépések

- [Hitelesítési forgatókönyvek Azure Active Directory](authentication-scenarios.md)
- [Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](quickstart-v1-aspnet-webapp.md)

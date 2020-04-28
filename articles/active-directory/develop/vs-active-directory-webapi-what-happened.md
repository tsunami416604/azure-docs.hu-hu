---
title: WebAPI-projektek módosításai az Azure AD-hez való csatlakozáskor
description: Leírja, hogy mi történik a WebAPI-projekttel, amikor az Azure AD-hez csatlakozik a Visual Studióval
author: ghogen
manager: jillfra
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 46fb0ad37b872a1d7ca72114f2f263df776aabf1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886058"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt a WebAPI-projekttel (Visual Studio Azure Active Directory csatlakoztatott szolgáltatás)

> [!div class="op_single_selector"]
> - [Bevezetés](vs-active-directory-webapi-getting-started.md)
> - [mi történt](vs-active-directory-webapi-what-happened.md)

Ez a cikk a ASP.NET WebAPI, a ASP.NET egyoldalas alkalmazás és a ASP.NET Azure API-projektek pontos módosításait mutatja be, amikor a [Visual Studio használatával hozzáadja a Azure Active Directory csatlakoztatott szolgáltatást](vs-active-directory-add-connected-service.md). A Visual Studio 2015 ASP.NET Azure Mobile Service-projektjeire is vonatkozik.

További információ a csatlakoztatott szolgáltatás használatáról: [első lépések](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Hozzáadott referenciák

Hatással van a (*. NET-referenciák `packages.config` ) projektfájl és a (NuGet-hivatkozások) fájlra.

| Típus | Referencia |
| --- | --- |
| NET NuGet | Microsoft. Owin |
| NET NuGet | Microsoft.Owin.Host.SystemWeb |
| NET NuGet | Microsoft. Owin. Security |
| NET NuGet | Microsoft. Owin. Security. ActiveDirectory |
| NET NuGet | Microsoft. Owin. Security. JWT |
| NET NuGet | Microsoft. Owin. Security. OAuth |
| NET NuGet | Owin |
| NET NuGet | System. IdentityModel. tokens. JWT |

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
| .NET        | Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms<br>(Csak Visual Studio 2015) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017 Only: a következő bejegyzést is hozzáadta `<appSettings>`a "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Hozzáadott `<dependentAssembly>` elemek a `<runtime><assemblyBinding>` csomópont alatt a `System.IdentityModel.Tokens.Jwt`következőhöz:.

- Ha a **címtárbeli adat olvasása** lehetőséget választotta, a következő konfigurációs bejegyzést adta `<appSettings>`hozzá a következőhöz:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Kód módosításai és kiegészítései

- Az attribútum `[Authorize]` hozzá lett `Controllers/ValueController.cs` adva a és más meglévő vezérlőkhöz.

- Egy hitelesítési indítási osztályt `App_Start/Startup.Auth.cs`adott hozzá, amely az Azure ad-hitelesítés indítási logikáját tartalmazza, vagy ennek megfelelően módosította. Ha a **Címtáradatok olvasása** lehetőséget választotta, ez a fájl a OAuth-kód fogadására és a hozzáférési tokenek cseréjére szolgáló kódot is tartalmaz.

- (Visual Studio 2015 csak ASP.NET 4 alkalmazással) Eltávolítva `App_Start/IdentityConfig.cs` és `Controllers/AccountController.cs`hozzáadva `Models/IdentityModel.cs`: `Providers/ApplicationAuthProvider.cs`, és.

- Hozzáadva `Connected Services/AzureAD/ConnectedService.json` (visual Studio 2017) `Service References/Azure AD/ConnectedService.json` vagy (Visual Studio 2015), amely azokat az információkat tartalmazza, amelyeket a Visual Studio használ a csatlakoztatott szolgáltatás hozzáadásának nyomon követésére.

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
- Frissítette az alkalmazást, hogy tartalmazza a **címtár-adatolvasási** engedélyt, ha ez a beállítás be van jelölve.

[További információ a Azure Active Directoryról](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>További lépések

- [Hitelesítési forgatókönyvek Azure Active Directory](authentication-scenarios.md)
- [Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](quickstart-v2-aspnet-webapp.md)

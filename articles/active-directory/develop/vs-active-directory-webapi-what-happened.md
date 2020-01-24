---
title: WebAPI-projektek módosításai az Azure AD-hez való csatlakozáskor
description: Leírja, hogy mi történik a WebAPI-projekttel, amikor az Azure AD-hez csatlakozik a Visual Studióval
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
ms.openlocfilehash: 9858fdb7da80fe03498392ea93bf3c83b8a7e4dd
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699869"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt a WebAPI-projekttel (Visual Studio Azure Active Directory csatlakoztatott szolgáltatás)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-webapi-getting-started.md)
> - [mi történt](vs-active-directory-webapi-what-happened.md)

Ez a cikk a ASP.NET WebAPI, a ASP.NET egyoldalas alkalmazás és a ASP.NET Azure API-projektek pontos módosításait mutatja be, amikor a [Visual Studio használatával hozzáadja a Azure Active Directory csatlakoztatott szolgáltatást](vs-active-directory-add-connected-service.md). A Visual Studio 2015 ASP.NET Azure Mobile Service-projektjeire is vonatkozik.

További információ a csatlakoztatott szolgáltatás használatáról: [első lépések](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Hozzáadott referenciák

Hatással van a projektfájl *. NET referenciái) és a `packages.config`ra (NuGet-referenciák).

| Type (Típus) | Leírások |
| --- | --- |
| NET NuGet | Microsoft. Owin |
| NET NuGet | Microsoft. Owin. host. SystemWeb |
| NET NuGet | Microsoft. Owin. Security |
| NET NuGet | Microsoft. Owin. Security. ActiveDirectory |
| NET NuGet | Microsoft. Owin. Security. JWT |
| NET NuGet | Microsoft. Owin. Security. OAuth |
| NET NuGet | Owin |
| NET NuGet | System. IdentityModel. tokens. JWT |

További hivatkozások ha a címtár- **adatok olvasása** lehetőséget választotta:

| Type (Típus) | Leírások |
| --- | --- |
| NET NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (csak Visual Studio 2015) |
| NET NuGet | Microsoft. Azure. ActiveDirectory. GraphClient |
| NET NuGet | Microsoft. EDM |
| NET NuGet | Microsoft. OData |
| NET NuGet | Microsoft. Services. Services. Client |
| NET NuGet | Microsoft. IdentityModel. clients. ActiveDirectory |
| .NET        | Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms<br>(Csak Visual Studio 2015) |
| NET NuGet | System. térbeli |

A következő hivatkozások törlődnek (csak ASP.NET 4 projekt, a Visual Studio 2015-as verziójában):

| Type (Típus) | Leírások |
| --- | --- |
| NET NuGet | Microsoft. AspNet. Identity. Core |
| NET NuGet | Microsoft. AspNet. Identity. EntityFramework |
| NET NuGet | Microsoft. AspNet. Identity. Owin |

## <a name="project-file-changes"></a>Projektfájl módosításai

- Állítsa a tulajdonságot `IISExpressSSLPort` egy külön számra.
- Állítsa a tulajdonságot a 0 értékre `WebProject_DirectoryAccessLevelKey`, vagy 1 értéket, ha a **Címtáradatok olvasása** lehetőséget választotta.
- Állítsa a `IISUrl` tulajdonságot `https://localhost:<port>/`, ahol a `<port>` megfelel a `IISExpressSSLPort` értéknek.

## <a name="webconfig-or-appconfig-changes"></a>a web. config vagy az app. config módosítása

- A következő konfigurációs bejegyzések lettek hozzáadva:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017 Only: a következő bejegyzést is hozzáadta `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- A `System.IdentityModel.Tokens.Jwt``<runtime><assemblyBinding>` csomópontjában `<dependentAssembly>` elemek lettek hozzáadva.

- Ha a **Címtáradatok olvasása** lehetőséget választotta, a következő konfigurációs bejegyzést adta hozzá a `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Kód módosításai és kiegészítései

- A `[Authorize]` attribútum hozzáadva a `Controllers/ValueController.cs`hoz és bármely más meglévő vezérlőhöz.

- Hozzáadott egy hitelesítési indítási osztályt, `App_Start/Startup.Auth.cs`, amely az Azure AD-hitelesítés indítási logikáját tartalmazza, vagy ennek megfelelően módosította azt. Ha a **Címtáradatok olvasása** lehetőséget választotta, ez a fájl a OAuth-kód fogadására és a hozzáférési tokenek cseréjére szolgáló kódot is tartalmaz.

- (Visual Studio 2015 csak ASP.NET 4 alkalmazással) Eltávolítva `App_Start/IdentityConfig.cs`, `Controllers/AccountController.cs`, `Models/IdentityModel.cs`és `Providers/ApplicationAuthProvider.cs`lett hozzáadva.

- Hozzáadott `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) vagy `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), amely azokat az információkat tartalmazza, amelyeket a Visual Studio használ a csatlakoztatott szolgáltatás hozzáadásának nyomon követésére.

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

## <a name="next-steps"></a>Következő lépések

- [Hitelesítési forgatókönyvek Azure Active Directory](authentication-scenarios.md)
- [Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](quickstart-v1-aspnet-webapp.md)

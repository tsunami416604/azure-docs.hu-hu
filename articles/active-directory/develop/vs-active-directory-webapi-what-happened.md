---
title: "Változások a WebApi projekt csatlakoztatása az Azure AD |} Microsoft Docs"
description: "Ismerteti, mi történik a WebApi-projektet a Visual Studio segítségével az Azure AD connect"
services: active-directory
documentationcenter: 
author: kraigb
manager: mtillman
editor: 
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 8a0f6e1838bcc550829c0da92dc224e1df859e9c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt a WebApi project (a Visual Studio Azure Active Directory szolgáltatás csatlakozik)
> [!div class="op_single_selector"]
> * [Első lépések](vs-active-directory-webapi-getting-started.md)
> * [mi történt](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Hivatkozások vannak adva.
### <a name="nuget-package-references"></a>NuGet-csomag hivatkozások
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

### <a name="net-references"></a>.NET-hivatkozások
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

## <a name="code-changes"></a>Kódmódosítások
### <a name="code-files-were-added-to-your-project"></a>Kód hozzáadott a projekthez
Egy hitelesítési indítási osztály **App_Start/Startup.Auth.cs** lett hozzáadva a projekthez az Azure AD-alapú hitelesítés ügyfélindítási logikája tartalmazó.

### <a name="startup-code-was-added-to-your-project"></a>Indítási kóddal a projekthez
Ha a projektben már volt indítási osztály a **konfigurációs** metódus hívása lett frissítve `ConfigureAuth(app)`. Ellenkező esetben egy indítási osztályt a projekthez lett adva.

### <a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>Az App.config fájlt vagy a web.config fájl új konfigurációs értékeket tartalmaz.
A következő konfigurációs bejegyzés hozzáadva.

```
    <appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

### <a name="an-azure-ad-app-was-created"></a>Az Azure AD-alkalmazás létrehozása
Az Azure AD-alkalmazások létrehozásának, a varázslóban megadott könyvtárban található.

[További tudnivalók az Azure Active Directoryban](https://azure.microsoft.com/services/active-directory/)

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Ha ellenőrizni szeretnék *tiltsa le az egyes felhasználói fiókok hitelesítési*, milyen további módosítások történtek a projektben?
Hivatkozások a NuGet csomag eltávolítása, és fájlok lettek eltávolítva, a biztonsági mentése. A projekt állapotától függően előfordulhat, hogy manuálisan távolítsa el a további hivatkozások vagy fájlokat, vagy szükség szerint kód módosítása.

### <a name="nuget-package-references-removed-for-those-present"></a>NuGet csomag hivatkozást eltávolítani (a jelen)
* `Microsoft.AspNet.Identity.Core`
* `Microsoft.AspNet.Identity.EntityFramework`
* `Microsoft.AspNet.Identity.Owin`

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Fájlok biztonsági mentése és eltávolítani (a jelen)
Alábbi fájlok biztonsági mentése és a projekt távolítva. A biztonságimásolat-fájlok a "Biztonsági mentés" mappával a gyökérkönyvtárban, a projekt könyvtárában találhatók.

* `App_Start\IdentityConfig.cs`
* `Controllers\AccountController.cs`
* `Controllers\ManageController.cs`
* `Models\IdentityModels.cs`
* `Providers\ApplicationOAuthProvider.cs`

### <a name="code-files-backed-up-for-those-present"></a>Kód fájlok biztonsági mentése (a jelen)
Alábbi fájlok készült biztonsági másolat cseréje előtt. A biztonságimásolat-fájlok a "Biztonsági mentés" mappával a gyökérkönyvtárban, a projekt könyvtárában találhatók.

* `Startup.cs`
* `App_Start\Startup.Auth.cs`

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Ha ellenőrizni szeretnék *címtáradatok olvasása*, milyen további módosítások történtek a projektben?
### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>További módosítások történtek az App.config fájlt vagy a Web.config fájlban
A következő további konfigurációs bejegyzés hozzáadva.

```
    <appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

### <a name="your-azure-active-directory-app-was-updated"></a>Az Azure Active Directory-alkalmazás frissült
Az Azure Active Directory-alkalmazás lett frissítve a *címtáradatok olvasása* engedéllyel és kulcsot hozták létre, amely majd használata a *ida: jelszó* a a `web.config` fájlt.

## <a name="next-steps"></a>Következő lépések
- [További tudnivalók az Azure Active Directoryban](https://azure.microsoft.com/services/active-directory/)


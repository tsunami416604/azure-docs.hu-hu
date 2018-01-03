---
title: "Változások a MVC projekt csatlakoztatása az Azure AD |} Microsoft Docs"
description: "Ismerteti, mi történik a MVC projekt, amikor csatlakozik a Visual Studio szolgáltatásainak a használatával az Azure AD connect"
services: active-directory
documentationcenter: na
author: kraigb
manager: mtillman
editor: 
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: eccff00847968b4293b6e7142af0cceff0476c46
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt a MVC projekt (a Visual Studio Azure Active Directory szolgáltatás csatlakozik)?
> [!div class="op_single_selector"]
> * [Első lépések](vs-active-directory-dotnet-getting-started.md)
> * [mi történt](vs-active-directory-dotnet-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Hivatkozások vannak adva.
### <a name="nuget-package-references"></a>NuGet-csomag hivatkozások
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>.NET-hivatkozások
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel**
* **System.IdentityModel.Tokens.Jwt**
* **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>Kód hozzá lett adva.
### <a name="code-files-were-added-to-your-project"></a>Kód hozzáadott a projekthez
Egy hitelesítési indítási osztály **App_Start/Startup.Auth.cs** lett hozzáadva a projekthez az Azure AD-alapú hitelesítés ügyfélindítási logikája tartalmazó. Emellett vezérlőosztály, Controllers/AccountController.cs hozzá lett adva tartalmazó **SignIn()** és **SignOut()** módszerek. Végezetül részleges nézet **Views/Shared/_LoginPartial.cshtml** hozzá lett adva egy művelet hivatkozására tartalmazó SignIn/SignOut.

### <a name="startup-code-was-added-to-your-project"></a>Indítási kóddal a projekthez
Ha a projektben már volt indítási osztály a **konfigurációs** metódus hívása lett frissítve **ConfigureAuth(app)**. Ellenkező esetben egy indítási osztályt a projekthez lett adva.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>Új konfigurációs értéket tartalmaz a az App.config fájlt vagy a Web.config fájlban
A következő konfigurációs bejegyzés hozzáadva.

    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>Egy Azure Active Directory (AD) alkalmazás létrehozása
Az Azure AD-alkalmazások létrehozásának, a varázslóban megadott könyvtárban található.

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Ha ellenőrizni szeretnék *tiltsa le az egyes felhasználói fiókok hitelesítési*, milyen további módosítások történtek a projektben?
Hivatkozások a NuGet csomag eltávolítása, és fájlok lettek eltávolítva, a biztonsági mentése. A projekt állapotától függően előfordulhat, hogy manuálisan távolítsa el a további hivatkozások vagy fájlokat, vagy szükség szerint kód módosítása.

### <a name="nuget-package-references-removed-for-those-present"></a>NuGet csomag hivatkozást eltávolítani (a jelen)
* **Microsoft.AspNet.Identity.Core**
* **Microsoft.AspNet.Identity.EntityFramework**
* **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Fájlok biztonsági mentése és eltávolítani (a jelen)
Alábbi fájlok biztonsági mentése és a projekt távolítva. A biztonságimásolat-fájlok a "Biztonsági mentés" mappával a gyökérkönyvtárban, a projekt könyvtárában találhatók.

* **App_Start\IdentityConfig.cs**
* **Controllers\ManageController.cs**
* **Models\IdentityModels.cs**
* **Models\ManageViewModels.cs**

### <a name="code-files-backed-up-for-those-present"></a>Kód fájlok biztonsági mentése (a jelen)
Alábbi fájlok készült biztonsági másolat cseréje előtt. A biztonságimásolat-fájlok a "Biztonsági mentés" mappával a gyökérkönyvtárban, a projekt könyvtárában találhatók.

* **Startup.cs**
* **App_Start\Startup.auth.cs**
* **Controllers\AccountController.cs**
* **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Ha ellenőrizni szeretnék *címtáradatok olvasása*, milyen további módosítások történtek a projektben?
További hivatkozások kerültek.

### <a name="additional-nuget-package-references"></a>NuGet-csomag további hivatkozások
* **EntityFramework**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **System.Spatial**

### <a name="additional-net-references"></a>.NET kapcsolódó témakörök
* **EntityFramework**
* **EntityFramework.SqlServer**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
* **System.Spatial**

### <a name="additional-code-files-were-added-to-your-project"></a>További kód hozzáadott a projekthez
Két hozzáadott támogatja a token-gyorsítótárazási: **Models\ADALTokenCache.cs** és **Models\ApplicationDbContext.cs**.  Egy további tartományvezérlő, és tekintse meg a rendszer adott elérése során felhasználói profillal kapcsolatos információk az Azure graph API-k segítségével mutatja be.  Ezek a fájlok **Controllers\UserProfileController.cs** és **Views\UserProfile\Index.cshtml**.

### <a name="additional-startup-code-was-added-to-your-project"></a>További indítási kóddal a projekthez
Az a **startup.auth.cs** fájlba, egy új **OpenIdConnectAuthenticationNotifications** objektumot hozzáadni az **értesítések** tagja a  **OpenIdConnectAuthenticationOptions**.  Ez egy, az OAuth-kód fogadása és cseréjét azt a hozzáférési token engedélyezése.

### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>További módosítások történtek az App.config fájlt vagy a Web.config fájlban
A következő további konfigurációs bejegyzés hozzáadva.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

A következő konfigurációs szakaszokat és a kapcsolati karakterlánc bővült.

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
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


### <a name="your-azure-active-directory-app-was-updated"></a>Az Azure Active Directory-alkalmazás frissült
Az Azure Active Directory-alkalmazás lett frissítve a *címtáradatok olvasása* engedéllyel és kulcsot hozták létre, amely majd használata a *ida: ClientSecret* a a  **Web.config** fájlt.

## <a name="next-steps"></a>További lépések
- [További tudnivalók az Azure Active Directoryban](https://azure.microsoft.com/services/active-directory/)


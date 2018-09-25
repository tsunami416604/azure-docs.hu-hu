---
title: Az Azure Active Directory 2.0-s verziójú hitelesítési kódtárai |} A Microsoft Docs
description: Kompatibilis klienskódtárak és a kiszolgáló közbenső tárak, és a kapcsolódó erőforrástárba, forrás és minták mutató hivatkozásokat, az Azure Active Directory v2.0-végpont.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2018
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 220627e6903abb643cde63cbc9b85402360ce3f3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999766"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Az Azure Active Directory 2.0-s verziójú hitelesítési kódtárai

A [Azure Active Directory (Azure AD) v2.0-végpont](active-directory-v2-compare.md) a szabványos OAuth 2.0 és OpenID Connect 1.0 protokoll használatát támogatja. A Microsoft-hitelesítési tár (MSAL) úgy tervezték, hogy az Azure AD v2.0-végpont használata. Akkor is, amely támogatja az OAuth 2.0 és OpenID Connect 1.0 nyílt forráskódú könyvtáraink használatára.

Javasoljuk, hogy használja-e hajtsa végre a biztonságos fejlesztési Életciklussal (SDL) módszer, például protokoll tartomány szakértők által összeállított kódtárak [a Microsoft követ][Microsoft-SDL]. Ha úgy dönt, hogy az aktuális-kódot az protokollok, mint a Microsoft SDL és használatalapú zárja be a biztonsági szempontok a szabványok specifikációk minden protokollhoz a figyelmet a kövesse módszer.

> [!NOTE]
> Az Azure ad-ben 1.0-s verziójú library (ADAL) keres? Kivétel a [ADAL könyvtár az útmutató](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Szalagtárak típusai

Az Azure AD v2.0-végpont működik együtt könyvtárak két típusa:

* **Ügyfélkódtárak**: natív ügyfelek és kiszolgálók használatával klienskódtárak lekérése egy erőforráshoz, például a Microsoft Graph hívásakor vonatkozó hozzáférési jogkivonatait.
* **Server közbenső könyvtárak**: Web apps server közbenső kódtárak használata a felhasználói bejelentkezés. Webes API-k server közbenső könyvtárak használatával natív ügyfelek vagy más kiszolgálók által küldött jogkivonatainak érvényesítéséhez.

## <a name="library-support"></a>Klienskódtár-támogatásával

Szalagtárak érkeznek két támogatási kategóriákban:

* **A Microsoft által támogatott**: Microsoft javításokat nyújt ezek a kódtárak és SDL végzett szerkezetkialakításra, ezek a könyvtárak a.
* **Kompatibilis**: a Microsoft tesztelte ezek a kódtárak alapszintű forgatókönyvekben és megerősítette, hogy működnek-e a v2.0-végponttal. A Microsoft nem biztosít javítását, ezek a kódtárak és nem végrehajtva ezek a kódtárak áttekintését. Problémák és a funkciókérések legyen átirányítva a tár nyílt forráskódú projekt.

Tekintse meg a következő szakasz ebben a cikkben, amelyek együttműködnek a v2.0-végpont könyvtárak listáját.

## <a name="microsoft-supported-client-libraries"></a>A Microsoft által támogatott ügyfélkódtárak

Védett webes API hívása egy token beszerzéséhez használt ügyfél-hitelesítési tárak

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások | Fogalmi doc | Ütemterv |
| --- | --- | --- | --- | --- | --- | --- | ---| ---|
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js (előzetes verzió) | [NPM](https://www.npmjs.com/package/msal) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  [Egyoldalas alkalmazás](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki)|
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | Az MSAL Angular(Preview) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET-keretrendszer](media/sample-v2-code/logo_NET.png) ![AZ UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | Az MSAL .NET (előzetes verzió) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Asztali alkalmazás](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [Ütemterv](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![iOS / Objective-C vagy SWIFT nyelven](media/sample-v2-code/logo_iOS.png) | Az MSAL obj_c (előzetes verzió) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-alkalmazás](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android / Java](media/sample-v2-code/logo_Android.png) | Az MSAL (előzetes verzió) | [ Központi tárolóhely](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-alkalmazás](quickstart-v2-android.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>A Microsoft által támogatott kiszolgálói közbenső kódtárak

Közbenső kódtárak webalkalmazások és webes API-k védelmére használhatók. Webalkalmazás vagy írt az ASP.NET- vagy ASP.NET Core webes API, a közbenső szoftver kódtárak használják az ASP.NET és ASP.NET Core

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET-biztonság |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[ASP.NET-biztonsági (GitHub)](https://github.com/aspnet/Security) |[MVC-alkalmazás](quickstart-v2-aspnet-webapp.md) |[Az ASP.NET API-referencia](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| A .NET-hez IdentityModel bővítmények| |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC-alkalmazás](quickstart-v2-aspnet-webapp.md) |[Referencia](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Az Azure AD-Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHubon](https://github.com/AzureAD/passport-azure-ad) | [Webalkalmazás](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>Kompatibilis ügyfélkódtárak

| Platform | Tár neve | A tesztelt verzió | Forráskód | Sample |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Java Scribe](https://github.com/scribejava/scribejava) | [Verzió 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [A PHP az NFL oauth2-ügyfél](https://github.com/thephpleague/oauth2-client) | [1.4.2 verziója](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
![iOS](media/sample-v2-code/logo_iOS.png) |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Natív alkalmazás minta](active-directory-v2-devquickstarts-ios.md) |

Bármely szabványokkal kompatibilis szalagtár használható a v2.0-végpont, ezért fontos, hogy tudja, hol találhat a támogatást.

* A problémákat, és új funkciókra vonatkozó kérések kódtár kód a szalagtár tulajdonosától.
* Problémákat és a Szolgáltatásoldali protokollmegvalósítás az új funkciókra vonatkozó kérések forduljon a Microsofthoz.
* [A fájl-funkcióigénylés](https://feedback.azure.com/forums/169401-azure-active-directory) további funkciók, tekintse meg a protokoll szeretné.
* [Hozzon létre egy támogatási kérést](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Ha problémát talál, az Azure AD v2.0-végpont esetében ez nem engedélyezett az OAuth 2.0 vagy OpenID Connect 1.0.

## <a name="related-content"></a>Kapcsolódó tartalom

Az Azure AD v2.0-végpont kapcsolatos további információkért lásd: a [az Azure AD app model v2.0 áttekintése][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/

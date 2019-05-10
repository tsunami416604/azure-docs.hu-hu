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
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2018
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda17994f0279c975682b52449c71ef88a252401
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139309"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Az Azure Active Directory 2.0-s verziójú hitelesítési kódtárai

A [Azure Active Directory (Azure AD) v2.0-végpont](active-directory-v2-compare.md) a szabványos OAuth 2.0 és OpenID Connect 1.0 protokoll használatát támogatja. A Microsoft-hitelesítési tár (MSAL) úgy tervezték, hogy az Azure AD v2.0-végpont használata. Akkor is, amely támogatja az OAuth 2.0 és OpenID Connect 1.0 nyílt forráskódú könyvtáraink használatára.

Javasoljuk, hogy használja-e hajtsa végre a biztonságos fejlesztési Életciklussal (SDL) módszer, például protokoll tartomány szakértők által összeállított kódtárak [a Microsoft követ][Microsoft-SDL]. Ha úgy dönt, hogy az aktuális-kódot az protokollok, mint a Microsoft SDL és használatalapú zárja be a biztonsági szempontok a szabványok specifikációk minden protokollhoz a figyelmet a kövesse módszer.

> [!NOTE]
> Az Azure ad-ben 1.0-s verziójú library (ADAL) keres? Kivétel a [ADAL könyvtár az útmutató](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Szalagtárak típusai

Az Azure AD v2.0-végpont működik együtt könyvtárak két típusa:

* **Ügyfélkódtárak**: Natív ügyfelek és kiszolgálók klienskódtárak használatával lekérése egy erőforráshoz, például a Microsoft Graph hívásakor vonatkozó hozzáférési jogkivonatait.
* **Server közbenső könyvtárak**: A felhasználói bejelentkezés kiszolgáló közbenső kódtárakat használó webalkalmazások. Webes API-k server közbenső könyvtárak használatával natív ügyfelek vagy más kiszolgálók által küldött jogkivonatainak érvényesítéséhez.

## <a name="library-support"></a>Klienskódtár-támogatásával

Szalagtárak érkeznek két támogatási kategóriákban:

* **Microsoft-supported**: A Microsoft javításokat nyújt ezek a kódtárak és SDL végzett szerkezetkialakításra, ezek a könyvtárak a.
* **Kompatibilis**: A Microsoft tesztelte ezek a kódtárak alapszintű forgatókönyvekben és megerősítette, hogy működnek-e a v2.0-végponttal. A Microsoft nem biztosít javítását, ezek a kódtárak és nem végrehajtva ezek a kódtárak áttekintését. Problémák és a funkciókérések legyen átirányítva a tár nyílt forráskódú projekt.

Tekintse meg a következő szakasz ebben a cikkben, amelyek együttműködnek a v2.0-végpont könyvtárak listáját.

## <a name="microsoft-supported-client-libraries"></a>A Microsoft által támogatott ügyfélkódtárak

Védett webes API hívása egy token beszerzéséhez használt ügyfél-hitelesítési tárak

| Platform | Kódtár | Letöltés | Forráskód | Sample | Leírások | Fogalmi doc | Ütemterv |
| --- | --- | --- | --- | --- | --- | --- | ---|
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js (előzetes verzió) | [NPM](https://www.npmjs.com/package/msal) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  [Egyoldalas alkalmazás](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Referencia](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core/docs/classes/_useragentapplication_.useragentapplication.html) | [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki)| [Ütemterv](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | Az MSAL Angular(Preview) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET-keretrendszer](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | Az MSAL .NET (előzetes verzió) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Asztali alkalmazás](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [Ütemterv](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![iOS / Objective-C vagy SWIFT nyelven](media/sample-v2-code/logo_iOS.png) | Az MSAL obj_c (előzetes verzió) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS app](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android / Java](media/sample-v2-code/logo_Android.png) | Az MSAL (előzetes verzió) | [Központi tárolóhely](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-alkalmazás](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>A Microsoft által támogatott kiszolgálói közbenső kódtárak

Közbenső kódtárak webalkalmazások és webes API-k védelmére használhatók. Webalkalmazás vagy írt az ASP.NET- vagy ASP.NET Core webes API, a közbenső szoftver kódtárak használják az ASP.NET és ASP.NET Core

| Platform | Kódtár | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET-biztonság |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHubon](https://github.com/aspnet/AspNetCore) |[MVC-alkalmazás](quickstart-v2-aspnet-webapp.md) |[Az ASP.NET API-referencia](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| IdentityModel Extensions for .NET| |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC-alkalmazás](quickstart-v2-aspnet-webapp.md) |[Referencia](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Az Azure AD-Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHubon](https://github.com/AzureAD/passport-azure-ad) | [Webalkalmazás](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>Kompatibilis ügyfélkódtárak

| Platform | Tár neve | A tesztelt verzió | Forráskód | Sample |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [Verzió 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect library](https://github.com/GluuFederation/oxAuth) | [Verzió 3.0.2-es](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect library](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [1.2.0-s vagy annál újabb verzió](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [Verzió 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [A PHP az NFL oauth2-ügyfél](https://github.com/thephpleague/oauth2-client) | [1.4.2 verziója](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1<br />omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| ![iOS](media/sample-v2-code/logo_iOS.png) ![Android](media/sample-v2-code/logo_Android.png) | [React Native Appauth](https://github.com/FormidableLabs/react-native-app-auth) | [Verzió 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [React Native Appauth](https://github.com/FormidableLabs/react-native-app-auth) | |

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
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
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

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
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

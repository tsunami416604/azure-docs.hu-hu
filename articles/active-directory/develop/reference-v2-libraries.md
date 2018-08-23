---
title: Az Azure Active Directory 2.0-s verziójú hitelesítési kódtárai |} A Microsoft Docs
description: Kompatibilis klienskódtárak és a kiszolgáló közbenső tárak, és a kapcsolódó erőforrástárba, forrás és minták mutató hivatkozásokat, az Azure Active Directory v2.0-végpont.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: celested
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: f3ac6050ca73f96facd621823dc4bad413b139c7
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060624"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Az Azure Active Directory 2.0-s verziójú hitelesítési kódtárai

A [Azure Active Directory (Azure AD) v2.0-végpont](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) a szabványos OAuth 2.0 és OpenID Connect 1.0 protokoll használatát támogatja. A Microsoft-hitelesítési tár (MSAL) úgy tervezték, hogy az Azure AD v2.0-végpont használata. Akkor is, amely támogatja az OAuth 2.0 és OpenID Connect 1.0 nyílt forráskódú könyvtáraink használatára.

Javasoljuk, hogy használja-e hajtsa végre a biztonságos fejlesztési Életciklussal (SDL) módszer, például protokoll tartomány szakértők által összeállított kódtárak [a Microsoft követ][Microsoft-SDL]. Ha úgy dönt, hogy az aktuális kód támogatása a protokollokat, hajtsa végre a módszer, például a Microsoft SDL és használatalapú zárja be a biztonsági szempontok a szabványok specifikációk minden protokollhoz a figyelmet.

> [!NOTE]
> Az Azure ad-ben 1.0-s verziójú tárak (ADAL) keres? Kivétel a [ADAL könyvtár az útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Szalagtárak típusai

Az Azure AD v2.0-végpont működik együtt könyvtárak két típusa:

* **Ügyfélkódtárak**. Natív ügyfelek és kiszolgálók klienskódtárak használatával lekérése egy erőforráshoz, például a Microsoft Graph hívásakor vonatkozó hozzáférési jogkivonatait.
* **Server közbenső könyvtárak**. A felhasználói bejelentkezés kiszolgáló közbenső kódtárakat használó webalkalmazások. Webes API-k server közbenső könyvtárak használatával natív ügyfelek vagy más kiszolgálók által küldött jogkivonatainak érvényesítéséhez.

## <a name="library-support"></a>Klienskódtár-támogatásával

Bármely szabványokkal kompatibilis könyvtár is választja, a v2.0-végpont használata esetén, mert fontos tudni, hogy hol találhat a támogatás. Problémákat és a funkciókérések kódtár kód a szalagtár tulajdonosától. Problémákat és a Szolgáltatásoldali protokollmegvalósítás a szolgáltatással kapcsolatos kéréseit forduljon a Microsofthoz. [A fájl-funkcióigénylés](https://feedback.azure.com/forums/169401-azure-active-directory) további funkciók, tekintse meg a protokoll szeretné. [Hozzon létre egy támogatási kérést](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Ha problémát talál, az Azure AD v2.0-végpont esetében ez nem engedélyezett az OAuth 2.0 vagy OpenID Connect 1.0.

Szalagtárak érkeznek két támogatási kategóriákban:

* **A Microsoft által támogatott**. A Microsoft javításokat nyújt ezek a kódtárak és SDL végzett szerkezetkialakításra, ezek a könyvtárak a.
* **Kompatibilis**. A Microsoft tesztelte ezek a kódtárak alapszintű forgatókönyvekben és megerősítette, hogy működnek-e a v2.0-végponttal. A Microsoft nem biztosít javítását, ezek a kódtárak és nem végrehajtva ezek a kódtárak áttekintését. Problémák és a funkciókérések legyen átirányítva a tár nyílt forráskódú projekt.

Tekintse meg a következő szakasz ebben a cikkben, amelyek együttműködnek a v2.0-végpont könyvtárak listáját.

## <a name="microsoft-supported-client-libraries"></a>A Microsoft által támogatott ügyfélkódtárak

> [!IMPORTANT]
> Éles környezetben használható az MSAL előzetes kódtárak alkalmasak. A Microsoft ezek a kódtárak, a jelenlegi éles tárak (ADAL) a azonos termelési szintű támogatást nyújt. Az előzetes verzióban azzal az MSAL API-t, a belső gyorsítótár-formátum és más mechanizmusok ezeket a könyvtárakat együtt a hibajavítások és a szolgáltatásokat érintő fejlesztések érvénybe szükséges előzetes értesítés nélkül. Ez hatással lehet az alkalmazás. A gyorsítótár-formátum módosítás például szükség lehet a felhasználókat, hogy jelentkezzen be újra. Egy API-módosítás megkövetelheti, hogy a kód frissítése. Amikor elérhetővé válik az általánosan elérhető (GA) kiadási hat hónapon belül frissítenie kell a a tár előzetes verzióját használó alkalmazásokhoz, vagy, előfordulhat, hogy tovább működni.

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| .NET-ügyfél Windows Store, az UWP, Xamarin iOS és Android rendszerhez | Az MSAL .NET (előzetes verzió) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Asztali alkalmazás](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (előzetes verzió) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Egylapos alkalmazás](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | Az MSAL (előzetes verzió) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-alkalmazás](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | Az MSAL (előzetes verzió) | [A központi tárházban](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-alkalmazás](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>A Microsoft által támogatott kiszolgálói közbenső kódtárak

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | Közbenső OWIN OpenID Connect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHubon](https://github.com/aspnet/AspNetKatana/) |[MVC-alkalmazás](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | Az Azure ad OAuth tulajdonosi OWIN közbenső |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHubon](https://github.com/aspnet/AspNetKatana/) |  | |
| .NET 4.x | A .NET 4.5-ös JWT-kezelő | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | ASP.NET-közbenső szoftver OpenID Connect |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.NET-biztonsági (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[MVC-alkalmazás](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | Az ASP.NET OAuth tulajdonosi közbenső szoftver |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.NET-biztonsági (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | A .NET Core JWT-kezelő  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Az Azure AD-Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHubon](https://github.com/AzureAD/passport-azure-ad) | [Webalkalmazás](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)| |

## <a name="compatible-client-libraries"></a>Kompatibilis ügyfélkódtárak

| Platform | Tár neve | A tesztelt verzió | Forráskód | Sample |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Natív alkalmazás minta](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Natív alkalmazás minta](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [Verzió 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| PHP | [A PHP az NFL oauth2-ügyfél](https://github.com/thephpleague/oauth2-client) | [1.4.2 verziója](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Kapcsolódó tartalom
Az Azure AD v2.0-végpont kapcsolatos további információkért lásd: a [az Azure AD app model v2.0 áttekintése][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
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

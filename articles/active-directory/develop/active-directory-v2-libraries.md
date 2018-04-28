---
title: Azure Active Directory v2.0 hitelesítési kódtárai |} Microsoft Docs
description: Kompatibilis ügyfél függvénytárainak és a server köztes könyvtárak, és a kapcsolódó könyvtár, a forrás és a minták mutató hivatkozásokat, az Azure Active Directory v2.0-végponttól.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/22/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 0d9e2831f9d8676eb3e7fac91c58f3977f2e0f32
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0 hitelesítési kódtárai
A [Azure Active Directory (Azure AD) v2.0-végponttól](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) a szabványos OAuth 2.0 és az OpenID Connect 1.0 protokoll használatát támogatja. A Microsoft és más szervezetek különböző szalagtárak használhatja a v2.0-végponttal.

A v2.0-végpontra használó alkalmazás építésekor azt javasoljuk, hogy használja-e a szalagtár szerepel, amely szerint hajtsa végre a biztonságos fejlesztési Életciklussal (SDL) módszer, például protokoll tartomány szakértők [a Microsoftkövet][Microsoft-SDL]. Ha úgy dönt, hogy az aktuális-kód támogatása a protokollokat, ajánlott hajtsa végre az SDL módszert és figyelmesen elolvassa a biztonsági szempontok a a szabványoknak specifikációi minden protokollhoz.

> [!NOTE]
> Az Azure AD 1.0-s verziójú függvénytárak (ADAL) keres? Kivételt a [ADAL-könyvtár útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Szalagtárak típusai
Az Azure AD v2.0-végponttól könyvtárak két tárolóhelytípussal működik:

* **Ügyféloldali kódtáraknál**. Natív ügyfelek és kiszolgálók hozzáférési jogkivonatok erőforrás, például a Microsoft Graph hívásakor használható klienskódtárak segítségével.
* **Kiszolgáló köztes szalagtárak**. A felhasználói bejelentkezés server köztes könyvtárak használó webalkalmazások. Webes API-k server köztes könyvtárak segítségével ellenőrizheti azokat vagy egyéb kiszolgálók natív ügyfelek által küldött jogkivonatokat.

## <a name="library-support"></a>Szalagtár támogatása
Szabványokkal kompatibilis függvénytárat, kiválaszthatja a v2.0-végpontra használatakor, mert fontos tudni, hogy önállóan hol találhatnak támogatásához. Problémák, illetve funkciókra vonatkozó kérések library Code lépjen kapcsolatba a szalagtár tulajdonos. A problémák és a Szolgáltatásoldali protokoll megvalósítása a funkciókra vonatkozó kérések lépjen kapcsolatba a Microsofttal.

Szalagtárak térjen két támogatási kategóriák:

* **A Microsoft által támogatott**. A Microsoft javítások biztosít ezekhez a könyvtárakhoz, és végzett SDL megfelelő gondossággal az ezekhez a könyvtárakhoz.
* **Kompatibilis**. A Microsoft ezeket a kódtárakat tesztelve az alapvető forgatókönyv és megerősítette, hogy működnek-e a v2.0-végponttal. A Microsoft nem biztosít javítását, ezeket a kódtárakat, és nem végrehajtva ezeket a könyvtárakat áttekintése. A könyvtár nyílt forráskódú projekt problémákat és funkciókra vonatkozó kérések legyenek irányítva.

Szalagtár szerepel, amely a v2.0-végpontra a listája tekintse meg a következő szakasz ebben a cikkben.


## <a name="microsoft-supported-client-libraries"></a>A Microsoft által támogatott ügyfél-függvénytárak

> [!IMPORTANT]
> A MSAL preview könyvtárak olyan éles környezetben használható. Nyújtunk a azonos szintű terméktámogatást et ezekhez a könyvtárakhoz mint mi a jelenlegi üzemi függvénytárak (ADAL). Az előzetes azt is módosíthatja a MSAL API-t, a belső gyorsítótár formátumot, és a más mechanizmusok ezeket a könyvtárakat értesítés nélkül, amely együtt a hibajavításokat és a szolgáltatásokat érintő fejlesztések érvénybe szükséges. Ez hatással lehet az alkalmazás. Például a gyorsítótár-formátum módosítva lett hatással lehet a felhasználók számára, például kelljen jelentkezzen be újra. Egy API-módosítást szükség lehet, hogy frissítse a kódot. Azt adja meg az általános rendelkezésre állási kiadásban lesz kérjük, frissítse az általánosan rendelkezésre álló verzióra hat hónapban, amikor előnézete használatával készítettek alkalmazásokként kódtár verziója előfordulhat, hogy nem fog működni.

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| .NET ügyfél, a Windows Store UWP, Xamarin iOS és Android rendszerhez | MSAL .NET (előzetes verzió) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Egy asztali alkalmazás](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (előzetes verzió) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Egyetlen alkalmazás](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | MSAL (előzetes verzió) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-alkalmazás](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (előzetes verzió) | [A központi tárházban](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-alkalmazás](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>A Microsoft által támogatott server köztes könyvtárak

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | OWIN OpenID Connect köztes |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[Codeplex webhelyen](http://katanaproject.codeplex.com) |[MVC-alkalmazáshoz](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | OWIN OAuth tulajdonosi köztes az AzureAD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[Codeplex webhelyen](http://katanaproject.codeplex.com) |  | |
| .NET 4.x | A .NET 4.5 JWT-kezelő | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | ASP.NET OpenID Connect köztes |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Az ASP.NET biztonsága (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[MVC-alkalmazáshoz](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | ASP.NET OAuth tulajdonosi köztes |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Az ASP.NET biztonsága (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | A .NET Core JWT-kezelő  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Az Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHubon](https://github.com/AzureAD/passport-azure-ad) | [Webalkalmazás](active-directory-v2-devquickstarts-node-web.md)| |

## <a name="compatible-client-libraries"></a>Kompatibilis ügyfél függvénytárainak
| Platform | Szalagtár neve | Tesztelt verziója | Forráskód | Sample |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Natív alkalmazás – minta](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Natív alkalmazás – minta](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [3.2.0 verzió](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | |
| PHP | [A PHP Liga oauth2-ügyfél](https://github.com/thephpleague/oauth2-client) | [1.4.2 verziója](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Kapcsolódó tartalom
Az Azure AD v2.0-végpontra vonatkozó további információkért lásd: a [az Azure AD app model v2.0 áttekintése][AAD-App-Model-V2-Overview].

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

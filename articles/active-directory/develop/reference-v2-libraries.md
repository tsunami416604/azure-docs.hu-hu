---
title: Microsoft identity platform hitelesítési tárak | Microsoft dokumentumok
description: Kompatibilis ügyfélkönyvtárak és kiszolgálói köztes szoftvertárak, valamint a kapcsolódó könyvtár-, forrás- és mintahivatkozások a Microsoft identity platform végpontjához.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9d2df175fa9d1ed33eb17ae85e01a4fd7a24e728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611939"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft identity platform hitelesítési kódtárak

A [Microsoft identity platform végpontja](active-directory-v2-compare.md) támogatja az iparági szabványnak megfelelő OAuth 2.0 és OpenID Connect 1.0 protokollokat. A Microsoft authentication library (MSAL) a Microsoft identity platform végpontjával való együttműködésre készült. Az OAuth 2.0 és az OpenID Connect 1.0-s szinkronizálást támogató nyílt forráskódú kódtárakat is használhat.

Azt javasoljuk, hogy a protokolltartomány-szakértők által írt tárakat használjon, akik a security development lifecycle (SDL) módszertant követik. Az ilyen módszerek közé tartozik [az egyik, hogy a Microsoft követi][Microsoft-SDL]. Ha átadja a protokollok kódját, olyan módszert kell követnie, mint például a Microsoft SDL. Fordítson különös figyelmet az egyes protokollok szabványspecifikációiban található biztonsági szempontokra.

> [!NOTE]
> Az Azure Active Directory hitelesítési könyvtárát (ADAL) keresi? Tekintse meg az [ADAL könyvtárútmutatót.](../azuread-dev/active-directory-authentication-libraries.md)

## <a name="types-of-libraries"></a>Könyvtárak típusai

A Microsoft identity platform végpontja kétféle tárral működik:

* **Ügyfélkódtárak:** A natív ügyfelek és kiszolgálók ügyfélkódtárakat használnak az erőforrások, például a Microsoft Graph hívásához használt hozzáférési jogkivonatok beszerzéséhez.
* **Kiszolgálói köztes szoftvertárak:** A webalkalmazások kiszolgálóköztes szoftvertárakat használnak a felhasználói bejelentkezéshez. A webes API-k kiszolgálói köztes szoftverkódtárakat használnak a natív ügyfelek vagy más kiszolgálók által küldött tokenek érvényesítéséhez.

## <a name="library-support"></a>Könyvtár támogatása

A könyvtárak két támogatási kategóriába sorolhatók:

* **Microsoft által támogatott**: A Microsoft javításokat biztosít ezekhez a könyvtárakhoz, és sdl-átvilágítást végzett ezeken a könyvtárakon.
* **Kompatibilis**: A Microsoft ezeket a könyvtárakat alapforgatókönyvekben tesztelte, és megerősítette, hogy együttműködnek a Microsoft identity platform végpontjával. A Microsoft nem biztosít javításokat ezekhez a könyvtárakhoz, és nem vizsgálta felül ezeket a könyvtárakat. A problémákat és a szolgáltatáskérelmeket a tár nyílt forráskódú projektjéhez kell irányítani.

A Microsoft identity platform végpontjával dolgozó tárak listáját a következő szakaszokban láthatja.

## <a name="microsoft-supported-client-libraries"></a>Microsoft által támogatott ügyféltárak

Az ügyfélhitelesítési kódtárak használatával jogkivonatot szerezhet a védett webes API-k hívásához.

| Platform | Erőforrástár | Letöltés | Forráskód | Sample | Referencia | Fogalmi dokumentum | Ütemterv |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[Github](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Egyoldalas alkalmazás](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Referencia](https://azuread.github.io/microsoft-authentication-library-for-js/docs/msal/) | [Fogalmi dokumentumok](msal-overview.md)| [Ütemterv](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL SzögLetE JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [Github](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL-szög (előzetes verzió) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[Github](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET-keretrendszer](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[Github](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Asztali alkalmazás](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Fogalmi dokumentumok](msal-overview.md) | [Ütemterv](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [Github](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Minták](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Ütemterv](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [Github](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Minták](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Referencia](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Ütemterv](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| az iOS & a macOS | MSAL iOS és macOS | [Github](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[Github](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS alkalmazás](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [macOS alkalmazás](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Referencia](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Fogalmi dokumentumok](msal-overview.md) | |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Központi adattár](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[Github](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-alkalmazás](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Fogalmi dokumentumok](msal-overview.md) |[Ütemterv](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft által támogatott kiszolgálói köztes szoftvertárak

A köztes szoftvertárak használatával megvédheti a webalkalmazásokat és a webes API-kat. A ASP.NET vagy ASP.NET Core-szal írt webalkalmazások vagy webes API-k a köztes szoftvertárakat használják.

| Platform | Erőforrástár | Letöltés | Forráskód | Sample | Referencia
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET biztonság |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[Github](https://github.com/aspnet/AspNetCore) |[MVC alkalmazás](quickstart-v2-aspnet-webapp.md) |[ASP.NET API-hivatkozás](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| IdentityModel extensions for .NET| |[Github](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC alkalmazás](quickstart-v2-aspnet-webapp.md) |[Referencia](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure Hirdetési passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[Github](https://github.com/AzureAD/passport-azure-ad) | [Webalkalmazás](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Microsoft által támogatott könyvtárak operációs rendszer /nyelv szerint

A támogatott operációs rendszerek és nyelvek kifejezése esetén a leképezés a következő:

|             | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET FW, Core, UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (MacOS)       | MSAL.Net (Xamarin.iOS) | MSAL.Net (Xamarin.Android)|
| Swift <br> Objective-C |            |            | [MSAL iOS és macOS rendszerekre](msal-overview.md) | [MSAL iOS és macOS rendszerekre](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passport.node | Passport.node | Passport.node |

Lásd [még: Támogatott platformok és nyelvek forgatókönyvei](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Kompatibilis ügyféltárak

| Platform | Szalagtár neve | Tesztelt verzió | Forráskód | Sample |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Helló.js](https://adodson.com/hello.js/) | 1.13.5-ös verzió |[Helló.js](https://github.com/MrSwitch/hello.js) |[Spa](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Írnok Java](https://github.com/scribejava/scribejava) | [3.2.0-s verzió](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect könyvtár](https://github.com/GluuFederation/oxAuth) | [3.0.2-es verzió](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect könyvtár](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Kérések-OAuthlib](https://github.com/requests/requests-oauthlib) | [1.2.0-s verzió](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Kérések-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-ügyfél](https://github.com/panva/node-openid-client) | [2.4.5-ös verzió](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-ügyfél](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [A PHP League oauth2-kliens](https://github.com/thephpleague/oauth2-client) | [1.4.2-es verzió](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-kliens](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth között](https://github.com/omniauth/omniauth/wiki) |mindenevő: 1.3.1<br />omniauth-oauth2: 1.4.0 |[OmniAuth között](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Reagálni Natív App Auth](https://github.com/FormidableLabs/react-native-app-auth) | [4.2.0-s verzió](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Reagálni Natív App Auth](https://github.com/FormidableLabs/react-native-app-auth) | |

Bármely szabványoknak megfelelő függvénytár, használhatja a Microsoft identity platform végpont. Fontos tudni, hogy hová kell menni a támogatást:

* A könyvtárkódjában felmerülő problémákról és új szolgáltatáskérésekről forduljon a tár tulajdonosához.
* A szolgáltatási oldali protokoll megvalósításában felmerülő problémákról és új szolgáltatáskérésekről a Microsofttól érdeklődjön.
* [Nyújtson be egy szolgáltatáskérelmet](https://feedback.azure.com/forums/169401-azure-active-directory) a protokollban megtekinteni kívánt további szolgáltatásokra vonatkozóan.
* [Hozzon létre egy támogatási kérelmet,](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) ha olyan problémát talál, amely miatt a Microsoft identitásplatform-végpontja nem felel meg az OAuth 2.0 vagy az OpenID Connect 1.0 szabványnak.

## <a name="related-content"></a>Kapcsolódó tartalom

A Microsoft identity platform végpontjáról a [Microsoft identity platform áttekintése][AAD-App-Model-V2-Overview]című témakörben olvashat bővebben.

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

---
title: Microsoft Identity platform hitelesítési kódtárak | Microsoft Docs
description: Kompatibilis ügyféloldali kódtárak és kiszolgáló köztes kódtárak, valamint a kapcsolódó függvénytár-, forrás-és mintavételi hivatkozások a Microsoft Identity platform végpontja számára.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 418ad3cc059b86df1c96a62c3979a0c5e5de2a61
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494540"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft Identity platform hitelesítési kódtárak

A [Microsoft Identity platform végpontja](active-directory-v2-compare.md) támogatja az iparági szabványnak megfelelő OAuth 2,0 és az OpenID Connect 1,0 protokollokat. A Microsoft Authentication Library (MSAL) úgy van kialakítva, hogy működjön a Microsoft Identity platform-végponttal. Használhat olyan nyílt forráskódú kódtárakat is, amelyek támogatják a OAuth 2,0 és az OpenID Connect 1,0-et.

Azt javasoljuk, hogy a biztonsági fejlesztési életciklus (SDL) módszereit követő protokoll-tartományi szakértők által írt könyvtárakat használjon. Ilyen kódtárak közé tartozik [a Microsoft által elkövetett egyik][Microsoft-SDL]. Ha kódot használ a protokollok számára, kövesse az olyan módszereket, mint a Microsoft SDL. Ügyeljen arra, hogy az egyes protokollokra vonatkozó szabványok részletes leírásában a biztonsági szempontokat kell figyelembe venni.

> [!NOTE]
> A Azure Active Directory hitelesítési függvénytárat (ADAL) keresi? Tekintse meg a [ADAL-könyvtár útmutatóját](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Könyvtárak típusai

A Microsoft Identity platform végpontja két típusú kódtáraval működik:

* **Ügyféloldali kódtárak**: A natív ügyfelek és kiszolgálók az ügyfél-kódtárak használatával kapnak hozzáférési jogkivonatokat az erőforrások (például Microsoft Graph) meghívásához.
* **Kiszolgáló middleware**-kódtárai: A Web Apps kiszolgáló köztes kódtárakat használ a felhasználói bejelentkezéshez. A webes API-k kiszolgálói middleware-kódtárak segítségével érvényesítik a natív ügyfelek vagy más kiszolgálók által eljuttatott jogkivonatokat.

## <a name="library-support"></a>Függvénytár-támogatás

A kódtárak két támogatási kategóriába tartoznak:

* **Microsoft által támogatott**: A Microsoft kijavítja ezeket a kódtárakat, és az SDL-átvilágítás miatt megtörtént a kódtárak használata.
* **Kompatibilis**: A Microsoft alapvető forgatókönyvekben tesztelte ezeket a kódtárakat, és megerősítette, hogy együttműködik a Microsoft Identity platform-végponttal. A Microsoft nem biztosít javításokat ezekhez a könyvtárakhoz, és nem végzett felülvizsgálatot ezen könyvtárakról. A problémákat és a szolgáltatási kérelmeket a könyvtár nyílt forráskódú projektjeire kell irányítani.

A Microsoft Identity platform-végponttal működő könyvtárak listáját a következő részekben találja.

## <a name="microsoft-supported-client-libraries"></a>Microsoft által támogatott ügyféloldali kódtárak

A védett webes API-k meghívásához használjon ügyfél-hitelesítési kódtárakat.

| Platform | Erőforrástár | Letöltés | Forráskód | Minta | Hivatkozás | Fogalmi doc | Ütemterv |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Egyoldalas alkalmazás](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Referencia](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core/docs/classes/_useragentapplication_.useragentapplication.html) | [Fogalmi dokumentumok](msal-overview.md)| [Ütemterv](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL szögletes (előzetes verzió) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET-keretrendszer](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Asztali alkalmazás](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Fogalmi dokumentumok](msal-overview.md) | [Ütemterv](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python (előzetes verzió) | [PyPI](https://pypi.org/project/msal) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Példák](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Ütemterv](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java (előzetes verzió) | [Maven 3](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Példák](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Ütemterv](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| ![iOS/Objective C vagy SWIFT](media/sample-v2-code/logo_iOS.png) | MSAL-obj_c (előzetes verzió) | [GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS app](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki) | |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL (előzetes verzió) | [Központi tárház](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-alkalmazás](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft által támogatott kiszolgálói middleware-kódtárak

A webalkalmazások és a webes API-k védelméhez használjon middleware-kódtárakat. A ASP.NET vagy ASP.NET Core által írt webalkalmazások vagy webes API-k a middleware-kódtárakat használják.

| Platform | Erőforrástár | Letöltés | Forráskód | Minta | Hivatkozás
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET biztonság |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHubon](https://github.com/aspnet/AspNetCore) |[MVC-alkalmazás](quickstart-v2-aspnet-webapp.md) |[ASP.NET API-referenciák](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| IdentityModel-bővítmények a .NET-hez| |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC-alkalmazás](quickstart-v2-aspnet-webapp.md) |[Referencia](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHubon](https://github.com/AzureAD/passport-azure-ad) | [Webalkalmazás](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Microsoft által támogatott kódtárak operációs rendszer/nyelv szerint

A támogatott operációs rendszerek és nyelvek kifejezésében a megfeleltetés a következő:

|             | ![Windows](media/sample-v2-code/logo_windows.png) Windows    | Linux      | macOS      | ![iOS](media/sample-v2-code/logo_iOS.png)        | ![Android / Java](media/sample-v2-code/logo_Android.png) Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET FW, Core, UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (MacOS)       | MSAL.Net (Xamarin. iOS) | MSAL.Net (Xamarin. Android)|
| <img alt="Objective C" src="../../cognitive-services/speech-service/media/index/logo_objc.jpg" width="64px" height="64px" /> |            |            |        | MSAL. iOS |            |
| ![Java](media/sample-v2-code/logo_java.png) Java       |        |        |       | | MSAL. Android
| ![Java](media/sample-v2-code/logo_java.png) Java       | msal4j       | msal4j       | msal4j      | |
| ![Python](media/sample-v2-code/logo_python.png) Python     | MSAL Python | MSAL Python | MSAL Python |
| ![Node. js](media/sample-v2-code/logo_nodejs.png) Node.JS     | Passport. Node | Passport. Node | Passport. Node |

Lásd még: [forgatókönyvek a támogatott platformok és nyelvek alapján](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Kompatibilis ügyféloldali kódtárak

| Platform | Könyvtár neve | Tesztelt verzió | Forráskód | Minta |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 1\.13.5 verziója |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Írástudó Java](https://github.com/scribejava/scribejava) | [3.2.0-es verzió](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect könyvtár](https://github.com/GluuFederation/oxAuth) | [3.0.2 verziója](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect könyvtár](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [1.2.0-s vagy annál újabb verzió](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [OpenID – ügyfél](https://github.com/panva/node-openid-client) | [2.4.5 verziója](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [OpenID – ügyfél](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [A PHP Liga oauth2 – ügyfél](https://github.com/thephpleague/oauth2-client) | [1.4.2 verziója](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-oauth2: 1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| ![iOS](media/sample-v2-code/logo_iOS.png) ![Android](media/sample-v2-code/logo_Android.png) | [Natív alkalmazás hitelesítésének megválaszolása](https://github.com/FormidableLabs/react-native-app-auth) | [4.2.0 verziója](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Natív alkalmazás hitelesítésének megválaszolása](https://github.com/FormidableLabs/react-native-app-auth) | |

Bármely szabványnak megfelelő kódtár esetében használhatja a Microsoft Identity platform végpontját. Fontos tudni, hogy hová kell menni a támogatáshoz:

* A problémákkal és az új szolgáltatási kérelmekkel kapcsolatban a könyvtár kódjában forduljon a könyvtár tulajdonosához.
* A szolgáltatási oldali protokollok megvalósításával kapcsolatos problémákról és új szolgáltatásokra vonatkozó kérésekhez forduljon a Microsofthoz.
* A protokollban megjelenő további funkciókra vonatkozó [szolgáltatás kérése](https://feedback.azure.com/forums/169401-azure-active-directory) .
* [Hozzon létre egy támogatási kérést](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) , ha olyan problémát talál, amelyben a Microsoft Identity platform végpontja nem felel meg a OAuth 2,0 vagy az OpenID Connect 1,0-nek.

## <a name="related-content"></a>Kapcsolódó tartalom

További információ a Microsoft Identity platform végpontról: a [Microsoft Identity platform áttekintése][AAD-App-Model-V2-Overview].

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

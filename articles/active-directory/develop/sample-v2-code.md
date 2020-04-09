---
title: Kódminták a Microsoft identity platformhoz
description: Az elérhető Microsoft identity platform (2.0-s verzióbeli végpont) kódminták indexét tartalmazza forgatókönyv szerint rendezve.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5596f5f40e293a7bd11ac6a5f900e05c0b8d3b0e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883134"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft identity platformkódminták (2.0-s verziós végpont)

A Microsoft identity platform segítségével:

- Adjon hozzá hitelesítést és engedélyezést a webes alkalmazásokhoz és webes API-khoz.
- A védett webes API eléréséhez hozzáférési jogkivonat ot kell igényelni.

Ez a cikk röviden ismerteti, és a Microsoft identity platform végpontmintáira mutató hivatkozásokat tartalmaz. Ezek a minták bemutatják, hogyan kell ezt elvégezni, és az alkalmazásokban használható kódrészleteket is biztosít. A kódmintalapon részletes részletes részletes témaköröktalálhatók, amelyek segítenek a követelményekkel, a telepítéssel és a telepítéssel kapcsolatban. A kódon belüli megjegyzések segítenek megérteni a kritikus szakaszokat.

> [!NOTE]
> Ha érdekli az 1.0-s v.0-s minták, lásd: [Azure AD-kód minták (v1.0 végpont)](../azuread-dev/sample-v1-code.md).

Az egyes mintatípusok alapvető forgatókönyvének megértéséhez olvassa el [a Microsoft identity platform végpontalkalmazás-típusai című témakört.](v2-app-types.md)

A GitHubon is hozzájárulhat a mintákhoz. Ebből, hogy miként tájékozódhat a [Microsoft Azure Active Directory-mintákról és dokumentációról.](https://github.com/Azure-Samples?page=3&query=active-directory)

## <a name="single-page-applications"></a>Egyoldalas alkalmazások

Ezek a minták azt mutatják be, hogyan írhat egy egyoldalas alkalmazást a Microsoft identity platformmal védett. Ezek a minták használata az egyik ízek MSAL.js.

| Platform | Leírás | Hivatkozás |
| -------- | --------------------- | -------- |
| ![Ezen a képen a](media/sample-v2-code/logo_js.png) JavaScript logo [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | A Microsoft Graph hívása |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Ezen a képen a](media/sample-v2-code/logo_js.png) JavaScript logo [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Hívások B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Ezen a képen a](media/sample-v2-code/logo_js.png) JavaScript logo [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Saját webes API-hívások |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Ez a kép a](media/sample-v2-code/logo_angular.png) Szögletes logo [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| A Microsoft Graph hívása  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Ez a kép a](media/sample-v2-code/logo_angular.png) Szögletes logo [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Hívások B2C |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |

## <a name="web-applications"></a>Webalkalmazások

A következő minták a felhasználókat bejelentkező webalkalmazásokat szemléltetik. Egyes minták is bemutatják az alkalmazás hívása a Microsoft Graph, vagy a saját webes API-t a felhasználó identitását.

| Platform | Csak jelek a felhasználókban | Jelek a felhasználókés kéri a Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Ezen a képen a ASP.NET Core embléma látható](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | [ASP.NET Core WebApp bejelentkezési felhasználóinak bemutatója](https://aka.ms/aspnetcore-webapp-sign-in) | Ugyanaz a minta a [ASP.NET Core Web App hívja a Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fázis |
| ![Ezen a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET – rövid útmutató](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-korlátozott hatókörű-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-képzés-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Ezen a képen a Java embléma látható](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Ezen a képen a Python embléma látható](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Ezen a képen a Ruby embléma látható](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-képzés-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Asztali és mobil nyilvános ügyfélalkalmazások

A következő minták a Microsoft Graph API-hoz vagy a saját webes API-hoz egy felhasználó nevében elérő nyilvános ügyfélalkalmazásokat (asztali vagy mobilalkalmazásokat) jelenítik meg. Ezek az ügyfélalkalmazások mindegyike Microsoft Authentication Library (MSAL) programot használ.

| Ügyfélalkalmazás | Platform | Folyamat/támogatás | A Microsoft Graph hívása | egy ASP.NET Core 2.0 webes API-t hív meg |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Asztal (WPF)      | ![Ezen a képen a .NET/C# embléma látható](media/sample-v2-code/logo_NET.png) | [Interaktív](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-natív-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Asztal (konzol)   | ![Ezen a képen a .NET/C# (Desktop) embléma látható](media/sample-v2-code/logo_NET.png) | [Integrált Windows-hitelesítés](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Asztal (konzol)   | ![Ezen a képen a Java embléma látható](media/sample-v2-code/logo_java.png) | [Integrált Windows-hitelesítés](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Asztal (konzol)   | ![Ezen a képen a .NET/C# (Desktop) embléma látható](media/sample-v2-code/logo_NETcore.png) | [Felhasználónév/jelszó](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Asztal (konzol) WAM-mel  | ![Ezen a képen a .NET/C# (Desktop) embléma látható](media/sample-v2-code/logo_NETcore.png) | [interaktív a WAM-mal](msal-authentication-flows.md#interactive) |[dotnet-natív-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Asztal (konzol)   | ![Ezen a képen a Java embléma látható](media/sample-v2-code/logo_java.png) | [Felhasználónév/jelszó](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Asztal (konzol)   | ![Ezen a képen a Python embléma látható](media/sample-v2-code/logo_python.png) | [Felhasználónév/jelszó](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Mobil (Android, iOS, UWP)   | ![Ezen a képen a .NET/C# (Xamarin) embléma látható](media/sample-v2-code/logo_xamarin.png) | [Interaktív](msal-authentication-flows.md#interactive) |[xamarin-natív-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobil (iOS)       | ![Ezen a képen iOS/Objective-C vagy Swift](media/sample-v2-code/logo_iOS.png) | [Interaktív](msal-authentication-flows.md#interactive) |[ios-swift-objc-natív-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-natív-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Asztal (macOS)       | macOS | [Interaktív](msal-authentication-flows.md#interactive) |[macOS-swift-objc-natív-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobil (Android-Java)   | ![Ezen a képen az Android embléma látható](media/sample-v2-code/logo_Android.png) | [Interaktív](msal-authentication-flows.md#interactive) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobil (Android-Kotlin)   | ![Ezen a képen az Android embléma látható](media/sample-v2-code/logo_Android.png) | [Interaktív](msal-authentication-flows.md#interactive) |  [android-Kotlin között](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Démonalkalmazások

A következő minták egy olyan alkalmazást mutatnak be, amely saját identitásával (felhasználó nélkül) fér hozzá a Microsoft Graph API-hoz.

| Ügyfélalkalmazás | Platform | Folyamat/támogatás | A Microsoft Graph hívása |
| ------------------ | -------- | ---------- | -------------------- |
| Konzol | ![Ezen a képen a .NET Core embléma látható](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webalkalmazás | ![Ezen a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [dotnet-démon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Konzol | ![Ezen a képen a Java embléma látható](media/sample-v2-code/logo_java.png) | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [ms-identity-java-démon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Konzol | ![Ezen a képen a Python embléma látható](media/sample-v2-code/logo_python.png) | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [ms-identity-python-démon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Fej nélküli alkalmazások

A következő minta egy webböngésző nélküli eszközön futó nyilvános ügyfélalkalmazást mutat be. Az alkalmazás lehet parancssori eszköz, Linuxvagy Mac rendszeren futó alkalmazás vagy IoT-alkalmazás. A minta tartalmaz egy alkalmazást, amely hozzáfér a Microsoft Graph API-hoz, egy olyan felhasználó nevében, aki interaktívan bejelentkezik egy másik eszközre (például egy mobiltelefonra). Ez az ügyfélalkalmazás a Microsoft Authentication Library (MSAL) függvényt használja.

| Ügyfélalkalmazás | Platform | Folyamat/támogatás | A Microsoft Graph hívása |
| ------------------ | -------- |  ----------| ---------- |
| Asztal (konzol)   | ![Ezen a képen a .NET/C# (Desktop) embléma látható](media/sample-v2-code/logo_NETcore.png) | [Eszközkód-áramlás](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Asztal (konzol)   | ![Ezen a képen a Java embléma látható](media/sample-v2-code/logo_java.png) | [Eszközkód-áramlás](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Asztal (konzol)   | ![Ezen a képen a Python embléma látható](media/sample-v2-code/logo_python.png) | [Eszközkód-áramlás](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Webes API-k

A következő minták bemutatják, hogyan védheti meg a webes API-t a Microsoft identity platform végpont, és hogyan lehet meghívni egy alsóbb rétegbeli API-t a webes API-t.

| Platform | Sample |
| -------- | ------------------- |
| ![Ezen a képen a ASP.NET Core embléma látható](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | ASP.NET [dotnet-native-aspnetcore-v2 core webes API-jának (szolgáltatásának)](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Ezen a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NET.png)</p>ASP.NET, MVC | Web API (szolgáltatás) az [ms-identity-aspnet-webapi-onbehalfi](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Ezen a képen a Java embléma látható](media/sample-v2-code/logo_java.png) | Az [ms-identity-java-webapi webes API-ja (szolgáltatása)](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Ezen a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png) | Az [active-directory-javascript-nodejs-webapi-v2 webes API-ja (szolgáltatása)](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Ezen a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png) | Az [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) B2C webes API-ja |

## <a name="azure-functions-as-web-apis"></a>Az Azure-függvények webes API-kként

A következő minták bemutatják, hogyan védheti meg az Azure-függvényt a HttpTrigger használatával, és hogyan lehet egy webes API-t a Microsoft identity platform-végpontjával, és hogyan hívhat meg egy alsóbb rétegbeli API-t a webes API-ból.

| Platform | Sample |
| -------- | ------------------- |
| ![Ezen a képen a ASP.NET Core embléma látható](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | ASP.NET Core web API (szolgáltatás) Azure-funkciója [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Ezen a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | [A NodeJS webAPI-ja (szolgáltatása) és az útlevél-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Ezen a képen a Python embléma látható](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) webAPI-ja (szolgáltatása) |
| ![Ezen a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | A NodeJS web API-ja [(szolgáltatása) és az útlevél-azure-ad használata a](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Egyéb Microsoft Graph-minták

A Microsoft Graph API különböző használati mintáit bemutató [mintákról](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) és oktatóanyagokról, beleértve az Azure AD-vel való hitelesítést is, tekintse meg a [Microsoft Graph Community-minták & oktatóanyagok című témakört.](https://github.com/microsoftgraph/msgraph-community-samples)

## <a name="see-also"></a>Lásd még

- [Az Azure Active Directory (1.0-s) fejlesztői útmutatója](../azuread-dev/v1-overview.md)
- [A Microsoft Graph API fogalmi és hivatkozási fogalma](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)

---
title: Kód minták a Microsoft Identity platformhoz | Microsoft Docs
description: A rendelkezésre álló Microsoft Identity platform (v 2.0 végpont) kód mintáinak indexét kínálja forgatókönyv szerint rendezve.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86f9b6a6139f329a27914eff7a61d0f59c7f48ad
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678071"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft Identity platform Code Samples (v 2.0 végpont)

A Microsoft Identity platformot a következő célra használhatja:

- Adja hozzá a hitelesítést és az engedélyezést a webalkalmazásokhoz és a webes API-khoz.
- Hozzáférési jogkivonat megkövetelése egy védett webes API eléréséhez.

Ez a cikk röviden leírja a Microsoft Identity platform végpontjának mintáit, és hivatkozásokat tartalmaz. Ezek a minták bemutatják, hogyan végezheti el, és az alkalmazásaiban használható kódrészleteket is megadhat. A kód mintája lapon a követelményekkel, a telepítéssel és a telepítéssel kapcsolatos részletes információkkal foglalkozó témaköröket talál. A kódban található megjegyzések segítenek megérteni a kritikus szakaszt.

> [!NOTE]
> Ha érdeklik a 1.0-s példák, tekintse meg az [Azure ad Code Samples (v 1.0 Endpoint)](sample-v1-code.md)című témakört.

Az egyes típusok alapvető forgatókönyvének megismeréséhez tekintse meg [a Microsoft Identity platform végpontjának alkalmazás-típusai](v2-app-types.md)című témakört.

Emellett a GitHubon is hozzájárulhat a mintákhoz. További információ: [Microsoft Azure Active Directory minták és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Egyoldalas alkalmazások

Ezek a minták azt mutatják be, hogyan lehet egy egyoldalas alkalmazást biztonságossá tennie a Microsoft Identity platformmal. Ezek a minták a MSAL. js egyik ízeit használják.

| Platform | Leírás | Összekapcsolás |
| -------- | --------------------- | -------- |
| ![Ez a képen a JavaScript-](media/sample-v2-code/logo_js.png) embléma [JavaScript (msal. js) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Hívások Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Ez a képen a JavaScript-](media/sample-v2-code/logo_js.png) embléma [JavaScript (msal. js) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | B2C-hívások |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Ez a képen a JavaScript-](media/sample-v2-code/logo_js.png) embléma [JavaScript (msal. js) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Saját webes API meghívása |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Ez a képen a szögletes js embléma](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Hívások Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![Ez a képen a szögletes embléma](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL szögletes) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Hívások Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Webalkalmazások

Az alábbi példák bemutatják a felhasználók bejelentkezési webalkalmazásait. Néhány minta azt is bemutatja, hogy az alkalmazás meghívja a Microsoft Grapht, vagy a saját webes API-ját a felhasználó identitásával.

| Platform | Csak a felhasználókhoz tartozó jelek | A felhasználók és a hívások jelei Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Ez a képen a ASP.NET Core embléma látható](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [ASP.NET Core WebApp Signs-in Users oktatóanyag](https://aka.ms/aspnetcore-webapp-sign-in) | Ugyanaz a minta a [ASP.net Core Web App calls Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fázisban |
| ![Ez a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET gyors útmutató](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-WebApp](https://github.com/Azure-Samples/ms-identity-java-webapp): egy MSAL4J webes alkalmazás, amely a Microsoft Graphot hívja meg |
| ![Ez a képen a Node. js-embléma látható](media/sample-v2-code/logo_nodejs.png)  |                   | [Node. js rövid útmutató](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Ez a képen a Ruby embléma látható](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Asztali és mobil nyilvános ügyfélalkalmazások

Az alábbi példák olyan nyilvános ügyfélalkalmazások (asztali vagy mobil alkalmazások) megjelenítését mutatják be, amelyek hozzáférnek a Microsoft Graph API-hoz vagy a saját webes API-ját egy felhasználó nevében. Ezek az ügyfélalkalmazások a Microsoft Authentication Library (MSAL) alkalmazást használják.

| Ügyfélalkalmazás | Platform | Folyamat/támogatás | Hívások Microsoft Graph | Egy ASP.NET Core 2,0 webes API meghívása |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Asztali (WPF)      | ![Ez a rendszerkép a .NET/C# emblémát jeleníti meg](media/sample-v2-code/logo_NET.png) | [interactive](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Asztal (konzol)   | ![Ez a rendszerkép a .NET/C# (asztali) emblémát jeleníti meg](media/sample-v2-code/logo_NET.png) | [Integrált Windows-hitelesítés](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Asztal (konzol)   | ![Ez a rendszerkép a .NET/C# (asztali) emblémát jeleníti meg](media/sample-v2-code/logo_NETcore.png) | [Felhasználónév/jelszó](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Mobil (Android, iOS, UWP)   | ![Ez a rendszerkép a .NET/C# (Xamarin) emblémát jeleníti meg](media/sample-v2-code/logo_xamarin.png) | [interactive](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobil (iOS)       | ![A képen az iOS/Objective-C vagy a Swift látható](media/sample-v2-code/logo_iOS.png) | [interactive](msal-authentication-flows.md#interactive) |[iOS-Swift-ObjC-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Asztali (macOS)       | macOS | [interactive](msal-authentication-flows.md#interactive) |[macOS-Swift-ObjC-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android – Java)   | ![Ez a rendszerkép az Android-emblémát jeleníti meg](media/sample-v2-code/logo_Android.png) | [interactive](msal-authentication-flows.md#interactive) |  [Android – Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobil (Android – Kotlin)   | ![Ez a rendszerkép az Android-emblémát jeleníti meg](media/sample-v2-code/logo_Android.png) | [interactive](msal-authentication-flows.md#interactive) |  [Android – Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Daemon-alkalmazások

A következő minták egy olyan alkalmazást mutatnak be, amely a saját identitásával fér hozzá a Microsoft Graph API-hoz (felhasználó nélkül).

| Ügyfélalkalmazás | Platform | Folyamat/támogatás | Hívások Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konzol | ![Ez a rendszerkép a .NET Core emblémát jeleníti meg](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web app | ![Ez a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Fej nélküli alkalmazások

Az alábbi példa egy, a webböngészőt nem használó eszközön futó nyilvános ügyfélalkalmazás. Az alkalmazás lehet egy parancssori eszköz, egy Linux vagy Mac rendszerű alkalmazás, vagy egy IoT-alkalmazás. A minta olyan alkalmazást tartalmaz, amely a Microsoft Graph API-t használja egy olyan felhasználó nevében, aki egy másik eszközön (például egy mobiltelefonon) interaktívan jelentkezik be. Ez az ügyfélalkalmazás a Microsoft Authentication Library (MSAL) szolgáltatást használja.

| Ügyfélalkalmazás | Platform | Folyamat/támogatás | Hívások Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Asztal (konzol)   | ![Ez a rendszerkép a .NET/C# (asztali) emblémát jeleníti meg](media/sample-v2-code/logo_NETcore.png) | [Eszköz kódjának folyamata](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>Webes API-k

Az alábbi példák bemutatják, hogyan lehet védelemmel ellátni egy webes API-t a Microsoft Identity platform-végponttal, és hogyan hívhat meg egy alsóbb rétegbeli API-t a webes API-ból.

| Platform | Minta |
| -------- | ------------------- |
| ![Ez a képen a ASP.NET Core embléma látható](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core web API (szolgáltatás) a [DotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Ez a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NET.png)</p>ASP.NET, MVC | [MS-Identity-ASPNET-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) webes API (szolgáltatás) |

## <a name="other-microsoft-graph-samples"></a>Egyéb Microsoft Graph minták

A Microsoft Graph API különböző használati mintáit bemutató [minták](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) és oktatóanyagok megismeréséhez, beleértve az Azure ad-vel történő hitelesítést, tekintse meg a [Microsoft Graph közösségi példákat & oktatóanyagokat](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Lásd még

- [Azure Active Directory (v 1.0) fejlesztői útmutató](v1-overview.md)
- [Az Azure AD Graph API fogalma és referenciája](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Azure AD Graph API segítő könyvtár](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

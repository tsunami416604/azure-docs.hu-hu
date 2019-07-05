---
title: A Microsoft identity platform Kódminták |} A Microsoft Docs
description: Itt indexe alapján elérhető a Microsoft identity platform (v2.0-végpontra) kódminták forgatókönyv szerint vannak rendezve.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ceae71766c3e51a84cd09e8ac88740353e783bea
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482519"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>A Microsoft identity platform Kódminták (v2.0-végpontra)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

A Microsoft identity platform használhatja:

- Hitelesítés és engedélyezés hozzáadása a webes alkalmazások és a webes API-kat.
- Hozzáférési jogkivonat egy védett webes API eléréséhez szükséges.

Ez a cikk röviden ismerteti, és hivatkozásokat tartalmaz, amelyek a mintákat a Microsoft identity platform végpont. Ezek a minták bemutatják, hogyan történik, és kódrészletek, amelyek használhatja alkalmazásaiban is biztosítanak. A minta kódlap, a részletes információs témakörök, amelyek segítenek a követelményeket, telepítési és beállítási találja. A kód megjegyzéseket segítségével megismerheti a kritikus fontosságú szakaszok.

> [!NOTE]
> Ha az 1.0-s verziójú minták érdekli, lásd: [az Azure AD-Kódminták (1.0-s verziójú végpont)](sample-v1-code.md).

Az alapszintű forgatókönyv az egyes minta ismertetése: [típusú alkalmazások a Microsoft identity platform végpont](v2-app-types.md).

Ön is hozzájárulhat a mintákat a Githubon. További információ [a Microsoft Azure Active Directory-példák és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Egylapos alkalmazások

Ezek a minták ismertetik, hogyan írhat egy védett Microsoft identity platform egyoldalas alkalmazás. Ezek a minták MSAL.js változata egyikét használhatja.

| Platform | Leírás | Hivatkozás |
| -------- | --------------------- | -------- |
| ![Az ábra a JavaScript embléma](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | A Microsoft Graph hívások |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Az ábra a JavaScript embléma](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Meghívja a B2C-vel |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Az ábra a JavaScript embléma](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Hívások saját webes API-k |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Az ábra az Angular JS embléma](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| A Microsoft Graph hívások  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![Az ábra az Angular embléma](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| A Microsoft Graph hívások  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Webalkalmazások

A következő példák bemutatják, webalkalmazások, amelyek a felhasználók. Bizonyos mintákat is bemutatják az alkalmazás a Microsoft Graph vagy a felhasználó identitását a saját webes API hívása.

| Platform | Csak a felhasználó bejelentkezik | Képes bejelentkeztetni a felhasználókat, és a Microsoft Graph-hívások |
| -------- | ------------------- | --------------------------------- |
| ![Az ábra az ASP.NET Core-embléma](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [Az ASP.NET Core-WebApp jelentkezik be a felhasználók oktatóanyag](https://aka.ms/aspnetcore-webapp-sign-in) | Ugyanazt a mintát a [ASP.NET Core-webalkalmazás hívja a Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fázis |
| ![Az ábra az ASP.NET-embléma](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Az ASP.NET a rövid útmutató](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Az ábra a Node.js-embléma](media/sample-v2-code/logo_nodejs.png)  |                   | [NODE.js – rövid útmutató](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Az ábra a Ruby-embléma](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Nyilvános ügyfél asztali és mobil alkalmazások

A következő példák bemutatják nyilvános ügyfél (asztali és mobil alkalmazások) alkalmazást, hogy a Microsoft Graph API, vagy saját webes API-t be a felhasználó nevét. Ezek ügyfélalkalmazások használja a Microsoft-hitelesítési tár (MSAL).

| Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások | Az ASP.NET Core 2.0-s webes API-hívások |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| (WPF) asztali      | ![Az ábra a .NET /C# embléma](media/sample-v2-code/logo_NET.png) | [interactive](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Asztal (konzol)   | ![Az ábra a .NET /C# (asztalon) embléma](media/sample-v2-code/logo_NET.png) | [Integrált Windows-hitelesítés](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Asztal (konzol)   | ![Az ábra a .NET /C# (asztalon) embléma](media/sample-v2-code/logo_NETcore.png) | [Felhasználónév/jelszó](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Mobilalkalmazás (Android, iOS, az UWP)   | ![Az ábra a .NET /C# (Xamarin) embléma](media/sample-v2-code/logo_xamarin.png) | [interactive](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobile (iOS)       | ![Az ábra iOS/Objective-C vagy SWIFT nyelven](media/sample-v2-code/logo_iOS.png) | [interactive](msal-authentication-flows.md#interactive) |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Mobile (Android)   | ![Az ábra az Android embléma](media/sample-v2-code/logo_Android.png) | [interactive](msal-authentication-flows.md#interactive) |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |  |

## <a name="daemon-applications"></a>Démon alkalmazások

A következő példák bemutatják egy alkalmazás, amely a Microsoft Graph API (a felhasználó nem) a saját identitással fér hozzá.

| Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások |
| ------------------ | -------- | ---------- | -------------------- |
| Konzol | ![Az ábra a .NET Core-embléma](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Ügyfél-hitelesítő adatok](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web app | ![Az ábra az ASP.NET-embléma](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Ügyfél-hitelesítő adatok](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Távfelügyelt alkalmazások

A következő minta bemutatja egy webes böngésző nélkül futó nyilvános ügyfélalkalmazás. Az alkalmazás a parancssori eszköz, egy alkalmazás futtatását Linux vagy Mac vagy IoT-alkalmazás is lehet. A minta egy alkalmazást a felhasználó, aki bejelentkezik interaktív módon (például mobiltelefon) egy másik eszközön be nevét a Microsoft Graph API elérése funkciókat. Az ügyfélalkalmazás használja a Microsoft-hitelesítési tár (MSAL).

| Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások |
| ------------------ | -------- |  ----------| ---------- |
| Asztal (konzol)   | ![Az ábra a .NET /C# (asztalon) embléma](media/sample-v2-code/logo_NETcore.png) | [Eszköz kódfolyamat](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>Webes API-k

A következő példák bemutatják, hogyan védheti meg a webes API-t a Microsoft identity platform végponttal, és hogyan hívhat meg egy alsóbb rétegbeli API-t a webes API-t.

| Platform | Sample |
| -------- | ------------------- |
| ![Az ábra az ASP.NET Core-embléma](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core webes API-k (szolgáltatás), [dotnet-natív-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Az ábra az ASP.NET-embléma](media/sample-v2-code/logo_NET.png)</p>ASP.NET, MVC | Webes API-t (szolgáltatás), [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |

## <a name="other-microsoft-graph-samples"></a>Más Microsoft Graph-minták

További információ [minták](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) és a Microsoft Graph API-hoz, többek között az Azure AD-hitelesítés különböző használati mintákat bemutató oktatóanyagokat talál [Microsoft Graph közösségi minták és oktatóanyagok](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Lásd még

- [Az Azure Active Directory (1.0-s verzió) fejlesztői útmutatója](v1-overview.md)
- [Az Azure AD Graph API elméleti és -referencia](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Az Azure AD Graph API Segédkódtárba helyezni](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

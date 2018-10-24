---
title: Az Azure Active Directory-Kódminták |} A Microsoft Docs
description: Itt érhető el az Azure Active Directory (V2 végpont) index Kódminták forgatókönyv szerint vannak rendezve.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7323f532787fa767d875e13262cce9f1ceaa79d8
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955521"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Az Azure Active Directory-Kódminták (V2 végpont)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Segítségével a Microsoft Azure Active Directory (Azure AD):

- Hitelesítés és engedélyezés hozzáadása a webes alkalmazások és a webes API-kat.
- Hozzáférési jogkivonat egy védett webes API eléréséhez szükséges.

Ez a cikk röviden ismerteti, és hivatkozásokat tartalmaz, amelyek a minták az Azure AD V2 végpont. Ezek a minták bemutatják, hogyan kész van kódrészletek, amelyek az alkalmazásokban használható együtt. A minta kódlap, a Súgó a követelményeket, telepítési, és állítsa be a részletes információs témát találhat. A kód megjegyzéseket-e segítenek megérteni a kritikus fontosságú szakaszok.

> [!NOTE]
> Ha érdekli a V1-minták, [az Azure AD-Kódminták (V1-végpontra)](sample-v1-code.md).

Az alapszintű forgatókönyv az egyes minta ismertetése: [típusú alkalmazások esetében az Azure Active Directory v2.0-végpont](v2-app-types.md).

Ön is hozzájárulhat a mintákat a Githubon. További információ [a Microsoft Azure Active Directory-példák és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>Egylapos alkalmazások (SPA)

Ez a példa bemutatja, hogyan írhat az Azure ad-vel védett egy egyoldalas alkalmazás. Ezek a minták valamelyikével MSAL.js változata: [Microsoft Authentication Library for JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core), [Angular Microsoft-hitelesítési tár](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular), [Microsoft-hitelesítési tár az AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

 Platform |  A Microsoft Graph hívások
 -------- |  ---------------------
![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [JavaScript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
![Angular](media/sample-v2-code/logo_angular.png) JavaScript (MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Webalkalmazások

A következő példák bemutatják, webalkalmazások, amelyek a felhasználók. Bizonyos mintákat is bemutatják az alkalmazás a Microsoft Graph vagy a felhasználó identitását a saját webes API hívása.

 Platform | Csak a felhasználó bejelentkezik | Képes bejelentkeztetni a felhasználókat, és a Microsoft Graph-hívások
 -------- | ------------------- | ---------------------------------
![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [DotNet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [ASPNET-connect-rest-minta](https://github.com/microsoftgraph/aspnet-connect-rest-sample)
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>Az ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-minta](https://github.com/microsoftgraph/aspnetcore-connect-sample)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [Ruby-connect-rest-minta](https://github.com/microsoftgraph/ruby-connect-rest-sample)

## <a name="desktop-and-mobile-public-client-apps"></a>Nyilvános ügyfél asztali és mobil alkalmazások

A következő példák bemutatják nyilvános ügyfél (asztali és mobil alkalmazások) alkalmazást, hogy a Microsoft Graph vagy saját webes API-be a felhasználó nevét. Ezek ügyfélalkalmazások használja a MicroSoft hitelesítési tárak (MSAL)

Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások | Az ASP.NET Core 2.0-s webes API-hívások
------------------ | -------- |  ----------| ---------- | -------------------------
(WPF) asztali      | ![.NET ÉS A C#](media/sample-v2-code/logo_NET.png) | Interaktív | [DotNet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [DotNet-rendszergazda – korlátozott-hatókörök-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [DotNet-natív-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Asztal (konzol)   | ![.NET és a C# (asztali verzió)](media/sample-v2-code/logo_NET.png) | Integrált Windows-hitelesítés |[DotNet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2)
Asztal (konzol)   | ![.NET és a C# (asztali verzió)](media/sample-v2-code/logo_NETcore.png) | Felhasználónév/jelszó |[dotnetcore – akár – v2](https://github.com/azure-samples/active-directory-dotnetcore-up-v2)
Mobilalkalmazás (UWP)   | ![.NET ÉS A C# (UWP)](media/sample-v2-code/logo_windows.png) | Interaktív |[DotNet-natív-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobilalkalmazás (Android, iOS, az UWP)   | ![.NET és a C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interaktív |[xamarin-natív-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobile (iOS)       | ![iOS / Objective-C vagy SWIFT nyelven](media/sample-v2-code/logo_iOS.png) | Interaktív |[iOS-swift-natív-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [iOS-natív-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobilalkalmazás (Android)   | ![Android / Java](media/sample-v2-code/logo_Android.png) | Interaktív |  [Android-natív-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Démon alkalmazások

Az alábbi példa egy alkalmazás, amely a Microsoft Graph (a felhasználó nem) a saját identitással fér hozzá.

Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások
------------------ | -------- | ---------- | --------------------
Konzol | ![.NET Core](media/sample-v2-code/logo_NETcore.png)<p/> ASP.NET  | Ügyfél-hitelesítő adatok | [dotnetcore-démon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)
Webalkalmazás | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET  | Ügyfél-hitelesítő adatok | [DotNet-démon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

## <a name="headless-applications"></a>Távfelügyelt alkalmazások

A következő példák bemutatják nyilvános ügyfél egy eszközt anélkül, hogy egy webes böngésző a futó alkalmazások. Ezek az alkalmazások parancssori eszközök, illetve a Linux/Mac vagy iOT-alkalmazást futtató alkalmazások is lehet. A minták a alkalmazások hozzáférjenek a felhasználó, aki bejelentkezik interaktív módon (például mobiltelefon) egy másik eszközön be nevét a Microsoft Graph szolgáltatás. Ezek ügyfélalkalmazások használja a MicroSoft hitelesítési tárak (MSAL)

Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások
------------------ | -------- |  ----------| ----------
Asztal (konzol)   | ![.NET és a C# (asztali verzió)](media/sample-v2-code/logo_NETcore.png) | Eszköz kódfolyamat |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)

## <a name="web-apis"></a>Webes API-k

A következő minta bemutatja, hogyan védheti meg a webes API-t az Azure AD V2 végpont. Ez az API gyakorolja a aplikaci WPF (de bármilyen alkalmazás valóban hívható)

Platform | Sample
 -------- | -------------------
![.NET ÉS A C#](media/sample-v2-code/logo_NET.png) | [DotNet-natív-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Más Microsoft Graph-minták

További információ [minták](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) és a Microsoft Graph API-hoz, többek között az Azure AD-hitelesítés különböző használati mintákat bemutató oktatóanyagokat talál [Microsoft Graph közösségi minták és oktatóanyagok](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Lásd még

[Az Azure Active Directory fejlesztői útmutatója](v1-overview.md)

[Az Azure AD Graph API elméleti és -referencia](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Az Azure AD Graph API Segédkódtárba helyezni](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

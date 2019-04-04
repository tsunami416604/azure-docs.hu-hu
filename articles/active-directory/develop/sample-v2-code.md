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
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1aca8eb16e9474f8ec834178748c27682372f7b
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905392"
---
# <a name="azure-active-directory-code-samples-v20-endpoint"></a>Az Azure Active Directory-Kódminták (v2.0-végpontra)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

A Microsoft identity platform használhatja:

- Hitelesítés és engedélyezés hozzáadása a webes alkalmazások és a webes API-kat.
- Hozzáférési jogkivonat egy védett webes API eléréséhez szükséges.

Ez a cikk röviden ismerteti, és hivatkozásokat tartalmaz, amelyek a minták az Azure AD v2.0-végpont számára. Ezek a minták bemutatják, hogyan kész van kódrészletek, amelyek az alkalmazásokban használható együtt. A minta kódlap, a Súgó a követelményeket, telepítési, és állítsa be a részletes információs témát találhat. A kód megjegyzéseket-e segítenek megérteni a kritikus fontosságú szakaszok.

> [!NOTE]
> Ha az 1.0-s verziójú minták érdekli, lásd: [az Azure AD-Kódminták (1.0-s verziójú végpont)](sample-v1-code.md).

Az alapszintű forgatókönyv az egyes minta ismertetése: [típusú alkalmazások esetében az Azure Active Directory v2.0-végpont](v2-app-types.md).

Ön is hozzájárulhat a mintákat a Githubon. További információ [a Microsoft Azure Active Directory-példák és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>Egylapos alkalmazások (SPA)

Ezek a minták ismertetik, hogyan írhat az Azure ad-vel védett egy egyoldalas alkalmazás. Ezek a minták a MSAL.js változata valamelyikével:

* [A Microsoft Authentication Library for JavaScript rendszerhez](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)
* [A Microsoft-hitelesítési tár Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)
* [AngularJS-Microsoft-hitelesítési tár](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

  Platform |  A Microsoft Graph hívások
  -------- |  ---------------------
  ![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
  ![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
  ![Angular](media/sample-v2-code/logo_angular.png) JavaScript (MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Webalkalmazások

A következő példák bemutatják, webalkalmazások, amelyek a felhasználók. Bizonyos mintákat is bemutatják az alkalmazás a Microsoft Graph vagy a felhasználó identitását a saját webes API hívása.

 Platform | Csak a felhasználó bejelentkezik | Képes bejelentkeztetni a felhasználókat, és a Microsoft Graph-hívások
 -------- | ------------------- | ---------------------------------
![ASP.NET-mag](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.1 | [Az ASP.NET Core-WebApp jelentkezik be a felhasználók oktatóanyag](https://aka.ms/aspnetcore-webapp-sign-in) | Ugyanazt a mintát a [ASP.NET Core-webalkalmazás hívja a Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fázis
![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Az ASP.NET a rövid útmutató](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p>[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [NODE.js – rövid útmutató](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp)

## <a name="desktop-and-mobile-public-client-apps"></a>Nyilvános ügyfél asztali és mobil alkalmazások

A következő példák bemutatják nyilvános ügyfél (asztali és mobil alkalmazások) alkalmazást, hogy a Microsoft Graph API vagy saját webes API-be a felhasználó nevét. Ezek ügyfélalkalmazások Microsoft hitelesítési tárak (MSAL) használja.

Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások | Az ASP.NET Core 2.0-s webes API-hívások
------------------ | -------- |  ----------| ---------- | -------------------------
(WPF) asztali      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | Interaktív | [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi)
Asztal (konzol)   | ![.NET/C# (Desktop)](media/sample-v2-code/logo_NET.png) | Integrált Windows-hitelesítés |[dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2)
Asztal (konzol)   | ![.NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Felhasználónév/jelszó |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)
Mobile (UWP)   | ![.NET/C# (UWP)](media/sample-v2-code/logo_windows.png) | Interaktív |[dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobilalkalmazás (Android, iOS, az UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interaktív |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobile (iOS)       | ![iOS / Objective-C vagy SWIFT nyelven](media/sample-v2-code/logo_iOS.png) | Interaktív |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobile (Android)   | ![Android / Java](media/sample-v2-code/logo_Android.png) | Interaktív |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Démon alkalmazások

A következő példák bemutatják egy alkalmazás, amely a Microsoft Graph API (a felhasználó nem) a saját identitással fér hozzá.

Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások
------------------ | -------- | ---------- | --------------------
Konzol | ![.NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | Ügyfél-hitelesítő adatok | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)
Webalkalmazás | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | Ügyfél-hitelesítő adatok | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

## <a name="headless-applications"></a>Távfelügyelt alkalmazások

A következő minta bemutatja egy webes böngésző nélkül futó nyilvános ügyfélalkalmazás. Az alkalmazás a parancssori eszköz vagy a Linux/Mac gépen fut, illetve IoT-alkalmazás lehet. A minta egy alkalmazást a felhasználó, aki bejelentkezik interaktív módon (például mobiltelefon) egy másik eszközön be nevét, a Microsoft Graph API elérése funkciókat. Az ügyfélalkalmazás MicroSoft hitelesítési tárak (MSAL).

Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások
------------------ | -------- |  ----------| ----------
Asztal (konzol)   | ![.NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Eszközkód folyamata |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)

## <a name="web-apis"></a>Webes API-k

A következő minta bemutatja, hogyan védheti meg az Azure AD v2.0-végpont a webes API-t. Az API-t a WPF-alkalmazás által történik, de minden olyan alkalmazás is meghívható. A webes API a Microsoft Graph is meghívja.

Platform | Sample
 -------- | -------------------
![.NET/C#](media/sample-v2-code/logo_NET.png) | A (szolgáltatás) WebAPI [dotnet-natív-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)

## <a name="other-microsoft-graph-samples"></a>Más Microsoft Graph-minták

További információ [minták](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) és a Microsoft Graph API-hoz, többek között az Azure AD-hitelesítés különböző használati mintákat bemutató oktatóanyagokat talál [Microsoft Graph közösségi minták és oktatóanyagok](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Lásd még

[Az Azure Active Directory fejlesztői útmutatója](v1-overview.md)

[Az Azure AD Graph API elméleti és -referencia](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Az Azure AD Graph API Segédkódtárba helyezni](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

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
ms.date: 04/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8d2ce5bb2f44d9e21fb12e96f9c68d4ecc0fc501
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581561"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Az Azure Active Directory-Kódminták (V2 végpont)

Segítségével a Microsoft Azure Active Directory (Azure AD):

- Hitelesítés és engedélyezés hozzáadása a webes alkalmazások és a webes API-kat.
- Hozzáférési jogkivonat egy védett webes API eléréséhez szükséges.

Ez a cikk röviden ismerteti, és hivatkozásokat tartalmaz, amelyek a minták az Azure AD V2 végpont. Ezek a minták bemutatják, hogyan kész van kódrészletek, amelyek az alkalmazásokban használható együtt. A minta kódlap, a Súgó a követelményeket, telepítési, és állítsa be a részletes információs témát találhat. A kód megjegyzéseket-e segítenek megérteni a kritikus fontosságú szakaszok.

> [!NOTE]
> Ha érdekli a V1-minták, [az Azure AD-Kódminták (V1-végpontra)](sample-v1-code.md).

Az alapszintű forgatókönyv az egyes minta ismertetése: [típusú alkalmazások esetében az Azure Active Directory v2.0-végpont](active-directory-v2-flows.md).

Ön is hozzájárulhat a mintákat a Githubon. További információ [a Microsoft Azure Active Directory-példák és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-apps"></a>Nyilvános ügyfél asztali és mobil alkalmazások

A következő példák bemutatják nyilvános ügyfél (asztali és mobil alkalmazások) alkalmazást, hogy a Microsoft Graph vagy egy webes API-t be a felhasználó nevét.

Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások | Az ASP.NET Core 2.0-s webes API-hívások
------------------ | -------- | ---------- | -------------------- | -------------------------
(WPF) asztali      | .NET ÉS A C#  | Interaktív | [DotNet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [DotNet-rendszergazda – korlátozott-hatókörök-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [DotNet-natív-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Mobilalkalmazás (UWP)   | .NET ÉS A C# (UWP) | Interaktív | [DotNet-natív-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobilalkalmazás (Android, iOS, az UWP)   | .NET és a C# (Xamarin) | Interaktív | [xamarin-natív-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobile (iOS)       | iOS / Objective-C vagy SWIFT nyelven | Interaktív | [iOS-swift-natív-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [iOS-natív-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobilalkalmazás (Android)   | Android / Java | Interaktív |   [Android-natív-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>Webalkalmazások

A következő példák bemutatják, webalkalmazások, a felhasználók, a Microsoft Graph, és a felhasználó identitását a webes API-hívás.

 Platform | Csak a felhasználó bejelentkezik | Képes bejelentkeztetni a felhasználókat, és a Microsoft Graph-hívások 
 -------- | ------------------- | --------------------------------- 
Az ASP.NET 4.x | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [DotNet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [ASPNET-connect-rest-minta](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
Az ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-minta](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [Ruby-connect-rest-minta](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>Démon alkalmazások

A következő minták asztali vagy webes alkalmazást, hogy a Microsoft Graph vagy az alkalmazás identitás (felhasználó nem) a webes API-k megjelenítése.

Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások 
------------------ | -------- | ---------- | -------------------- 
Webalkalmazás | .NET ÉS A C#  | Ügyfél-hitelesítő adatok | [DotNet-démon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>Egylapos alkalmazások (SPA)

Ez a példa bemutatja, hogyan írhat az Azure ad-vel védett egy egyoldalas alkalmazás.

 Platform |  A Microsoft Graph hívások 
 -------- |  --------------------- 
JavaScript (msal.js)  | [JavaScript-graphapi-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript (msal.js + AngularJS) | [angular-connect-rest-minta](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript (Hello.JS)  | [JavaScript-graphapi-web-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript (hello.js + Angular 4) | [angular4-connect-minta](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>Webes API-k

### <a name="web-api-protected-by-azure-ad"></a>Az Azure AD által védett webes API

A következő minta bemutatja, hogyan védheti meg a webes API-t az Azure AD V2 végpont.

Platform | Sample | Leírás
 -------- | ------------------- | ---------------------
Node.js | [DotNet-natív-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | Egy ASP.NET Core Web API meghívja a WPF-alkalmazás Azure AD V2 használatával.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webes API-t a Microsoft Graph vagy egy másik webes API hívása

Ez a minta még nem érhető el.

## <a name="other-microsoft-graph-samples"></a>Más Microsoft Graph-minták

További információ [minták](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) és a Microsoft Graph API-hoz, többek között az Azure AD-hitelesítés különböző használati mintákat bemutató oktatóanyagokat talál [Microsoft Graph közösségi minták és oktatóanyagok](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Lásd még

[Az Azure Active Directory fejlesztői útmutatója](azure-ad-developers-guide.md)

[Az Azure AD Graph API elméleti és -referencia](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Az Azure AD Graph API Segédkódtárba helyezni](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

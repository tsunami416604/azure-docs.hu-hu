---
title: Az Azure Active Directory-Kódminták |} Microsoft Docs
description: Itt az elérhető Azure Active Directory (V2 végpont) index mintakódok forgatókönyv szerint vannak rendezve.
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
ms.openlocfilehash: b7a894ccd27ddcda2ab4b98c69333d37de077863
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Az Azure Active Directory-Kódminták (V2 végpont)

Segítségével a Microsoft Azure Active Directory (Azure AD):

- Hitelesítési és engedélyezési hozzáadása a webalkalmazások és webes API-khoz.
- Olyan hozzáférési jogkivonatot egy védett webes API eléréséhez szükséges.

Ez a cikk röviden ismerteti, és hivatkozásokat tartalmaz, amelyek a mintákat a Azure AD V2 végpont. Ezeket a mintákat bemutatják, hogyan elkészült, az alkalmazásokban használható kódtöredékek együtt. A kód a minta lapon talál részletes információs témakörök, amelyek segítenek az követelményeinek, a telepítés, és állítsa be. A kód megjegyzéseket-e a fontos részek jobb megértése érdekében.

> [!NOTE]
> Ha érdekli a V1 mintában, lásd: [az Azure AD-mintakódok (V1 végpont)](active-directory-code-samples.md).

Az egyes minta alapvető forgatókönyv ismertetése: [alkalmazástípusok az Azure Active Directory v2.0-végpontra vonatkozó](active-directory-v2-flows.md).

A minták a Githubon is vehet részt. Megtudhatja, hogyan, lásd: [Microsoft Azure Active Directory-példák és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-apps"></a>Az asztali és mobil nyilvános ügyfélalkalmazások

A következő minták megjelenítése nyilvános ügyfél alkalmazások (asztali vagy hordozható alkalmazások), amelyhez hozzáférhetnek a Microsoft Graph vagy egy webes API-t egy felhasználó nevében.

ügyfélalkalmazás | Platform | Attribútumfolyam/támogatás | A Microsoft Graph hívások | Meghívja az ASP.NET Core 2.0 webes API-k
------------------ | -------- | ---------- | -------------------- | -------------------------
Asztali (WPF)      | .NET / C#  | Interaktív | [DotNet-asztal-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [DotNet-rendszergazda – korlátozott-hatókörök-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [DotNet-natív-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Mobileszköz-(UWP)   | .NET / C#-(UWP) | Interaktív | [DotNet-natív-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobile (Android, iOS, UWP)   | .NET / C# (Xamarin) | Interaktív | [xamarin-natív-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobile (csak iOS)       | iOS / Objective C vagy swift | Interaktív | [iOS-swift-natív-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [iOS-natív-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobile (Android)   | Android / Java | Interaktív |   [Android-natív-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>Webalkalmazások

A következő minták jelentkezzen be a felhasználók, hívható meg Microsoft Graph vagy a felhasználó identitását a webes API-hívás webalkalmazások mutatják be.

 Platform | Csak képes bejelentkeztetni a felhasználókat | Képes bejelentkeztetni a felhasználókat, és meghívja a Microsoft Graph 
 -------- | ------------------- | --------------------------------- 
Az ASP.NET 4.x | [appmodelv2-webalkalmazás-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [DotNet-webalkalmazás-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [ASPNET-csatlakozás-rest-minta](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
Az ASP.NET Core 2.0 | [aspnetcore-webalkalmazás-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore csatlakozás minta](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2-webalkalmazás-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [Ruby-csatlakozás-rest-minta](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>Démon alkalmazások

A következő mintákat a Microsoft Graph vagy webes API-k a szolgáltatásalkalmazás-identitásnak (nincs felhasználói) az asztal vagy a webes alkalmazások megjelenítése.

ügyfélalkalmazás | Platform | Attribútumfolyam/támogatás | A Microsoft Graph hívások 
------------------ | -------- | ---------- | -------------------- 
Webalkalmazás | .NET / C#  | Ügyfél hitelesítő adatait | [DotNet-démon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>Egylapos alkalmazások (SPA)

Ez a példa bemutatja, hogyan az Azure ad-vel védett egylapos alkalmazások írásával.

 Platform |  A Microsoft Graph hívások 
 -------- |  --------------------- 
JavaScript (msal.js)  | [JavaScript-graphapi-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript (msal.js + AngularJS) | [szögben kifejezett-csatlakozás-rest-minta](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript (Hello.JS)  | [JavaScript-graphapi-webalkalmazás-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript (hello.js + szögben kifejezett 4) | [angular4 csatlakozás minta](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>Webes API-k

### <a name="web-api-protected-by-azure-ad"></a>Webes API-t az Azure AD által védett

Az alábbi minta bemutatja, hogyan védi meg a webes API-t az Azure AD V2-végponthoz.

Platform | Minta | Leírás
 -------- | ------------------- | ---------------------
Node.js | [DotNet-natív-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | Meghívja az ASP.NET Core webes API-k használata az Azure AD V2 WPF-alkalmazás.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>A Microsoft Graph vagy egy másik webes API hívása webes API

Ez a minta még nem érhető el.

## <a name="other-microsoft-graph-samples"></a>Más Microsoft Graph-minták

További információt [minták](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) és oktatóprogramot kínál, amelyek bemutatják a különböző használati minták a Microsoft Graph API, például a hitelesítés az Azure ad-vel, a [Microsoft Graph közösségi minták & oktatóanyagok](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Lásd még

[Az Azure Active Directory fejlesztői útmutatója](active-directory-developers-guide.md)

[Az Azure AD Graph API fogalmi és referencia](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Az Azure AD Graph API Segédkódtárba helyezni](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

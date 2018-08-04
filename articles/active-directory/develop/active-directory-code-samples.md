---
title: Az Azure Active Directory-Kódminták |} A Microsoft Docs
description: Itt az Azure Active Directory (v1-végpontra) index Kódminták forgatókönyv szerint vannak rendezve.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 35d837f346e3aab4f8b08286f1e0806f385762ec
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493451"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Az Azure Active Directory-Kódminták (V1-végpontra)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

A Microsoft Azure Active Directory (Azure AD) használatával adja hozzá a hitelesítés és engedélyezés a webalkalmazásokhoz és webes API-kat.

Ez a szakasz a minták segítségével további információ az Azure AD V1-végpontra mutató hivatkozásokat tartalmaz. Ezek a minták bemutatják, hogyan történik az alkalmazásokban használható kódrészletek együtt. A kód minta oldalon találhat részletes olvasási-me témakörök, amelyek segítenek a követelményeket, telepítési és beállítási. És a kód segítségével megismerheti a fontos részek van ellátva.

> [!NOTE]
> Ha Ön Azure AD V2 Kódminták érdekli, lásd: [2.0-s verziójú Kódminták forgatókönyv szerint](active-directory-v2-code-samples.md).

Az alapszintű forgatókönyv az egyes minta ismertetése: [hitelesítési forgatókönyvek az Azure ad-ben](authentication-scenarios.md).

Ön is hozzájárulhat a mintákat a Githubon. További információ [a Microsoft Azure Active Directory-példák és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Asztali és mobil verziójának nyilvános ügyfélalkalmazások számára a Microsoft Graph vagy egy webes API hívása

A következő példák bemutatják nyilvános ügyfél (asztali és mobil alkalmazások) alkalmazást, hogy a Microsoft Graph vagy egy webes API-t be a felhasználó nevét.

Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások | Egy ASP.NET- vagy ASP.NET Core 2.0-s webes API-hívások
------------------ | -------- | ---------- | -------------------- | -------------------------
(WPF) asztali           | .NET ÉS A C# | Interaktív | [DotNet-natív-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [DotNet-natív-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [DotNet-natív-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [DotNet-webapi-manuális-jwt-ellenőrzés](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobilalkalmazás (UWP)            | .NET ÉS A C#  | Interaktív | [DotNet-natív-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [DotNet-windows-áruház](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (webes API egyetlen bérlőt) </p> [DotNet-webapi-több-bérlős – windows-áruház](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (több-bérlős webes API-t)|
Mobilalkalmazás (Android, iOS, az UWP)   | .NET és a C# (Xamarin) | Interaktív | [DotNet-natív-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobilalkalmazás (Android)           | A Java/Android | Interaktív |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | iOS/Objective C | Interaktív |   [nativeClient – iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Asztal (konzol)          | .NET ÉS A C# | Felhasználónév / jelszó </p> Integrált Windows-hitelesítés | | [DotNet-natív távfelügyelt](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Asztal (konzol)           | A .NET core / C# | Eszközprofil | | [DotNet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Webalkalmazások

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webes alkalmazások felhasználói bejelentkezés, a Microsoft Graph vagy a felhasználó identitását a webes API hívása

 Platform | Csak a felhasználó bejelentkezik | Hívások a Microsoft Graph vagy az AAD Graph| Egy másik ASP.NET- vagy ASP.NET Core 2.0-s webes API-hívások
 -------- | ------------------- | --------------------- | -------------------------
AZ ASP.NET 4.5-ÖS VERZIÓJA | [Webalkalmazás-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [webalkalmazás-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [DotNet-webapp – több-bérlős-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (az AAD Graph) |
Az ASP.NET Core 2.0 | [DotNet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webalkalmazás-webapi-több-bérlős-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (az AAD Graph) | [DotNet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
AZ ASP.NET 4.5-ÖS VERZIÓJA | [DotNet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [Python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [Java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [php-graphapi-webalkalmazás](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Szerepköralapú hozzáférés-vezérlés (Engedélyezés) bemutatására webalkalmazások

A következő példák bemutatják, hogyan szerepköralapú hozzáférés-vezérlést, amelynek korlátozhatja az engedélyeket a webes alkalmazás az egyes felhasználók bizonyos funkciók megvalósításához. A felhasználók jogosultak, attól függően, hogy egy Azure AD-csoport vagy szerepkör tartoznak.

Platform | Sample | Leírás
 -------- | ------------------- | ---------------------
AZ ASP.NET 4.5-ÖS VERZIÓJA | [DotNet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Az Azure AD használó .NET 4.5-ös MVC webalkalmazás **csoportok** használ a hitelesítéshez
AZ ASP.NET 4.5-ÖS VERZIÓJA | [DotNet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Az Azure AD használó .NET 4.5-ös MVC webalkalmazás **szerepkörök** használ a hitelesítéshez

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Démon alkalmazások (webes API-k elérése az Alkalmazásidentitás)

A következő példák bemutatják asztali vagy webes alkalmazást, hogy a Microsoft Graph vagy webes API-k (együtt az alkalmazás azonosítója) nélkül felhasználóval.

Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások | Egy ASP.NET- vagy ASP.NET Core 2.0-s webes API-hívások
------------------ | -------- | ---------- | -------------------- | -------------------------
Démon alkalmazások (konzol)          | .NET ÉS A C#  | Ügyfél-hitelesítő adatok titkos Alkalmazáskulcs vagy tanúsítvány | | [DotNet-démon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [DotNet-démon-tanúsítvány-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Démon alkalmazások (konzol)         | A .NET core / C# | Ügyfél-hitelesítő adatok tanúsítvánnyal| | [dotnetcore-démon-tanúsítvány-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Asztal            | Java | Ügyfél-hitelesítő adatok |   [Java-natív távfelügyelt](https://github.com/azure-samples/active-directory-java-native-headless) |
ASP.NET-webalkalmazás  | .NET ÉS A C# | Ügyfél-hitelesítő adatok |    | [DotNet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webes API-k

### <a name="web-api-protected-by-azure-active-directory"></a>Azure Active Directory által védett webes API

A következő minta bemutatja, hogyan védheti meg a node.js webes API Azure AD-vel.

Platform | Sample | Leírás
 -------- | ------------------- | ---------------------
Node.js | [csomópont-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  NodeJS webes API-t az Azure AD-vel védett és az OAuth 2.0 hozzáférési jogkivonatok.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webes API-t a Microsoft Graph vagy egy másik webes API hívása

A következő példák bemutatják, webes API-t egy másik webes API meghívásához. A második minta bemutatja, hogyan legyen kezelve a feltételes hozzáférés.

 Platform |  A Microsoft Graph hívások | Egy másik ASP.NET- vagy ASP.NET Core 2.0-s webes API-hívások
 -------- |  --------------------- | -------------------------
AZ ASP.NET 4.5-ÖS VERZIÓJA | [DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
AZ ASP.NET 4.5-ÖS VERZIÓJA | [DotNet-webapi-onbehalfof-hitelesítésszolgáltató](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[DotNet-webapi-onbehalfof-hitelesítésszolgáltató](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>Egylapos alkalmazások

Ez a példa bemutatja, hogyan írhat az Azure ad-vel védett egy egyoldalas alkalmazás.

 Platform |  A Microsoft Graph hívások | A saját API-hívások | Egy másik webes API-hívások
 -------- |  --------------------- | ------------------ | ----------------
JavaScript / az ASP.NET 4.x |  | [JavaScript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
JavaScript (AngularJS) és az ASP.NET 4.x |  | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |
JavaScript (AngularJS) és az ASP.NET 4.x |  |  | [a cors singlepageapp angularjs](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="other-microsoft-graph-samples"></a>Más Microsoft Graph-minták

Minták és a Microsoft Graph API-hoz, többek között az Azure AD-hitelesítés különböző használati mintákat bemutató oktatóanyagokat talál [Microsoft Graph-közösségi minták és oktatóanyagok](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Lásd még

[Az Azure Active Directory fejlesztői útmutatója](azure-ad-developers-guide.md)

[Az Azure AD Graph API elméleti és -referencia](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Az Azure AD Graph API Segédkódtárba helyezni](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

---
title: Az Azure Active Directory-Kódminták |} Microsoft Docs
description: Itt az Azure Active Directory (v1 végpont) index mintakódok forgatókönyv szerint vannak rendezve.
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
ms.openlocfilehash: 5d7f0d1fc32f18991be6614bb7661b63570a8700
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264922"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Az Azure Active Directory-Kódminták (V1 végpont)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Microsoft Azure Active Directory (Azure AD) segítségével vegye fel a hitelesítési és engedélyezési a webalkalmazások és webes API-k.

Ez a szakasz hivatkozások segítségével további tudnivalók az Azure AD V1 végpont minták. Ezeket a mintákat bemutatják, hogyan történik az alkalmazásokban használható kódtöredékek együtt. A kód a minta oldalon találhat részletes olvasás-me témakörök, amelyek a követelmények, a telepítés és a telepítés számára. És megismerheti a fontos részek megjegyzésként a kódot.

> [!NOTE]
> Ha Ön Azure AD V2 mintakódok iránt érdeklődik, lásd: [v2.0 mintakódok forgatókönyv szerint](active-directory-v2-code-samples.md).

Az egyes minta alapvető forgatókönyv ismertetése: [hitelesítési forgatókönyvek az Azure AD](active-directory-authentication-scenarios.md).

A Githubon az általunk biztosított mintákat is vehet részt. Megtudhatja, hogyan, lásd: [Microsoft Azure Active Directory-példák és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Az asztali és mobil nyilvános ügyfélalkalmazások Microsoft Graph vagy a Web API hívása

A következő minták megjelenítése nyilvános ügyfél alkalmazások (asztali vagy hordozható alkalmazások), amelyhez hozzáférhetnek a Microsoft Graph vagy egy webes API-t egy felhasználó nevében.

ügyfélalkalmazás | Platform | Attribútumfolyam/támogatás | A Microsoft Graph hívások | Az ASP.NET- vagy ASP.NET Core 2.0 webes API-t
------------------ | -------- | ---------- | -------------------- | -------------------------
Asztali (WPF)           | .NET / C# | Interaktív | [DotNet-natív-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [DotNet-natív-asztal](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [DotNet-natív-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [DotNet-webapi-manuális-jwt-érvényesítése](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobileszköz-(UWP)            | .NET / C#  | Interaktív | [DotNet-natív-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [DotNet-windows-tároló](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (egyetlen bérlői webes API-hoz) </p> [DotNet-webapi – több-bérlős-windows-tároló](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (több-bérlős webes API)|
Mobile (Android, iOS, UWP)   | .NET / C# (Xamarin) | Interaktív | [DotNet-natív-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | Android/Java | Interaktív |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (csak iOS)           | iOS/Objective C | Interaktív |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Asztal (konzol)          | .NET / C# | Felhasználónév / jelszó </p> Integrált Windows-hitelesítés | | [DotNet-natív távfelügyeleti](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Asztal (konzol)           | A .NET core / C# | Eszközprofil | | [DotNet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Webalkalmazások

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webalkalmazások felhasználói bejelentkezés, a Microsoft Graph, vagy a Web API-t a felhasználó identitását hívása

 Platform | Csak képes bejelentkeztetni a felhasználókat | Hívások Microsoft Graph vagy AAD grafikon| Egy másik ASP.NET vagy az ASP.NET Core 2.0 webes API
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [Webalkalmazás-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [webalkalmazás-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [DotNet-webalkalmazás-több-bérlős-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD-grafikon) |
Az ASP.NET Core 2.0 | [DotNet-webalkalmazás-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webalkalmazás-webapi – több-bérlős-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD-grafikon) | [DotNet-webalkalmazás-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [DotNet-webalkalmazás-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [Python-webalkalmazás-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [Java-webalkalmazás-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [php-graphapi-webalkalmazás](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Szerepköralapú hozzáférés-vezérlés (Engedélyezés), amely tartalmazza a webes alkalmazások

A következő minták bemutatják, hogyan szerepköralapú hozzáférés-vezérlés, amely korlátozni a hozzáférést a webes alkalmazás bizonyos felhasználókra bizonyos funkciók végrehajtásához. A felhasználók jogosultak a attól függően, hogy az Azure AD-csoport vagy szerepkör tartozik.

Platform | Sample | Leírás
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [DotNet-webalkalmazás-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | A .NET 4.5 MVC webalkalmazás az Azure AD használó **csoportok** a hitelesítéshez
ASP.NET 4.5 | [DotNet-webalkalmazás-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | A .NET 4.5 MVC webalkalmazás az Azure AD használó **szerepkörök** a hitelesítéshez

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>(Az alkalmazás azonosítóját webes API-k elérése) démon alkalmazások

A következő mintákat a Microsoft Graph vagy webes API-k nincs (a identitású alkalmazás) felhasználói asztali vagy a webes alkalmazások megjelenítése.

ügyfélalkalmazás | Platform | Attribútumfolyam/támogatás | A Microsoft Graph hívások | Az ASP.NET- vagy ASP.NET Core 2.0 webes API-t
------------------ | -------- | ---------- | -------------------- | -------------------------
Démon app (konzol)          | .NET / C#  | Ügyfél hitelesítő adatait az alkalmazás titkos kulcs vagy tanúsítvány | | [DotNet-démon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [DotNet-démon-tanúsítvány-hitelesítő adatok](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Démon app (konzol)         | A .NET core / C# | Tanúsítvány az ügyfél hitelesítő adatait| | [dotnetcore-démon-tanúsítvány-hitelesítő adatok](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Asztal            | Java | Ügyfél-hitelesítő adatok |   [Java-natív távfelügyeleti](https://github.com/azure-samples/active-directory-java-native-headless) |
ASP.NET-webalkalmazás  | .NET / C# | Ügyfél-hitelesítő adatok |    | [DotNet-webalkalmazás-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webes API-k

### <a name="web-api-protected-by-azure-active-directory"></a>Webes API-t védi az Azure Active Directoryban

A következő minta bemutatja, hogyan védi meg egy node.js webes API-t az Azure AD.

Platform | Sample | Leírás
 -------- | ------------------- | ---------------------
Node.js | [csomópont-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  NodeJS webes API-t, amely az Azure AD használatával lett biztonságossá téve és OAuth 2.0 hozzáférési jogkivonatok.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>A Microsoft Graph vagy egy másik webes API hívása webes API

A következő mintákat a webes API-t egy másik webes API-t meghívó bemutatása. A második minta bemutatja, hogyan kezeli a feltételes hozzáférés.

 Platform |  A Microsoft Graph hívások | Egy másik ASP.NET vagy az ASP.NET Core 2.0 webes API
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [DotNet-webapi-onbehalfof-Kanada](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[DotNet-webapi-onbehalfof-Kanada](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>Egylapos alkalmazások

Ez a példa bemutatja, hogyan az Azure ad-vel védett egylapos alkalmazások írásával.

 Platform |  A Microsoft Graph hívások | A saját API-t | Egy másik webes API-t
 -------- |  --------------------- | ------------------ | ----------------
JavaScript vagy az ASP.NET 4.x |  | [JavaScript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
JavaScript (AngularJS) vagy az ASP.NET 4.x |  | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |
JavaScript (AngularJS) vagy az ASP.NET 4.x |  |  | [a cors singlepageapp angularjs](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="other-microsoft-graph-samples"></a>Más Microsoft Graph-minták

Mintákat és oktatóprogramot kínál, amelyek a Microsoft Graph API, például a hitelesítés az Azure ad-vel, a különböző használati minták bemutatása: [Microsoft Graph közösségi minták & oktatóanyagok](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Lásd még

[Az Azure Active Directory fejlesztői útmutatója](active-directory-developers-guide.md)

[Az Azure AD Graph API fogalmi és referencia](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Az Azure AD Graph API Segédkódtárba helyezni](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

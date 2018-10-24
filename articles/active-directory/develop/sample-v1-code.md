---
title: Az Azure Active Directory-Kódminták |} A Microsoft Docs
description: Itt az Azure Active Directory (1.0-s verziójú végpont) index Kódminták forgatókönyv szerint vannak rendezve.
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
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 46b66a6e718f18b76db6332487c350c2c199c342
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957774"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Az Azure Active Directory-Kódminták (1.0-s verziójú végpont)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

A Microsoft Azure Active Directory (Azure AD) használatával adja hozzá a hitelesítés és engedélyezés a webalkalmazásokhoz és webes API-kat.

Ebben a szakaszban tudhat meg többet az Azure AD-1.0-s verziójú végpont használhatja minták mutató hivatkozásokat tartalmaz. Ezek a minták bemutatják, hogyan történik az alkalmazásokban használható kódrészletek együtt. A kód minta oldalon találhat részletes olvasási-me témakörök, amelyek segítenek a követelményeket, telepítési és beállítási. És a kód segítségével megismerheti a fontos részek van ellátva.

> [!NOTE]
> Ha Ön Azure AD V2 Kódminták érdekli, lásd: [2.0-s verziójú Kódminták forgatókönyv szerint](sample-v2-code.md).

Az alapszintű forgatókönyv az egyes minta ismertetése: [hitelesítési forgatókönyvek az Azure ad-ben](authentication-scenarios.md).

Ön is hozzájárulhat a mintákat a Githubon. További információ [a Microsoft Azure Active Directory-példák és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Egylapos alkalmazások

Ez a példa bemutatja, hogyan írhat az Azure ad-vel védett egy egyoldalas alkalmazás.

 Platform | A saját API-hívások | Egy másik webes API-hívások
 -------- |  --------------------- | ------------------ | ----------------
![JavaScript](media/sample-v2-code/logo_js.png) | [JavaScript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Angular JS](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [a cors singlepageapp angularjs](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Webalkalmazások

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webes alkalmazások felhasználói bejelentkezés, a Microsoft Graph vagy a felhasználó identitását a webes API hívása

A következő példák bemutatják, felhasználók aláírási webes alkalmazásokhoz. Néhány alkalmazást is meghívhat, a Microsoft Graph vagy saját webes API, a bejelentkezett felhasználó nevében.

 Platform | Csak a felhasználó bejelentkezik | Hívások a Microsoft Graph vagy az AAD Graph| Egy másik ASP.NET- vagy ASP.NET Core 2.0-s webes API-hívások
 -------- | ------------------- | --------------------- | -------------------------
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>Az ASP.NET Core 2.0 | [DotNet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webalkalmazás-webapi-több-bérlős-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) <p/>(Az AAD Graph) | [DotNet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![AZ ASP.NET 4.5-ÖS VERZIÓJA](media/sample-v2-code/logo_NETframework.png)<p/> AZ ASP.NET 4.5-ÖS VERZIÓJA | [Webalkalmazás-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) <p/> [webalkalmazás-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) <p/> [DotNet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [DotNet-webapp – több-bérlős-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)<p/> (Az AAD Graph) |
![Python](media/sample-v2-code/logo_python.png) | | [Python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Java](media/sample-v2-code/logo_java.png)  | | [Java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![A php](media/sample-v2-code/logo_php.png) | | [php-graphapi-webalkalmazás](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Szerepköralapú hozzáférés-vezérlés (Engedélyezés) bemutatására webalkalmazások

A következő példák bemutatják, hogyan szerepköralapú hozzáférés-vezérlés (RBAC) megvalósításához. Az RBAC korlátozni a hozzáférést bizonyos funkciók, egy webalkalmazás, az egyes felhasználók szolgál. A felhasználók jogosultak attól függően, hogy tartoznak egy **Azure AD-csoport** vagy egy adott alkalmazás **szerepkör**.

Platform | Sample
 -------- | -------------------
![AZ ASP.NET 4.5-ÖS VERZIÓJA](media/sample-v2-code/logo_NETframework.png)<p/> AZ ASP.NET 4.5-ÖS VERZIÓJA | [DotNet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) <p/>  [DotNet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Az Azure AD használó .NET 4.5-ös MVC webalkalmazás **szerepkörök** használ a hitelesítéshez

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Asztali és mobil verziójának nyilvános ügyfélalkalmazások számára a Microsoft Graph vagy egy webes API hívása

A következő példák bemutatják, nyilvános ügyfél alkalmazást (asztali és mobil alkalmazások), hogy a Microsoft Graph vagy egy webes API-t be a felhasználó nevét. Az eszközökön és platformokon függően alkalmazások bejelentkezhet felhasználók különböző módon (folyamatok és támogatás): 

- interaktív,
- beavatkozás nélkül (az integrált Windows-hitelesítés a Windows vagy a felhasználónév/jelszó), 
- vagy akár az interaktív bejelentkezés egy másik eszközre (eszköz kódfolyamat használt eszközök, amelyek nem biztosítják a webes vezérlők) delegálásával.

Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | A Microsoft Graph hívások | Az ASP.NET- vagy ASP.NET Core 2.x-es webes API-hívások
------------------ | -------- | ---------- | -------------------- | -------------------------
(WPF) asztali           | ![.NET ÉS A C#](media/sample-v2-code/logo_NET.png)  | Interaktív | Része [dotnet-natív-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [DotNet-natív-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [DotNet-natív-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [DotNet-webapi-manuális-jwt-ellenőrzés](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobilalkalmazás (UWP)            | .![.NET/C#/UWP](media/sample-v2-code/logo_Windows.png)   | Interaktív | [DotNet-natív-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Ebben a példában [WAM](https://docs.microsoft.com/windows/uwp/security/web-account-manager), nem [ADAL.NET](https://aka.ms/adalnet)|  [DotNet-windows-áruház](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (UWP-alkalmazás használatával ADAL.NET egyetlen új bérlő webes API meghívásához) </p> [DotNet-webapi-több-bérlős – windows-áruház](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (UWP-alkalmazás ADAL.NET használatával egy több-bérlős webes API hívása)|
Mobilalkalmazás (Android, iOS, az UWP)   | ![.NET és a C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interaktív | [DotNet-natív-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobilalkalmazás (Android)           | ![Android / Java](media/sample-v2-code/logo_Android.png) | Interaktív |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | ![iOS / Objective-C vagy SWIFT nyelven](media/sample-v2-code/logo_iOS.png) | Interaktív |   [nativeClient – iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Asztal (konzol)          | ![.NET ÉS A C#](media/sample-v2-code/logo_NET.png) | Felhasználónév / jelszó </p>  Integrált Windows-hitelesítés | | [DotNet-natív távfelügyelt](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Asztal (konzol)          | ![Java-konzol](media/sample-v2-code/logo_Java.png) | Felhasználónév / jelszó | | [Java-natív távfelügyelt](https://github.com/Azure-Samples/active-directory-java-native-headless)
Asztal (konzol)           | ![A .NET core / C#](media/sample-v2-code/logo_NETcore.png) | Eszköz kódfolyamat | | [DotNet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Démon alkalmazások (webes API-k elérése az Alkalmazásidentitás)

A következő példák bemutatják asztali vagy webes alkalmazást, hogy a Microsoft Graph vagy webes API-k (együtt az alkalmazás azonosítója) nélkül felhasználóval.

Ügyfélalkalmazás | Platform | A folyamat/engedélyezés | Egy ASP.NET- vagy ASP.NET Core 2.0-s webes API-hívások
------------------ | -------- | ---------- | -------------------- | -------------------------
Démon alkalmazások (konzol)          | ![.NET](media/sample-v2-code/logo_NETframework.png) | Ügyfél-hitelesítő adatok titkos Alkalmazáskulcs vagy tanúsítvány | [DotNet-démon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [DotNet-démon-tanúsítvány-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Démon alkalmazások (konzol)         | ![.NET](media/sample-v2-code/logo_NETcore.png) | Ügyfél-hitelesítő adatok tanúsítvánnyal| [dotnetcore-démon-tanúsítvány-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET-webalkalmazás  | ![.NET](media/sample-v2-code/logo_NETframework.png) | Ügyfél-hitelesítő adatok | [DotNet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webes API-k

### <a name="web-api-protected-by-azure-active-directory"></a>Azure Active Directory által védett webes API

A következő minta bemutatja, hogyan védheti meg a node.js webes API Azure AD-vel.

Ez a cikk a korábbi szakaszokban is megtalálhatja más ügyfélalkalmazás ábrázoló minták **hívása** egy ASP.NET- vagy ASP.NET Core **webes API-t**. Ezek a minták nem szerepelnek újra ebben a szakaszban, de megtalálja őket a táblázat alatti vagy feletti az utolsó oszlopban

Platform | Sample
 -------- | -------------------
![A php](media/sample-v2-code/logo_nodejs.png)  | [csomópont-webapi](https://github.com/Azure-Samples/active-directory-node-webapi)

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webes API-t a Microsoft Graph vagy egy másik webes API hívása

A következő példák bemutatják, webes API-t egy másik webes API meghívásához. A második minta bemutatja, hogyan legyen kezelve a feltételes hozzáférés.

 Platform |  A Microsoft Graph hívások | Egy másik ASP.NET- vagy ASP.NET Core 2.0-s webes API-hívások
 -------- |  --------------------- | -------------------------
![AZ ASP.NET 4.5-ÖS VERZIÓJA](media/sample-v2-code/logo_NETframework.png)<p/> AZ ASP.NET 4.5-ÖS VERZIÓJA | [DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [DotNet-webapi-onbehalfof-hitelesítésszolgáltató](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [DotNet-webapi-onbehalfof-hitelesítésszolgáltató](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="other-microsoft-graph-samples"></a>Más Microsoft Graph-minták

Minták és a Microsoft Graph API-hoz, többek között az Azure AD-hitelesítés különböző használati mintákat bemutató oktatóanyagokat talál [Microsoft Graph-közösségi minták és oktatóanyagok](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Lásd még

[Az Azure Active Directory fejlesztői útmutatója](v1-overview.md)

[Azure Active Directory hitelesítési kódtárai](active-directory-authentication-libraries.md)

[Az Azure AD Graph API elméleti és -referencia](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Az Azure AD Graph API Segédkódtárba helyezni](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

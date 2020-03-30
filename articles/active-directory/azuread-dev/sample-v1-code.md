---
title: Kódminták az Azure Active Directory 1.0-s számához | Microsoft dokumentumok
description: Az Azure Active Directory (1.0-s végpont) kódminták indexe forgatókönyv szerint rendezve.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ae283529abb3b71ee50fc710dd1ebe0d17a12be0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154831"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Az Azure Active Directory kódmintái (1.0-s kódpont)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A Microsoft Azure Active Directory (Azure AD) segítségével hitelesítést és engedélyezést adhat a webalkalmazásokhoz és a webes API-khoz.

Ez a szakasz az Azure AD 1.0-s végpontról szóló további tudnivalókhoz használható mintákra mutató hivatkozásokat tartalmaz. Ezek a minták megmutatják, hogyan történik a kódrészletek, amelyek et az alkalmazásokban használhat. A kódmintalapon részletes olvasmánytémakörök találhatók, amelyek segítenek a követelményekkel, a telepítéssel és a beállítással kapcsolatban. És a kód kommentálta, hogy segítsen megérteni a kritikus szakaszok.

> [!NOTE]
> Ha érdekli az Azure AD V2-kódminták, tekintse meg [a v2.0-s kódminták forgatókönyv szerint.](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

Az egyes mintatípusok alapvető forgatókönyvének megértéséhez olvassa el [az Azure AD hitelesítési forgatókönyveit](v1-authentication-scenarios.md)című témakört.

A GitHubon is hozzájárulhat a mintákhoz. Ebből, hogy miként tájékozódhat a [Microsoft Azure Active Directory-mintákról és dokumentációról.](https://github.com/Azure-Samples?page=3&query=active-directory)

## <a name="single-page-applications"></a>Egyoldalas alkalmazások

Ez a minta bemutatja, hogyan írhat egy egyoldalas alkalmazást az Azure AD-vel védett.

 Platform | Meghívja saját API-ját | Másik webes API hívása
 -------- |  --------------------- | ------------------ 
![Ezen a képen a JavaScript embléma látható](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Ezen a képen az Angular JS embléma látható](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Webalkalmazások

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>A felhasználók bejelentkező, microsoft graph-hívását vagy webes API-t a felhasználó identitásával

Az alábbi minták a felhasználókat aláíró webalkalmazásokat szemléltetik. Ezen alkalmazások némelyike a bejelentkezett felhasználó nevében a Microsoft Graph-ot vagy a saját webes API-t is meghívja.

 Platform | Csak jelek a felhasználókban | A Microsoft Graph hívása | Másik ASP.NET vagy ASP.NET Core 2.0 web API-t hív
 -------- | ------------------- | --------------------- | -------------------------
![Ezen a képen a ASP.NET embléma látható](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD grafikon) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Ezen a képen a ASP.NET embléma látható](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 |  </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD grafikon) |
![Ezen a képen a Python embléma látható](media/sample-v2-code/logo-python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Ezen a képen a Java-napló látható](media/sample-v2-code/logo-java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Ezen a képen a PHP embléma látható](media/sample-v2-code/logo-php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Szerepköralapú hozzáférés-vezérlést (engedélyezést) bemutató webalkalmazások

A következő minták bemutatják, hogyan valósítható meg a szerepköralapú hozzáférés-vezérlés (RBAC). Az RBAC segítségével bizonyos funkciók engedélyeit bizonyos felhasználókra korlátozhatja. A felhasználók attól függően engedélyezettek, hogy **egy Azure AD-csoporthoz** tartoznak-e, vagy egy adott **alkalmazásszerepkörrel**rendelkeznek.

Platform | Sample |
 -------- | ------------------- |
![Ezen a képen a ASP.NET embléma látható](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-csoportkövetelések](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-szerepkörjogcímek](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | A .NET 4.5 MVC webalkalmazás, amely Azure **AD-szerepköröket** használ az engedélyezéshez

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Microsoft Graph-ot vagy webes API-t hívó asztali és mobil nyilvános ügyfélalkalmazások

A következő minták a Microsoft Graph vagy egy webes API-t felhasználó nevében hozzáférő nyilvános ügyfélalkalmazásokat (deskto/pmobile alkalmazásokat) szemléltetik. Az eszközöktől és a platformoktól függően az alkalmazások különböző módokon jelentkezhetnek be a felhasználókba (folyamatok/támogatások):

- Interaktívan
- Csendben (integrált Windows-hitelesítés sel Windows rendszeren, vagy felhasználónév/jelszó)
- Az interaktív bejelentkezés delegálásával egy másik eszközre (olyan eszközökön használt eszközkód,) amelyek nem biztosítanak webes vezérlőket)

Ügyfélalkalmazás | Platform | Folyamat/támogatás | A Microsoft Graph hívása | ASP.NET vagy ASP.NET Core 2.x Web API hívása
------------------ | -------- | ---------- | -------------------- | -------------------------
Asztal (WPF)           | ![Ezen a képen a .NET/C# embléma látható](media/sample-v2-code/logo-net.png)  | Interaktív | A [dotnet-natív-multitarget része](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-natív-asztal](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-natív-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-érvényesítés](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobil (UWP)            | .![Ezen a képen a .NET/C#/UWP](media/sample-v2-code/logo-windows.png)   | Interaktív | [dotnet-natív-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Ez a minta [WAM-et](/windows/uwp/security/web-account-manager)használ, nem [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (UWP-alkalmazás, amely ADAL.NET egyetlen bérlőweb-API hívására használja) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (UWP-alkalmazás, amely ADAL.NET használatával több-bérlős webes API-hívására)|
Mobil (Android, iOS, UWP)   | ![Ezen a képen a .NET/C# (Xamarin)](media/sample-v2-code/logo-xamarin.png) | Interaktív | [dotnet-natív-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobil (Android)           | ![Ezen a képen az Android embléma látható](media/sample-v2-code/logo-android.png) | Interaktív |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobil (iOS)           | ![Ezen a képen iOS / C célkitűzés vagy Swift](media/sample-v2-code/logo-ios.png) | Interaktív |   [natívClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Asztal (konzol)          | ![Ezen a képen a .NET/C# embléma látható](media/sample-v2-code/logo-net.png) | Felhasználónév / Jelszó </p>  Beépített Windows-hitelesítés | | [dotnet-natív-fej nélküli](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Asztal (konzol)          | ![Ezen a képen a Java embléma látható](media/sample-v2-code/logo-java.png) | Felhasználónév / Jelszó | | [java-natív-fej nélküli](https://github.com/Azure-Samples/active-directory-java-native-headless)
Asztal (konzol)           | ![Ezen a képen a .NET Core/C# embléma látható](media/sample-v2-code/logo-netcore.png) | Eszközkód-áramlás | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Démonalkalmazások (webes API-k elérése az alkalmazás identitásával)

A következő minták olyan asztali vagy webes alkalmazásokat mutatnak be, amelyek a Microsoft Graph-ot vagy egy webes API-t felhasználói (alkalmazásidentitással) nem férnek hozzá.

Ügyfélalkalmazás | Platform | Folyamat/támogatás | ASP.NET vagy ASP.NET Core 2.0 web API-t hív
------------------ | -------- | ---------- | -------------------- 
Démon alkalmazás (konzol)          | ![Ezen a képen a .NET embléma látható](media/sample-v2-code/logo-netframework.png) | Ügyfélhitelesítő adatok alkalmazástitkos adattal vagy tanúsítvánnyal | [dotnet-démon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-tanúsítvány-hitelesítő adatok](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Démon alkalmazás (konzol)         | ![Ezen a képen a .NET embléma látható](media/sample-v2-code/logo-netcore.png) | Ügyfélhitelesítő adatok tanúsítvánnyal| [dotnetcore-daemon-tanúsítvány-hitelesítő adatok](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET webalkalmazás  | ![Ezen a képen a .NET embléma látható](media/sample-v2-code/logo-netframework.png) | Ügyfél-hitelesítő adatok | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webes API-k

### <a name="web-api-protected-by-azure-active-directory"></a>Az Azure Active Directory által védett webes API

Az alábbi minta bemutatja, hogyan védheti meg a node.js webes API-t az Azure AD-vel.

A cikk előző szakaszaiban más mintákat is találhat, amelyek egy ASP.NET vagy ASP.NET Core **Web API-t** **hívó** ügyfélalkalmazást szemléltetnek. Ezek a minták nem szerepelnek újra ebben a szakaszban, de megtalálja őket az utolsó oszlopban a fenti vagy alatti táblázatok

| Platform | Sample |
|--------|-------------------|
| ![Ezen a képen a Node.js embléma látható](media/sample-v2-code/logo-nodejs.png)  | [csomópont-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Microsoft Graph vagy más webes API hívása webes API

A következő minták egy webes API-t mutatnak be, amely egy másik webes API-t hív meg. A második minta bemutatja, hogyan kell kezelni a feltételes hozzáférés.

| Platform |  A Microsoft Graph hívása | Másik ASP.NET vagy ASP.NET Core 2.0 web API-t hív |
| -------- |  --------------------- | ------------------------- |
| ![Ezen a képen a ASP.NET embléma látható](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-nevében](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-nevében-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-nevében](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-nevében-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Egyéb Microsoft Graph-minták

A Microsoft Graph API különböző használati mintáit bemutató mintákat és oktatóanyagokat, beleértve az Azure AD-vel való hitelesítést is, olvassa el a Microsoft Graph közösségi minták & oktatóanyagok című [témakört.](https://github.com/microsoftgraph/msgraph-community-samples)

## <a name="see-also"></a>Lásd még

- [Az Azure Active Directory fejlesztői útmutatója](v1-overview.md)
- [Azure Active Directory hitelesítési kódtárak](active-directory-authentication-libraries.md)
- [A Microsoft Graph API fogalmi és hivatkozási fogalma](https://docs.microsoft.com/graph/use-the-api)

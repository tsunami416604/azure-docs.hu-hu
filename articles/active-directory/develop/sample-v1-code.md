---
title: Példák a Azure Active Directory 1.0-s verziójának kódjára | Microsoft Docs
description: Az Azure Active Directory (v 1.0 végpont) kód mintáinak indexét biztosítja forgatókönyv szerint rendezve.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a71eaebcc6345d6550d6ddd38350fe709706d740
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809298"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory kód mintái (v 1.0 végpont)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

A Microsoft Azure Active Directory (Azure AD) használatával a webalkalmazásokhoz és a webes API-khoz való hitelesítést és engedélyezést adhat hozzá.

Ez a szakasz olyan mintákra mutató hivatkozásokat tartalmaz, amelyek segítségével többet tudhat meg az Azure AD v 1.0-végpontról. Ezek a minták azt mutatják be, hogyan történik az alkalmazásokban használható kódrészletekkel együtt. A mintakód lapon részletes, a követelményekkel, a telepítéssel és a beállítással kapcsolatos témaköröket talál. A kód pedig a kritikus fejezetek megismerését segítő Megjegyzés.

> [!NOTE]
> Ha érdekli az Azure AD v2-kódok, tekintse meg a következő témakört: [v 2.0 Code Samples by forgatókönyv](sample-v2-code.md).

Az egyes mintavételi típusok alapvető forgatókönyvének megismeréséhez lásd: [hitelesítési forgatókönyvek az Azure ad-hez](v1-authentication-scenarios.md).

A GitHubon is hozzájárulhat a mintákhoz. További információ: [Microsoft Azure Active Directory minták és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Egyoldalas alkalmazások

Ez a minta bemutatja, hogyan írhat egy egyoldalas alkalmazást az Azure AD-vel.

 Platform | Saját API meghívása | Egy másik webes API meghívása
 -------- |  --------------------- | ------------------ 
![Ez a rendszerkép a JavaScript emblémát jeleníti meg](media/sample-v2-code/logo_js.png) | [JavaScript – singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Ez a képen a szögletes JS embléma látható](media/sample-v2-code/logo_angular.png) | [angularjs – singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-CORS](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Webalkalmazások

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Felhasználók számára bejelentkezett webalkalmazások, Microsoft Graph meghívása vagy webes API a felhasználó identitásával

Az alábbi példák bemutatják a felhasználók aláírására szolgáló webalkalmazásokat. Ezen alkalmazások némelyike a bejelentkezett felhasználó nevében is meghívja a Microsoft Graph vagy a saját webes API-ját.

 Platform | Csak a felhasználókhoz tartozó jelek | Hívások Microsoft Graph vagy HRE gráf| Egy másik ASP.NET vagy ASP.NET Core 2,0 webes API meghívása
 -------- | ------------------- | --------------------- | -------------------------
![Ez a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,0 | [DotNet-WebApp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [WebApp-webapi-több-bérlős-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(HRE gráf) | [DotNet-WebApp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Ez a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4,5 | [webApp-openidconnect-DotNet](quickstart-v1-aspnet-webapp.md) </p> [WebApp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [DotNet-WebApp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [DotNet-WebApp-több-bérlős openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (HRE gráf) |
![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png) | | [Python-WebApp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Ez a rendszerkép a Java-naplót jeleníti meg](media/sample-v2-code/logo_java.png)  | | [Java-WebApp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Ez a rendszerkép a PHP-emblémát jeleníti meg](media/sample-v2-code/logo_php.png) | | [PHP-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Szerepköralapú hozzáférés-vezérlést bemutató webalkalmazások (Engedélyezés)

A következő példák bemutatják, hogyan valósítható meg a szerepköralapú hozzáférés-vezérlés (RBAC). A RBAC egy webalkalmazás bizonyos szolgáltatásai engedélyeinek bizonyos felhasználókra való korlátozására szolgál. A felhasználók jogosultak attól függően, hogy egy **Azure ad-csoporthoz** tartoznak-e, vagy egy adott alkalmazási **szerepkörrel**rendelkeznek.

Platform | Minta |
 -------- | ------------------- |
![Ez a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4,5 | [DotNet-WebApp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [DotNet-WebApp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Azure AD- **szerepköröket** használó .net 4,5 MVC-alapú webalkalmazások engedélyezéséhez

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Microsoft Graph vagy webes API-t hívó asztali és mobil nyilvános ügyfélalkalmazások

A következő minták olyan nyilvános ügyfélalkalmazások (iskolapad/pmobile-alkalmazások) szemléltetését mutatják be, amelyek a felhasználó nevében férnek hozzá a Microsoft Graphhoz vagy egy webes API-hoz. Az eszközöktől és a platformoktól függően az alkalmazások különböző módokon jelentkezhetnek be a felhasználókba (folyamatok/támogatások):

- Interaktív módon
- Csendes (integrált Windows-hitelesítéssel Windows rendszeren vagy felhasználónév/jelszó)
- Ha az interaktív bejelentkezést egy másik eszközre delegálja (az eszköz kódját olyan eszközökön használják, amelyek nem biztosítanak webes vezérlőket)

Ügyfélalkalmazás | Platform | Folyamat/támogatás | Hívások Microsoft Graph | ASP.NET vagy ASP.NET Core 2. x webes API meghívása
------------------ | -------- | ---------- | -------------------- | -------------------------
Asztali (WPF)           | ![Ez a rendszerkép a .NET/C# emblémát jeleníti meg](media/sample-v2-code/logo_NET.png)  | Interaktív | A [DotNet-natív-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) része | [DotNet – natív – asztali](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [DotNet – natív – aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [DotNet-webapi-Manual-JWT-validate](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobil (UWP)            | .![Ez a rendszerkép a .NET/C#/UWP jeleníti meg](media/sample-v2-code/logo_Windows.png)   | Interaktív | [DotNet-Native-uwp-WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Ez a példa a [WAM](/windows/uwp/security/web-account-manager)-t használja, nem [ADAL.net](https://aka.ms/adalnet)|  [DotNet – Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (UWP alkalmazás a ADAL.NET használatával egyetlen bérlői webes API meghívásához) </p> [DotNet-webapi-több-bérlős-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (UWP-alkalmazás a ADAL.NET használatával egy több-bérlős webes API meghívásához)|
Mobil (Android, iOS, UWP)   | ![Ez a képen a .NET/C# (Xamarin) látható](media/sample-v2-code/logo_xamarin.png) | Interaktív | [DotNet – natív – többcél](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobil (Android)           | ![Ez a rendszerkép az Android-emblémát jeleníti meg](media/sample-v2-code/logo_Android.png) | Interaktív |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobil (iOS)           | ![A képen az iOS/Objective C vagy SWIFT látható](media/sample-v2-code/logo_iOS.png) | Interaktív |   [nativeClient – iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Asztal (konzol)          | ![Ez a rendszerkép a .NET/C# emblémát jeleníti meg](media/sample-v2-code/logo_NET.png) | Felhasználónév/jelszó </p>  Integrált Windows-hitelesítés | | [DotNet – natív – fej nélküli](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Asztal (konzol)          | ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_Java.png) | Felhasználónév/jelszó | | [Java – natív – fej nélküli](https://github.com/Azure-Samples/active-directory-java-native-headless)
Asztal (konzol)           | ![Ez a rendszerkép a .NET Core/C# emblémát jeleníti meg](media/sample-v2-code/logo_NETcore.png) | Eszköz kódjának folyamata | | [DotNet – deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon-alkalmazások (webes API-k elérése az alkalmazás identitásával)

A következő minták olyan asztali vagy webalkalmazásokat mutatnak be, amelyek hozzáférnek a Microsoft Graphhoz vagy egy webes API-hoz, amely nem rendelkezik felhasználóval (az alkalmazás identitásával).

Ügyfélalkalmazás | Platform | Folyamat/támogatás | ASP.NET vagy ASP.NET Core 2,0 webes API meghívása
------------------ | -------- | ---------- | -------------------- 
Daemon-alkalmazás (konzol)          | ![Ez a rendszerkép a .NET-emblémát jeleníti meg](media/sample-v2-code/logo_NETframework.png) | Ügyfél hitelesítő adatai az alkalmazás titkos kódjával vagy tanúsítvánnyal | [DotNet-Daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [DotNet-Daemon-Certificate-hitelesítőadat](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Daemon-alkalmazás (konzol)         | ![Ez a rendszerkép a .NET-emblémát jeleníti meg](media/sample-v2-code/logo_NETcore.png) | Ügyfél hitelesítő adatai tanúsítvánnyal| [dotnetcore-Daemon-Certificate-hitelesítőadat](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET-webalkalmazás  | ![Ez a rendszerkép a .NET-emblémát jeleníti meg](media/sample-v2-code/logo_NETframework.png) | Ügyfél-hitelesítő adatok | [DotNet-WebApp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webes API-k

### <a name="web-api-protected-by-azure-active-directory"></a>A Azure Active Directory által védett webes API

Az alábbi példa bemutatja, hogyan védhető a Node. js webes API-k az Azure AD-vel.

A cikk előző részeiben más mintákat is megtalálhat, amelyek egy ASP.NET vagy ASP.NET Core **webes API**-t **hívó** ügyfélalkalmazás bemutatnak. Ezek a minták még nem szerepelnek ebben a szakaszban, de a fenti táblázatok utolsó oszlopában is megtalálhatók.

| Platform | Minta |
|--------|-------------------|
| ![Ez a képen a Node. js-embléma látható](media/sample-v2-code/logo_nodejs.png)  | [csomópont – webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webes API meghívása Microsoft Graph vagy egy másik webes API

Az alábbi példák egy webes API-t mutatnak be, amely egy másik webes API-t hív meg. A második minta bemutatja, hogyan kezelheti a feltételes hozzáférést.

| Platform |  Hívások Microsoft Graph | Egy másik ASP.NET vagy ASP.NET Core 2,0 webes API meghívása |
| -------- |  --------------------- | ------------------------- |
| ![Ez a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4,5 | [DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [DotNet-webapi-onbehalfof-CA](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [DotNet-webapi-onbehalfof-CA](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Egyéb Microsoft Graph minták

A Microsoft Graph API különböző használati mintáit bemutató minták és oktatóanyagok esetében, beleértve az Azure AD-vel történő hitelesítést, lásd: [Microsoft Graph közösségi minták & oktatóanyagok](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Lásd még:

[Azure Active Directory fejlesztői útmutató](v1-overview.md)

[Azure Active Directory hitelesítési kódtárak](active-directory-authentication-libraries.md)

[Az Azure AD Graph API fogalma és referenciája](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API segítő könyvtár](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

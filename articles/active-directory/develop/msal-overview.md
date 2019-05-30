---
title: További tudnivalók a Microsoft-hitelesítési tár (MSAL) |} Az Azure
description: A Microsoft-hitelesítési tár (MSAL) lehetővé teszi az alkalmazásfejlesztők annak érdekében, hogy a védett webes API-jainak hívására jogkivonatok beszerzéséhez. Webes API-k a Microsoft Graph, más Microsoft APIS, külső webes API-k vagy saját webes API-t is lehet. Az MSAL több architektúrák és platformokat támogatja.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bd3e7d47b6e3083af6f388a5cd750da240a76b6
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392880"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>A Microsoft-hitelesítési tár (MSAL) áttekintése
A Microsoft-hitelesítési tár (MSAL) lehetővé teszi a fejlesztők beszerezni [jogkivonatok](developer-glossary.md#security-token) a Microsoft identity platform végpont eléréséhez a védett webes API-k. Webes API-k a Microsoft Graph, más Microsoft APIS, külső webes API-k vagy saját webes API-t is lehet. Az MSAL érhető el .NET, JavaScript, Android és iOS-, amely számos különböző architektúrák és platformokat támogatja.

Az MSAL lekérni a jogkivonatokat, a különböző platformokhoz egységes API többféle lehetőséget kínál. Az MSAL használatával a következő előnyökkel jár:

* Nem kell közvetlenül az OAuth-kódtárak vagy a protokoll kódot használhatja az alkalmazásban.
* Jogkivonatok nevében egy alkalmazás (ha alkalmazható a platform) vagy a felhasználó nevében beszerzi.
* Token gyorsítótárazza, és frissíti az Ön számára jogkivonatok, amelyek közel jár le. Nem kell saját jogkivonat lejárati kezelni.
* Adja meg, mely a célközönség az alkalmazásban (a szervezeti, több cégek vagy intézmények, munkahelyi, és az iskolai és személyes Microsoft-fiókok esetében közösségi identitások az Azure AD B2C felhasználóinak független, és az országos felhők) segítségével.
* Állítsa be a konfigurációs fájlokat az alkalmazás segítségével.
* Az alkalmazás hibaelhárítása teszi elérhetővé a gyakorlatban hasznosítható kivételek, a naplózás és a telemetriai adatok segítségével.

## <a name="application-types-and-scenarios"></a>Alkalmazástípusok és forgatókönyvek
Az MSAL használatával, egy jogkivonat szerezhetők be számos különböző alkalmazástípussal: webes alkalmazások, webes API-kat, egylapos alkalmazások (JavaScript), mobil- és natív alkalmazások, és démonok és kiszolgálóoldali alkalmazásoknak. 

Az MSAL használható számos alkalmazás-forgatókönyvek, beleértve a következőket:

* [Egylapos alkalmazások (JavaScript)](scenario-spa-overview.md) 
* [Bejelentkezés a felhasználók a webes alkalmazás](scenario-web-app-sign-user-overview.md)
* [Webes alkalmazás a felhasználó bejelentkezik, és a felhasználó nevében a webes API meghívása](scenario-web-app-call-api-overview.md)
* [Webes API-k védelme csak a hitelesített felhasználók hozzá tud férni](scenario-protected-web-api-overview.md)
* [Webes API meghívása egy másik alsóbb rétegbeli webes API-t a bejelentkezett felhasználó nevében](scenario-web-api-call-api-overview.md)
* [Asztali alkalmazás hívja a webes API a bejelentkezett felhasználó nevében](scenario-desktop-overview.md)
* [Webes API meghívása interaktív módon van bejelentkezett felhasználó nevében mobilalkalmazás](scenario-mobile-overview.md).
* [Asztali/szolgáltatás démon alkalmazás saját maga nevében webes API meghívása](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Nyelvek és keretrendszerek

| Erőforrástár | Támogatott platformok és keretrendszerek|
| --- | --- | 
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/>[MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET-keretrendszer, a .NET Core, Xamarin Android, Xamarin iOS-, univerzális Windows-Platform|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/>[MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/TypeScript keretrendszerek, például az AngularJS, az Ember.js vagy Durandal.js|
| ![Android az MSAL](media/sample-v2-code/logo_Android.png) <br/>[Az MSAL az Androidhoz (előzetes verzió)](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| ![Az MSAL iOS-hez](media/sample-v2-code/logo_iOS.png) <br/>[MSAL.Objective-C (preview)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS|

## <a name="differences-between-adal-and-msal"></a>Adal-t és az MSAL közötti különbségek
Az Active Directory Authentication Library (ADAL) integrálható az Azure AD fejlesztőknek (1.0-s verzió) végponton, ahol a MSAL integrálható a Microsoft identity platform (2.0-s verzió) végponthoz. Az 1.0-s verziójú végpont munkahelyi fiókokat, de nem személyes fiókokat támogatja. A v2.0-végpont egyetlen hitelesítési rendszerben egyesíti a személyes és munkahelyi fiókokat. Ezenkívül az MSAL is kaphat az Azure AD B2C hitelesítések.

További információkért olvassa el [MSAL.NET való áttérés ADAL.NET](msal-net-migration.md) és [MSAL.js való áttérés ADAL.js](msal-compare-msal-js-and-adal-js.md).

            
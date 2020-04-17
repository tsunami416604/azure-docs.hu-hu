---
title: További információ az MSAL | Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazásfejlesztők számára, hogy jogkivonatokat szerezzenek be a biztonságos webes API-k hívásához. Ezek a webes API-k lehetnek a Microsoft Graph, más Microsoft API-k, külső webes API-k vagy a saját webes API-k. Az MSAL több alkalmazásarchitektúrát és platformot támogat.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 89c5117e59f7856c3bd572bbea297a836b5ad589
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536216"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>A Microsoft hitelesítési könyvtár (MSAL) áttekintése
A Microsoft Authentication Library (MSAL) lehetővé teszi a fejlesztők számára, hogy [jogkivonatokat szerezzenek](developer-glossary.md#security-token) be a Microsoft identity platform végpontjáról a biztonságos webes API-k elérése érdekében. Ezek a webes API-k lehetnek a Microsoft Graph, más Microsoft API-k, külső webes API-k vagy a saját webes API-k. Az MSAL .NET, JavaScript, Android és iOS rendszerekhez érhető el, amelyek számos különböző alkalmazásarchitektúrát és platformot támogatnak.

Az MSAL számos lehetőséget biztosít a tokenek beszerzésére, számos platformkkon konzisztens API-val. Az MSAL használata a következő előnyöket biztosítja:

* Nem kell közvetlenül használni a OAuth könyvtárak vagy kód ellen a protokoll az alkalmazásban.
* Jogkivonatok at szerez be egy felhasználó vagy egy alkalmazás nevében (ha az alkalmazható a platformra).
* Jogkivonat-gyorsítótárat tart fenn, és frissíti a tokeneket, amikor azok hamarosan lejárnak. Nem kell kezelni a token lejárati saját.
* Segítségével megadhatja, hogy az alkalmazás melyik célközönségbe jelentkezzen be (a szervezet, több szervezeti, munkahelyi és iskolai és Microsoft-személyes fiók, az Azure AD B2C-vel rendelkező közösségi identitások, a szuverén és a nemzeti felhők felhasználói).
* Segít beállítani az alkalmazást a konfigurációs fájlokból.
* Segít az alkalmazás hibaelhárításához a végrehajtható kivételek, a naplózás és a telemetriai adatok felfedésével.

## <a name="application-types-and-scenarios"></a>Alkalmazástípusok és forgatókönyvek
Az MSAL használatával egy jogkivonat számos alkalmazástípusból szerezhető be: webalkalmazásokból, webes API-kból, egyoldalas alkalmazásokból (JavaScript), mobil és natív alkalmazásokból, valamint démonokból és kiszolgálóoldali alkalmazásokból.

Az MSAL számos alkalmazási forgatókönyvben használható, többek között a következőkben:

* [Egyoldalas alkalmazások (JavaScript)](scenario-spa-overview.md)
* [Webalkalmazás-bejelentkezés a felhasználókközött](scenario-web-app-sign-user-overview.md)
* [A webalkalmazás bejelentkezése egy felhasználóba és webes API hívása a felhasználó nevében](scenario-web-app-call-api-overview.md)
* [Webes API védelme, hogy csak a hitelesített felhasználók férhessenek hozzá](scenario-protected-web-api-overview.md)
* [Egy másik alsóbb rétegbeli webes API-t hívmeg a bejelentkezett felhasználó nevében](scenario-web-api-call-api-overview.md)
* [Webes API-hívása a bejelentkezett felhasználó nevében](scenario-desktop-overview.md)
* [A webes API-t interaktívan bejelentkezett felhasználó nevében webes API-t hív meg.](scenario-mobile-overview.md)
* [Asztali/szolgáltatás démonalkalmazás, amely saját nevében hívja meg a webes API-t](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Nyelvek és keretek

| Erőforrástár | Támogatott platformok és keretrendszerek|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Univerzális Windows platform|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/TypeScript keretrendszerek, például AngularJS, Ember.js vagy Durandal.js|
| [MSAL Androidra](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL iOS és macOS rendszerekre](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS és macOS|
| [MSAL Java (előzetes verzió)](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|
| [MSAL Python (előzetes verzió)](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Python|

## <a name="differences-between-adal-and-msal"></a>Az ADAL és az MSAL közötti különbségek

Az Active Directory hitelesítési könyvtár (ADAL) integrálható az Azure AD fejlesztőknek (1.0-s verzió) végponttal, ahol az MSAL integrálható a Microsoft identity platform (2.0- as verzió) végponttal. A v1.0-s végpont támogatja a munkahelyi fiókokat, de a személyes fiókokat nem. A 2.0-s verzióvégpont a Microsoft személyes fiókok és a munkahelyi fiókok egyetlen hitelesítési rendszerbe való egyesítése. Emellett az MSAL-lal is kaphat hitelesítéseket az Azure AD B2C-hez.

További információt a [ADAL.NET-ről MSAL.NET és az](msal-net-migration.md) [ADAL.js-ből az MSAL.js-re való áttérésről](msal-compare-msal-js-and-adal-js.md)talál.

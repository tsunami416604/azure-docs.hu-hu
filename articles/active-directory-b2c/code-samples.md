---
title: Azure Active Directory B2C-mintakódok | Microsoft Docs
description: Kódminták mobil, asztali, webes és egyoldalas Active Directory B2C-alkalmazásokhoz.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 76cacbea3f98f65e290cb2722062127d2264e171
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91664834"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Azure Active Directory B2C-mintakódok

Az alábbi táblázat többek között iOS-, Android-, .NET- és Node.js-mintaalkalmazásokra mutató hivatkozásokat tartalmaz.

## <a name="mobile-and-desktop-apps"></a>Mobil- és asztali alkalmazások

| Sample | Leírás |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Swift nyelvű iOS-minta, amely az Azure AD B2C-felhasználókat hitelesíti, és meghív egy API-t az OAuth 2.0 használatával. |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | Egy egyszerű Android-alkalmazás, amely azt mutatja be, hogyan használható az MSAL felhasználók hitelesítéséhez az Azure Active Directory B2C segítségével, és hogyan lehet hozzáférni egy webes API-hoz az eredményül kapott jogkivonatokkal. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Ez a minta azt mutatja be, hogyan használhatók a külső fejlesztőktől származó kódtárak egy olyan iOS-alkalmazás létrehozásához az Objective-C-ben, amely Microsoft identitás-felhasználókat hitelesít az Azure AD B2C identitásszolgáltatáshoz. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Ez a minta azt mutatja be, hogyan használhatók a külső fejlesztőktől származó kódtárak egy olyan Android-alkalmazás létrehozásához, amely Microsoft identitás-felhasználókat hitelesít a B2C identitásszolgáltatáshoz, és meghív egy webes API-t OAuth 2.0 hozzáférési jogkivonatok használatával. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Ez a minta azt mutatja be, hogyan tud egy Windows asztali .NET- (WPF-) alkalmazás bejelentkeztetni egy felhasználót az Azure AD B2C-vel, hogyan szerezhet be egy hozzáférési jogkivonatot az MSAL.NET használatával, és hogyan hívhat meg egy API-t. |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Egy egyszerű Xamarin Forms-alkalmazás, amely azt mutatja be, hogyan használható az MSAL felhasználók hitelesítéséhez az Azure Active Directory B2C segítségével, és hogyan lehet hozzáférni egy webes API-hoz az eredményül kapott jogkivonatokkal. |

## <a name="web-apps-and-apis"></a>Webalkalmazások és API-k

| Sample | Leírás |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Összetett minta egy .NET-webalkalmazáshoz, amely egy .NET-es webes API-t hív meg, és mindkettő védelméről az Azure AD B2C gondoskodik. |
| [dotnetcore-WebApp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | Egy ASP.NET Core webalkalmazás, amely OpenID-kapcsolatot használ a felhasználók bejelentkezési Azure AD B2Cba való bekapcsolásához. |
| [dotnetcore-WebApp-msal-API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Egy ASP.NET Core-webalkalmazás, amely felhasználókat jelentkeztethet be az Azure AD B2C-vel, hozzáférési jogkivonatot szerez be az MSAL.NET használatával, és meghív egy API-t. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | Egy olyan Node.js-alkalmazás, amellyel gyorsan és egyszerűen állítható be egy Express-webalkalmazás az OpenID Connect használatával. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Az Azure AD B2C kis méretű Node.js webes API-ja, amely bemutatja, hogyan lehet gondoskodni a webes API védelméről, és hogyan fogadhatók el a B2C hozzáférési jogkivonatok a passport.js használatával. |
| [MS-Identity-Python-WebApp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | Bemutatjuk, hogyan integrálhatja a Microsoft Identity platform B2C-t egy Python-webalkalmazással.  |

## <a name="single-page-apps"></a>Egylapos appok

| Sample | Leírás |
|--------| ----------- |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Webes API-t meghívó egyoldalas alkalmazás (single-page application, SPA). A hitelesítés Azure AD B2C MSAL.js használatával történik. |

## <a name="saml-test-application"></a>SAML-teszt alkalmazás

| Sample | Leírás |
|--------| ----------- |
| [SAML-SP-Tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | SAML-tesztelési alkalmazás, amely SAML-identitás-szolgáltatóként való kiAzure AD B2Cre van konfigurálva. |

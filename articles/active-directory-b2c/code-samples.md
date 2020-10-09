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
ms.openlocfilehash: 63a79df8e540e88d0f1e3f4c5b8d339ff03c877f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91854290"
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

## <a name="api-connectors"></a>API-összekötők

Az alábbi táblázatok a felhasználói folyamatokban lévő webes API-k [API-összekötők](api-connectors-overview.md)használatával történő kihasználására szolgáló kódrészletekre mutató hivatkozásokat tartalmaznak.

## <a name="api-connector-azure-function-quickstarts"></a>API-összekötő Azure-függvény rövid útmutatói

| Sample                                                                                                                          | Leírás                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Ez a .NET Core Azure Function minta azt mutatja be, hogyan lehet korlátozni a regisztrációt adott e-mail tartományokra, és érvényesíteni a felhasználó által megadott adatokat. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Ez a Node.js Azure Function minta azt mutatja be, hogyan lehet korlátozni a regisztrációt adott e-mail tartományokra, és érvényesíteni a felhasználó által megadott adatokat.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Ez a Python Azure Function minta azt mutatja be, hogyan lehet korlátozni a regisztrációkat adott e-mail tartományokra, és érvényesíteni a felhasználó által megadott adatokat.    |
---
title: Az API-hitelesítés ismertetése - Azure Digital Twins | Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakozhat api-khoz, és hogyan hitelesítheti magát api-kkal az Azure Digital Twins használatával.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: d950d41186d578702343645875dd7c565002d5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512999"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Csatlakozás API-khoz és hitelesítés

Az Azure Digital Twins az Azure Active Directory (Azure AD) segítségével hitelesíti a felhasználókat és védi az alkalmazásokat. Az Azure AD számos modern architektúra hitelesítését támogatja. Mindegyik az OAuth 2.0 vagy az OpenID Connect szabványprotokollokon alapul. Emellett a fejlesztők az Azure AD segítségével egy-bérlős és üzletági (LOB) alkalmazások at hozhatnak létre. A fejlesztők az Azure AD segítségével is fejleszthetnek [több-bérlős alkalmazásokat.](how-to-multitenant-applications.md)

Az Azure AD áttekintéséhez keresse fel az [alapokat tartalmazó lapon,](https://docs.microsoft.com/azure/active-directory/fundamentals/) ahol részletes útmutatókat, fogalmakat és rövid útmutatókat talál.

> [!TIP]
> Kövesse az [oktatóanyagot](tutorial-facilities-setup.md) egy Azure Digital Twins mintaalkalmazás beállításához és futtatásához.

Az alkalmazás vagy szolgáltatás az Azure AD-vel történő integrálásához a fejlesztőnek először regisztrálnia kell az alkalmazást az Azure AD-ben. Részletes útmutatásért és képernyőképekért olvassa el [ezt a rövid útmutatót.](../active-directory/develop/quickstart-register-app.md)

Az Azure AD [öt elsődleges alkalmazásforgatókönyvet](../active-directory/develop/v2-app-types.md) támogat:

* Egyoldalas alkalmazás (SPA): A felhasználónak be kell jelentkeznie egy egyoldalas alkalmazásba, amelyet az Azure AD biztosít.
* Webböngésző a webalkalmazás: A felhasználónak be kell jelentkeznie egy webalkalmazás, amely az Azure AD által védett.
* Natív alkalmazás webes API: Egy natív alkalmazás, amely fut a telefon, táblagép vagy pc kell hitelesíteni a felhasználót, hogy az erőforrásokat egy webes API-t, amely az Azure AD által védett.
* Webalkalmazás web API-hoz: Egy webalkalmazás kell erőforrásokat beszerezni egy webes API-t az Azure AD által védett.
* Démon vagy kiszolgálóalkalmazás web API: Egy démon-alkalmazás vagy egy webes felhasználói felület nélküli kiszolgálóalkalmazásnak erőforrásokat kell beszereznie az Azure AD által védett webes API-ból.

> [!IMPORTANT]
> Az Azure Digital Twins mindkét alábbi hitelesítési kódtárat támogatja:
> * A legújabb [Microsoft authentication library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * Az [Azure Active Directory hitelesítési könyvtára (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Digitális twins hívása egy középső rétegű webes API-ból

Amikor a fejlesztők digitális twins-megoldásokat terveznek, általában egy középső rétegű alkalmazást vagy API-t hoznak létre. Az alkalmazás vagy API ezután meghívja a Digital Twins API-t a későbbi folyamatokban. A szabványos webes megoldásarchitektúra támogatásához először győződjön meg arról, hogy a felhasználók:

1. Hitelesítés a középső rétegbeli alkalmazással

1. Az OAuth 2.0 A token nevében a hitelesítés során beszerzi az OAuth 2.0-t

1. A beszerzett jogkivonatezután hitelesítésre szolgál, vagy hívja api-k, amelyek tovább lejjebb a nevében folyamat

A folyamat nevében történő vezénylésével kapcsolatos utasításokért olvassa el az [OAuth 2.0 A folyamat nevében című útmutatót.](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) A kódmintákat [az alsóbb rétegbeli webes API hívása](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)mezőben is megtekintheti.

## <a name="next-steps"></a>További lépések

Az Azure Digital Twins konfigurálásához és teszteléséhez az OAuth 2.0 implicit támogatási folyamat használatával olvassa el [a Postman konfigurálása](./how-to-configure-postman.md)című olvasni.

Ha többet szeretne megtudni az Azure Digital Twins biztonságáról, olvassa el [a Szerepkör-hozzárendelések létrehozása és kezelése](./security-create-manage-role-assignments.md)című olvasni.

---
title: Webes API-kat hívó démonalkalmazások regisztrálása – Microsoft identity platform | Azure
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívja a démonalkalmazásokat – alkalmazásregisztráció
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 922a484d111746e5073c08a64d7c92e2b6b4a7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773398"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>A webes API-kat meghívjad Daemon alkalmazás – alkalmazásregisztráció

Démonalkalmazás esetén az alábbiakat kell tudnia az alkalmazás regisztrálásakor.

## <a name="supported-account-types"></a>Támogatott fióktípusok

A démonalkalmazásoknak csak az Azure AD-bérlőkben van értelme. Így amikor létrehozza az alkalmazást, ki kell választania az alábbi lehetőségek közül:

- **Csak ebben a szervezeti címtárban lévő fiókok**. Ez a választás a leggyakoribb, mert a démonalkalmazásokat általában üzletági (LOB) fejlesztők írják.
- **Bármely szervezeti címtárban lévő fiókok**. Ezt a döntést akkor választhatja meg, ha független független vállalatnak ad segítséget ügyfeleinek. A jóváhagyáshoz az ügyfelek bérlői rendszergazdáinak kell lennie.

## <a name="authentication---no-reply-uri-needed"></a>Hitelesítés - nincs szükség válaszURI-ra

Abban az esetben, ha a bizalmas ügyfélalkalmazás *csak* az ügyfél hitelesítő adatok áramlását használja, a válasz URI-t nem kell regisztrálni. Ez nem szükséges az alkalmazás konfigurációjához vagy felépítéséhez. Az ügyfél hitelesítő adatai nem használják.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API-engedélyek – alkalmazásengedélyek és rendszergazdai jóváhagyás

Egy démonalkalmazás csak alkalmazásengedélyeket kérhet az API-khoz (delegált engedélyekhez nem). Az **alkalmazásregisztráció API-engedélyei** lapján, miután kiválasztotta az **Engedély hozzáadása** és az API-család kiválasztását, válassza az **Alkalmazásengedélyek**lehetőséget, majd válassza ki az engedélyeket.

![Alkalmazásengedélyek és rendszergazdai jóváhagyás](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> A hívni kívánt webes API-nak *alkalmazásengedélyeket (alkalmazásszerepköröket)* kell definiálnia, nem pedig delegált engedélyeket. Az ilyen API-k felfedésével kapcsolatos részletekért olvassa el [a Védett webes API: Alkalmazásregisztráció című témakört, amikor a webes API-t egy démonalkalmazás nevezi meg.](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)

A démonalkalmazások megkövetelik, hogy a bérlői rendszergazda előzetesen beleegyezését adja a webes API-t hívó alkalmazáshoz. A bérlői rendszergazdák ugyanezen az **API-engedélyoldalon** adják meg ezt a hozzájárulást a ** *Szervezetünknek* adott rendszergazdai hozzájárulás kiválasztásával.**

If you're an ISV building a multitenant application, you should read the section [Deployment - case of multitenant daemon apps](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Démon alkalmazás – alkalmazáskód-konfiguráció](./scenario-daemon-app-configuration.md)

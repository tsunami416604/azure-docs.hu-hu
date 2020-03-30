---
title: Webalkalmazások az Azure Active Directoryban
description: Bemutatja, hogy milyen webalkalmazások, valamint az alkalmazástípus protokollfolyamatának, regisztrációjának és tokenelévülésének alapjai.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154406"
---
# <a name="web-apps"></a>Webalkalmazások

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A webalkalmazások olyan alkalmazások, amelyek a webböngészőben hitelesítik a felhasználót egy webalkalmazásba. Ebben a forgatókönyvben a webalkalmazás irányítja a felhasználó böngészőjét, hogy jelentkezzen be az Azure AD-be. Az Azure AD egy bejelentkezési választ ad vissza a felhasználó böngészőjében keresztül, amely egy biztonsági jogkivonatban tartalmazza a felhasználóra vonatkozó jogcímeket. Ez a forgatókönyv támogatja az OpenID Connect, SAML 2.0 és WS-Federation protokollok használatával történő bejelentkezést.

## <a name="diagram"></a>Ábra

![A böngésző és a webalkalmazás hitelesítési folyamata](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Protokoll-folyamat

1. Amikor egy felhasználó felkeresi az alkalmazást, és be kell jelentkeznie, a rendszer átirányítja őket egy bejelentkezési kérelemen keresztül az Azure AD hitelesítési végpontjára.
1. A felhasználó bejelentkezik a bejelentkezési oldalon.
1. Ha a hitelesítés sikeres, az Azure AD létrehoz egy hitelesítési jogkivonatot, és egy bejelentkezési választ ad vissza az alkalmazás Válasz URL-címére, amely az Azure Portalon konfigurálva volt. Éles alkalmazás esetén ez a válasz URL-cím kell HTTPS. A visszaadott jogkivonat tartalmazza a felhasználó és az Azure AD, amelyek az alkalmazás által a jogkivonat érvényesítéséhez szükséges jogcímek.
1. Az alkalmazás érvényesíti a jogkivonatot egy nyilvános aláíró kulcs és a kiállító i. az Azure AD összevonási metaadat-dokumentumban elérhető információk használatával. Miután az alkalmazás érvényesíti a jogkivonatot, új munkamenetet indít el a felhasználóval. Ez a munkamenet lehetővé teszi a felhasználó számára, hogy hozzáférjen az alkalmazáshoz, amíg le nem jár.

## <a name="code-samples"></a>Kódminták

Tekintse meg a kódmintákat a webböngésző webalkalmazás-forgatókönyvek. És, látogasson vissza gyakran az új mintákat adnak gyakran.

## <a name="app-registration"></a>Alkalmazásregisztráció

Webalkalmazás regisztrálásához olvassa el [az Alkalmazás regisztrálása témakört.](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

* Egyetlen bérlő – Ha csak a szervezet számára hoz létre alkalmazást, az Azure Portal használatával regisztrálnikell a vállalati címtárban.
* Több-bérlős – Ha olyan alkalmazást hoz össze, amelyet a szervezeten kívüli felhasználók is használhatnak, akkor azt regisztrálni kell a vállalat címtárában, de regisztrálni kell minden egyes szervezet címtárában, amely az alkalmazást fogja használni. Ahhoz, hogy az alkalmazás elérhető vé válik a címtárban, megadhat egy regisztrációs folyamatot az ügyfelek számára, amely lehetővé teszi számukra, hogy hozzájáruljanak az alkalmazáshoz. Amikor regisztrálnak az alkalmazásra, megjelenik egy párbeszédablak, amely megjeleníti az alkalmazás által igényelt engedélyeket, majd a hozzájárulás lehetőségét. A szükséges engedélyektől függően előfordulhat, hogy a másik szervezet rendszergazdájának kell beleegyezést adnia. Amikor a felhasználó vagy a rendszergazda hozzájárul, az alkalmazás regisztrálva lesz a címtárban.

## <a name="token-expiration"></a>Token lejárata

A felhasználó munkamenete akkor jár le, amikor az Azure AD által kiadott jogkivonat élettartama lejár. Az alkalmazás lerövidítheti ezt az időszakot, ha szükséges, például a felhasználók kijelentkezése egy inaktív időszak alapján. Amikor a munkamenet lejár, a rendszer kéri a felhasználót, hogy jelentkezzen be újra.

## <a name="next-steps"></a>További lépések

* További információ az [egyéb alkalmazástípusokról és -forgatókönyvekről](app-types.md)
* Ismerje meg az Azure [AD-hitelesítés alapjait](v1-authentication-scenarios.md)

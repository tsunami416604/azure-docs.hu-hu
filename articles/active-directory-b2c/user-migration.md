---
title: Felhasználói Migrálás módszerei
titleSuffix: Azure AD B2C
description: Áttelepítheti a felhasználói fiókokat egy másik identitás-szolgáltatótól a tömeges importálás vagy a zökkenőmentes áttelepítési módszerek használatával Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 90ef9402e0891915be4ed6bb89573eced546c59a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183142"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Felhasználók migrálása Azure AD B2Cre

A másik identitás-szolgáltatóról Azure Active Directory B2C (Azure AD B2C) való Migrálás a meglévő felhasználói fiókok áttelepítését is szükségessé teheti. Itt két áttelepítési módszert tárgyalunk, a *tömeges importálást* és a *zökkenőmentes áttelepítést*. Mindkét módszer esetében olyan alkalmazást vagy parancsfájlt kell írnia, amely a [Microsoft Graph API](manage-user-accounts-graph-api.md) -t használja a felhasználói fiókok létrehozásához Azure ad B2Cban.

## <a name="bulk-import"></a>Tömeges importálás

A tömeges importálási folyamat során az áttelepítési alkalmazás végrehajtja ezeket a lépéseket minden felhasználói fiókhoz:

1. Olvassa el a felhasználói fiókot a régi identitás-szolgáltatótól, beleértve az aktuális hitelesítő adatait (Felhasználónév és jelszó).
1. Hozzon létre egy megfelelő fiókot a Azure AD B2C címtárban az aktuális hitelesítő adatokkal.

A tömeges importálási folyamat mindkét esetben a következő két helyzetben használható:

- Hozzáférése van a felhasználó egyszerű szöveges hitelesítő adataihoz (a felhasználónevet és a jelszót).
- A hitelesítő adatok titkosítva vannak, de visszafejtheti őket.

További információ a felhasználói fiókok programozott létrehozásáról: [Azure ad B2C felhasználói fiókok kezelése Microsoft Graph](manage-user-accounts-graph-api.md)használatával.

## <a name="seamless-migration"></a>Zökkenőmentes áttelepítés

Ha a régi identitás-szolgáltató szöveges jelszavai nem érhetők el, használja a zökkenőmentes áttelepítési folyamatot. Például:

- A jelszót egy egyirányú titkosított formátumban, például egy kivonatoló függvénnyel tárolja a rendszer.
- A jelszót a régi identitás-szolgáltató tárolja oly módon, hogy nem férhet hozzá. Ha például az identitás-szolgáltató egy webszolgáltatás meghívásával érvényesíti a hitelesítő adatokat.

A zökkenőmentes áttelepítési folyamat továbbra is a felhasználói fiókok tömeges áttelepítését igényli, azonban egy [Egyéni szabályzattal](restful-technical-profile.md) kérdez le egy [REST API](rest-api-claims-exchange-dotnet.md) (amelyet létrehoz) az egyes felhasználók jelszavának az első bejelentkezéskor történő megadásához.

A zökkenőmentes áttelepítési folyamatnak két fázisa van: a *tömeges importálás* és a *hitelesítő adatok beállítása*.

### <a name="phase-1-bulk-import"></a>1\. fázis: tömeges importálás

1. Az áttelepítési alkalmazás beolvassa a felhasználói fiókokat a régi identitás-szolgáltatótól.
1. Az áttelepítési alkalmazás a megfelelő felhasználói fiókokat hozza létre a Azure AD B2C könyvtárban, de *nem állítja be a jelszavakat*.

### <a name="phase-2-set-credentials"></a>2\. fázis: hitelesítő adatok beállítása

A fiókok tömeges áttelepítése után az egyéni házirend és a REST API akkor hajtsa végre a következőket, amikor egy felhasználó bejelentkezik:

1. Olvassa el a megadott e-mail-címre vonatkozó Azure AD B2C felhasználói fiókot.
1. A logikai bővítmény attribútum kiértékelésével győződjön meg arról, hogy a fiók az áttelepítésre van-e megjelölve.
    - Ha a bővítmény attribútum `True`ad vissza, hívja meg a REST API, hogy érvényesítse a jelszót az örökölt identitás-szolgáltatón.
      - Ha a REST API határozza meg, hogy a jelszó helytelen, egy felhasználóbarát hibát ad vissza a felhasználónak.
      - Ha a REST API határozza meg a jelszót, írja be a jelszót a Azure AD B2C-fiókba, és módosítsa a logikai bővítmény attribútumát `False`re.
    - Ha a logikai bővítmény attribútuma `False`ad vissza, folytassa a bejelentkezési folyamatot a szokásos módon.

Az egyéni szabályzatok és REST APIek megjelenítéséhez tekintse meg a [zökkenőmentes felhasználói áttelepítési mintát](https://aka.ms/b2c-account-seamless-migration) a githubon.

a felhasználók áttelepítésének zökkenőmentes áttelepítési módszerét ![folyamatábra-diagram](./media/user-migration/diagram-01-seamless-migration.png)<br />*Ábra: zökkenőmentes áttelepítési folyamat*

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="security"></a>Biztonság

A zökkenőmentes áttelepítési módszer a saját egyéni REST API használatával érvényesíti a felhasználó hitelesítő adatait az örökölt identitás-szolgáltatón.

**Meg kell védenie REST API a találgatásos támadásokkal szemben.** A támadók több jelszót is elküldhetnek abban a reményben, hogy végül kitalálják a felhasználó hitelesítő adatait. Az ilyen támadások legyőzéséhez állítsa le a kérések kézbesítését a REST API, ha a bejelentkezési kísérletek száma egy bizonyos küszöbértéket eredményez. A Azure AD B2C és a REST API közötti kommunikációt is biztonságossá teheti az [ügyféltanúsítvány](secure-rest-api-dotnet-certificate-auth.md)használatával.

### <a name="user-attributes"></a>Felhasználói attribútumok

A régi identitás-szolgáltató összes adatát át kell telepíteni a Azure AD B2C könyvtárba. Az áttelepítés előtt azonosítsa a Azure AD B2C tárolandó felhasználói attribútumok megfelelő készletét.

- **Tároló Azure ad B2C**
  - Felhasználónév, jelszó, e-mail-cím, telefonszámok, tagsági számok/azonosítók.
  - Az adatvédelmi szabályzat és a Végfelhasználói licencszerződések megjelölése.
- **Ne tárolja a** Azure ad B2C
  - Bizalmas adatok, például hitelkártyaszám, társadalombiztosítási számok (SSN), orvosi nyilvántartások vagy egyéb, kormányzati vagy iparági megfelelőségi szervek által szabályozott adatok.
  - Marketing-vagy kommunikációs beállítások, felhasználói viselkedés és bepillantást nyerhet.

### <a name="directory-clean-up"></a>Címtár törlése

Az áttelepítési folyamat megkezdése előtt végezze el a könyvtár törlésének lehetőségét.

- Azonosítsa a Azure AD B2C tárolandó felhasználói attribútumok készletét, és csak azt telepítse át, amire szüksége van. Ha szükséges, létrehozhat [Egyéni attribútumokat](custom-policy-custom-attributes.md) a felhasználóval kapcsolatos további információk tárolásához.
- Ha több hitelesítési forrásból származó környezetről végez áttelepítést (például az egyes alkalmazások saját felhasználói címtárral rendelkeznek), telepítse át a Azure AD B2C egyesített fiókjába.
- Ha több alkalmazásnak eltérő felhasználóneve van, akkor az identitások gyűjtemény használatával egy Azure AD B2C felhasználói fiókban tárolhatók. A jelszóval kapcsolatban hagyja, hogy a felhasználó válasszon egyet, és állítsa be a könyvtárba. A zökkenőmentes áttelepítéssel például csak a kiválasztott jelszót kell tárolni a Azure AD B2C fiókban.
- Távolítsa el a nem használt felhasználói fiókokat az áttelepítés előtt, vagy ne telepítse át az elavult fiókokat.

### <a name="password-policy"></a>Jelszóházirend

Ha az áttelepíteni kívánt fiókok gyengébb jelszóval rendelkeznek, mint az Azure AD B2C által kényszerített [erős jelszó](../active-directory/authentication/concept-sspr-policy.md) erőssége, letilthatja a jelszó erős követelményét. További információ: [jelszóházirend tulajdonsága](manage-user-accounts-graph-api.md#password-policy-property).

## <a name="next-steps"></a>További lépések

Az [Azure-ad-B2C/felhasználó-áttelepítési](https://github.com/azure-ad-b2c/user-migration) adattár a githubon egy zökkenőmentes áttelepítési egyéni házirendet tartalmaz, és REST API a kód minta:

[Zökkenőmentes felhasználói áttelepítési egyéni házirend & REST API mintakód](https://aka.ms/b2c-account-seamless-migration)

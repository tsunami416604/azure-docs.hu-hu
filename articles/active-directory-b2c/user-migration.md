---
title: Felhasználói áttelepítési megközelítések
titleSuffix: Azure AD B2C
description: Felhasználói fiókok áttelepítése egy másik identitásszolgáltatótól az Azure AD B2C-be a tömeges importálási vagy zökkenőmentes áttelepítési módszerek használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b3ee069985fd39288a562d3caafc50b12290c060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332334"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Felhasználók áttelepítése az Azure AD B2C szolgáltatásba

Más identitásszolgáltatóról az Azure Active Directory B2C -be (Azure AD B2C) való áttelepítése a meglévő felhasználói fiókok áttelepítését is szükségessé teheti. Itt két áttelepítési módszert vitatunk meg, *a tömeges importálást* és *a zökkenőmentes áttelepítést.* Mindkét megközelítés, akkor kell írni egy alkalmazást vagy parancsfájlt, amely a [Microsoft Graph API-t](manage-user-accounts-graph-api.md) használja felhasználói fiókok létrehozásához az Azure AD B2C.With either approach, you're required to write an application or script that uses the Microsoft Graph API to create user accounts in Azure AD B2C.

## <a name="bulk-import"></a>Tömeges importálás

A tömeges importálási folyamat során az áttelepítési alkalmazás minden felhasználói fiókhoz végrehajtja a következő lépéseket:

1. Olvassa el a felhasználói fiókot a régi identitásszolgáltatótól, beleértve az aktuális hitelesítő adatokat (felhasználónév és jelszó).
1. Hozzon létre egy megfelelő fiókot az Azure AD B2C címtárban az aktuális hitelesítő adatokkal.

A tömeges importforgalmat a következő két helyzet valamelyikében használja:

- Hozzáférhet a felhasználó egyszerű szöveges hitelesítő adataihoz (a felhasználónevükhöz és a jelszavukhoz).
- A hitelesítő adatok titkosítva vannak, de visszafejthetők.

A felhasználói fiókok programozott létrehozásáról az [Azure AD B2C felhasználói fiókok kezelése a Microsoft Graph programmal című témakörben talál.](manage-user-accounts-graph-api.md)

## <a name="seamless-migration"></a>Zökkenőmentes áttelepítés

Használja a zökkenőmentes áttelepítési folyamatot, ha a régi identitásszolgáltató egyszerű szöveges jelszavai nem érhetők el. Például, ha:

- A jelszó egyirányú titkosított formátumban, például kivonatoló funkcióval van tárolva.
- A jelszót az örökölt identitásszolgáltató tárolja úgy, hogy nem érhető el. Például, ha az identitásszolgáltató ellenőrzi hitelesítő adatok at egy webszolgáltatás hívásával.

A zökkenőmentes áttelepítési folyamat továbbra is a felhasználói fiókok tömeges áttelepítését igényli, de ezután egy [egyéni házirend et](custom-policy-get-started.md) használ a REST [API](custom-policy-rest-api-intro.md) lekérdezéséhez (amelyet létrehoz), hogy beállítsa az egyes felhasználók jelszavát az első bejelentkezéskor.

A zökkenőmentes áttelepítési folyamat nak tehát két fázisa van: *tömeges importálás* és hitelesítő *adatok beállítása.*

### <a name="phase-1-bulk-import"></a>1. fázis: Tömeges importálás

1. Az áttelepítési alkalmazás beolvassa a felhasználói fiókokat a régi identitásszolgáltatótól.
1. Az áttelepítési alkalmazás létrehozza a megfelelő felhasználói fiókokat az Azure AD B2C könyvtárban, de *nem állít be jelszavakat.*

### <a name="phase-2-set-credentials"></a>2. fázis: Hitelesítő adatok beállítása

A fiókok tömeges áttelepítése után az egyéni házirend és a REST API a következőműveleteket hajtsa végre, amikor a felhasználó bejelentkezik:

1. Olvassa el az Azure AD B2C felhasználói fiókot, amely megfelel a megadott e-mail-címnek.
1. Ellenőrizze, hogy a fiók meg van-e jelölve az áttelepítéshez egy logikai bővítményattribútum kiértékelésével.
    - Ha a bővítmény `True`attribútum visszaadja, hívja meg a REST API-t a jelszó érvényesítéséhez az örökölt identitásszolgáltatóval szemben.
      - Ha a REST API megállapítja, hogy a jelszó helytelen, rövid hibát ad vissza a felhasználónak.
      - Ha a REST API helyesnek találja a jelszót, írja be a jelszót az Azure AD `False`B2C-fiókba, és módosítsa a logikai bővítmény attribútumát .
    - Ha a logikai bővítmény `False`attribútum a szokásos módon folytatja a bejelentkezési folyamatot.

Egy példa egyéni szabályzat és a REST API megtekintéséhez tekintse meg a [zökkenőmentes felhasználói áttelepítési minta](https://aka.ms/b2c-account-seamless-migration) a GitHubon.

![Folyamatábra a felhasználók áttelepítésének zökkenőmentes áttelepítési megközelítéséről](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagram: Zökkenőmentes áttelepítési folyamat*

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="security"></a>Biztonság

A zökkenőmentes áttelepítési megközelítés a saját egyéni REST API-t használja a felhasználó hitelesítő adatainak érvényesítéséhez az örökölt identitásszolgáltatóval szemben.

**Meg kell védenie a REST API-t a találgatásos támadások ellen.** A támadó több jelszót is beküldhet annak reményében, hogy végül kitalálja a felhasználó hitelesítő adatait. Az ilyen támadások elleni vereség érdekében állítsa le a rest API-nak érkező kérelmek kiszolgálását, ha a bejelentkezési kísérletek száma egy bizonyos küszöbértéket halad meg. Emellett biztonságossá kell tenni az Azure AD B2C és a REST API közötti kommunikációt. A RESTful API-k éles környezetben való védelméről a [Biztonságos RESTful API című témakörben](secure-rest-api.md)olvashat.

### <a name="user-attributes"></a>Felhasználói attribútumok

Nem minden információt az örökölt identitásszolgáltató át kell telepíteni az Azure AD B2C könyvtárba. Az áttelepítés előtt azonosítsa az Azure AD B2C-ben tárolandó felhasználói attribútumok megfelelő készletét.

- **DO** áruház az Azure AD B2C-ben
  - Felhasználónév, jelszó, e-mail címek, telefonszámok, tagsági számok/azonosítók.
  - Hozzájárulási jelölők az adatvédelmi irányelvekhez és a végfelhasználói licencszerződésekhez.
- **NE** tárolja az Azure AD B2C-ben
  - Érzékeny adatok, például hitelkártyaszámok, társadalombiztosítási számok (SSN), orvosi feljegyzések vagy a kormányzati vagy iparági megfelelőségi szervek által szabályozott egyéb adatok.
  - Marketing- vagy kommunikációs beállítások, felhasználói viselkedések és elemzések.

### <a name="directory-clean-up"></a>Könyvtár karbantartása

Az áttelepítési folyamat megkezdése előtt vegye ki az alkalmat a címtár karbantartására.

- Azonosítsa az Azure AD B2C-ben tárolandó felhasználói attribútumokat, és csak azt telepítse át, amire szüksége van. Szükség esetén egyéni [attribútumokat](custom-policy-custom-attributes.md) hozhat létre a felhasználótovábbi adatainak tárolásához.
- Ha több hitelesítési forrással rendelkező környezetből (például minden alkalmazás saját felhasználói könyvtárral rendelkezik) telepít át egy egyesített fiókba az Azure AD B2C-ben.
- Ha több alkalmazás különböző felhasználónévvel rendelkezik, az identitásgyűjtemény használatával tárolhatja az összeset egy Azure AD B2C felhasználói fiókban. Ami a jelszót, hagyja, hogy a felhasználó válasszon egyet, és állítsa be a könyvtárban. Például a zökkenőmentes áttelepítés, csak a kiválasztott jelszót kell tárolni az Azure AD B2C-fiókban.
- Távolítsa el a nem használt felhasználói fiókokat az áttelepítés előtt, vagy ne telepítse át az elavult fiókokat.

### <a name="password-policy"></a>Jelszóházirend

Ha az áttelepíti a fiókokat gyengébb jelszóerősséget, mint az Azure AD B2C által kényszerített [erős jelszóerősséget,](../active-directory/authentication/concept-sspr-policy.md) letilthatja az erős jelszókövetelményt. További információt a [Jelszóházirend tulajdonságcímű témakörben talál.](manage-user-accounts-graph-api.md#password-policy-property)

## <a name="next-steps"></a>További lépések

Az [azure-ad-b2c/user-migration](https://github.com/azure-ad-b2c/user-migration) repository a GitHubon egy zökkenőmentes áttelepítési egyéni szabályzatpéldát és REST API-kódmintát tartalmaz:

[Zökkenőmentes felhasználói áttelepítésegyéni házirend & REST API-kódminta](https://aka.ms/b2c-account-seamless-migration)

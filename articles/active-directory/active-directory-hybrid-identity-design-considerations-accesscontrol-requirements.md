---
title: "Az Azure Active Directory hibrid identitáskezelési elrendezésével kapcsolatos szempontok - határozza meg a hozzáférés-vezérlési követelményeinek |} Microsoft Docs"
description: "Identitás és a hibrid környezetben a felhasználók az erőforrásokhoz való hozzáférés feltételeit azonosító ismerteti."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 6404940da460461632616fe49f055d50c2a7aba3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás hozzáférés-vezérlési követelményeinek meghatározása
Egy szervezet a hibrid identitáskezelési megoldás rendszer tervezése során ezt a lehetőséget is használhatnak tekintse át az erőforrásokat, azok tervezi, hogy elérhetővé tétele a felhasználók hozzáférési követelményeit. Az adatelérési közötti összes négy oszlopok identitását, amelyek:

* Adminisztráció
* Authentication
* Engedélyezés
* Naplózás

Az alábbi szakaszok a hitelesítési és engedélyezési további részleteket a vállat, felügyeleti és a naplózás a hibrid identitáskezelési életciklusának. Olvasási [határozza meg a hibrid identitáskezelési felügyeleti feladatok](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) ezek képességeivel kapcsolatos további információk.

> [!NOTE]
> Olvasási [a négy oszlopok az identitás - Identity Management szolgáltatásra a hibrid informatikai kora](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) ezen oszlopok előtti mindegyiknél további információt.
> 
> 

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
A hitelesítéshez és engedélyezéshez különböző forgatókönyv, ezek a forgatókönyvek, amelyek meg kell felelnie a hibrid identitáskezelési megoldás, amelyet a vállalat bevezetni kívánt adott követelményekkel rendelkezik. Forgatókönyvek a vállalatok (B2B) kommunikáció használata esetén adhat hozzá egy extra kihívás az IT-rendszergazdák, mivel győződjön meg arról, hogy a szervezet által használt hitelesítési és engedélyezési módszer kommunikál az üzleti partnerekkel kell rendelkezniük. A tervezése során a hitelesítési és engedélyezési követelményeinek győződjön meg arról, hogy az alábbi kérdések és válaszok:

* Lesz a szervezet felhasználók hitelesítéséhez és engedélyezéséhez csak az identity management rendszerének helyen?
  * Vannak-e bármilyen tervek B2B forgatókönyvek esetén?
  * Ha igen, már tudja, mely protokollok (SAML, OAuth, a Kerberos, jogkivonatok vagy tanúsítványok) használható mindkét vállalatok csatlakozni?
* A hibrid identitáskezelési megoldás, amely a támogatási elfogadják kívánja ezeket a protokollokat használ?

Egy másik fontos és megfontolandó szempont, amelyben a felhasználók és a partnerei által használandó hitelesítési tárházban található és a felügyeleti modell használható. Vegye figyelembe a következő két fő lehetőségeket:

* Központosított: Ebben a modellben a felhasználói hitelesítő adatok, házirendek és felügyeleti lehet helyszínen központosított vagy a felhőben.
* Hibrid: Ebben a modellben a felhasználói hitelesítő adatok, házirendek és felügyeleti lesz helyszínen központosított és a replikált a felhőben.

A szervezet alkalmazott modellt az üzleti igények függvényében változik, szeretné azonosítani a identity management rendszer tároló és a felügyeleti módot használja a következő kérdések:

* A szervezet jelenleg rendelkezik egy identitáskezelést a helyszínen?
  * Ha igen, akkor fogja a legyen?
  * Vannak-e valamilyen szabályozás vagy megfelelőségi követelményeknek, hogy a szervezet kell követnie, hogy azt határozza meg a felügyeleti identitásrendszere tároló kell?
* Használ a szervezete egyszeri bejelentkezésre található alkalmazásokhoz a helyszínen vagy a felhőben?
  * Ha igen, az egy hibrid identitás modell bevezetését érinti ez a folyamat?

## <a name="access-control"></a>Access Control
Hitelesítési és engedélyezési elemei core felhasználó érvényesítési keresztül a vállalati adatokhoz való hozzáférés engedélyezése, amíg fontos is szabályozhatja, hogy ezek a felhasználók lesz, és a rendszergazdák mértékét az azok által kezelt erőforrások keresztül kell hozzáférési szintet. A hibrid identitáskezelési megoldás részletes hozzáférést biztosít erőforrásokat, a delegálással és a szerepköralapú hozzáférés-vezérlést képesnek kell lennie. Győződjön meg arról, hogy a következő kérdésre adott válaszok vonatkozó hozzáférés-vezérlés:

* Rendelkezik a vállalata kezelheti az azonosítási rendszer emelt szintű jogosultságokkal egynél több felhasználó?
  * Ha igen, minden felhasználónak nem kell ugyanazt a hozzáférési szintet?
* A vállalat kell felhasználók kezelheti egy adott erőforráshoz való hozzáférés delegálására?
  * Ha igen, milyen gyakran ez történik?
* A vállalat integrálható a helyszíni és a felhő közötti hozzáférés-vezérlés képességeinek kell erőforrásokat?
* A vállalat kell bizonyos feltételeknek megfelelően erőforrásokhoz való hozzáférés korlátozása?
* A vállalat bármely olyan egyéni vezérlő erőforrásokat kell elérnie néhány alkalmazás rendelkezik?
  * Ha igen, az alkalmazások helyét (a helyszínen vagy a felhőben)?
  * Ha igen, ahol vannak a cél lévő erőforrások (a helyszínen vagy a felhőben)?

> [!NOTE]
> Ügyeljen arra, hogy minden válaszról jegyzeteket, és megismerheti a válaszok indokait. [Data Protection stratégia meghatározása](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) ismerteti a rendelkezésre álló lehetőségeket, illetve az egyes lehetőségek előnyeit és hátrányait.  Ezen kérdések megválaszolásával kiválaszthatja az üzleti igényeinek megfelelő legjobb lehetőség.
> 
> 

## <a name="next-steps"></a>Következő lépések
[Incidensválasz-követelmények meghatározása](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)


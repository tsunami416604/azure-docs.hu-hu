---
title: Hibrid identitás Tervező hozzáférés-vezérlési követelményeinek Azure |} Microsoft Docs
description: Identitás és a hibrid környezetben a felhasználók az erőforrásokhoz való hozzáférés feltételeit azonosító ismerteti.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 3a61e7ab4c738f6cba17bcc74c3bfd335378ab83
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801218"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás hozzáférés-vezérlési követelményeinek meghatározása
Tervezésekor szervezet van a hibrid identitáskezelési megoldás, ezt a lehetőséget is használhatnak tekintse át az erőforrásokat, azok tervezi, hogy elérhetővé tétele a felhasználók hozzáférési követelményeit. Az adatelérési közötti összes négy oszlopok identitását, amelyek:

* Adminisztráció
* Hitelesítés
* Engedélyezés
* Naplózás

A következő szakaszok foglalkozunk, hitelesítési és engedélyezési további részleteit, adminisztrációs, és naplózása a hibrid identitáskezelési életciklusának. Olvasási [határozza meg a hibrid identitáskezelési felügyeleti feladatok](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) ezek képességeivel kapcsolatos további információk.

> [!NOTE]
> Olvasási [a négy oszlopok az identitás - Identity Management szolgáltatásra a hibrid informatikai kora](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) ezen oszlopok előtti mindegyiknél további információt.
> 
> 

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
A hitelesítéshez és engedélyezéshez különböző forgatókönyv, ezek a forgatókönyvek, amelyek meg kell felelnie a hibrid identitáskezelési megoldás, amelyet a vállalat bevezetni kívánt adott követelményekkel rendelkezik. Forgatókönyvek a vállalatok (B2B) kommunikáció használata esetén adhat hozzá egy extra kihívás az IT-rendszergazdák, mivel győződjön meg arról, hogy a szervezet által használt hitelesítési és engedélyezési módszer kommunikál az üzleti partnerekkel kell rendelkezniük. A tervezése során a hitelesítési és engedélyezési követelményeinek győződjön meg arról, hogy az alábbi kérdések és válaszok:

* Lesz a szervezet felhasználók hitelesítéséhez és engedélyezéséhez csak az identity management rendszerének helyen?
  * Vannak-e bármilyen tervek B2B forgatókönyvek esetén?
  * Ha igen, már tudja, mely protokollok (SAML, OAuth, Kerberost vagy tanúsítványokat) használható mindkét vállalatok csatlakozni?
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
Hitelesítési és engedélyezési elemei core felhasználó érvényesítési keresztül a vállalati adatokhoz való hozzáférés engedélyezése, amíg fontos is szabályozhatja, hogy ezek a felhasználók lesz, és a rendszergazdák mértékét az azok által kezelt erőforrások keresztül kell hozzáférési szintet. A hibrid identitáskezelési megoldás a szerepköralapú hozzáférés-vezérlést, erőforrások és delegálás részletes hozzáférés biztosítására képesnek kell lennie. Győződjön meg arról, hogy a következő kérdést megválaszolták-e vonatkozó hozzáférés-vezérlés:

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

## <a name="next-steps"></a>További lépések
[Incidensválasz-követelmények meghatározása](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)


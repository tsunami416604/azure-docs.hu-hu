---
title: Hibrid identitás tervezési hozzáférés-vezérlési követelményeinek Azure |} A Microsoft Docs
description: Identitás és a hibrid környezetben felhasználók erőforrásokhoz való hozzáférés feltételeit azonosítása ismerteti.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84b786a1701892823554a83fa2015ac88d6eff4d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56197385"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás hozzáférés-vezérlési követelményeinek meghatározása
Amikor a szervezetek saját hibrid identitáskezelési megoldás tervez, ezt a lehetőséget is használhatják tekintse át az erőforrásokat, hogy tervezi, hogy a felhasználók számára elérhetővé tenni a hozzáférési követelmények. Az adatelérési közötti összes négy területei legyenek elérhetők az identitás, amelyek:

* Adminisztráció
* Authentication
* Engedélyezés
* Naplózás

Ez a rész azzal foglalkozik, hitelesítés és engedélyezés az adminisztrációs, további részletekért és a naplózás a hibrid identitás-életciklus részét képezik. Olvasási [határozza meg a hibrid identitáskezelési feladatokat](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) információ ezekről a funkciókról további információt.

> [!NOTE]
> Olvasási [a négy területei legyenek elérhetők az identitás - Identitáskezelés az életkor hibrid informatikai](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) ezeket alappillérét mindegyike további információt.
> 
> 

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
Hitelesítés és engedélyezés különböző forgatókönyv, ezeket a forgatókönyveket fog rendelkezni a konkrét követelmények, meg kell felelnie a hibrid identitáskezelési megoldás, amelyet a vállalat szeretne fogad el. Vállalatközi (B2B) kommunikáció forgatókönyveket is hozzáadhat egy további kihívást informatikai rendszergazdák számára, mivel győződjön meg arról, hogy a szervezet által használt hitelesítési és engedélyezési módszer kommunikál az üzleti partnerekkel kell. A tervezési folyamat a hitelesítési és engedélyezési követelményeinek győződjön meg arról, hogy az alábbi kérdések és válaszok:

* Lesznek a szervezet felhasználóinak hitelesítése és engedélyezése csak az identitáskezelési rendszerekkel található?
  * Vannak-e azokat a csomagokat a B2B-forgatókönyvekhez?
  * Ha igen, már tudja, mely protokollok (SAML, OAuth, Kerberost vagy tanúsítványokat) való csatlakozáshoz mindkét vállalat használható?
* A hibrid identitáskezelési megoldás, amely a támogatási fogad el kívánja ezeket a protokollokat támogatja?

Egy másik fontos figyelembe venni, hogy a felhasználók és a partnerek által használandó hitelesítési tárház kerülnek, és a felügyeleti modell használható. Vegye figyelembe a következő két fő beállítások:

* Központosított: Ebben a modellben a felhasználói hitelesítő adatok, szabályzatok és felügyeleti lehet központosított helyszíni vagy a felhőben.
* Hibrid: Ebben a modellben a felhasználói hitelesítő adatok, szabályzatok és felügyeleti lesz helyszínen központosított és a egy replikált a felhőben.

A szervezet fogad el, melyik modellben az üzleti igényeknek megfelelően változhatnak, szeretné, hol helyezkednek el az identitáskezelési rendszerekkel és a felügyeleti üzemmódban használja a következő kérdések megválaszolásával:

* Rendelkezik a szervezete jelenleg egy identitáskezelést a helyszínen?
  * Ha igen, akkor tervezi legyen?
  * Vannak, hogy a szervezet kell követnie, hogy azt határozza meg az identitáskezelési rendszerekkel ket kell szabályozás vagy megfelelőségi kapcsolatos követelmények?
* Használja a vállalata az egyszeri bejelentkezés található alkalmazásokhoz a helyszínen vagy a felhőben?
  * Ha igen, az egy hibrid identitás modell bevezetését érinti ez a folyamat?

## <a name="access-control"></a>Hozzáférés-vezérlés
Hitelesítési és engedélyezési alapvető elemeit, amelyek felhasználó érvényesítési vállalati adatokhoz való hozzáférés engedélyezése, amelyek is fontos, hogy ezek a felhasználók és rendszergazdák hozzáférési szintjét rendelkeznek majd ezzel az erőforrások hozzáférési szintje hogy azok kezel. A hibrid identitáskezelési megoldás részletes hozzáférés-erőforrások, a delegálás és a szerepköralapú hozzáférés-vezérlést biztosít képesnek kell lennie. Győződjön meg arról, hogy a következő kérdést megválaszolták vonatkozó hozzáférés-vezérlés:

* Emelt szintű jogosultságokkal a identitáskezelő rendszerbe kezeléséhez egynél több felhasználó rendelkezik a vállalata?
  * Ha igen, minden felhasználónak nem kell ugyanazt a hozzáférési szintet?
* Lenne szüksége van a vállalatának adott erőforrások kezelése a felhasználók hozzáférésének delegálása?
  * Ha igen, milyen gyakran ez történik?
* A vállalat kell integrálhatja a helyszíni és a felhő között a hozzáférés-vezérlési funkciókkal erőforrások?
* A vállalati erőforrások bizonyos feltételek alapján való hozzáférés korlátozására kellene?
* A vállalat kellene bármilyen alkalmazás, amely az egyéni vezérlő erőforrásokat kell elérnie néhány?
  * Ha igen, ezeknek az alkalmazásoknak helyét (helyszíni vagy felhőbeli)?
  * Ha igen, hol vannak a célerőforrások található (helyszíni vagy felhőbeli)?

> [!NOTE]
> Ügyeljen arra, hogy minden válaszról feljegyzéseket, és megismerheti a válaszok indokait. [Data Protection stratégia kidolgozása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) halad keresztül a rendelkezésre álló lehetőségek előnyeit és hátrányait az egyes lehetőségek.  Ezen kérdések megválaszolásával kiválaszthatja melyik lehetőség felel az üzleti igényeinek megfelelő.
> 
> 

## <a name="next-steps"></a>További lépések
[Incidensválasz-követelmények meghatározása](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)


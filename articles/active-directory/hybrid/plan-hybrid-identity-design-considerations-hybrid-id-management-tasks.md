---
title: Hibrid identitás tervezési - kezelési feladatainak Azure |} A Microsoft Docs
description: A feltételes hozzáférés-vezérléssel Azure Active Directory ellenőrzi a meghatározott feltételek, válassza ki, amikor a felhasználó hitelesítése és engedélyezése az alkalmazáshoz való hozzáférés előtt. Ha ezek a feltételek teljesülnek, a felhasználó hitelesítése és hozzáférhessen az alkalmazáshoz.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 436bb3c235c76f04e0f3d0fec1beeec3872f1dfb
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475815"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Hibrid identitás-életciklus megtervezése
Identitás a nagyvállalati mobilitás és az alkalmazás a hozzáférési stratégia alapjainak egyike. Mobileszköz vagy SaaS-alkalmazás aláírása, az identitás-e a kulcsot minden hozzáférjenek. A legmagasabb szintjén az identitáskezelési megoldás magában foglalja a egységes és az identitás-adattárak közötti szinkronizálása mely automatizálása, valamint a folyamat üzembe helyezésének erőforrások központosítása tartalmazza. Az identitáskezelési megoldás kell egy központi identitás kell a helyszíni és felhőben és a segítségével is identitás-összevonási valamilyen központosított hitelesítés kezelése és biztonságos megosztás és együttműködés a külső felhasználók és a vállalatok. Erőforrások között operációs rendszerek és alkalmazások számára, vagy fiókazonosítójával szervezetekben. Szervezeti felépítés is módosítható úgy, hogy megfeleljen a kiépítési irányelveinknek és eljárásainknak.

Célszerű is fontos, hogy az identitáskezelési megoldás a felhasználók hatékony munkájának támogatása az önkiszolgáló élményt, hogy hatékonyan megadásával egyszerűsítését szolgálja. Az identitáskezelési megoldás sokkal hatékonyabban osztja, ha egyszeri bejelentkezést a felhasználók számára lehetővé teszi különböző összes erőforrást, hozzá kell férniük. A rendszergazdák minden szinten szabványos eljárásokkal felhasználói hitelesítő adatok kezeléséhez. Bizonyos szintű felügyeleti csökken, vagy nem szükséges, attól függően, a kiépítési felügyeleti megoldás technológiai spektrumunk kihasználtságának növelését is. Továbbá biztonságosan terjesztheti felügyeleti képességeket, manuálisan vagy automatikusan, a különböző szervezetek között. Például egy tartományi rendszergazda szolgálhat csak a felhasználók és erőforrások ebben a tartományban. Ez a felhasználó rendszergazdai és üzembe helyezési feladatokat is végrehajthat, de nem jogosult a konfigurációs feladatokat, például a munkafolyamatok létrehozása.

## <a name="determine-hybrid-identity-management-tasks"></a>Hibrid identitáskezelési feladatokat meghatározása
A szervezet felügyeleti feladatok javítja a pontosságot és a felügyelet hatékonyságának és javítja a fennmaradó részét a szervezet a számítási feladatok. Az alábbiakban a kimutatás, amelyek meghatározzák a hatékony identitáskezelési rendszerekkel.

 ![](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Hibrid identitáskezelési feladatokat megadásához ismernie kell a szervezet, amely a hibrid identitás bevezetése fog néhány alapvető jellemzői. Fontos tudni, hogy az aktuális tárházak identitás források használja. Úgy, hogy ezen alapvető elemeit, az alapvető követelményeknek fog rendelkezni, és alapján, hogy ki kell kérnie a részletesebb kérdésekhez, amely vezet, a jobb tervezési döntés az identitáskezelési megoldás.  

Ezek a követelmények meghatározásánál tart, miközben győződjön meg arról, hogy legalább az alábbi kérdések és válaszok

* Üzembe helyezési lehetőségek: 
  
  * Egy robusztus fiók hozzáférés-kezelés és üzembe helyezési rendszer támogatja a hibrid identitáskezelési megoldás?
  * Hogyan történik a felhasználók, csoportok és jelszavak fogja kezelni?
  * Válaszol-e az identitás-életciklus-kezelését? 
    * Mennyi jelszó frissítések fiók felfüggesztését tart?
* Licenckezelés: 
  
  * Támogatja a hibrid identitáskezelési megoldás kezeli Licenckezelés?
    * Ha igen, milyen lehetőségek állnak rendelkezésre?
* A megoldás kezeli a felügyeleti csoport alapú licenc? 
  
      - Ha igen, van egy biztonsági csoport hozzárendelése lehetséges? 
       - Ha igen, a felhőalapú címtárral automatikusan hozzárendeli licencek a csoport összes tagját? 
        - Mi történik, ha a felhasználó ezt követően hozzá, vagy eltávolítása a csoportból, lesz egy licencet kell automatikusan kiosztani vagy megvonni szükség szerint? 
* Integráció más külső Identitásszolgáltatók:
* A hibrid megoldás integrálható legyen az egyszeri bejelentkezés megvalósítása külső Identitásszolgáltatók?
* Az esetleges egyesítheti a különböző identitás-szolgáltatóktól javul identitás rendszerbe?
* Ha igen, hogyan, és melyek ezek, és milyen lehetőségek állnak rendelkezésére?

## <a name="synchronization-management"></a>Szinkronizálás kezelése
Az identitás-szolgáltatóktól használata, és láthatóan tartja őket a célja, az identity manager egyik szinkronizálva. A szinkronizált adatok maradjon egy mérvadó fő identitásszolgáltató alapján. Hibrid identitáskezelési forgatókönyvben a szinkronizált felügyeleti modell egy helyszíni kiszolgálón lévő összes felhasználó és eszköz identitások kezelésére, és a fiókok és opcionálisan a felhőbe jelszavak szinkronizálása. A felhasználó megadja az azonos jelszót a helyi, az a felhőben, és a bejelentkezés, a jelszót az identitáskezelési megoldás által ellenőrzött. Ez a modell a címtár-Szinkronizáló eszköz használja.

![](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) A megfelelő kialakítás a szinkronizálást az a hibrid identitáskezelési megoldás győződjön meg arról, hogy az alábbi kérdések és válaszok: • Mik azok a hibrid identitáskezelési megoldás érhető el a szinkronizálási megoldások?
• Mik azok az egyszeri bejelentkezés elérhető funkciók?
• Mik a lehetőségek az identitás-összevonási B2B és B2C között?

## <a name="next-steps"></a>További lépések
[Hibrid identitás-kezelés bevezetési stratégiájának meghatározása](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)


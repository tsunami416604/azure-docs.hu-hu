---
title: "Az Azure Active Directory hibrid identitás kialakítási szempontok - adja meg, hibrid identitás felügyeleti feladatai |} Microsoft Docs"
description: "Feltételes hozzáférés-vezérlést az Azure Active Directory ellenőrzi a megadott feltételek, ha a felhasználó hitelesítése és az alkalmazáshoz való hozzáférés előtt válasszon. Ha ezek a feltételek teljesülnek, a felhasználó hitelesítése és hozzáférni az alkalmazáshoz engedélyezett."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 921c8391fc18eca35d10c3ade158a98ae88df397
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Hibrid identitás életciklusának tervezése
Identitás az egyik a vállalati mobilitás és alkalmazás-hozzáférési stratégia alapjainak. A mobileszköz vagy SaaS-alkalmazás meg bejelentkezni, hogy a személyazonosságát jelenti a kulcsot minden hozzáférjenek. A legmagasabb szintjén az identitáskezelési megoldás magában foglalja a egységesíti és az identitás tárházak találhatók, ide tartozik az automatizálását és erőforrások kiépítési folyamat központosítása közötti szinkronizálása. A megoldást egy központosított identitás a helyszíni és a felhő között legyen, és segítségével is identitás-összevonási valamilyen központosított hitelesítés karbantartása biztonságosan megosztani, és a külső felhasználók és a vállalatok közötti együttműködés. Erőforrások operációs rendszereket és alkalmazásokat olyan személyek között, vagy (DEP) részt, a szervezetek. Szervezeti felépítés is lehet módosítva, hogy megfeleljen a kiépítési szabályzatokat és eljárásokat.

Fontos továbbá van igazítva lehetővé teszik a felhasználók önkiszolgáló lép hatékony leegyszerűsítheti a megadásával identitáskezelési megoldás. Az identitás-megoldás robusztusabb, ha egyszeri bejelentkezéshez a felhasználók számára lehetővé teszi egyes rendszergazdák minden eléréséhez szükséges összes erőforrások szintek felhasználói hitelesítő adatok kezelésére szolgáló szabványosított eljárásokat használhatja. Bizonyos szintű felügyeleti csökkenthetők vagy szüntetni, attól függően, hogy a létesítési-kezelési megoldás a hardverekről. Továbbá biztonságos terjesztheti felügyeleti képességek, manuálisan vagy automatikusan, különböző szervezetek között. A tartományi rendszergazda lehetnek például csak a személyek és az erőforrások az adott tartományban. Ez a felhasználó rendszergazdai és üzembe helyezési feladatokat is végrehajthat, de nem jogosult a konfigurációs feladatok, munkafolyamatok létrehozása.

## <a name="determine-hybrid-identity-management-tasks"></a>Hibrid identitás felügyeleti feladatok meghatározása
A szervezet felügyeleti feladatok javítja a pontosság és a felügyelet hatékonysága és növeli a munkaterhelés a szervezet egyenleg. Az alábbiakban a pivots, amelyek meghatározzák egy robusztus identitás rendszert.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)

Hibrid identitás felügyeleti feladatok megadásához ismernie kell a szervezet, amely a hibrid identitás bevezetése fog néhány alapvető jellemzői. Fontos tudni, hogy az aktuális adattárak identitás adatforrások is használatban. Ismeretében core elemek, a legalapvetőbb követelményekkel rendelkezik, és annak alapján, hogy szüksége lesz kérje meg a részletesebb kérdésekhez, amely fog alapján egy jobb tervezési döntés a identitáskezelési megoldás.  

Ezek a követelmények meghatározásánál tart, közben ügyeljen arra, hogy legalább a következő kérdések és válaszok

* Üzembe helyezési lehetőségek: 
  
  * Támogatja-e a hibrid identitáskezelési megoldás egy robusztus fiók kezelési és üzembe helyezési rendszert?
  * Hogyan történik a felhasználók, csoportok és jelszavak kíván kezelni?
  * Az Identitáskezelés életciklus rugalmas van? 
    * Mennyi időt jelszó frissítések fiók felfüggesztését igénybe?
* Licenckezelés: 
  
  * A hibrid identitáskezelési megoldás leírók Licenckezelés használ?
    * Ha igen, milyen lehetőségek érhetők el?
* Nem a megoldás kezeli a csoport-alapú Licenckezelése? 
  
      - Ha igen, van lehetőség egy biztonsági csoport hozzárendelése? 
       - Ha igen, a felhőalapú címtárban automatikusan hozzárendeli licencek a csoport összes tagját? 
        - Mi történik, ha a felhasználók ezt követően hozzá, vagy eltávolítani a csoportból, lesz egy licencet kell automatikusan kiosztani vagy megvonni szükség szerint? 
* Integráció más külső Identitásszolgáltatók:
* A hibrid megoldás integrálható az egyszeri bejelentkezés megvalósítása harmadik fél Identitásszolgáltatók?
* Az is egyesítése, ezáltal az összes különböző identitás-szolgáltatóktól javul identitás rendszerben?
* Ha igen, hogyan és azokat, és milyen lehetőségek érhetők el?

## <a name="synchronization-management"></a>Szinkronizálás kezelése
Egyik a célja az identity manager, az identitás-szolgáltatóktól és láthatóan tartja őket tenni szinkronizálva. A szinkronizált adatok mindig a fő identitásszolgáltató mérvadó alapján. Hibrid identitáskezelési forgatókönyvben a szinkronizált felügyeleti modellt identitáskezelést minden felhasználó és eszköz egy helyszíni kiszolgálón, és a fiókok és opcionálisan a felhőbe a jelszavak szinkronizálása. A felhasználó megadja a azonos jelszót a helyszíni, akkor vagy megteszi a felhőben, és bejelentkezéskor, a jelszó ellenőrizte-e a megoldást. Ez a modell a címtár-Szinkronizáló eszköz használja.

![](./media/hybrid-id-design-considerations/Directory_synchronization.png)Megfelelő kialakítás a szinkronizálás a hibrid identitáskezelési megoldás győződjön meg arról, hogy az alábbi kérdések és válaszok: • Mik a szinkronizálási megoldások érhető el a hibrid identitáskezelési megoldás?
• Az elérhető lehetőségeket egyszeri bejelentkezés Mik?
• Mik azok a beállítások az identitás-összevonási B2B és B2C között?

## <a name="next-steps"></a>Következő lépések
[Hibrid identitás kezelés bevezetési stratégiájának meghatározása](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)


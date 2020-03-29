---
title: Hibrid identitástervezés – felügyeleti feladatok Az Azure | Microsoft dokumentumok
description: Feltételes hozzáférés-vezérléssel az Azure Active Directory ellenőrzi a felhasználó hitelesítésekénél és az alkalmazáshoz való hozzáférés engedélyezésekor választott feltételeket. Ha ezek a feltételek teljesülnek, a felhasználó hitelesítve lesz, és hozzáférést biztosít az alkalmazáshoz.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a829d39ff21a1abeafd3b4362747894d196d9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109387"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Hibrid identitáséletciklus megtervezése
Az identitás a vállalati mobilitási és alkalmazáshozzáférési stratégia egyik alapja. Akár mobileszközére, akár SaaS-alkalmazásra jelentkezik be, az Ön identitása a kulcs a hozzáféréshez. A legmagasabb szinten az identitáskezelési megoldás magában foglalja az identitásadattárak egyesítését és szinkronizálását, amely magában foglalja az erőforrások kiépítésének automatizálását és központosítását. Az identitáskezelési megoldásnak központi identitásnak kell lennie a helyszíni és a felhőbeli, és az identitás-összevonás valamilyen formáját is használnia kell a központosított hitelesítés fenntartásához, valamint a külső felhasználókkal és vállalkozásokkal való biztonságos megosztáshoz és együttműködéshez. Az erőforrások az operációs rendszerektől és alkalmazásoktól a szervezetben lévő vagy azzal kapcsolatban álló személyekig terjednek. Szervezeti struktúra módosítható, hogy megfeleljen a létesítési szabályzatok és eljárások.

Az is fontos, hogy egy identitás-megoldás célja, hogy felhatalmazza a felhasználók által nyújtott önkiszolgáló tapasztalatok at tartani őket produktív. Az identitáskezelési megoldás robusztusabb, ha lehetővé teszi az egyszeri bejelentkezést a felhasználók számára az összes szükséges erőforrásközött. A rendszergazdák minden szinten szabványosított eljárásokat használhatnak a felhasználói hitelesítő adatok kezeléséhez. Egyes felügyeleti szintek csökkenthetők vagy megszüntethetők, a kiépítési felügyeleti megoldás szélessége függően. Ezenkívül biztonságosan terjesztheti a felügyeleti képességeket, manuálisan vagy automatikusan a különböző szervezetek között. A tartományi rendszergazda például csak az adott tartományban lévő személyeket és erőforrásokat tudja kiszolgálni. Ez a felhasználó felügyeleti és kiépítési feladatokat végezhet, de nincs jogosult konfigurációs feladatok elvégzésére, például munkafolyamatok létrehozására.

## <a name="determine-hybrid-identity-management-tasks"></a>Hibrid identitáskezelési feladatok meghatározása
A felügyeleti feladatok elosztása a szervezetben javítja a felügyelet pontosságát és hatékonyságát, és javítja a szervezet munkaterhelésének egyensúlyát. A következőkben a pivotok, amelyek meghatározzák a robusztus identitáskezelő rendszer.

 ![identitáskezelési szempontok](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Hibrid identitáskezelési feladatok meghatározásához meg kell értenie a szervezet néhány alapvető jellemzőit, amelyek hibrid identitást fognak elfogadni. Fontos megérteni az identitásforrásokhoz használt aktuális adattárakat. Ismerve ezeket az alapvető elemeket, akkor az alapvető követelményeket, és az alapján, hogy meg kell kérni részletesebb kérdéseket, amelyek elvezetnek egy jobb tervezési döntés az identitás megoldás.  

E követelmények meghatározása során biztosítani kell, hogy legalább a következő

* Kiépítési lehetőségek: 
  
  * A hibrid identitáskezelési megoldás támogatja a megbízható fiókhozzáférés-kezelési és -kiépítési rendszert?
  * Hogyan történik a felhasználók, csoportok és jelszavak kezelése?
  * Az identitás életciklus-kezelése reszponzív? 
    * Mennyi ideig tart a jelszófrissítések fiók felfüggesztése?
* Licenckezelés: 
  
  * A hibrid identitáskezelési megoldás kezeli a licenckezelést?
    * Ha igen, milyen képességek érhetők el?
  * A megoldás kezeli a csoportalapú licenckezelést? 
  
    * Ha igen, lehetséges biztonsági csoportot hozzárendelni? 
    * Ha igen, a felhőkönyvtár automatikusan hozzárendeli a licenceket a csoport összes tagjához? 
    * Mi történik, ha egy felhasználót később hozzáadnak a csoporthoz, vagy eltávolítanak a csoportból, a rendszer automatikusan hozzárendel vagy eltávolítja a licencet? 
* Integráció más külső identitásszolgáltatókkal:
  * Integrálható-e ez a hibrid megoldás harmadik fél identitásszolgáltatóival az egyszeri bejelentkezés megvalósítása érdekében?
  * Lehetséges-e az összes különböző identitásszolgáltató egységesidentitás-rendszerré való egyesítése?
  * Ha igen, hogyan és melyek ezek, és milyen képességek állnak rendelkezésre?

## <a name="synchronization-management"></a>Szinkronizálás kezelés
Az identitáskezelő egyik célja, hogy az összes identitásszolgáltatót eltudja hozni, és szinkronizálva tarthassa őket. Az adatok szinkronizálása mérvadó főidentitás-szolgáltató alapján történik. Hibrid identitásesetén egy szinkronizált felügyeleti modellel kezelheti az összes felhasználói és eszközidentitást egy helyszíni kiszolgálón, és szinkronizálhatja a fiókokat és adott esetben a jelszavakat a felhővel. A felhasználó ugyanazt a jelszót adja meg a helyszínen, mint a felhőben, és a bejelentkezéskor a jelszót az identitásmegoldás ellenőrzi. Ez a modell címtár-szinkronizálási eszközt használ.

![könyvtárszinkronizálás](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) A hibrid identitáskezelési megoldás megfelelő kialakításához győződjön meg arról, hogy a rendszer választ ad a következő kérdésekre:
*    Melyek a hibrid identitáskezelési megoldáshoz elérhető szinkronizálási megoldások?
*    Melyek a rendelkezésre álló egyszeri bejelentkezési funkciók?
*    Milyen lehetőségek vannak a B2B és a B2C közötti identitás-összevonásra?

## <a name="next-steps"></a>További lépések
[Hibrid identitáskezelési bevezetési stratégia meghatározása](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok – áttekintés](plan-hybrid-identity-design-considerations-overview.md)


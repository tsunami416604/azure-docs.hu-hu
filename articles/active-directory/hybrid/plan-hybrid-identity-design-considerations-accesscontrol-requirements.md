---
title: Hibrid identitástervezési hozzáférés-vezérlési követelmények Az Azure | Microsoft dokumentumok
description: Kiterjed az identitás pillérei, és meghatározza a hozzáférési követelményeket az erőforrások a felhasználók számára a hibrid környezetben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60295143"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás hozzáférés-vezérlési követelményeinek meghatározása
Amikor egy szervezet a hibrid identitáskezelési megoldását tervezi, ezt a lehetőséget is használhatja az on-t a felhasználók számára elérhetővé tenni kívánt erőforrások hozzáférési követelményeinek áttekintésére. Az adatokhoz való hozzáférés az identitás mind a négy pillérét átlépi, amelyek a következők:

* Adminisztráció
* Hitelesítés
* Engedélyezés
* Naplózás

Az alábbi szakaszok további részleteket, felügyeletet és naplózást a hitelesítésés engedélyezés a hibrid identitás életciklusának részét képezik. Olvassa [el A hibrid identitáskezelési feladatok meghatározása](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) című, ezekről a képességekről szóló további információkért olvassa el a hibrid identitáskezelési feladatok meghatározása című részcímű információt.

> [!NOTE]
> Olvassa el [az identitás négy pillére – Identitáskezelés a hibrid it korában](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) című információt az egyes pillérekről.
> 
> 

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
A hitelesítéshez és az engedélyezéshez különböző forgatókönyvek léteznek, ezek a forgatókönyvek olyan speciális követelményekkel rendelkeznek, amelyeket a vállalat által alkalmazni kívánt hibrid identitáskezelési megoldásnak kell teljesítenie. A Vállalkozás–üzleti (B2B) kommunikációt érintő forgatókönyvek további kihívást jelentenek az informatikai rendszergazdák számára, mivel biztosítaniuk kell, hogy a szervezet által használt hitelesítési és engedélyezési módszer kommunikáljon az üzleti partnerekkel. A hitelesítési és engedélyezési követelmények tervezési folyamata során győződjön meg arról, hogy a következő kérdésekre válaszol:

* A szervezet csak az identitáskezelő rendszerében található felhasználókat hitelesíti és engedélyezi?
  * Vannak-e tervek a B2B forgatókönyvekhez?
  * Ha igen, tudja már, hogy mely protokollokat (SAML, OAuth, Kerberos vagy Tanúsítványok) fogja használni a két vállalkozás összekapcsolására?
* Támogatja-e a hibrid identitáskezelési megoldás, amelyet el fog fogadni, és támogatja ezeket a protokollokat?

Egy másik fontos szempont, hogy fontolja meg, ahol a hitelesítési tárház, amely a felhasználók és partnerek fogják használni, és a felügyeleti modell t kell használni. Vegye figyelembe a következő két alapvető lehetőséget:

* Központosított: ebben a modellben a felhasználó hitelesítő adatait, szabályzatait és felügyeletét központosíthatja a helyszínen vagy a felhőben.
* Hibrid: ebben a modellben a felhasználó hitelesítő adatait, szabályzatait és felügyeletét a helyszíni központosított és a felhőben replikált lesz.

Az, hogy a szervezet milyen modellt fog alkalmazni, az üzleti követelményektől függően változik, a következő kérdésekre szeretne válaszolni, hogy azonosítsa, hol fog az identitáskezelő rendszer, és hogy milyen felügyeleti módot kell használni:

* A szervezet jelenleg rendelkezik egy identitáskezelés a helyszínen?
  * Ha igen, tervezik megtartani?
  * Vannak-e olyan szabályozási vagy megfelelőségi követelmények, amelyeket a szervezetnek követnie kell, és amely meghatározza, hogy az identitáskezelő rendszer hol tartózkodjon?
* A szervezet egyszeri bejelentkezést használ a helyszíni vagy a felhőben található alkalmazásokhoz?
  * Ha igen, a hibrid identitásmodell elfogadása befolyásolja ezt a folyamatot?

## <a name="access-control"></a>Hozzáférés-vezérlés
Bár a hitelesítés és az engedélyezés alapvető elemei a vállalati adatokhoz való hozzáféréslehetővé tétele érdekében a felhasználó ellenőrzése révén, fontos annak szabályozása is, hogy ezek a felhasználók milyen szintű hozzáféréssel rendelkeznek, és hogy a rendszergazdák milyen szintű hozzáféréssel rendelkeznek az erőforrásokhoz. amelyeket kezelni fognak. A hibrid identitáskezelési megoldásnak képesnek kell lennie az erőforrásokhoz, a delegáláshoz és a szerepkör-alap hozzáférés-vezérléshez való részletes hozzáférés biztosításához. Győződjön meg arról, hogy a következő kérdésre választ kap a hozzáférés-vezérléssel kapcsolatban:

* A vállalat egynél több, emelt szintű jogosultsággal rendelkező felhasználóval rendelkezik az identitásrendszer kezeléséhez?
  * Ha igen, minden felhasználónak ugyanazt a hozzáférési szintet kell biztosítania?
* A vállalatnak hozzá kell adnia a felhasználókhoz való hozzáférést bizonyos erőforrások kezeléséhez?
  * Ha igen, milyen gyakran történik ez?
* A vállalatnak integrálnia kell a hozzáférés-vezérlési képességeket a helyszíni és a felhőbeli erőforrások között?
* Szükség van-e a vállalatának az erőforrásokhoz való hozzáférés korlátozására bizonyos feltételek szerint?
* Van-e a vállalatának olyan alkalmazása, amelyegyéni vezérlési hozzáférést igényel bizonyos erőforrásokhoz?
  * Ha igen, hol találhatók ezek az alkalmazások (a helyszínen vagy a felhőben)?
  * Ha igen, hol találhatók ezek a célerőforrások (a helyszínen vagy a felhőben)?

> [!NOTE]
> Minden válaszról készítsen feljegyzéseket, és ismerje meg a válaszok indokait. [Az Adatvédelmi stratégia meghatározása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) az egyes lehetőségek rendelkezésre álló lehetőségeit és előnyeit/hátrányait veszi át.  Ezeknek a kérdéseknek a megválaszolásával kiválaszthatja, hogy melyik lehetőség felel meg leginkább üzleti igényeinek.
> 
> 

## <a name="next-steps"></a>További lépések
[Az incidensmegoldáshoz kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok – áttekintés](plan-hybrid-identity-design-considerations-overview.md)


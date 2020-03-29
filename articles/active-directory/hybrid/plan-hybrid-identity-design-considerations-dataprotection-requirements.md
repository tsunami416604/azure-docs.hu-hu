---
title: Hibrid identitástervezés – adatvédelmi követelmények Azure | Microsoft dokumentumok
description: A hibrid identitáskezelési megoldás tervezésekor határozza meg a vállalkozása adatvédelmi követelményeit, és hogy mely lehetőségek állnak rendelkezésre a követelmények legjobb teljesítéséhez.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
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
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64918778"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Tervezze meg az adatbiztonság növelését egy erős identitásmegoldás révén
Az adatok védelmének első lépése annak azonosítása, hogy ki férhet hozzá az adatokhoz. Emellett olyan identitáskezelési megoldással kell rendelkeznie, amely integrálható a rendszerrel a hitelesítési és engedélyezési képességek biztosításához. A hitelesítést és az engedélyezést gyakran összekeverik egymással, és a szerepüket félreértik. A valóságban ezek különbözőek, amint azt az alábbi ábra mutatja:

![mobileszköz életciklusa](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**A mobileszköz-kezelés életciklusának szakaszai**

A hibrid identitáskezelési megoldás tervezésekor ismernie kell a vállalkozásra vonatkozó adatvédelmi követelményeket, és hogy mely lehetőségek állnak rendelkezésre a követelmények legjobb teljesítéséhez.

> [!NOTE]
> Miután befejezte az adatbiztonság tervezését, tekintse [át a Többtényezős hitelesítési követelmények meghatározása című](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) részt, amely biztosítja, hogy a többtényezős hitelesítési követelményekkel kapcsolatos beállításokat ne befolyásolják az ebben a szakaszban hozott döntések.
> 
> 

## <a name="determine-data-protection-requirements"></a>Az adatvédelmi követelmények meghatározása
A mobilitás korában a legtöbb vállalatnak közös célja van: lehetővé tenni a felhasználók számára, hogy a termelékenység növelése érdekében a helyszínen vagy távolról is hatékonyan dolgozhassanak mobileszközeiken. Az ilyen követelményekkel rendelkező vállalatokat az is aggasztja, hogy hány fenyegetést kell enyhíteni a vállalat adatainak biztonsága és a felhasználók adatainak védelme érdekében. E tekintetben minden vállalatnak eltérő követelményei lehetnek; a különböző megfelelőségi szabályok, amelyek attól függően változnak, hogy a vállalat melyik iparágban jár el, különböző tervezési döntésekhez vezetnek. 

Vannak azonban olyan biztonsági szempontok, amelyeket meg kell vizsgálni és érvényesíteni kell, függetlenül az iparágtól.

## <a name="data-protection-paths"></a>Adatvédelmi útvonalak
![adatvédelmi útvonalak](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Adatvédelmi útvonalak**

A fenti ábrán az identitás-összetevő lesz az első, amelyet ellenőrizni kell az adatok elérése előtt. Ezek az adatok azonban különböző állapotokban lehetnek a hozzáférés ideje alatt. A diagram minden egyes száma egy olyan útvonalat jelöl, amelyben az adatok egy adott időpontban elhelyezhetők. Ezeket a számokat az alábbiakban ismertetjük:

1. Adatvédelem az eszköz szintjén.
2. Adatvédelem szállítás közben.
3. Adatvédelem a helyszíni nyugalmi körülmények között.
4. Adatvédelem a felhőben való nyugalmi környezetben.

Szükséges, hogy a hibrid identitáskezelési megoldás képes a helyszíni és a felhőbeli identitáskezelési erőforrások a felhasználó azonosítására, mielőtt hozzáférést biztosít az adatokhoz. A hibrid identitáskezelési megoldás tervezésekor győződjön meg arról, hogy a szervezet követelményeinek megfelelően válaszol a következő kérdésekre:

## <a name="data-protection-at-rest"></a>Adatvédelem nyugalmi
Függetlenül attól, hogy az adatok hol vannak nyugalmi állapotban (eszköz, felhő vagy helyszíni), fontos, hogy végezzen értékelést, hogy megértsék a szervezet igényeit ebben a tekintetben. Ezen a területen győződjön meg arról, hogy a következő kérdéseket kéri:

* A vállalatnak meg kell védenie az adatokat nyugalmi helyzetben?
  * Ha igen, a hibrid identitáskezelési megoldás képes integrálni a jelenlegi helyszíni infrastruktúrával?
  * Ha igen, a hibrid identitáskezelési megoldás képes integrálni a számítási feladatok a felhőben található?
* A felhőalapú identitáskezelés képes megvédeni a felhasználó hitelesítő adatait és a felhőben tárolt egyéb adatokat?

## <a name="data-protection-in-transit"></a>Adatvédelem szállítás közben
Az eszköz és az adatközpont között, illetve az eszköz és a felhő között átadott adatokat védeni kell. Az átvitel azonban nem feltétlenül jelenti azt, hogy a felhőszolgáltatáson kívüli összetevővel folytatott kommunikációs folyamat; mozog belsőleg is, mint például a két virtuális hálózatok. Ezen a területen győződjön meg arról, hogy a következő kérdéseket kéri:

* Az Ön vállalatának meg kell védenie az adatokat az átvitel során?
  * Ha igen, a hibrid identitáskezelési megoldás képes integrálni a biztonságos vezérlők, például az SSL/TLS?
* A felhőidentitás-kezelés a címtártárba (az adatközpontokon belül és azok között) irányuló forgalmat aláírva tartja?

## <a name="compliance"></a>Megfelelőség
A szabályzatokra, törvényekre és jogszabályi megfelelőségi követelményekre vonatkozó követelmények a vállalatvállalatvállalatának iparágától függően változnak. A magas szabályozott iparágakban működő vállalatoknak foglalkozniuk kell a megfelelési kérdésekkel kapcsolatos identitáskezelési aggályokkal. Az olyan rendeletek, mint a Sarbanes-Oxley (SOX), az egészségbiztosítási hordozhatóságról és elszámoltathatóságról szóló törvény (HIPAA), a Gramm-Leach-Bliley Act (GLBA) és a Payment Card Industry Data Security Standard (PCI DSS) szigorúak az identitás és a hozzáférés tekintetében. A hibrid identitáskezelési megoldás, amely et a vállalat elfogadja rendelkeznie kell az alapvető képességek, amelyek megfelelnek a követelményeknek egy vagy több ilyen rendeletek. Ezen a területen győződjön meg arról, hogy a következő kérdéseket kéri:

* A hibrid identitáskezelési megoldás megfelel a vállalkozása szabályozási követelményeinek?
* A hibrid identitáskezelési megoldás 
* olyan képességekben, amelyek lehetővé teszik, hogy vállalata megfeleljen a szabályozási követelményeknek? 

> [!NOTE]
> Minden válaszról készítsen feljegyzéseket, és ismerje meg a válaszok indokait. [Az Adatvédelmi stratégia meghatározása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) az egyes lehetőségek rendelkezésre álló lehetőségeit és előnyeit/hátrányait veszi át.  A kérdések megválaszolásával kiválaszthatja, hogy melyik lehetőség felel meg legjobban üzleti igényeinek.
> 
> 

## <a name="next-steps"></a>További lépések
 [A tartalomkezeléshez kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok – áttekintés](plan-hybrid-identity-design-considerations-overview.md)


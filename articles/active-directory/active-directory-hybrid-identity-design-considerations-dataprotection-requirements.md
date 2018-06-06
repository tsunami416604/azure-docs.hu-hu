---
title: Hibrid identitás Tervező - Azure adatvédelmi követelményeinek |} Microsoft Docs
description: A hibrid identitáskezelési megoldás tervezésekor az üzleti és melyik lehetőségek érhetők el a legjobban a ezek a követelmények teljesítéséhez adatvédelmi követelményeinek azonosítása
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 423624e999e4170ceddf097125e4fb3e9a40384b
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34800921"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>A erős identitáskezelési megoldással adatbiztonság továbbfejlesztésének tervezése
Az első lépés az adatok védelmének adatok hozzáférő felhasználók azonosításához. Is szükség van egy identitás-megoldás, amely integrálható a hitelesítési és engedélyezési képességek biztosítása a rendszer. Hitelesítési és engedélyezési rendszer gyakran összetéveszthető egymáshoz, és azok a szerepkörök böngésző. A valóságban ezek eltérnek, az alábbi ábrán látható módon:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)

**Mobileszköz-kezelés életciklusának szakaszait**

A hibrid identitáskezelési megoldás tervezésekor ismernie kell az üzleti és melyik lehetőségek elérhető legjobb adatvédelmi követelményeinek teljesítése ezeket a követelményeket.

> [!NOTE]
> Miután befejezte az adatok biztonsági tervezése, tekintse át a [határozza meg a multi-factor authentication követelményeinek](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) annak érdekében, hogy a multi-factor authentication követelményeinek vonatkozó beállításokat nem érintette a hozott döntések ebben a szakaszban.
> 
> 

## <a name="determine-data-protection-requirements"></a>Adatvédelmi követelményeinek meghatározása
A mobilitási kora, a legtöbb vállalat rendelkezik egy közös cél: lehetővé teszi a felhasználók számára a termelékenység növelése érdekében mobileszközükről, a helyszínen, vagy távolról bárhonnan munkavégzést. Vállalatok számára, hogy ezek a követelmények részletesebben szeretnének foglalkozni az szolgáló ahhoz, hogy a vállalati adatok biztonsága, és a felhasználó adatvédelmének kell mérsékelni számát is. Minden vállalat igényei lehetnek az különböző ebben a tekintetben; különböző megfelelőségi szabályok eltérőek, mely iparági megfelelően jár el a vállalati különböző tervezési döntésekhez vezethet. 

Van azonban néhány biztonsági szempontok felfedezte és érvényesítve, iparágtól függetlenül.

## <a name="data-protection-paths"></a>Data protection elérési utak
![](./media/hybrid-id-design-considerations/data-protection-paths.png)

**Data protection elérési utak**

A fenti ábrán az identitás összetevő lesz az elsőt ellenőrizni kell, mielőtt az adatokhoz. Azonban ezek az adatok különböző állapota lehet az időre, amíg azt történt hozzáférés. Ez az ábra egyes szám egy elérési utat, ahol lehet az adatokat bizonyos ponton található időben jelzi. Ezeket a számokat ismertetése az alábbiakban olvasható:

1. Az adatvédelem eszközszinten.
2. Az adatvédelem az átvitel során.
3. A többi helyszíni adatvédelem.
4. Az adatvédelem aktívan a felhőben.

Szükséges, hogy a hibrid identitáskezelési megoldás által képes, ami mind a helyszíni és felhőalapú identitás-kezelési források a felhasználó azonosítására, mielőtt az adatokhoz való hozzáférést. A hibrid identitáskezelési megoldás tervezésekor győződjön meg arról, hogy a szervezet igényeinek megfelelően a következő kérdéseket válaszok:

## <a name="data-protection-at-rest"></a>Az adatvédelem aktívan
Függetlenül attól, ahol az adatok aktívan (eszköz, felhőalapú vagy helyszíni) fontos egy értékelése megértéséhez Ebben a tekintetben a szervezet igényeinek. Ez a terület gondoskodjon arról, hogy a rendszer felkéri a következő kérdéseket:

* Vállalatának meg kell inaktív adatok védelmét?
  * Ha igen, az a hibrid identitáskezelési megoldás képes lesz integrálni a jelenlegi helyszíni infrastruktúrával?
  * Ha igen, van a számítási feladatait a felhőben található integrálása tudni a hibrid identitáskezelési megoldás?
* Az a cloud identity management a felhasználói hitelesítő adatokat és egyéb a felhőben tárolt adatok védelme?

## <a name="data-protection-in-transit"></a>Adatok védelmére átvitel közben
Az eszköz és az Adatközpont között, vagy az eszköz és a felhő között átvitt adatokat kell védeni. Azonban az átvitel alatt nem feltétlenül jelenti a kommunikációs folyamat egy összetevő kívül a felhőalapú szolgáltatás; átvitel során, belső is, mint például két virtuális hálózatot. Ez a terület gondoskodjon arról, hogy a rendszer felkéri a következő kérdéseket:

* Vállalatának meg kell az adatok védelmére átvitel?
  * Ha igen, van például az SSL/TLS biztonságos vezérlők integrálása képes a hibrid identitáskezelési megoldás?
* Nem a felhő Identitáskezelés megtartja aláírással forgalma és a directory-tárolóban (belül és adatközpontok között)?

## <a name="compliance"></a>Megfelelőség
Szabályzat, a törvényi és az előírásoknak való megfelelés követelmények eltérőek az iparág, amely a vállalat tartozik. A vállalatok a magas szabályozott iparágakban kell venni a Identitáskezelés maillel kapcsolatos megfelelőségi problémákat. Például a Sarbanes-Oxley (SOX), az egészségügyi biztosítás hordozhatóságáról és elszámolási kötelezettségéről szóló törvény (HIPAA), a Gramm-Leach-Bliley Act (GLBA) és a Payment Card adatvédelmi szabvány (PCI DSS) azok szigorú identitások és hozzáférések kapcsolatban. A hibrid identitáskezelési megoldás a vállalat által alkalmazni kell rendelkeznie, hogy a rendszer megfelel a követelményeknek, egy vagy több szabályzatban legfontosabb funkcióit. Ez a terület gondoskodjon arról, hogy a rendszer felkéri a következő kérdéseket:

* Megfelel a hibrid identitáskezelési megoldás a saját üzleti szabályozási követelmények?
* A hibrid identitáskezelési megoldás rendelkezik beépített nem 
* a képességek, amely lehetővé teszi a vállalat számára megfelelő szabályozási követelmények? 

> [!NOTE]
> Ügyeljen arra, hogy minden válaszról jegyzeteket, és megismerheti a válaszok indokait. [Data Protection stratégia meghatározása](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) ismerteti a rendelkezésre álló lehetőségeket, illetve az egyes lehetőségek előnyeit és hátrányait.  Ezen kérdések mely leginkább megfelelő lehetőséget az üzleti megválaszolása szükséges.
> 
> 

## <a name="next-steps"></a>További lépések
 [A Tartalomkezelés követelmények meghatározása](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)


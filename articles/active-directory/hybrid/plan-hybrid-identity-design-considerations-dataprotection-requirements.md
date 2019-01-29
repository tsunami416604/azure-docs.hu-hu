---
title: Hibrid identitás Tervező – az adatvédelmi követelmények Azure |} A Microsoft Docs
description: A hibrid identitáskezelési megoldás megtervezésekor azonosítsa az adatvédelmi követelmények az üzleti és melyik lehetőség is elérhető a legjobban megfelelnek ezeknek a követelményeknek.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: c31f104b3a7ae37d768a2a483a70385852dc47ca
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156819"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Egy erős identitása-megoldáson keresztül adatbiztonság továbbfejlesztésének tervezése
Az adatok védelmének első lépése az azonosíthatja férhet hozzá az adatokat. Emellett szüksége lesz egy identitás-megoldás, amely integrálható a hitelesítési és engedélyezési képességeket biztosít a rendszer. Hitelesítési és engedélyezési gyakran összetéveszteni egymással, és azok szerepköreivel böngésző. A valóságban a végzett ezek eltérnek, az alábbi ábrán látható módon:

![](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Mobileszköz-kezelés életciklusának szakaszai**

A hibrid identitáskezelési megoldás megtervezésekor ismernie kell az üzleti és mely funkciói tartoznak a elérhető legjobb az adatvédelmi követelmények teljesítése érdekében ezeket a követelményeket.

> [!NOTE]
> Miután végzett az adatbiztonság tervezése, tekintse át a [határozza meg a multi-factor authentication követelményeinek](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) , győződjön meg arról, hogy a multi-factor authentication szolgáltatás követelményekre vonatkozó beállításokat nem érinti a döntéseket, Ebben a szakaszban arról.
> 
> 

## <a name="determine-data-protection-requirements"></a>Az adatvédelmi követelmények meghatározása
Az Age of mobilitás, a legtöbb vállalat rendelkezik közös célja: engedélyezése a felhasználók a mobileszközeiket a helyszínen vagy távolról bárhonnan dolgozhatnak annak érdekében, hogy a termelékenység növelése. Vállalatok számára, hogy ezeknek a követelményeknek is fontos szempont annak érdekében, hogy a vállalati adatok biztonságos tartja fenn a felhasználók adatait és Védekezzen fenyegetések számát. Minden vállalat különböző követelményekkel, rendelkezhetnek különböző megfelelőségi szabályokat, amelyek változhatnak mely iparág szerint jár el a vállalat különböző tervezési döntésekhez vezethet. 

Vannak azonban bizonyos biztonsági szempontok megvizsgálta és érvényesítve, iparágtól függetlenül.

## <a name="data-protection-paths"></a>Data protection elérési utak
![](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Data protection elérési utak**

A fenti ábrán az identitás-összetevők lesz az elsőt ellenőrizhetők, mielőtt adatokhoz. Azonban ezeket az adatokat különböző állapota lehet fértek hozzá ez idő alatt. Ez az ábra a számok jelölik, amelyben adatok lehetnek bizonyos ponton található szerinti elérési utat. Ezek a számok alá vannak írva:

1. Adatok védelme az eszköz szintjén.
2. Adatok védelme az átvitel során.
3. Adatvédelem helyszíni rest címen.
4. Aktívan a felhőbeli adatok védelmére.

Szükséges, hogy a hibrid identitáskezelési megoldás a következőkre képes kihasználva a helyszíni és felhőbeli identity management-erőforrásokkal a felhasználó azonosítására, mielőtt az adatokhoz való hozzáférést. A hibrid identitáskezelési megoldás tervezésekor győződjön meg arról, hogy a szervezet igényeinek megfelelően válaszok a következő kérdéseket:

## <a name="data-protection-at-rest"></a>Inaktív adatok védelmére
Függetlenül az adatok inaktív állapotban (eszköz, felhőbeli vagy helyszíni) fontos elvégez egy vizsgálatot e tekintetben megérteni a szervezet igényeinek megfelelően. Ezen a területen győződjön meg arról, hogy a rendszer felkéri a következő kérdéseket:

* A vállalatának van szüksége az inaktív adatok védelmét?
  * Ha igen, az a hibrid identitáskezelési megoldás integrálhatják a jelenlegi helyszíni infrastruktúrát?
  * Ha igen, az a számítási feladatokat a felhőben található integrálhatják a hibrid identitáskezelési megoldás?
* Az a felhasználó hitelesítő adatait és más, a felhőben tárolt adatok védelmét tudja a felhőalapú identitáskezelést?

## <a name="data-protection-in-transit"></a>Adatok védelmére átvitel közben
Meg kell védeni az adatokat átvitel közben az eszköz és az Adatközpont között, vagy az eszköz és a felhő között. Azonban az átvitel alatt álló nem feltétlenül jelenti egy kommunikációs folyamat egy összetevővel kívül a cloud service; áthelyezi azt belsőleg is, mint például egy két virtuális hálózat. Ezen a területen győződjön meg arról, hogy a rendszer felkéri a következő kérdéseket:

* A vállalatának van szüksége az adatok védelmére átvitel?
  * Ha igen, az biztonságos vezérlők, például az SSL/TLS integrálhatják a hibrid identitáskezelési megoldás?
* A felhőalapú identitáskezelést megtartja nem aláírt forgalmat, és a directory-tárolóban (belül és adatközpontok között)?

## <a name="compliance"></a>Megfelelőség
Szabályzat, törvényeket és jogszabályi követelményeknek, amelyek a vállalati tartozik az iparágban függően változnak. Magas szabályozott iparágakban vállalatok kell cím Identitáskezelés – aggályokat kapcsolatos megfelelőségi problémákat. Identitás- és hozzáférés vonatkozó előírásoknak, például a Sarbanes-Oxley (SOX), az egészségbiztosítás hordozhatóságáról és Accountability Act (HIPAA), a Gramm-Leach-féle törvénynek (GLBA) és a fizetési Card Industry Data Security Standard (PCI DSS) olyan szigorú. A hibrid identitáskezelési megoldás, amely a vállalat legfontosabb funkcióit, hogy a rendszer megfelel a követelményeknek, egy vagy több szabályzatnak kell rendelkeznie. Ezen a területen győződjön meg arról, hogy a rendszer felkéri a következő kérdéseket:

* Az üzleti szabályozási követelményeknek megfeleljenek a hibrid identitáskezelési megoldás?
* Nem a hibrid identitáskezelési megoldás felépítette 
* a képességek, amely lehetővé teszi vállalata számára megfelelő szabályozási követelmények? 

> [!NOTE]
> Ügyeljen arra, hogy minden válaszról feljegyzéseket, és megismerheti a válaszok indokait. [Data Protection stratégia kidolgozása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) halad keresztül a rendelkezésre álló lehetőségek előnyeit és hátrányait az egyes lehetőségek.  A fenti melyik leginkább megfelelő lehetőséget az üzleti kiválaszthatja kérdések megválaszolása szükséges.
> 
> 

## <a name="next-steps"></a>További lépések
 [A Tartalomkezelés követelmények meghatározása](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)


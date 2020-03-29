---
title: Hibrid identitástervezés – incidensválasz-követelmények Azure | Microsoft dokumentumok
description: Határozza meg a hibrid identitáskezelési megoldás figyelési és jelentéskészítési képességeit, amelyet az informatikai rendszer a potenciális fenyegetések azonosítására és csökkentésére használhat fel
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52b5e37c29e4b3df3f171f683266b5d0a3e0c95d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109278"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás incidensválasz-követelményeinek meghatározása
A nagy- és közepes szervezetek valószínűleg [biztonsági incidensekre adott válaszokkal](https://technet.microsoft.com/library/cc700825.aspx) fognak rendelkezni, amelyek segítenek az informatikai szervezeteknek az incidens szintjének megfelelő műveletekben. Az identitáskezelő rendszer fontos eleme az incidens-válasz folyamatnak, mivel segítségével azonosítható, hogy ki hajtott végre egy adott műveletet a cél ellen. A hibrid identitáskezelési megoldásnak képesnek kell lennie olyan figyelési és jelentéskészítési képességek biztosítására, amelyeket az informatikai rendszer használhat a potenciális fenyegetés azonosítására és csökkentésére irányuló műveletek hez. Egy tipikus incidens-választervben a következő fázisok lesznek a terv részeként:

1. Első értékelés.
2. Váratlan kommunikáció.
3. Kárelhárítás és kockázatcsökkentés.
4. Azonosítása, hogy mi volt a kompromisszum és súlyossága.
5. Bizonyíték megőrzése.
6. Értesítés a megfelelő feleknek.
7. Rendszer-helyreállítás.
8. Dokumentáció.
9. Kár- és költségfelmérés.
10. A felülvizsgálat feldolgozása és megtervezése.

A kompromitmát és súlyossági fázis meghatározása során azonosítani kell a feltört rendszereket, a hozzáfért fájlokat, és meg kell határozni a fájlok érzékenységét. A hibrid identitásrendszer képesnek kell lennie arra, hogy megfeleljen ezeknek a követelményeknek, hogy segítsen azonosítani a felhasználót, aki ezeket a módosításokat. 

## <a name="monitoring-and-reporting"></a>Figyelés és jelentéskészítés
Az identitáskezelő rendszer sokszor segíthet a kezdeti értékelési fázisban is, főként akkor, ha a rendszer beépített naplózási és jelentéskészítési képességekkel rendelkezik. A kezdeti értékelés során a rendszergazdának képesnek kell lennie a gyanús tevékenység azonosítására, vagy a rendszernek képesnek kell lennie arra, hogy egy előre konfigurált feladat alapján automatikusan elindítsa azt. Számos tevékenység lehetséges támadásra utalhat, más esetekben azonban egy rosszul konfigurált rendszer számos hamis pozitív eredményt eredményezhet egy behatolásérzékelő rendszerben. 

Az identitáskezelő rendszernek segítenie kell az informatikai rendszergazdákat a gyanús tevékenységek azonosításában és jelentésében. Általában ezek a műszaki követelmények az összes rendszer figyelésével és olyan jelentési képességgel rendelkeznek, amely kiemelheti a potenciális fenyegetéseket. Az alábbi kérdések segítségével megtervezheti hibrid identitáskezelési megoldását, miközben figyelembe veszi az incidensek válaszának követelményeit:

* A vállalat a biztonsági incidensekre adott válasza van érvényben?
  * Ha igen, a jelenlegi identitáskezelő rendszer a folyamat részeként használatos?
* A vállalatnak azonosítania kell a felhasználók gyanús bejelentkezési kísérleteit különböző eszközökön?
* A vállalatnak fel kell mutatnia a potenciálisan feltört felhasználó hitelesítő adatait?
* A vállalatnak naplóznia kell a felhasználók hozzáférését és tevékenységét?
* A vállalatnak tudnia kell, hogy a felhasználó mikor állítja vissza a jelszavát?

## <a name="policy-enforcement"></a>Szabályzatbetartatás
A kárelhárítás és a kockázatcsökkentési fázis során fontos, hogy gyorsan csökkentse a támadás tényleges és lehetséges hatásait. Ez a cselekvés, hogy meg fogja tenni ezen a ponton lehet, hogy a különbség a kisebb és a nagyobb egyet. A pontos válasz a szervezettől és a támadás jellegétől függ. Ha a kezdeti értékelés arra a következtetésre jutott, hogy egy fiók feltörték, akkor a fiók letiltásához házirendet kell érvényesítenie. Ez csak egy példa, ahol az identitáskezelő rendszer lesz kihasználva. Az alábbi kérdések segítségével megtervezheti hibrid identitáskezelési megoldását, miközben figyelembe veszi, hogy a szabályzatok hogyan lesznek érvényesítve egy folyamatban lévő incidensre való reagáláshoz:

* Rendelkezik a vállalat olyan házirendjeivel, amelyek lehetővé teszik a felhasználók hozzáférését a hálózathoz, ha szükséges?
  * Ha igen, a jelenlegi megoldás integrálható a hibrid identitáskezelő rendszerrel, amelyet alkalmazni fog?
* A vállalatnak ki kell kényszerítenie a feltételes hozzáférést a karanténban lévő felhasználók számára? 

> [!NOTE]
> Minden válaszról készítsen feljegyzéseket, és ismerje meg a válaszok indokait. [Határozza meg az adatvédelmi stratégia](plan-hybrid-identity-design-considerations-data-protection-strategy.md) megy át a rendelkezésre álló lehetőségeket és előnyeit / hátrányait az egyes lehetőségeket.  A kérdések megválaszolásával kiválaszthatja, hogy melyik lehetőség felel meg legjobban üzleti igényeinek.
> 
> 

## <a name="next-steps"></a>További lépések
[Adatvédelmi stratégia meghatározása](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok – áttekintés](plan-hybrid-identity-design-considerations-overview.md)


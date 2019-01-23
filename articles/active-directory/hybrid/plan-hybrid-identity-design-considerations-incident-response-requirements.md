---
title: Hibrid identitás Tervező – Azure incidensválasz-követelmények |} A Microsoft Docs
description: Határozza meg a figyelési és jelentéskészítési funkciókat a hibrid identitáskezelési megoldás, amely szerint is javítható az informatikai műveletek azonosításához és elhárításához szükséges egy potenciális fenyegetések
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 5a5b166e9b90ede5f5c51b2c288ce1d2d6290ba6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464833"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Hibrid identitáskezelési megoldás incidensválasz-követelmények meghatározása
Nagy- vagy közepes szervezetek nagy valószínűséggel fog rendelkezni a [biztonsági incidensek megoldási](https://technet.microsoft.com/library/cc700825.aspx) segítségével történő informatikai műveleteket ennek megfelelően az incidens szintjét. Az identitáskezelési rendszerekkel az incidensmegoldási folyamat során fontos összetevője azért használható ki végzett egy adott művelet a célon azonosítása érdekében. A hibrid identitáskezelési megoldás, amely jól használható a figyelési és jelentéskészítési képességeket biztosít képesnek kell lennie az informatikai műveletek azonosításához és elhárításához szükséges potenciális fenyegetést. Egy tipikus incidensmegoldási terv a csomag részeként a következő fázisok fog rendelkezni:

1. Kezdeti felmérést.
2. Incidens kommunikációt.
3. Károk vezérlő és a kockázat csökkentése érdekében.
4. Milyen azonosítása biztonsági sérülés és a súlyosság volt.
5. Bizonyítékok megőrzését.
6. Értesítés a megfelelő feleknek.
7. Rendszer-helyreállítás.
8. Dokumentáció.
9. Sérülés és a költségek értékelése.
10. Folyamat és a csomag verziójának.

Során az azonosítását, amit a rendszergazda volt a biztonsági sérülés és a súlyossági fázisú, de a rendszerek biztonsága sérült, elért, és határozza meg azokat a fájlokat az érzékeny fájlok azonosítása érdekében. A hibrid identitáskezelő rendszerbe kell tudni teljesítése ezek a követelmények azonosításának elősegítése a felhasználót, hogy a változtatásokat. 

## <a name="monitoring-and-reporting"></a>Figyelés és jelentéskészítés
Sokszor az azonosítási rendszer is segíthetnek a kezdeti megfelelőségvizsgálati fázis főleg, ha a rendszer rendelkezik beépített naplózási és jelentéskészítési lehetőségeket. Az első vizsgálat során a rendszergazdának a gyanús tevékenységek azonosítása érdekében képesnek kell lennie, vagy a rendszer automatikusan alapú előre konfigurált feladatok eseményindító képesnek kell lennie. Számos tevékenység jelezheti a lehetséges támadások, azonban más esetekben a rosszul konfigurált rendszer vezethet a vakriasztások számát egy behatolás-észlelési rendszerben. 

Az identitáskezelési rendszerekkel kell segítséget nyújt a rendszergazdáknak, hogy azonosítsa és a jelentés az adott gyanús tevékenységek. Általában ezek a technikai követelmények teljesíthetők az összes rendszerek figyelése, és a egy jelentéskészítő képesség, amely képes kiemelni a potenciális fenyegetéseket. A hibrid identitáskezelési megoldás incidensválasz-követelmények figyelembe vételével alakítsa használja az alábbi kérdésekre:

* A vállalat rendelkezik olyan biztonsági incidensmegoldási helyen?
  * Ha igen, az aktuális identitáskezelési rendszerének használja a folyamat részeként?
* A vállalatának van szüksége, azonosíthatja a gyanús bejelentkezési kísérletet a felhasználók különböző eszközökön?
* A vállalatának van szüksége észleli a potenciális feltört felhasználói hitelesítő adatokat?
* A vállalatának van szüksége a felhasználó hozzáférési és művelet naplózását?
* Vállalatának meg kell ismernie, amikor egy felhasználó a jelszó alaphelyzetbe állítása?

## <a name="policy-enforcement"></a>Szabályzat érvénybe léptetése
Kárelhárítási és a kockázat csökkentésének-fázis során fontos gyorsan csökkentheti a támadás tényleges és lehetséges hatásait. Vesz igénybe, a művelet ezen a ponton végezhet egy kisebb és nagyobb egy közötti különbség. A pontos válasz a szervezet és a szolgálatára áll, amely a támadás természetének függ. Ha a kezdeti felmérést kötni, hogy egy fiókot feltörték, szüksége lesz a szabályzatban, ez a fiók blokkolása. Ez csupán egy példa, ahol az identitáskezelési rendszerekkel jól használható. Segítséget nyújtanak a hibrid identitáskezelési megoldás tervezése során figyelembe véve, hogyan házirendek érvénybe reagálni a folyamatban lévő incidens használja az alábbi kérdésekre:

* A vállalat rendelkezik házirendek felhasználók számára a hozzáférés a hálózati szükség esetén?
  * Ha igen, nem az aktuális megoldáshoz integrálhatja a hibrid identitáskezelési rendszerekkel, amelyek elfogadják kívánja?
* A vállalatának van szüksége a karanténba helyezett felhasználók esetében a feltételes hozzáférés kényszerítésére? 

> [!NOTE]
> Ügyeljen arra, hogy minden válaszról feljegyzéseket, és megismerheti a válaszok indokait. [Data protection stratégia kidolgozása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) halad keresztül a rendelkezésre álló lehetőségek előnyeit és hátrányait az egyes lehetőségek.  A fenti melyik leginkább megfelelő lehetőséget az üzleti kiválaszthatja kérdések megválaszolása szükséges.
> 
> 

## <a name="next-steps"></a>További lépések
[Data protection stratégia kidolgozása](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)


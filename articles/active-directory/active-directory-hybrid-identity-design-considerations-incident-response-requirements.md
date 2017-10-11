---
title: "Az Azure Active Directory hibrid identitáskezelési elrendezésével kapcsolatos szempontok - incidens rResponse követelmények meghatározása |} Microsoft Docs"
description: "Határozza meg a figyelési és jelentéskészítési szolgáltatásait a hibrid identitáskezelési megoldás, amely szerint is javítható az informatikai részleg feltételkészleteket azonosításában és kiküszöbölésében potenciális fenyegetések"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 536071ec61d093af243bfd42faa6bb404172fb8e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás incidensválasz-követelmények meghatározása
Közepes vagy nagy szervezetek valószínűleg fog rendelkezni a [biztonsági incidensekre adott reakciók](https://technet.microsoft.com/library/cc700825.aspx) lehetőségét informatikai műveletek ennek megfelelően az incidens szintjére. Az identity management rendszer fontos összetevője a incidensválasz folyamat oka használható végző felhasználók listáját egy bizonyos művelet a célon azonosítása érdekében. A hibrid identitáskezelési megoldás lehet kell biztosítania a figyelési és jelentéskészítési képességekkel, amelyek szerint kihasználható informatikai azonosításához, és a potenciális fenyegetések mérséklésére műveletekre. Egy tipikus incidensválasz tervben a következő fázisok lesz a terv részeként:

1. Kezdeti értékelése.
2. Incidens kommunikációt.
3. Kárelhárítási és a kockázat csökkentése érdekében.
4. Mi azonosítása biztonsági sérülés és a súlyosság volt.
5. Bizonyító adatok megőrzését.
6. Megfelelő felek értesítést.
7. Rendszer-helyreállítás.
8. Dokumentációját.
9. Sérülés és a költséghatékonyság értékelése.
10. Folyamat és a csomag verziójának.

Amit a rendszergazda azonosítása során biztonsági sérülés és a súlyosság fázisú, az, hogy melyik biztonsága sérült, hozzáfért lett, és határozza meg azokat a fájlokat érzékenységének fájlok szükség lesz. A hibrid identitáskezelési rendszer tudják ezeket a módosításokat létrehozó felhasználó azonosításának elősegítése érdekében ezek a követelmények teljesítéséhez. 

## <a name="monitoring-and-reporting"></a>Figyelés és jelentéskészítés
Az azonosítási rendszer többször is segíthetnek kezdeti felmérési fázisban főleg, ha a rendszer létrehozta a naplózás és a jelentéskészítési képességek. A kezdeti értékelés során kell, hogy a rendszergazda lehet azonosítani a gyanús tevékenységet, vagy a rendszer a feladat egy előre konfigurált automatikusan alapján eseményindító képesnek kell lennie. Számos tevékenység a lehetséges támadások jelezhet, azonban más esetekben a rosszul konfigurált rendszer vezethet a vakriasztások számos egy behatolás-észlelés rendszerben. 

Az identity management rendszer segítséget kell IT-rendszergazdák azonosítása és azokat a gyanús tevékenységek jelentése. Ezek a technikai követelmények általában minden rendszerek figyelése és a jelentéskészítési képességet is jelölje ki a lehetséges veszélyforrásokkal szemben, amelyek teljesíthetők. Az alábbi kérdésekre használatával alakítsa ki a hibrid identitáskezelési megoldás incidensválasz-követelmények figyelembe vételével:

* Nem a vállalat rendelkezik egy biztonsági incidensekre adott reakciók?
  * Ha igen, a jelenlegi felügyeleti identitásrendszere szerepel a folyamat részeként?
* Nem a vállalat kell azonosítani a gyanús bejelentkezési kísérletet a felhasználók különböző eszközökön?
* Vállalatának meg kell észleli a potenciális feltört felhasználói hitelesítő adatokat?
* Vállalatának meg kell való naplózását a felhasználó hozzáférési és művelet?
* Vállalatának meg kell ismernie, amikor a felhasználó a jelszó alaphelyzetbe állítása?

## <a name="policy-enforcement"></a>Házirendek érvényesítése
Kárelhárítási és a kockázatot csökkentő-fázis során fontos gyorsan csökkentheti a támadás tényleges és esetleges hatásait. Adott elvégzendő lesz ezen a ponton tehet egy kisebb és nagyobb egy közötti különbség. A tényleges válasz a szervezet és a támadás, amely akkor szembesülhetnek jellegétől függ. Ha a kezdeti assessment arra a következtetésre jutott, hogy a fiók biztonsági szempontból sérült, akkor ez a fiók letiltására vonatkozó szabályzat alkalmazása. Ez mindössze egy példa, ahol az identity management rendszer elkészítéséhez használja. Az alábbi kérdésekre használatával alakítsa ki a hibrid identitáskezelési megoldás tervezése során figyelembe véve, hogyan házirendekkel kényszeríti ki a folyamatban lévő incidens reagálni:

* Nem rendelkezik a vállalata házirendekkel helyen megtiltják a felhasználók számára a hozzáférés a hálózati szükség?
  * Ha igen, nem az aktuális megoldás címtárszolgáltatással hibrid identitás felügyeleti rendszerre fogad el kívánja?
* Vállalatának meg kell karanténba helyezés felhasználók feltételes hozzáférés kényszerítésére? 

> [!NOTE]
> Ügyeljen arra, hogy minden válaszról jegyzeteket, és megismerheti a válaszok indokait. [Data protection stratégia meghatározása](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) ismerteti a rendelkezésre álló lehetőségeket, illetve az egyes lehetőségek előnyeit és hátrányait.  Ezen kérdések mely leginkább megfelelő lehetőséget az üzleti megválaszolása szükséges.
> 
> 

## <a name="next-steps"></a>Következő lépések
[Data protection stratégia meghatározása](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)


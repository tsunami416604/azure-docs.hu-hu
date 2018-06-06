---
title: Hibrid identitás Tervező - tartalomkezelési követelményeit Azure |} Microsoft Docs
description: Annak megállapítása, az üzleti tartalomkezelési követelményeit betekintést nyújt. Általában akkor, amikor a felhasználó rendelkezik a saját eszközét, akkor lehet az általuk használt alkalmazás megfelelően a rendszer váltakozó több hitelesítő adatot is. Fontos megkülönböztetéséhez tartalmat lett létrehozva, és a vállalati hitelesítő adatok használatával létrehozott megfelelően személyes hitelesítő adataival. A megoldást kell is használhassák a felhőalapú szolgáltatások zökkenőmentes élményt biztosíthat a végfelhasználó közben az adatvédelem biztosításához a, és növeli az adatszivárgás elleni védelem.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: b414a1e93d5a0f4be5be82011482b492fd9ce378
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34800793"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás tartalomkezelési követelmények meghatározása
A Tartalomkezelés felméréséről a vállalati közvetlen hatással lehet a döntést mely hibrid identitáskezelési megoldás használata. A több eszközre és a felhasználók a saját eszközeik elterjedése rendelkező ([BYOD](https://aka.ms/byodcg)), a vállalat a saját adatok kell védeni, de azt is kell módosulna felhasználók adatait. Általában akkor, amikor a felhasználó rendelkezik a saját eszközét, akkor lehet az általuk használt alkalmazás megfelelően a rendszer váltakozó több hitelesítő adatot is. Fontos megkülönböztetéséhez tartalmat lett létrehozva, és a vállalati hitelesítő adatok használatával létrehozott megfelelően személyes hitelesítő adataival. A megoldást kell is használhassák a felhőalapú szolgáltatások zökkenőmentes élményt biztosíthat a végfelhasználó közben az adatvédelem biztosításához a, és növeli az adatszivárgás elleni védelem. 

A megoldást fogja javítható, különböző technikai vezérlőkkel ahhoz, hogy adja meg a tartalom kezelésére, az alábbi ábrán látható módon:

![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Az identitás-kezelő rendszer használni fogja biztonsági vezérlők**

A Tartalomkezelés követelmények fogja használni, általában az identitás-kezelő rendszer a következő területeken:

* Adatvédelem: erőforrás birtokló felhasználó azonosítására, és alkalmazza a megfelelő vezérlők egységének fenntartására szolgáló módszert.
* Adatok besorolása: azonosítsa a felhasználót vagy csoportot, és megfelelően a besorolását objektumhoz hozzáférési szintjét. 
* Az Adatszivárgás adatvédelem: biztonsági vezérlők védelmét az adatok kiszivárgásának elkerülésére kell interakciót folytatni a rendszer ellenőrzi a felhasználó identitását. Ez fontos is ellenőrzési célból naplózását.

> [!NOTE]
> Olvasási [felhő készítve az adatok besorolását](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) további információt az ajánlott eljárásokról és útmutatást az adatok besorolása érdekében.
> 
> 

Ha a hibrid identitáskezelési megoldás tervezésének részeként győződjön meg arról, hogy a szervezet igényeinek megfelelően a következő kérdéseket válaszok:

* A vállalat rendelkezik biztonsági vezérlő érvényesítését adatvédelem?
  * Ha igen, a biztonsági funkciók lesz képes a hibrid identitáskezelési megoldás, amely fogad el kívánja integrálni?
* A vállalat használ az adatok besorolásával?
  * Ha igen, van az aktuális megoldás képes a hibrid identitáskezelési megoldás, amely fogad el kívánja integrálni?
* Rendelkezik a vállalata jelenleg bármely, a megoldás az adatok kiszivárgásának? 
  * Ha igen, van az aktuális megoldás képes a hibrid identitáskezelési megoldás, amely fogad el kívánja integrálni?
* A vállalati erőforrásokhoz való hozzáférés naplózása nem kell?
  * Ha igen, milyen típusú erőforrásokat?
  * Ha igen, milyen szintű adatokat szükség?
  * Ha igen, ahol a biztonsági naplóban találhatók kell? A helyszíni vagy a felhőben?
* Vállalatának meg kell a bizalmas adatok (taj számok esetében, hitelkártyaszámok stb.) tartalmazó e-mailek titkosításához?
* Nem a vállalat titkosítania kell az összes dokumentumok/tartalma a külső üzleti partnerek megosztott?
* A vállalatának meg kell bizonyos típusú e-maileket a vállalati házirendeknek az érvényesítését (nincs válasz mindenkinek végezni, ne továbbítsa)?

> [!NOTE]
> Ügyeljen arra, hogy minden válaszról jegyzeteket, és megismerheti a válaszok indokait. [Data Protection stratégia meghatározása](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) ismerteti a rendelkezésre álló lehetőségeket, illetve az egyes lehetőségek előnyeit és hátrányait.  Ezen kérdések mely leginkább megfelelő lehetőséget az üzleti megválaszolása szükséges.
> 
> 

## <a name="next-steps"></a>További lépések
[Hozzáférés-vezérlési követelményeinek meghatározása](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)


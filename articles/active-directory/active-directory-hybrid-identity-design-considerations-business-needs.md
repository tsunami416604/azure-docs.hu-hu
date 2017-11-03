---
title: "Az Azure Active Directory hibrid identitáskezelési elrendezésével kapcsolatos szempontok - identitás követelmények meghatározása |} Microsoft Docs"
description: "Azonosítsa a vállalat üzleti igényei, amelyek a hibrid identitáskezelési kialakításának követelményei adható meg."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 6503034b3f5a17a2a42338c73329eef0b01f2f27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás az identitás-követelmények meghatározása
A hibrid identitáskezelési megoldás tervezésének első lépése a követelmények, ez a megoldás kihasználva fog üzleti szervezet.  Hibrid identitás (támogatott minden más felhőalapú megoldás, adja meg a hitelesítés) támogató szerepkörként elindul, és adja meg az új és érdekes képességekkel, amelyek a felhasználók új munkaterhelések feloldásához kerül be.  A munkaterhelések vagy a szolgáltatások, a felhasználók elfogadják kívánt szabja meg, a hibrid identitáskezelési kialakításának követelményei.  Hibrid identitás kihasználhatják a helyszínen kell ezeket a szolgáltatásokat és munkaterhelések és a felhőben.  

Meg kell vizsgálni, hogy mi az üzleti kulcsfontosságú elemeit most követelmény, és mi a vállalati a jövőben tervez. Ha nincs látható-e a hosszú távú stratégiát hibrid identitás tervezési, valószínűleg, hogy a megoldás nem lesz méretezhető az üzleti igények növekedésének és változásának megfelelően.   Ezután az alábbi ábrán egy példa egy hibrid identitás architektúra és a munkaterhelések vannak zárja nyitás alatt a felhasználók T. Ez az csak egy példa az új lehetőségeket, amelyek oldhatja fel, és egy teli hibrid identitás stratégia fel. 

A hibrid identitáskezelési architektúrájának részét képező egyes összetevői![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Az üzleti igények meghatározása
Minden vállalat különböző követelményekkel rendelkezik, akkor is, ha a vállalatok azonos iparágba, a valódi üzleti követelmények is eltérők lehetnek. Továbbra is használhatja az iparág ajánlott eljárásait, de végső soron a vállalat üzleti igényei, amelyek a hibrid identitáskezelési kialakításának követelményei adható meg. 

Győződjön meg arról, hogy a üzleti igények meghatározása a következő kérdések megválaszolásához:

* Keresi a vállalat informatikai működési költségek kivágni?
* A vállalat keresése (SaaS-alkalmazásokhoz, infrastruktúra) felhő eszközök védelmét?
* Az informatikai korszerűsítésére keresi a vállalat?
  * A felhasználók vannak, mobil, és kibővített informatikai részleg a DMZ, hogy a különböző típusú forgalom különböző erőforrások eléréséhez a kivételeket hozhat létre?
  * Rendelkezik a vállalata e modern felhasználók való közzétételhez szükséges, de nem könnyen újraírási örökölt alkalmazások?
  * Vállalatának meg kell elvégezni ezeket a feladatokat, és ezzel egy időben control alá vonásához?
* A felhasználói identitások biztosításához és a kockázatok csökkentése érdekében, hogy kihasználja a Microsoft Azure biztonsági segítséget a helyszíni szakértőinek új eszközök keresése a vállalat?
* A vállalat próbál hogyan távolíthatja el a dreaded "external" fiókok a helyszíni, és helyezze azokat a felhő, ahol azok már nem inaktív fenyegetést belül a helyszíni környezetben?

## <a name="analyze-on-premises-identity-infrastructure"></a>A helyszíni identitás-infrastruktúra elemzése
Most, hogy egy ötletet kapcsolatban a vállalat üzleti igényei, fel kell mérnie a helyszíni identitás-infrastruktúra. Ez a kiértékelés fontos az aktuális identitás-megoldás a felhőalapú identitás felügyeleti rendszerbe integrálhatja a műszaki követelmények meghatározásánál tart. Győződjön meg arról, hogy válaszoljon a következő kérdésekre:

* Milyen hitelesítési és engedélyezési megoldás does a vállalat helyszíni használni? 
* Rendelkezik a vállalata jelenleg helyszíni szinkronizálási szolgáltatások?
* A vállalat használ bármely harmadik fél Identitásszolgáltatók (IdP)?

Is kell figyelembe vennie a felhőszolgáltatások, amelyek a vállalati lehet. Nagyon fontos tudni, hogy a környezetében Szolgáltatottszoftver, IaaS és PaaS modellek aktuális integrációja értékelését végrehajtása. Győződjön meg arról, hogy ez az értékelés során a következő kérdések megválaszolása:

* Rendelkezik a vállalata a felhőbeli szolgáltatás szolgáltatója együtt?
* Ha igen, mely szolgáltatásokat használják?
* Ez az integráció jelenleg éles vagy a próbaüzem?

> [!NOTE]
> Ha nem rendelkezik egy pontos leképezése az alkalmazások és a felhőalapú szolgáltatások, használhatja a Cloud App Discovery eszközt. Ez az eszköz az informatikai részleg biztosíthat a szervezet üzleti és fogyasztói felhőalkalmazások láthatósága. Így minden eddiginél egyszerűbben tárhatók fel olyan IT-adatok a szervezetben, mint például a részletes használati minták és a felhőalkalmazásokhoz hozzáférő felhasználók. Az első lépésekért lásd: [a Cloud app discovery](active-directory-cloudappdiscovery-whatis.md).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Identity integration követelmények felmérése
Ezt követően fel kell mérnie integrációs identitáskövetelmények. Ez a kiértékelés fontos, hogy a felhasználók hogyan hitelesíti magát, a szervezet jelenléte megjelenését a felhőben, hogyan lehetővé teszi a szervezet engedélyezési és a felhasználói élmény folyamatainak kell műszaki követelményeinek meghatározását. Győződjön meg arról, hogy válaszoljon a következő kérdésekre:

* A szervezet használni összevonási, szabványon alapuló hitelesítési vagy mindkettő?
* Ez a követelmény az összevonási?  Miatt a következő:
  * Kerberos-alapú egyszeri bejelentkezés
  * A vállalat egy (vagy a belső fejlesztésű vagy 3. fél beépített) a helyszíni alkalmazások, amely SAML vagy hasonló összevonási képességekkel rendelkezik.
  * Többtényezős hitelesítés az intelligens kártyák keresztül. RSA securid-titkosítást, stb.
  * Ügyfelekre vonatkozó hozzáférési szabályok a címet az alábbi kérdésekre:
    1. Letilthatom az Office 365 szolgáltatásra, az ügyfél IP-címe alapján minden külső hozzáférés?
    2. Letilthatom az Office 365, az Exchange ActiveSync kivételével minden külső hozzáférés?
    3. Letilthatom minden külső hozzáférés az Office 365 szolgáltatásra, kivéve a böngészőalapú alkalmazások (OWA, SPO)
    4. Letilthatom az Office 365 összes külső hozzáférés kijelölt AD-csoportok tagjai számára
* Biztonsági vagy naplózási vonatkozik
* Az összevont hitelesítés már meglévő befektetések
* Milyen nevet a szervezet használja a tartományban, a felhőben?
* A szervezet rendelkezik az egyéni tartománynév?
  1. Az adott tartomány nyilvános és könnyen ellenőrizhető DNS-rendszerben?
  2. Ha nem, majd van egy nyilvános tartományt, amely egy alternatív UPN regisztrálni az Active Directory is használható?
* Megegyeznek a felhasználói azonosítók a felhő ábrázolását? 
* A szervezet rendelkezik a felhőalapú szolgáltatásokkal való integrációt igénylő alkalmazások?
* Rendelkezik a szervezete több tartományt, és azok minden hitelesítést fog használni standard vagy összevont?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Értékelje ki a környezetben futó alkalmazások
Most, hogy rendelkezik egy ötletet kapcsolatban a helyszíni és felhőalapú infrastruktúra, fel kell mérnie ezekben a környezetekben futó alkalmazást. Ez a kiértékelés fontos, hogy ezek az alkalmazások a felhő-identity felügyeleti rendszerbe integrálhatja műszaki követelményeinek meghatározása. Győződjön meg arról, hogy válaszoljon a következő kérdésekre:

* Lakhelyétől fog az alkalmazások?
* Felhasználók hozzáférhetnek a helyszíni alkalmazások?  A felhőben? Vagy mindkét?
* Vannak-e a meglévő alkalmazások és szolgáltatások állapotba, és helyezze azokat a felhő tervek?
* Vannak-e, amely a helyszínen található, vagy a felhőben található, használja a felhő új alkalmazások fejlesztéséhez tervek hitelesítés?

## <a name="evaluate-user-requirements"></a>Értékelje ki a felhasználói követelmények
Akkor is, a felhasználói követelmények kiértékeléséhez. Ez a kiértékelés fontos, hogy meghatározzák a szükséges előkészítésének és segítik a felhasználókat, akkor szüntesse meg a felhőhöz. Győződjön meg arról, hogy válaszoljon a következő kérdésekre:

* Felhasználók hozzáférhetnek alkalmazásokhoz a helyszínen?
* Felhasználók hozzáférhetnek alkalmazásaikat a felhőben?
* Honnan általában a felhasználók a helyszíni környezetben bejelentkezni?
* Hogyan fogja felhasználók jelentkezzen be a felhőben?

> [!NOTE]
> Ügyeljen arra, hogy minden válaszról jegyzeteket, és megismerheti a válaszok indokait. [Incidensválasz-követelmények meghatározása](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) ismerteti a rendelkezésre álló lehetőségeket, illetve az egyes lehetőségek előnyeit/hátrányait.  Ezen kérdések mely leginkább megfelelő lehetőséget az üzleti megválaszolása szükséges.
> 
> 

## <a name="next-steps"></a>Következő lépések
[Címtár-szinkronizálás követelmények meghatározása](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)


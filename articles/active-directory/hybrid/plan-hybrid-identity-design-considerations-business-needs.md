---
title: Hibrid identitáskezelési követelményei felhőkialakítási identitás Azure |} A Microsoft Docs
description: A vállalat üzleti igényei, amelyek vezethet, hogy meghatározza a hibrid identitás követelményeinek azonosítása.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: ac32e332b0e805c2261a47a3d8656160f977507e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488595"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás az identitáskezelési követelmények meghatározása
A hibrid identitáskezelési megoldás kialakításának első lépéseként feladata annak megállapítása, hogy ez a megoldás kihasználva lesz az üzleti szervezet követelményeinek.  Hibrid identitás (támogatott összes egyéb felhőalapú megoldások azáltal, hogy hitelesítés) támogató szerepkörként elindul, és mutat be olyan új és érdekes képességeket biztosítanak, zárolásának feloldása új számítási feladatok, a felhasználók számára.  Ezek a számítási feladatok vagy szolgáltatások, a felhasználók elfogadják kívánt fogja diktálni a hibrid identitás követelményeinek.  Ezek a szolgáltatások és számítási feladatok kell kihasználva hibrid identitás a helyszínen és a felhőben.  

Meg kell vizsgálni kulcsfontosságú elemeit azonosítására, hogy mi az üzleti mostantól a követelmény, és mi a vállalat a jövőben tervez. Ha nem rendelkezik a hosszú távú stratégiát hibrid identitás tervezési láthatóságát, valószínűleg, hogy a megoldás nem lesz méretezhető az üzleti igények növekedésének és változásának megfelelően.   T, akkor az alábbi ábra egy példát a számítási feladatok vannak folyamatban feloldotta a felhasználók számára és a egy hibrid identitás architektúra. Ez a csak egy példa az új lehetőségeket, amelyek oldhatja fel és egy szilárd hibrid identitás stratégia előtelepítve. 

Néhány összetevőt, amelyek részei a hibrid identitás-architektúra ![](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Üzleti igények meghatározása
Minden vállalat különböző követelményekkel, rendelkezik akkor is, ha ezek a vállalatok azonos iparágba, a valódi üzleti követelmények jelentősen változhat a részét képezik. Az iparág ajánlott eljárásait felhasználhatja, de végső soron a vállalat üzleti igényei, amelyek a hibrid identitás követelményeinek meghatározását. 

Ügyeljen arra, hogy a üzleti igények meghatározása a következő kérdések megválaszolásával:

* A vállalat informatikai üzemeltetési költségek Kivágás szeretne készíteni van?
* Van a vállalat szeretne tartalmai felhőalapú (SaaS-alkalmazások, infrastruktúra)?
* A vállalat szeretne az informatikai RÉSZLEG korszerűsítéséhez?
  * A felhasználók közül ez a nagyobb mobil- és erőforrás-igényű kivételek a DMZ-t, hogy eltérő típusú forgalom különböző erőforrások eléréséhez való létrehozásához?
  * Rendelkezik a vállalata az örökölt alkalmazásokat, közzé kell tenni a modern felhasználóknak szükséges, de nem könnyű újraírási?
  * Vállalatának meg kell elvégezni ezeket a feladatokat, és a egy időben ellenőrzés alá vonhatja?
* A cég igényeinek megfelelő biztonságossá tétele a felhasználók identitását, és a kockázat csökkentéséhez, és új eszközök, melyek a Microsoft Azure biztonsági szakértelem helyszíni megoldásokkal?
* A vállalat próbál távolíthatja el a helyszíni dreaded "external" fiókokat, és helyezze át őket a felhőben, ahol azok nem lesznek a helyszíni környezeten belül inaktív fenyegetést?

## <a name="analyze-on-premises-identity-infrastructure"></a>A helyszíni identitás-infrastruktúra elemzése
Most, hogy a vállalat üzleti igényei kapcsolatban van egy ötletem, kell kiértékelni a helyszíni identitás-infrastruktúrát. Ezt a próbaidőszakot fontos az aktuális identitás-megoldás a felhőalapú identitáskezelési rendszerének integrálható a technikai követelmények meghatározásánál tart. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Milyen hitelesítési és engedélyezési megoldás használható a vállalat a helyszínen? 
* Rendelkezik a vállalata jelenleg semmilyen a helyszíni szinkronizálási szolgáltatásokat?
* A vállalat bármely külső identitásszolgáltató (IdP) használ?

Is kell figyelembe venni a felhőszolgáltatásokat, előfordulhat, hogy a vállalat. Egy értékelés tudni, hogy az aktuális integráció SaaS, IaaS vagy PaaS modellel a környezetében nagyon fontos. Ügyeljen arra, hogy ez az értékelés közben a következő kérdések megválaszolásával:

* Rendelkezik a vállalata felhőszolgáltató integrálva vannak?
* Ha igen, mely szolgáltatások vannak használatban?
* Ez az integráció jelenleg éles környezetben, vagy egy próba?

> [!NOTE]
> A cloud Discovery összeveti a forgalmi naplóit a Microsoft Cloud App Security felhőalapú alkalmazáskatalógusával a több mint 16000 felhőalapú alkalmazásokat, amelyek szerint rangsorolunk és pontozunk több mint 70 kockázati tényezők alapján, adja meg a folyamatban lévő bepillantást nyerhetnek a felhőalapú használja, informatikai árnyék-infrastruktúrára, és a kockázat Árnyék be a szervezet informatikai kockázatot. Az első lépésekért lásd: [Cloud Discovery beállítása](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Identity integration-követelmények felmérése
Ezt követően kell az integrációs identitással kapcsolatos követelmények kiértékeléséhez. Ezt a próbaidőszakot fontos, hogy hogyan fogja hitelesíteni a felhasználókat, a szervezet jelenlét megjelenését a felhőben, hogyan lehetővé teszik a szervezet hitelesítési és a felhasználói élmény eseményeit kell technikai követelmények meghatározása. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* A szervezet használni összevonási, a standard hitelesítési vagy mindkettő?
* Ez a követelmény az összevonási?  Miatt a következő:
  * Kerberos-based SSO
  * A vállalat egy (vagy a belső fejlesztésű vagy a 3. fél létrehozása) a helyszíni alkalmazások SAML vagy hasonló összevonási funkciókat használó rendelkezik.
  * MFA-n keresztül az intelligens kártyák. RSA SecurID, és így tovább.
  * Ügyfél-hozzáférési szabályokat a címet az alábbi kérdésekre:
    1. Letilthatom az összes külső hozzáférés az Office 365, az ügyfél IP-címe alapján?
    2. Letilthatom az Office 365-höz, az Exchange ActiveSync kivételével minden külső hozzáférés?
    3. Letilthatom az összes külső hozzáférés Office 365-höz (OWA, SPO) böngészőalapú alkalmazások kivételével
    4. Letilthatom az Office 365 minden külső hozzáférés kijelölt AD-csoportok tagjai számára
* Biztonság és naplózás a problémák
* Az összevont hitelesítés már meglévő befektetéseket
* Milyen nevet fogja a szervezet használni a tartományhoz, a felhőben?
* A szervezet rendelkezik egy egyéni tartományt?
  1. Nyilvános és a DNS-könnyen ellenőrizhető, az adott tartomány?
  2. Ha nem érhető el, majd rendelkezik, amely egy alternatív UPN regisztrálni az Active Directory segítségével szabadon?
* A felhasználói azonosítók konzisztens felhőalapú ábrázolása az vannak? 
* A szervezet rendelkezik a felhőalapú szolgáltatásokkal való integrációt igénylő alkalmazások?
* Rendelkezik a szervezete több tartományt, és a használandó minden standard szintű vagy összevont hitelesítés?

## <a name="evaluate-applications-that-run-in-your-environment"></a>A környezetben futó alkalmazások kiértékelése
Most, hogy van egy ötletem kapcsolatban a helyszíni és felhőalapú infrastruktúrára, kell kiértékelni az ezekben a környezetekben futó alkalmazások. Ezt a próbaidőszakot fontos, hogy a felhőalapú identitáskezelési rendszerének ezeket az alkalmazásokat integrálhatja technikai követelmények meghatározása. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Ahol kapnak helyet az alkalmazások?
* Felhasználók hozzáférhetnek a helyszíni alkalmazások?  A felhőben? Vagy mindkettő?
* Vannak-e a meglévő alkalmazások számítási feladatai és a felhőbe történő áthelyezésének terveket?
* Vannak-e csomagok találhatók a helyszínen vagy a felhőben, amelyet használni fog a felhő új alkalmazások fejlesztéséhez hitelesítés?

## <a name="evaluate-user-requirements"></a>Felhasználó-követelmények felmérése
Akkor is, a felhasználói követelmények kiértékeléséhez. Ezt a próbaidőszakot fontos, hogy adja meg a lépéseket, amelyek az előkészítési és segítik a felhasználókat, hogy váltson a felhőre lesz szükség. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Felhasználók érik el a helyszíni alkalmazásokhoz?
* Felhasználók hozzáférhetnek a felhőbeli alkalmazások?
* Honnan általában a felhasználók saját helyszíni környezetükben bejelentkezni?
* Hogyan fogja bejelentkeznek a felhőbe?

> [!NOTE]
> Ügyeljen arra, hogy minden válaszról feljegyzéseket, és megismerheti a válaszok indokait. [Incidensválasz-követelmények meghatározása](plan-hybrid-identity-design-considerations-incident-response-requirements.md) a rendelkezésre álló lehetőségeket és az egyes lehetőségek előnyeit/hátrányai.  A fenti melyik leginkább megfelelő lehetőséget az üzleti kiválaszthatja kérdések megválaszolása szükséges.
> 
> 

## <a name="next-steps"></a>További lépések
[Határozza meg a címtár-szinkronizálás követelményei](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Lásd még
[Kialakítási szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)


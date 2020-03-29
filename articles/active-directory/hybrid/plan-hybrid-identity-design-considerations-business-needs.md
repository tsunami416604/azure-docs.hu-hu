---
title: A hibrid felhőalapú identitástervezés identitáskövetelményei Azure | Microsoft dokumentumok
description: Azonosítsa a vállalat üzleti igényeit, amelyek alapján meghatározhatja a hibrid identitástervezés követelményeit.
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
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ecc90e13f49c231d8d3ab0cff1de91443b80f21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65950899"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás identitáskövetelményeinek meghatározása
A hibrid identitáskezelési megoldás tervezésének első lépése a megoldás thasználó üzleti szervezet követelményeinek meghatározása.  A hibrid identitás támogató szerepkörként indul (hitelesítéssel támogatja az összes többi felhőalapú megoldást), és új és érdekes képességeket biztosít, amelyek új számítási feladatokat oldanak fel a felhasználók számára.  Ezek a számítási feladatok vagy szolgáltatások, amelyeket a felhasználók számára szeretne alkalmazni, diktálja a hibrid identitástervezés követelményeit.  Ezek a szolgáltatások és számítási feladatok kell kihasználni a hibrid identitás, mind a helyszíni és a felhőben.  

Meg kell, hogy menjen át ezeket a kulcsfontosságú szempontokat az üzleti megérteni, mi ez a követelmény most, és mi a cég tervei a jövőre nézve. Ha nem rendelkezik a láthatósága a hosszú távú stratégia hibrid identitás tervezés, valószínű, hogy a megoldás nem lesz skálázható, mint az üzleti igények növekedését és változását. Az alábbi ábrán egy hibrid identitásarchitektúra és a felhasználók számára feloldott számítási feladatok egy példa látható. Ez csak egy példa az összes új lehetőségeket, hogy lehet feloldani, és szállított egy szilárd hibrid identitás stratégia. 

Egyes összetevők, amelyek a hibrid identitásarchitektúra ![hibrid identitásarchitektúra hibrid identitásarchitektúra](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Határozza meg az üzleti igényeket
Minden vállalatnak különböző követelményei lesznek, még akkor is, ha ezek a vállalatok ugyanannak az iparágnak a részét képezik, a valós üzleti követelmények változhatnak. Továbbra is kihasználhatja az iparág bevált gyakorlatait, de végső soron a vállalat üzleti igényei vezetnek a hibrid identitástervezés követelményeinek meghatározásához. 

Ügyeljen arra, hogy válaszoljon a következő kérdésekre, hogy azonosítsa az üzleti igényeket:

* Vállalata csökkenteni szeretné az informatikai működési költségeket?
* Vállalata felhőalapú kellékek (SaaS-alkalmazások, infrastruktúra)biztosítását keresi?
* A vállalat modernizálni szeretné az informatikai informatikai webhelyet?
  * A felhasználók mobilabb és igényesinformatikai informatikai létrehozni kivételek a DMZ, hogy a különböző típusú forgalom eléréséhez különböző erőforrásokat?
  * Rendelkezik-e a vállalat a modern felhasználók számára közzétenni kívánt örökölt alkalmazásokkal, de nem könnyű átírni?
  * A vállalatnak el kell végeznie ezeket a feladatokat, és egyszerre kell ellenőrzés alá vonnia?
* Vállalata a felhasználók identitásának védelmére és a kockázat csökkentésére törekszik azáltal, hogy olyan új eszközöket hoz létre, amelyek kihasználják a Microsoft Azure-beli biztonsági szakértelmének szakértelmét a helyszínen?
* A vállalat megpróbál megszabadulni a rettegett "külső" fiókoktól a helyszínen, és áthelyezni őket a felhőbe, ahol már nem alvó fenyegetést jelentenek a helyszíni környezetben?

## <a name="analyze-on-premises-identity-infrastructure"></a>Helyszíni identitás-infrastruktúra elemzése
Most, hogy van egy ötlete a vállalati üzleti követelményekkel kapcsolatban, ki kell értékelnie a helyszíni identitás-infrastruktúrát. Ez a kiértékelés fontos a technikai követelmények meghatározásához a jelenlegi identitáskezelési megoldás integrálásához a felhőalapú identitáskezelő rendszerbe. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Milyen hitelesítési és engedélyezési megoldást használ a vállalat a helyszínen? 
* A vállalat a jelenleg rendelkezik helyszíni szinkronizálási szolgáltatásokkal?
* Használ a vállalata külső identitásszolgáltatókat??

Azt is tisztában kell lennie a felhőszolgáltatások, amelyek a vállalat esetleg. A környezetében a SaaS, IaaS vagy PaaS-modellekkel való jelenlegi integráció megértéséhez végzett értékelés nagyon fontos. Ügyeljen arra, hogy válaszoljon a következő kérdésekre az értékelés során:

* Van-e a vállalata integrációval egy felhőszolgáltatóval?
* Ha igen, milyen szolgáltatásokat használnak?
* Ez az integráció jelenleg a termelés, vagy ez egy kísérleti?

> [!NOTE]
> A Cloud Discovery elemzi a forgalmi naplókat a Microsoft Cloud App Security több mint 16 000 felhőalapú alkalmazáskatalógusával, amely több mint 70 kockázati tényező alapján rangsorolja és pontozta a forgalmat, hogy folyamatos betekintést nyújtson a felhőhasználatba, a Shadow IT-be és a felhőinformatikai kockázatra, amelyet a szervezetben jelent. Első lépésekért olvassa el A [felhőfelderítés beállítása](/cloud-app-security/set-up-cloud-discovery)című témakört.
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Identitásintegrációs követelmények kiértékelése
Ezután ki kell értékelnie az identitásintegrációs követelményeket. Ez a kiértékelés fontos a felhasználók hitelesítésének technikai követelményeinek meghatározásához, a szervezet felhőbeli jelenlétének, a szervezet engedélyezésének módjához és a felhasználói élmény hez. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* A szervezet összevonást, szabványos hitelesítést vagy mindkettőt fogja használni?
* Az összevonás követelmény?  A következők miatt:
  * Kerberos-alapú egyszeri bejelentkezés
  * A vállalat rendelkezik egy helyszíni alkalmazásokkal (beépített házon belül vagy harmadik fél), amely SAML vagy hasonló összevonási képességeket használ.
  * MFA intelligens kártyákon keresztül. RSA SecurID stb.
  * Az alábbi kérdéseket megválaszoló ügyfélhozzáférési szabályok:
    1. Letilthatom az Office 365-höz való összes külső hozzáférést az ügyfél IP-címe alapján?
    2. Letilthatom az Office 365 összes külső hozzáférését az Exchange ActiveSync kivételével?
    3. Letilthatom az Office 365 összes külső hozzáférését, kivéve a böngészőalapú alkalmazásokat (OWA, SPO)
    4. Letilthatok minden külső hozzáférést az Office 365-höz a kijelölt AD-csoportok tagjai számára
* Biztonsági/naplózási aggályok
* Már meglévő befektetés az összevont hitelesítésbe
* Milyen nevet fog használni a szervezet a felhőben lévő tartományunkhoz?
* Rendelkezik a szervezet egyéni tartománnyal?
  1. Ez a domain nyilvános és könnyen ellenőrizhető DNS-en keresztül?
  2. Ha nem, akkor van egy nyilvános, amely használható regisztrálni egy alternatív UPN AD?
* Konzisztensek a felhasználói azonosítók a felhőábrázoláshoz? 
* A szervezet rendelkezik olyan alkalmazásokkal, amelyek a felhőszolgáltatásokkal való integrációt igénylik?
* A szervezet több tartománnyal rendelkezik, és mindegyik szabványos vagy összevont hitelesítést fog használni?

## <a name="evaluate-applications-that-run-in-your-environment"></a>A környezetben futó alkalmazások kiértékelése
Most, hogy van egy ötlete a helyszíni és a felhőalapú infrastruktúrával kapcsolatban, ki kell értékelnie az ilyen környezetekben futó alkalmazásokat. Ez a kiértékelés fontos, hogy meghatározza a technikai követelményeket integrálni ezeket az alkalmazásokat a felhőalapú identitáskezelő rendszerbe. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Hol fognak élni a jelentkezéseink?
* A felhasználók hozzáférhetnek a helyszíni alkalmazásokhoz?  A felhőben? Vagy mindkettő?
* Vannak-e tervek, hogy a meglévő alkalmazás számítási feladatok, és helyezze át őket a felhőbe?
* Vannak-e olyan új alkalmazások kifejlesztése, amelyek a helyszínen vagy a felhőben találhatók, és felhőalapú hitelesítést fognak használni?

## <a name="evaluate-user-requirements"></a>Felhasználói követelmények kiértékelése
Azt is ki kell értékelnie a felhasználói követelményeket. Ez az értékelés fontos, hogy meghatározza a lépéseket, amelyek szükségesek lesznek a beszállás, és segíti a felhasználókat, ahogy áttérnek a felhőbe. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* A felhasználók a helyszínen fognak hozzáférni az alkalmazásokhoz?
* A felhasználók hozzáférhetnek az alkalmazásokhoz a felhőben?
* Hogyan felhasználók általában jelentkezzen be a helyszíni környezetben?
* Hogyan jelentkeznek be a felhasználók a felhőbe?

> [!NOTE]
> Minden válaszról készítsen feljegyzéseket, és ismerje meg a válaszok indokait. [Határozza meg az incidensválasz követelményeit,](plan-hybrid-identity-design-considerations-incident-response-requirements.md) és adja át a rendelkezésre álló lehetőségeket, és az egyes lehetőségek előnyeit/hátrányait.  A kérdések megválaszolásával kiválaszthatja, hogy melyik lehetőség felel meg legjobban üzleti igényeinek.
> 
> 

## <a name="next-steps"></a>További lépések
[Címtár-szinkronizálási követelmények meghatározása](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Lásd még
[Tervezési szempontok – áttekintés](plan-hybrid-identity-design-considerations-overview.md)


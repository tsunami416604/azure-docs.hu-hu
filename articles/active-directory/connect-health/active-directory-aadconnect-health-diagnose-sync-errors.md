---
title: Az Azure AD Connect Health - diagnosztizálása duplikált attribútum szinkronizálási hibák |} Microsoft Docs
description: Ez a dokumentum ismerteti a duplikált attribútum szinkronizálási hibák diagnosztizálása folyamata és a potenciális javítás az árva objektumra forgatókönyvek közvetlenül az Azure portálról.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 4a6e0924492c26c9bad4ed0af207ad9764c3cc5c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831897"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnosztizálásához és elhárításához duplikált attribútum szinkronizálási hibák

## <a name="overview"></a>Áttekintés
Jelölje ki a szinkronizálási hibák véve az távolabb egy lépést, a Azure Active Directory (Azure AD) Connect Health önkiszolgáló műveletekre vezet be. Ismétlődő attribútum szinkronizálási hibák hibaelhárítást végez, és kijavítja a objektumok árván marad az Azure AD.
A diagnosztika funkció a előnyök rendelkezik:
- Ismétlődő attribútum szinkronizálási hibák le típusra van szűkítve diagnosztikai eljárás biztosít. És adott javítások biztosít.
- Egy javítást alkalmaztunk dedikált forgatókönyvek vonatkozik, az Azure AD egyetlen lépésben hiba megoldása érdekében.
- Ez a szolgáltatás nincs frissítés vagy a konfiguráció szükséges.
Az Azure AD kapcsolatos további információkért lásd: [identitás-szinkronizálással és duplikált attribútum rugalmassági](https://aka.ms/dupattributeresdocs).

## <a name="problems"></a>Problémák
### <a name="a-common-scenario"></a>Egy általános forgatókönyv
Ha **QuarantinedAttributeValueMustBeUnique** és **AttributeValueMustBeUnique** szinkronizálási hibák fordulhat elő, gyakori, hogy egy **UserPrincipalName** vagy **Proxycímeket** ütközés az Azure ad-ben. A szinkronizálási hibák megoldhatja az ütköző adatforrás-objektum a helyszíni oldal frissítésével. A szinkronizálási hiba történt a következő szinkronizálás után fel lesz oldva. Például a lemezkép azt jelzi, hogy, hogy két felhasználók rendelkeznek-e az ütközést a **UserPrincipalName**. Mindkettő **Joe.J@contoso.com**. Az ütköző objektumok karanténba helyezve az Azure ad-ben.

![Szinkronizálási hiba történt egy gyakori alaphelyzete diagnosztizálása](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Árva objektumra forgatókönyv
Bizonyos esetekben előfordulhat, hogy egy meglévő felhasználó elveszíti a **Forráshorgony**. Az adatforrás-objektum törlése történt a helyszíni Active Directoryban. Azonban a törlés jel soha nem kapott szinkronizált módosítása az Azure ad Szolgáltatásba. Ez a veszteség okait, például a szinkronizálási motor problémák vagy a tartomány áttelepítése történik. Amikor ugyanazt az objektumot lekérdezi vissza vagy újból logikailag, egy meglévő felhasználó kell-e a felhasználót, hogy rendszerből való szinkronizálás a **Forráshorgony**. 

Ha egy meglévő felhasználót egy kizárólag felhőalapú objektum, megtekintheti a ütköző szinkronizálva az Azure AD-felhasználó is. A felhasználó nem egyeztethető a meglévő objektum szinkronban. Nincs újramegfeleltetése közvetlen mód a **Forráshorgony**. További tudnivalók a [meglévő Tudásbázis](https://support.microsoft.com/help/2647098). 

Tegyük fel a meglévő objektumot az Azure AD Joe licenc megőrzi. Egy újonnan szinkronizált objektumot egy másik **Forráshorgony** duplikált attribútum állapotban az Azure AD akkor következik be. A helyszíni Active Directoryban Joe változást az Azure AD Joe eredeti felhasználó (meglévő objektum) nem alkalmazható.  

![Szinkronizálási hiba árva objektumra forgatókönyv diagnosztizálása](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnosztikai és a hibaelhárítási lépéseket a Connect Health 
A diagnosztika funkció támogatja a felhasználói objektumok a következő duplikált attribútumokkal:

| Attribútum neve | Szinkronizálási hiba típusú|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique vagy AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique vagy AttributeValueMustBeUnique | 
| sipProxyAddress | AttributeValueMustBeUnique | 
| onPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Ez a funkció eléréséhez **globális rendszergazda** engedéllyel, vagy **közreműködő** RBAC beállításai közül, engedélyre szükség.
>

Kövesse a lépéseket a megadásával szűkíthető, a szinkronizálási hiba részletes adatait, és adjon meg pontosabb megoldások Azure-portálon:

![Szinkronizálási hiba diagnosztikai lépései](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

Az Azure portálról lépéseket néhány meghatározott kezelhető helyzetek azonosításához:  
1.  Ellenőrizze a **állapot diagnosztizálása** oszlop. Az állapotüzenet látható, ha nincs-e a szinkronizálási hiba elhárításához közvetlenül az Azure Active Directory egy lehetséges módszer. Más szóval a hibaelhárítási folyamat létezik, amely a hiba eset szűkítéséhez és potenciálisan megjavítani is.
| status | Mit jelent? |
| ------------------ | -----------------|
| Nem indult el | A diagnosztikai folyamat még nem meglátogatott. Diagnosztikai eredményétől függően végezzen el egy potenciális úgy lehet kijavítani a hibát a portálról közvetlenül van. |
| Manuális javítás szükséges | A hiba nem fér el a feltételeket a portálon elérhető javításokat. Vagy ütköző típusú objektumokat nem felhasználók, vagy már került a diagnosztikai lépéseit, és javítás megoldást nem volt elérhető a portálról. Az utóbbi esetben egy javítást a helyszíni oldal még mindig a megoldásoknak a valamelyikét. [További részletek a helyi javítások](https://support.microsoft.com/help/2647098). | 
| Folyamatban lévő szinkronizálás | Egy javítást alkalmaztak. A portál arra vár, törölje a hiba a következő szinkronizálási ciklusban. |
  >[!IMPORTANT]
  > A diagnosztikai állapot oszlop alaphelyzetbe állítja a minden egyes szinkronizálási ciklust követően. 
  >

2.  Válassza ki a **derítse** gomb a hiba részletei alapján. Néhány kérdések megválaszolása lesz, és azonosíthatja a szinkronizálási hiba részletes adatait. A kérdésekre adott válaszok azonosításához egyik árva objektum esetében.

3.  Ha egy **Bezárás** gombja megjelenik ugyan a diagnosztika végén érhető el egyetlen gyorsjavítást a portálról, a válaszok alapján. Tekintse át az előző lépésben megoldás. A helyszíni javítások még a megoldásokat. Válassza ki a **Bezárás** gombra. A jelenlegi szinkronizálási hiba állapotának vált **szükséges manuális javítás**. Az állapot marad, a jelenlegi szinkronizálási ciklusban.

4.  Azután egyik árva objektum esetében, megoldhatja a duplikált attribútumok szinkronizálási hibák közvetlenül a portálról. A folyamat indításához válassza ki a **alkalmazása javítsa** gombra. A jelenlegi szinkronizálási hiba frissítések állapotának **függőben lévő szinkronizálási**.

5.  A következő szinkronizálási ciklusban után a hiba el kell távolítani a listából.

## <a name="how-to-answer-the-diagnosis-questions"></a>A diagnosztikai kérdések megválaszolása hogyan 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>A felhasználó létezik a helyszíni Active Directoryban?

Ez a kérdés megpróbálja azonosítsa az adatforrás-objektumát a meglévő felhasználói a helyszíni Active Directoryból.  
1.  Ellenőrizze, hogy az Azure Active Directory rendelkezik a megadott objektum **UserPrincipalName**. Ha nem fogadja a hívást **nem**.
2.  Ha igen, ellenőrizze, hogy az objektum még a szinkronizálás hatókörében.  
  - Az Azure AD-kapcsolódási térbe keresni a megkülönböztető név használatával.
  - Ha az objektum található a **függőben lévő hozzáadása** állapot, fogadja a hívást **nem**. Az Azure AD Connect nem tud kapcsolódni az objektumot közvetlenül az Azure AD-objektum.
  - Ha az objektum nem található, fogadja a hívást **Igen**.

Ezekben a példákban a kérdés megpróbálja azonosítani e **Joe Jackson** továbbra is a helyszíni Active Directoryban.
Az a **egy gyakori alaphelyzete**, mindkét felhasználók **Joe Johnson** és **Joe Jackson** találhatók a helyszíni Active Directoryban. A karanténba helyezett objektumok két különböző felhasználók.

![Szinkronizálási hiba történt egy gyakori alaphelyzete diagnosztizálása](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

Az a **árva objektumra forgatókönyv**, csak az egyetlen felhasználó **Joe Johnson** megtalálható-e a helyszíni Active Directory:

![Szinkronizálási hiba árva objektum diagnosztizálása * felhasználó létezik * forgatókönyv](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Hajtsa végre a fenti fiókok tartoznak ugyanazon felhasználó?
Ez a kérdés ellenőrzi egy bejövő ütköző felhasználó és a meglévő felhasználói objektum az Azure AD-tekintse meg, ha ugyanazon felhasználó tartozik.  
1.  Az ütköző objektum újonnan szinkronizált az Azure Active Directoryhoz. Hasonlítsa össze az objektumok attribútumok:  
  - Megjelenítendő név
  - Egyszerű felhasználónév
  - Objektumazonosító
2.  Ha az Azure AD összehasonlítja őket, ellenőrizze, hogy az Active Directory van-e a megadott objektumok **UserPrincipalNames**. Válasz **nem** Ha mindkét találja.

A következő példában a két objektum tartozik ugyanahhoz a felhasználóhoz **Joe Johnson**.

![Szinkronizálási hiba árva objektum diagnosztizálása * azonos felhasználói * forgatókönyv](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Mi történik, a javítás az árva objektumra forgatókönyvben alkalmazása után
Alapján az előző kérdésekre adott válaszokat, láthatja a **alkalmazása javítsa** gombot, ha van egy Azure AD-ből érhető el. Ebben az esetben a helyszíni objektum szinkronizál-e egy váratlan Azure AD-objektum. A két objektum használatával vannak leképezve a **Forráshorgony**. A **alkalmazása javítsa** módosítása ezek vagy hasonló lépéseket:
1. Frissítések a **Forráshorgony** és az Azure ad-ben a megfelelő objektum.
2. Törli az ütköző objektumot az Azure AD, ha telepítve.

![Szinkronizálási hiba a javítás után diagnosztizálása](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> A **alkalmazása javítsa** módosítás csak azokra az esetekre árva objektumra vonatkozik.
>

Az előző lépések után a felhasználó hozzáférhet az eredeti erőforrást, amely egy hivatkozás található meglévő objektum. A **állapot diagnosztizálása** érték a listanézetben frissítései **függőben lévő szinkronizálási**. A szinkronizálási hiba történt a következő szinkronizálás után fel lesz oldva. Csatlakozás állapot lesz nem hosszabb megjelenítése a feloldott szinkronizálási hiba a listanézetben.


## <a name="faq"></a>GYIK
**K.** Mi történik, ha végrehajtása a **alkalmazása javítsa** sikertelen?  
**V.** Végrehajtása nem sikerül, akkor lehetséges, hogy az Azure AD Connect fut exportálási hiba. Frissítse a portál lapot, és ismételje meg a következő szinkronizálás után. Az alapértelmezett szinkronizálási ciklusban értéke 30 perc. 


**K.** Mi történik, ha a **meglévő objektum** kell lennie a törlendő objektum?  
**V.** Ha a **meglévő objektum** kell törölni, a folyamat nem tartalmaz, amely megváltoztak a **Forráshorgony**. Általában ezt úgy javíthatja ki azt a helyszíni Active Directoryból. 


**K.** Milyen engedély nem a felhasználó kell alkalmazni a javítást?  
**V.** **Globális rendszergazda**, vagy **közreműködő** RBAC beállításai van hozzáférése a diagnosztikai és a hibaelhárítási folyamat.


**K.** Van konfigurálása az Azure AD Connect, vagy a funkció az Azure AD Connect Health ügynök frissítéséhez?  
**V.** A diagnosztikai folyamat nem, a felhő alapú szolgáltatások teljes.


**K.** Ha a meglévő objektum helyreállítható törlése, a diagnosztikai folyamat teszi az objektum active újra?  
**V.** Nem, a javítás nem frissítése címtárobjektum-attribútumok eltérő **Forráshorgony**.

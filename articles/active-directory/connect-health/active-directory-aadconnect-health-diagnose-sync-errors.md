---
title: Az Azure AD Connect Health - diagnosztizálása duplikált attribútum szinkronizálási hibák |} Microsoft Docs
description: Ez a dokumentum ismerteti a duplikált attribútum szinkronizálási hibák diagnosztizálása folyamata és a potenciális javítsa ki az árva objektumra forgatókönyvek közvetlenül az Azure portálról.
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
ms.openlocfilehash: 0a345fd3443fb33d6f5912c8a04677091e20ecc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="duplicate-attribute-sync-errors-diagnosis-and-remediation"></a>Ismétlődő attribútum szinkronizálási hibák diagnosztizálása és eltávolítása 

## <a name="overview"></a>Áttekintés
Szinkronizálási hibák kiemelésének egy lépéssel tovább tart, az Azure Active Directory Connect Health bevezeti a duplikált attribútum szinkronizálási hibák és árva elhárítása az Azure AD egy önkiszolgáló műveletekre kezelését. A fő előnyt a diagnosztika funkció:
- Adja meg a diagnosztikai eljárás duplikált attribútum szinkronizálási hiba forgatókönyvek szűkítéséhez, és feltünteti az adott megoldások
- Azure ad-egyetlen kattintással a hiba megoldása érdekében dedikált forgatókönyvek javítás telepítése
- Ez a szolgáltatás nincs frissítés vagy a konfiguráció szükséges.
Az Azure AD kapcsolatos további részletekért olvassa el [rugalmasság duplikált](https://aka.ms/dupattributeresdocs).

## <a name="problems"></a>Problémák
### <a name="common-scenario"></a>Egy gyakori alaphelyzete
Ha **QuarantinedAttributeValueMustBeUnique** és **AttributeValueMustBeUnique** szinkronizálási hibák fordulhat elő, gyakori, hogy egyszerű felhasználónév vagy a Proxy-címek ütközés az Azure AD. A szinkronizálási hibák megoldhatja az ütköző forrás objektumot a helyi oldalon frissítésével. A szinkronizálási hiba történt a következő szinkronizálás után fel lesz oldva. Például az alábbi képen azt jelzi, hogy a két felhasználó tapasztal-e, mint a UserPrincipalName ütközés *Joe.J@contoso.com*. Az ütköző objektumok karanténba helyezve az Azure ad-ben. 

![Szinkronizálási hiba történt egy gyakori alaphelyzete diagnosztizálása](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Árva objektumra forgatókönyv
Alkalmanként azt tapasztalhatja, egy meglévő felhasználó elveszíti a Forráshorgony. A helyszíni AD történt az adatforrás-objektum törlését, de a törlés jel soha nem kapott szinkronizált módosítása az Azure ad Szolgáltatásba. Ez akkor fordulhat elő, például a szinkronizálási motor probléma vagy a tartomány áttelepítésére oknál. Ugyanahhoz az objektumhoz lett visszaállítva, vagy újra létrehozza, logikailag meglévő felhasználói a felhasználót, hogy a Forráshorgony szinkronizálás kell lennie. Felhő csak objektumként meglévő felhasználó is láthatja, hogy ütköző szinkronizálva az Azure AD-felhasználó, és nem egyeztethető a meglévő objektum szinkronban. Nincs a Forráshorgony újramegfeleltetése közvetlen mód. További információ a [meglévő KB](https://support.microsoft.com/help/2647098). A meglévő objektumot az Azure ad-ben például a Joe licenc megőrzi. Különböző forráshorgony újonnan szinkronizált objektum duplikált attribútum állapotban történt, az Azure AD. Változások a Joe helyszíni AD nem fogja tudni alkalmazható Joe eredeti felhasználó (meglévő objektum) Azure AD-ben.  

![Szinkronizálási hiba árva objektumra forgatókönyv diagnosztizálása](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnosztikai és a hibaelhárítási lépéseket a Connect Health 
A szolgáltatás támogatja a következő duplikált attribútumokkal rendelkező felhasználóobjektumot diagnosztizálásához:

| Attribútum neve | Szinkronizálási hiba típusú|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique vagy AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique vagy AttributeValueMustBeUnique | 
| sipProxyAddress | AttributeValueMustBeUnique | 
| onPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Igényel **globális rendszergazda** engedéllyel vagy **közreműködő** a Szerepalapú beállítások érhetők el ezt a szolgáltatást. 
>

Azure-portálon lépéseinek fogja tudni a megadásával szűkíthető, a szinkronizálási hiba részletes adatait, és adjon meg pontosabb megoldások:

![Szinkronizálási diagnosztizálása hiba diagnosztizálása lépései](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

Az Azure portálról lesz lépés néhány lépésben meghatározott kezelhető helyzetek azonosításához:  
1.  Derítse Állapot oszlopban az állapot jelennek meg van-e a potenciális hibaelhárítási adatfolyamok hiba esetben szűkítéséhez, és közvetlenül az Azure Active Directory potenciálisan javításához.
| status | Mit jelent? |
| ------------------ | -----------------|
| Nem indult el | A diagnosztikai folyamat nem meglátogatott. Függ a diagnosztikai eredmények nincs potenciálisan oly módon, hogy javítsa ki a hibát a portálról közvetlenül. |
| Manuális javítás szükséges | A hiba nem fér el a feltételeket a portálon elérhető javítás. A case típusok nincsenek felhasználók (2) már a végrehajtás során a diagnosztikai lépésekre, és nem, javítsa ki a portálon elérhető megoldás (1.) az ütköző objektum lehet. Ebben az esetben a helyszínen oldaláról javítás marad a megoldásoknak a valamelyikét. [További részletek a helyszíni-javítás](https://support.microsoft.com/help/2647098) | 
| Folyamatban lévő szinkronizálás | Javítsa ki lett alkalmazva. Törölje a hiba a következő szinkronizálási ciklusban vár. |
>[!IMPORTANT]
> A diagnosztikai állapot oszlop minden egyes szinkronizálási ciklust követően vissza kell állítani. 
>

2.  Kattintva **derítse** gomb a hiba a részletek panelen, akkor néhány kérdést és azonosítsa a szinkronizálási hiba részletes adatait. A kérdések megválaszolásával azonosítsa az árva objektumra forgatókönyvek esetében. 

3.  Ha egy **Bezárás** gomb a diagnosztika végén jelent gyorsjavítást nem áll rendelkezésre a portálról, az adott válaszok alapján. Tekintse át az előző lépésben megoldás. Javítsa ki a helyszínen marad a megoldásokat. A Bezárás gombra kattintást követően megtalálja a jelenlegi szinkronizálási hiba állapotának kell váltani **szükséges manuális javítás**. Az állapot megőrzi a jelenlegi szinkronizálási ciklusban.

4.  Amennyiben az árva objektum esetében, amelynél, lesz hárítsa el a duplikált attribútumok szinkronizálási hibák közvetlenül a portálról. Kattintson a a **alkalmazása javítsa** gombra a folyamat indításához. A jelenlegi szinkronizálási hiba állapotának frissíteni kíván kell **függőben lévő szinkronizálási**.

5.  A következő szinkronizálási ciklusban után a hiba el kell távolítani a listából.

## <a name="how-to-answer-the-diagnosis-questions"></a>A diagnosztikai kérdések megválaszolása hogyan 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Azonban a felhasználó szerepel a helyszíni Active Directory?

A kérdés megpróbálja azonosítani az adatforrás-objektum egy létező felhasználó helyszíni Active Directoryból.  
1.  Ellenőrizze, hogy az Active Directory rendelkezik-e a megadott UserPrincipalName objektum. Ha nem, a válasz nem.
2.  Ha igen, ellenőrizze, hogy az objektum még mindig a szinkronizálás hatókörébe esik-e.  
  - Hajtson végre keresést a DN-nel az Azure AD-összekötő-térben.
  - Az objektum található Ha a **függőben lévő hozzáadása** állapot, válaszolja meg nem. Az Azure AD Connect nincs csatlakozni az objektum a megfelelő AD-objektum.
  - Az objektum nem található, ha igennel válaszol.

> A következő ábra tart, például a kérdés megpróbálja megállapítani, hogy *Joe Jackson* továbbra is szerepel a helyszíni Active Directoryban.
A **egy gyakori alaphelyzete**, mind a felhasználó *Joe Johnson* és *Joe Jackson* szerepel a helyszíni Active Directoryban. A karanténba helyezett objektumok két különböző felhasználók.

![Szinkronizálási hiba történt egy gyakori alaphelyzete diagnosztizálása](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

> A **árva objektumra forgatókönyv**, csak egyetlen felhasználó- *Joe Johnson* kerül be a helyszíni Active Directoryból:

![Szinkronizálási hiba árva objektumra forgatókönyv diagnosztizálása](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-these-accounts-belong-to-the-same-user"></a>Ugyanahhoz a felhasználóhoz tartozik mindkét fiók?
A kérdés bejövő ütköző felhasználói és a meglévő felhasználói objektum, ellenőrzi az Azure AD, ha ugyanazon felhasználó tartozik.  
1.  Ütköző objektum újonnan szinkronizált az Azure Active Directoryhoz. Hasonlítsa össze az objektumot a:  
  - Megjelenített név
  - Egyszerű felhasználónév
  - Objektumazonosító
2.  Nem sikerült összehasonlítani a őket, ha ellenőrizze az Active Directory van-e a megadott UserPrincipalNames objektumok. Nemmel, ha mindkét találhatók.  

> Az alábbi esetben a két objektum tartozik ugyanahhoz a felhasználóhoz *Joe Johnson*.

![Szinkronizálási hiba árva objektumra forgatókönyv diagnosztizálása](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happened-after-fix-is-applied-for-orphaned-object-scenario"></a>Mi történt a javítás az árva objektumra forgatókönyv alkalmazása után
A válaszok emelt kérdések alapján, fogja látni **alkalmazása hárítsa el** gombra kattint, ha a javítás az Azure AD-ből érhető el. Ebben az esetben az a helyi objektum szinkronizálása egy váratlan Azure AD-objektum. A két objektum képezi le a "Forráshorgony" használatával. Az alkalmazás módosítás például műveleteket fogja elvégezni:
- A Forráshorgony frissítse a megfelelő objektum az Azure ad-ben.
- Az Azure AD az ütköző objektum törlését, ha azt mutatja be.

![Szinkronizálási hiba a javítás után diagnosztizálása](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> A javítás alkalmazni módosítása csak az árva objektumra esetekben vonatkozik.
>

A fenti lépéseket, miután a felhasználó érhessék el az eredeti erőforrás használata, amely hivatkozás található meglévő objektum lesz. A **állapot diagnosztizálása** érték a listanézetben frissíti kell **függőben lévő szinkronizálási**. Szinkronizálási hiba a következő szinkronizálás után fel lesz oldva. Csatlakozás állapotfigyelő fog megjelenni már megoldott szinkronizálási hiba listájából. 


## <a name="faq"></a>GYIK
 -  Mi történt, ha az alkalmazás végrehajtása sikertelen?  
Ha végrehajtása meghiúsul, a rendszer lehetséges az Azure AD Connect exportálási hiba fut egyidejűleg. Frissítse a portál lapot, és ismételje meg a következő szinkronizálás után. Az alapértelmezett szinkronizálási ciklust értéke 30 perc. 

 -  Mi történik, ha a **meglévő objektum** kell lennie a törlendő objektum?  
Ha a meglévő objektum ebben az esetben törölni kell, a folyamat nem jár Forráshorgony módosítását. Meg kell a helyszíni a javításhoz AD.  

 -  Mi az a felhasználót, hogy tudja alkalmazni a javítást engedély?  
Globális rendszergazda vagy a Szerepalapú beállítások közreműködői lesz a hozzáférése a diagnosztikai és a hibaelhárítási folyamat.

 -  I AAD-csatlakozás konfigurációban kell vagy a funkció az Azure AD Connect Health-ügynök frissítése?  
Diagnosztikai folyamat nem, a felhő alapú szolgáltatások teljes.

 -  Ha a meglévő objektum helyreállítható törlése, a diagnosztizálás folyamat visszaállítja az objektum újra aktívnak?  
A javítás nem, nem fogja frissíteni eltérő Forráshorgony eszközobjektum-attribútum. 

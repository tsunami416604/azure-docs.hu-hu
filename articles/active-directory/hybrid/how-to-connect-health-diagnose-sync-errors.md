---
title: Az Azure AD Connect Health - diagnosztizálhatja a duplikált attribútummal szinkronizálási hibák |} A Microsoft Docs
description: Ez a dokumentum ismerteti a duplikált attribútummal szinkronizálási hibák diagnosztizálása folyamatán és a egy lehetséges javítás az árva objektumra forgatókönyvek közvetlenül az Azure Portalról.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 7f367049cda76eea2dfb3040f714a7050a26520e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495904"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnosztizálásához és elhárításához a duplikált attribútummal szinkronizálási hibák

## <a name="overview"></a>Áttekintés
Még egy lépést tart, jelölje ki a szinkronizálási hibák, az Azure Active Directory (Azure AD) Connect Health a önkiszolgáló hibaelhárítást mutatja be. Ez a hibaelhárítást végez a duplikált attribútummal szinkronizálási hibák, és kijavítja a rendszer árva objektumokat az Azure ad-ből.
A diagnosztikai szolgáltatás alábbi előnyökkel jár:
- Diagnosztikai eljárást, amely a duplikált attribútummal szinkronizálási hibák leszűkíti biztosít. És adott javításokat biztosítja.
- A javítás dedikált forgatókönyvek az Azure ad-ből egyetlen lépésben a hiba megoldásához vonatkozik.
- Nincs frissítés vagy a konfiguráció a funkció engedélyezéséhez szükséges.
Az Azure AD kapcsolatos további információkért lásd: [identitás identitásszinkronizálás és ismétlődő attribútumok rugalmassága](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problémák
### <a name="a-common-scenario"></a>Egy általános forgatókönyv
Amikor **QuarantinedAttributeValueMustBeUnique** és **AttributeValueMustBeUnique** szinkronizálási hiba fordulhat elő, gyakori, hogy egy **UserPrincipalName** vagy **Proxycímeket** ütközés az Azure ad-ben. A szinkronizálási hibák megoldhatja az ütköző adatforrás-objektum a helyszíni oldalról frissítésével. A szinkronizálási hiba történt a következő szinkronizálás után fel lesz oldva. Például a lemezkép azt jelzi, hogy két felhasználók rendelkeznek-e az ütközés az **UserPrincipalName**. Mindkettő **Joe.J@contoso.com**. Az ütköző objektumok karanténba helyezve az Azure ad-ben.

![Szinkronizálási hiba gyakori forgatókönyv diagnosztizálása](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Árva objektumra mutat a forgatókönyv
Néha előfordul, Észreveheti, hogy egy meglévő felhasználó elveszíti a **Forráshorgony**. Az adatforrás-objektum törlése történt a helyszíni Active Directoryban. De a módosítás jel törlése soha nem lett szinkronizálva az Azure AD. Az adatvesztés történik, például a szinkronizálási motor problémák vagy a tartomány áttelepítés miatt. Ha ugyanazt az objektumot lekérdezi visszaállítása vagy újból, logikailag, egy meglévő felhasználó kell-e a felhasználót, hogy a szinkronizálás a a **Forráshorgony**. 

Ha egy meglévő felhasználót egy kizárólag felhőalapú objektumot, megtekintheti az ütköző, szinkronizálva az Azure AD-felhasználó is. A felhasználóegyeztetés nem sikerült a meglévő objektum szinkronizálva. Nincs közvetlen módon nem lehet újból a **Forráshorgony**. Tudjon meg többet a [meglévő Tudásbázis](https://support.microsoft.com/help/2647098). 

Tegyük fel a meglévő objektumot az Azure ad-ben megőrzi a Joe licence. Egy újonnan szinkronizált objektumot egy másik **Forráshorgony** duplikált attribútummal állapotban lévő Azure AD-ben történik. A helyszíni Active Directoryban János módosítása az Azure ad-ben Joe eredeti felhasználónak (meglévő objektum) alkalmazza.  

![Szinkronizálási hiba árva objektumra forgatókönyv diagnosztizálása](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>A diagnosztikai és hibaelhárítási lépések a Connect Health 
A diagnosztizálás funkció támogatja a felhasználói objektumok, a következő ismétlődő attribútumokkal:

| Attribútum neve | Alkalmazáshiba-típusok szinkronizációs|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique vagy AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique vagy AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Ez a funkció eléréséhez **globális rendszergazdai** engedéllyel, vagy **közreműködői** engedélyt az RBAC-beállítások megadása kötelező.
>

Kövesse a lépéseket leszűkíteni a szinkronizálási hiba részletes adatait, és további megoldásokat az Azure Portalról:

![Szinkronizálási hiba diagnosztikai lépések](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Az Azure Portalról lépéseket néhány meghatározott kezelhető helyzetek azonosításához:  
1.  Ellenőrizze a **állapotának diagnosztizálása** oszlop. Az állapot mutatja, hogy van-e egy lehetséges megoldást az egy szinkronizálási hiba közvetlenül az Azure Active Directoryból. Más szóval a hibaelhárítási folyamat, amely létezik is a hibaesetét szűkítéséhez és potenciálisan javítást.
| status | Mit jelent? |
| ------------------ | -----------------|
| Nincs elindítva | A diagnosztikai folyamat még nem járt. A diagnosztikai eredményei függően egy lehetséges megoldást az közvetlenül a portálról a szinkronizálási hiba van. |
| Manuális javítás szükséges | A hiba nem fér el a feltételeket a portálon elérhető javításokat. Mindkét ütköző típusú objektumokat nem a felhasználók, vagy már a diagnosztikai lépések végrehajtása, és a javítás megoldás nem érhető el a portálról. Az utóbbi esetben a javítás, ahonnan a helyszíni még mindig megoldás egyikét. [Tudjon meg többet a helyszíni javításokat](https://support.microsoft.com/help/2647098). | 
| Folyamatban lévő szinkronizálás | Egy javítást alkalmaztak. A portál vár, hogy törölje a hiba a következő szinkronizálási ciklus. |
  >[!IMPORTANT]
  > A diagnosztikai állapot oszlop minden egyes szinkronizálási ciklust követően alaphelyzetbe állnak. 
  >

2.  Válassza ki a **diagnosztizálása** gomb a hiba részletei alapján. Fog válaszolni néhány kérdést, és azonosíthatja a szinkronizálási hiba részletes adatait. A kérdésekre adott válaszok azonosíthatja, hogy az árva objektumra eset.

3.  Ha egy **Bezárás** gomb jelenik meg a diagnosztikai végén érhető nincs gyorsjavítás a portálról, a válaszok alapján. Tekintse meg az utolsó lépés látható a megoldás. A helyszíni javításokat azok továbbra is a megoldások. Válassza ki a **Bezárás** gombra. A jelenlegi szinkronizálási hiba állapotának vált, amennyiben az **manuális javítás szükséges**. Az állapot marad, a jelenlegi szinkronizálási ciklus során.

4.  Azután egy árva objektumra eset, oldható meg a duplikált attribútumok szinkronizálási hibák közvetlenül a portálról. A folyamat indításához válassza ki a **javítás alkalmazása** gombra. A jelenlegi szinkronizálási hiba frissítések állapotának **függőben lévő szinkronizálás**.

5.  A következő szinkronizálási ciklus után a hibát el kell távolítani a listából.

## <a name="how-to-answer-the-diagnosis-questions"></a>Hogyan lehet a diagnózis kérdésre 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>A felhasználó létezik a helyszíni Active Directoryban?

Azonosítsa az adatforrás-objektum a helyszíni Active Directoryból a meglévő felhasználó megpróbálja ezt a kérdést.  
1.  Ellenőrzi, hogy az Azure Active Directory tartalmazza-e a megadott objektum **UserPrincipalName**. Ha nem, akkor válaszoljon **nem**.
2.  Ha igen, ellenőrizze, hogy az objektum még a szinkronizálás hatókörében.  
  - Keresés az Azure ad-ben összekötőtérben a DN-t.
  - Ha az objektum megtalálható a **folyamatban lévő** állapotban van, választ **nem**. Az Azure AD Connect nem tud csatlakozni az objektumot a megfelelő Azure AD-objektum.
  - Ha az objektum nem található választ **Igen**.

Ezekben a példákban a kérdés megpróbálja azonosítani e **Joe Jackson** továbbra is a helyszíni Active Directoryban.
Az a **gyakori forgatókönyv**, mindkét felhasználók **Joe Johnson** és **Joe Jackson** jelen a helyszíni Active Directoryban. A karanténba helyezett objektumok két különböző felhasználók közül.

![Szinkronizálási hiba gyakori forgatókönyv diagnosztizálása](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Az a **árva objektumra mutat a forgatókönyv**, csak egyetlen felhasználó **Joe Johnson** szerepel a helyszíni Active Directory:

![Szinkronizálási hiba árva objektum diagnosztizálása * felhasználó létezik * forgatókönyv](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Hajtsa végre ezeket a fiókokat mindkét tartozik ugyanahhoz a felhasználóhoz?
Ezt a kérdést ellenőrzi egy bejövő ütköző felhasználót és a meglévő felhasználói objektum megjelenítéséhez, ha ugyanazon felhasználó tartoznak az Azure AD-ben.  
1.  Az ütköző objektumot az Azure Active Directoryban újonnan szinkronizálva van. Hasonlítsa össze az objektumok attribútumok:  
  - Megjelenítendő név
  - Felhasználó egyszerű neve
  - Objektumazonosító
2.  Ha az Azure AD nem tudja őket összehasonlítani, ellenőrizze, hogy az Active Directory rendelkezik-e a megadott objektum **objektum megtalálható, nemmel**. Válasz **nem** Ha is talál.

A következő példában a két objektum tartozik ugyanahhoz a felhasználóhoz **Joe Johnson**.

![Szinkronizálási hiba árva objektum diagnosztizálása * ugyanazon felhasználó * forgatókönyv](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Mi történik az árva objektumra mutat a forgatókönyvben a javítás alkalmazása után
Az előző kérdésekre adott válaszai alapján, látni fogja a **javítás alkalmazása** gombra, ha a javítás érhető el az Azure ad-ből. Ebben az esetben a helyszíni objektum szinkronizálása folyamatban van egy nem várt Azure AD-objektum. A két objektum használatával vannak leképezve a **Forráshorgony**. A **javítás alkalmazása** módosítás életbe léptetése ezek vagy hasonló lépéseket követve:
1. Frissítések a **Forráshorgony** az Azure ad-ben a megfelelő objektum.
2. Törli az ütköző objektumot az Azure ad-ben, ha telepítve.

![Szinkronizálási hiba kivizsgálásában a javítás után](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> A **javítás alkalmazása** a módosítás csak azokra az esetekre, árva objektumra vonatkozik.
>

Az előző lépések után a felhasználó hozzáférhessen az eredeti erőforrás, amely egy meglévő objektum egy hivatkozás. A **állapotának diagnosztizálása** frissíti a listanézetben érték **folyamatban lévő szinkronizálás**. A szinkronizálási hiba történt a következő szinkronizálás után fel lesz oldva. Connect Health újraindításával nem hosszabb megjelenítése a feloldott szinkronizálási hiba a listanézetben.

## <a name="failures-and-error-messages"></a>Hibák és hibaüzenetek
**Ütköző attribútummal rendelkező felhasználó helyreállítható törlése az Azure Active Directoryban. Győződjön meg arról, a felhasználó nem rögzített újrapróbálkozás előtt törölni.**  
Ütköző attribútum az Azure ad-ben a felhasználót törölni kell a javítás alkalmazása előtt. Tekintse meg [véglegesen törli a felhasználó Azure AD-ben hogyan](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) mielőtt újra próbálkozna a javítást. A felhasználó is automatikusan törlődni fognak 30 nap után véglegesen helyreállíthatóan törölt állapotban. 

**A felhőalapú felhasználói a bérlő frissítése forráshorgony nem támogatott.**  
Az Azure AD a felhőalapú felhasználói nem rendelkezhet forráshorgony. A forráshorgony frissítése nem támogatott ebben az esetben. Manuális javítás megadása kötelező a helyszínen. 

## <a name="faq"></a>GYIK
**K.** Mi történik, ha végrehajtását a **javítás alkalmazása** sikertelen?  
**V.** Végrehajtása meghiúsul, ha, lehetséges, hogy az Azure AD Connect fut-e exportálási hiba. A portáloldal frissítési, és ismételje meg a következő szinkronizálás után. Az alapértelmezett szinkronizálási ciklus érték 30 perc. 


**K.** Mi történik, ha a **meglévő objektum** kell lennie az objektum, melyet törölni?  
**V.** Ha a **meglévő objektum** kell hagyni, a folyamat módosítását nem járnak **Forráshorgony**. Általában hogy javíthassuk azt a helyszíni Active Directoryból. 


**K.** Milyen engedélyekre kell a felhasználó a alkalmazni a javítást?  
**V.** **Globális rendszergazda**, vagy **közreműködői** az RBAC-beállítások a van hozzáférése a diagnosztikai és hibaelhárítási folyamatának.


**K.** Kell konfigurálnia az Azure AD Connect, vagy a funkció az Azure AD Connect Health-ügynök frissítése?  
**V.** Nem, a diagnosztikai folyamatot egy olyan teljes felhőalapú szolgáltatás.


**K.** Ha a meglévő objektumot helyreállíthatóan törölt, a diagnosztikai folyamat objektummá alakíthatja az aktív újra?  
**V.** Nem, a javítás nem frissítése címtárobjektum-attribútumok nem **Forráshorgony**.

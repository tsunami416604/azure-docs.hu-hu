---
title: A felhő kiépítés igény szerinti kiépítés Azure AD Connect
description: Ez a cikk az igény szerinti üzembe helyezési funkciót ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72623d69f9fb0cbc68758a362bb977e5e02d2c00
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637171"
---
# <a name="azure-ad-connect-cloud-provisioning-on-demand-provisioning"></a>A felhő kiépítés igény szerinti kiépítés Azure AD Connect

Azure AD Connect a Felhőbeli kiépítés új funkciót vezetett be, amely lehetővé teszi a konfigurációs változások tesztelését, ha ezeket a módosításokat egyetlen felhasználóra alkalmazza.  Ezzel ellenőrizheti, hogy a konfiguráción végrehajtott módosítások megfelelően lettek-e alkalmazva, és megfelelően szinkronizálva vannak-e az Azure AD-vel.  

> [!IMPORTANT] 
> Igény szerinti kiépítés használata esetén a rendszer nem alkalmazza a hatóköri szűrőket a kiválasztott felhasználóra.  Ez azt jelenti, hogy igény szerinti üzembe helyezést használhat a megadott szervezeti egységeken kívüli felhasználóknál.


## <a name="using-on-demand-provisioning"></a>Igény szerinti kiépítés használata
Az új funkció használatához kövesse az alábbi lépéseket.


1.  A Azure Portal válassza a **Azure Active Directory**lehetőséget.
2.  Válassza a **Azure ad Connect**lehetőséget.
3.  Válassza a **kiépítés kezelése**lehetőséget.

    ![Kiépítés kezelése](media/how-to-configure/manage1.png)
4. A **konfiguráció**területen válassza ki a konfigurációt.
5. Az **ellenőrzés** alatt kattintson a **felhasználó kiépítése** gombra. 

 ![Felhasználó kiépítése](media/how-to-on-demand-provision/on-demand2.png)

6. Az igény szerinti üzembe helyezési képernyőn.  Adja meg a felhasználó **megkülönböztető nevét** , és kattintson a **kiépítés** gombra.  
 
 ![Igény szerinti kiépítés](media/how-to-on-demand-provision/on-demand3.png)
7. Ha befejeződik, megjelenik egy sikeres képernyő és 4 zöld jelölőnégyzet, amely azt jelzi, hogy a rendszer sikeresen kiépített.  A hibák a bal oldalon jelennek meg.

  ![Success](media/how-to-on-demand-provision/on-demand4.png)

Most megtekintheti a felhasználót, és meghatározhatja, hogy alkalmazta-e a konfigurációban végrehajtott módosításokat.  A dokumentum hátralévő része leírja azokat az egyes szakaszokat, amelyek egy sikeresen szinkronizált felhasználó részleteiben jelennek meg.

## <a name="import-user-details"></a>Felhasználó adatainak importálása
Ez a szakasz a Active Directoryból importált felhasználóval kapcsolatos információkat tartalmazza.  A felhasználó így néz ki az Azure AD-ben való üzembe helyezés előtt.  Az információk megjelenítéséhez kattintson a **részletek megtekintése** hivatkozásra.

![Felhasználó importálása](media/how-to-on-demand-provision/on-demand5.png)

Ezen információk használatával megtekintheti az importált különböző attribútumokat és azok értékeit.  Ha egyéni attribútum-hozzárendelést hozott létre, Itt láthatja az értéket.
![Felhasználó adatainak importálása](media/how-to-on-demand-provision/on-demand6.png)

## <a name="determine-if-user-is-in-scope-details"></a>Annak megállapítása, hogy a felhasználó hatóköre részletes-e
Ez a szakasz azt ismerteti, hogy az Azure AD-ba importált felhasználó hatókörben van-e.  Az információk megjelenítéséhez kattintson a **részletek megtekintése** hivatkozásra.

![Felhasználói hatókör](media/how-to-on-demand-provision/on-demand7.png)

Ezen információk használatával további információkat tekinthet meg a felhasználók hatóköréről.

![Felhasználói hatókör részletei](media/how-to-on-demand-provision/on-demand10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>Felhasználó egyeztetése a forrás-és a célként megadott rendszer részletei között
Ez a szakasz arról nyújt tájékoztatást, hogy a felhasználó már létezik-e az Azure AD-ben, és hogy egy új felhasználó üzembe helyezése helyett kell-e csatlakoznia.  Az információk megjelenítéséhez kattintson a **részletek megtekintése** hivatkozásra.
![Részletek megtekintése](media/how-to-on-demand-provision/on-demand8.png)

Ezen információk alapján megtekintheti, hogy talált-e egyezést, vagy új felhasználót fog-e létrehozni.

![Felhasználói adatok](media/how-to-on-demand-provision/on-demand11.png)

A megfeleltetés részletei egy üzenetet jelenítenek meg a következő három művelet egyikével.  Ezek a következők:
- Létrehozás – felhasználó létrehozása az Azure AD-ben
- Frissítés – a felhasználó a konfigurációban történt változás alapján frissül.
- Delete (Törlés) – a felhasználó törlődik az Azure AD-ből.

A végrehajtott művelet típusától függően az üzenet változhat.

## <a name="perform-action-details"></a>Művelet részleteinek végrehajtása
Ez a szakasz a konfiguráció alkalmazása után az Azure AD-ba kiosztott vagy exportált felhasználóval kapcsolatos információkat tartalmaz.  Így néz ki a felhasználó az Azure AD-ben való üzembe helyezés után.  Az információk megjelenítéséhez kattintson a **részletek megtekintése** hivatkozásra.
![Művelet részleteinek végrehajtása](media/how-to-on-demand-provision/on-demand9.png)

Ezen információk használatával megtekintheti az attribútumok értékeit a konfiguráció alkalmazása után.  Ugyanúgy néznek ki, mint az importált vagy a különböző elemek?  A konfiguráció alkalmazása sikeres?  

Ezzel a folyamattal nyomon követheti az attribútumok átalakítását, ahogy az a felhőben és az Azure AD-bérlőn halad át.

![nyomkövetési attribútum](media/how-to-on-demand-provision/on-demand12.png)

## <a name="next-steps"></a>További lépések 

- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
- [A Azure AD Connect Cloud kiépítés telepítése](how-to-install.md)
 
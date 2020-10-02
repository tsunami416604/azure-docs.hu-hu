---
title: Új ügynök konfigurációjának Azure AD Connect a felhőbe
description: Ez a cikk a felhőalapú kiépítés telepítését ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b18629a776dd98950f49b1f607cbc876abcd9c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628891"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Új konfiguráció létrehozása Azure AD Connect felhőalapú kiépítés számára

Miután telepítette az ügynököt, be kell jelentkeznie a Azure Portalba, és konfigurálnia kell a Azure Active Directory (Azure AD) kapcsolódási felhőhöz való üzembe helyezését. Az ügynök engedélyezéséhez kövesse az alábbi lépéseket.

## <a name="configure-provisioning"></a>Kiépítés konfigurálása
A kiépítés konfigurálásához kövesse az alábbi lépéseket.

 1. A Azure Portal válassza a **Azure Active Directory**
 2. Válassza a **Azure ad Connect**lehetőséget.
 3. Válassza a **kiépítés kezelése**lehetőséget.

 ![Kiépítés kezelése](media/how-to-configure/manage1.png)
 
 4. Válassza az **Új konfiguráció**lehetőséget.
 5. A konfiguráció képernyőn válassza ki a tartományt, és adja meg, hogy engedélyezi-e a jelszó-kivonat szinkronizálását.  Kattintson a **Létrehozás**gombra.  
 
 ![Új konfiguráció létrehozása](media/how-to-configure/configure1.png)


 6.  Ekkor megnyílik a kiépítési konfiguráció szerkesztése képernyő.

   ![Konfiguráció szerkesztése](media/how-to-configure/configure2.png)

 7. Adja meg az **értesítő e-mailt**. Az e-mail értesítést kap, ha a kiépítés állapota nem kifogástalan.
 8. Helyezze át a választót az engedélyezéshez, majd válassza a mentés lehetőséget.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Hatókör-kiépítés adott felhasználókra és csoportokra
Az ügynök hatókörével a helyi Active Directory csoportok vagy szervezeti egységek használatával szinkronizálhatja az adott felhasználókat és csoportokat. Nem konfigurálhat csoportokat és szervezeti egységeket a konfiguráción belül. 

 1.  A Azure Portal válassza a **Azure Active Directory**lehetőséget.
 2. Válassza a **Azure ad Connect**lehetőséget.
 3. Válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.
 4. A **konfiguráció**területen válassza ki a konfigurációt.

 ![Konfigurációs szakasz](media/how-to-configure/scope1.png)
 
 5. A **Konfigurálás**területen válassza a **hatókör-szűrők szerkesztése** lehetőséget a konfigurációs szabály hatókörének módosításához.
 ![Hatókör szerkesztése](media/how-to-configure/scope3.png)
 7. A jobb oldalon módosíthatja a hatókört.  Ha elkészült, kattintson a **kész**  gombra, és **mentse a mentést** .
 8. A hatókör módosítása után [újra kell indítani a kiépítés](#restart-provisioning) szolgáltatást a módosítások azonnali szinkronizálásának indításához.

## <a name="attribute-mapping"></a>Attribútumleképezés
Azure AD Connect a felhő-kiépítés lehetővé teszi az attribútumok egyszerű leképezését a helyszíni felhasználói/csoport objektumai és az Azure AD objektumai között.  Az alapértelmezett attribútum-hozzárendelések testreszabhatók az üzleti igényeknek megfelelően. Így módosíthatja vagy törölheti a meglévő attribútum-hozzárendeléseket, illetve létrehozhat új attribútum-hozzárendeléseket is.  További információ: [attribútumok leképezése](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Igény szerinti kiépítés
Azure AD Connect a felhő-kiépítés lehetővé teszi a konfigurációs változások tesztelését, ha ezeket a módosításokat egyetlen felhasználóra vagy csoportra alkalmazza.  Ezzel ellenőrizheti, hogy a konfiguráción végrehajtott módosítások megfelelően lettek-e alkalmazva, és megfelelően szinkronizálva vannak-e az Azure AD-vel.  További információ: [igény szerinti kiépítés](how-to-on-demand-provision.md).

## <a name="restart-provisioning"></a>Kiépítés újraindítása 
Ha nem szeretné megvárni a következő ütemezett futtatást, aktiválja a kiépítési futtatást az **Újraindítás kiépítés** gomb használatával. 
 1.  A Azure Portal válassza a **Azure Active Directory**lehetőséget.
 2. Válassza a **Azure ad Connect**lehetőséget.
 3.  Válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.
 4. A **konfiguráció**területen válassza ki a konfigurációt.

   ![Konfiguráció kiválasztása a kiépítés újraindításához](media/how-to-configure/scope1.png)

 5. A felső részen válassza a **kiépítés újraindítása**lehetőséget.

## <a name="remove-a-configuration"></a>Konfiguráció eltávolítása
A konfiguráció törléséhez kövesse az alábbi lépéseket.

 1.  A Azure Portal válassza a **Azure Active Directory**lehetőséget.
 2. Válassza a **Azure ad Connect**lehetőséget.
 3. Válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.
 4. A **konfiguráció**területen válassza ki a konfigurációt.
   
   ![Konfiguráció eltávolítására szolgáló konfigurációs beállítás](media/how-to-configure/scope1.png)

 5. A konfiguráció képernyő felső részén válassza a **Törlés**lehetőséget.

>[!IMPORTANT]
>A konfiguráció törlése előtt nincs megerősítés. Győződjön meg arról, hogy ezt a műveletet szeretné végrehajtani a **Törlés**kiválasztása előtt.


## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)

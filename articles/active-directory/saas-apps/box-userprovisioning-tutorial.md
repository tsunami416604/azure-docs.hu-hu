---
title: 'Oktatóanyag: A box konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Box között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1397b4189a9c2c15e3878687ea8c67c1da7567f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058569"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Oktatóanyag: A doboz konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy a Box és az Azure AD-ben végrehajtott lépéseket jelenítse meg a felhasználói fiókok automatikus kiépítéséhez és az Azure AD boxból való dekprovision kiépítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Box alkalmazáshoz a következő elemekre van szükség:

- Egy Azure AD-bérlő
- A Box Üzleti terv vagy jobb

> [!NOTE]
> Ha teszteli az oktatóanyag lépéseit, azt javasoljuk, hogy *ne* használjon éles környezetet.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi ajánlásokat:

- Ne használja az éles környezetet, kivéve, ha ez szükséges.
- Ha nem rendelkezik Egy Azure AD próbakörnyezettel, [egy hónapos próbaverziót kaphat.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="assigning-users-to-box"></a>Felhasználók hozzárendelése a Box hoz 

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva van.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azon felhasználókat, akiknek hozzáférésre van szükségük a Box alkalmazáshoz. Miután eldöntötte, ezeket a felhasználókat hozzárendelheti a Box alkalmazáshoz az alábbi utasításokat követve:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Felhasználók és csoportok hozzárendelése
Az Azure Portal **on > Felhasználók és csoportok** jelölőnégyzetet lehetővé teszi annak megadását, hogy mely felhasználók és csoportok kapjanak hozzáférést a Boxhoz. Egy felhasználó vagy csoport hozzárendelése a következő dolgokat okozza:

* Az Azure AD lehetővé teszi a hozzárendelt felhasználó (akár közvetlen hozzárendelés vagy csoporttagság) a Box hitelesítésére. Ha egy felhasználó nincs hozzárendelve, majd az Azure AD nem engedélyezi számukra, hogy jelentkezzen be a Box, és hibaüzenetet ad vissza az Azure AD bejelentkezési oldalon.
* A doboz alkalmazáscsempéje hozzáadódik a felhasználó [alkalmazásindítójához.](../manage-apps/end-user-experiences.md)
* Ha az automatikus kiépítés engedélyezve van, majd a hozzárendelt felhasználók és/vagy csoportok hozzáadódnak a létesítési várólistához, hogy automatikusan kilegyenek építve.
  
  * Ha csak a felhasználói objektumok vannak konfigurálva, hogy kiépítésre, majd az összes közvetlenül hozzárendelt felhasználók kerülnek a létesítési várólistába, és minden felhasználó, amely tagja bármely hozzárendelt csoportok kerülnek a létesítési várólistába. 
  * Ha a csoportobjektumok kiépítésre vannak konfigurálva, akkor az összes hozzárendelt csoportobjektum a Box ba van kiépítve, és minden olyan felhasználó, aki tagja ezeknek a csoportoknak. A csoport- és felhasználói tagságok a Box ba való íráskor megmaradnak.

Az **Attribútumok > egyszeri bejelentkezés** lapon konfigurálhatja, hogy mely felhasználói attribútumok (vagy jogcímek) jelennek meg a Box saml-alapú hitelesítés során, és az **attribútumok > kiépítés** lapon konfigurálhatja, hogy a felhasználói és csoportattribútumok hogyan áramlanak az Azure AD-ről a Boxra a kiépítési műveletek során.

### <a name="important-tips-for-assigning-users-to-box"></a>Fontos tippek a felhasználók Box hoz való hozzárendeléséhez 

*   Javasoljuk, hogy egy Azure AD-felhasználó rendelt box a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

*   Amikor egy felhasználót a dobozhoz rendel, érvényes felhasználói szerepkört kell választania. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítés.

## <a name="enable-automated-user-provisioning"></a>Automatikus felhasználói kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-t a Box felhasználói fiók létesítési API-jához való csatlakoztatásával, és konfigurálja a kiépítési szolgáltatást a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a Boxban az Azure AD-ben a felhasználó- és csoporthozzárendelés alapján.

Ha az automatikus kiépítés engedélyezve van, majd a hozzárendelt felhasználók és/vagy csoportok hozzáadódnak a létesítési várólistához, hogy automatikusan kilegyenek építve.
    
 * Ha csak a felhasználói objektumok vannak konfigurálva a kiépítésre, akkor a közvetlenül hozzárendelt felhasználók kerülnek a létesítési várólistába, és minden olyan felhasználó, aki bármely hozzárendelt csoport tagja, a létesítési várólistába kerül. 
    
 * Ha a csoportobjektumok kiépítésre vannak konfigurálva, akkor az összes hozzárendelt csoportobjektum a Box ba van kiépítve, és minden olyan felhasználó, aki tagja ezeknek a csoportoknak. A csoport- és felhasználói tagságok a Box ba való íráskor megmaradnak.

> [!TIP] 
> Az [Azure Portalon](https://portal.azure.com)megadott utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést is. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-account-provisioning"></a>A felhasználói fiókok automatikus kiépítésének konfigurálása:

Ez a szakasz célja, hogy körvonalazza, hogyan engedélyezheti az Active Directory felhasználói fiókok kiépítését a Boxba.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

2. Ha már beállította a Box ot egyszeri bejelentkezéshez, keresse meg a Box példányát a keresőmező használatával. Ellenkező esetben válassza **a Hozzáadás** és **keresés** lehetőséget az alkalmazásgyűjteményben. Válassza a Doboz elemet a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

3. Válassza ki a doboz példányát, majd válassza **a Kiépítés** lapot.

4. Állítsa a **létesítési módot** **Automatikus**ra. 

    ![Kiépítés](./media/box-userprovisioning-tutorial/provisioning.png)

5. A **Rendszergazdai hitelesítő adatok csoportban** kattintson az **Engedélyezés** gombra, ha egy új böngészőablakban szeretne megnyitni egy dobozbejelentkezési párbeszédpanelt.

6. A **Bejelentkezés a Box laphoz való hozzáférés engedélyezéséhez** adja meg a szükséges hitelesítő adatokat, majd kattintson az **Engedélyezés gombra.** 
   
    ![Automatikus felhasználói kiépítés engedélyezése](./media/box-userprovisioning-tutorial/IC769546.png "Automatikus felhasználói kiépítés engedélyezése")

7. A művelet engedélyezéséhez és az Azure Portalra való visszatéréshez kattintson a Hozzáférés engedélyezése a **dobozhoz** elemre. 
   
    ![Automatikus felhasználói kiépítés engedélyezése](./media/box-userprovisioning-tutorial/IC769549.png "Automatikus felhasználói kiépítés engedélyezése")

8. Az Azure Portalon kattintson a **Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Box alkalmazáshoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Box-fiók rendelkezik a Csoport felügyeleti engedélyével, és próbálkozzon újra az **"Engedélyezés"** lépéssel.

9. Adja meg annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia az **Értesítési e-mail** mezőben, és jelölje be a jelölőnégyzetet.

10. Kattintson a **Mentés gombra.**

11. A leképezések szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása a box.**

12. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD-ről boxra szinkronizált felhasználói attribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok a felhasználói fiókok egyeztetésére szolgálnak a Box ban a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

13. Az Azure AD-létesítési szolgáltatás engedélyezéséhez módosítsa a **Kiépítés állapotát** **be a** Beállítások szakaszban

14. Kattintson a **Mentés gombra.**

Ezzel elindítja a Felhasználók és csoportok szakasz Box hoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti a folyamatot, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által a Box alkalmazásban végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

A Box bérlőben a szinkronizált felhasználók a Felügyeleti konzol **Felügyelt felhasználók** **listájában**találhatók.

![Integráció állapota](./media/box-userprovisioning-tutorial/IC769556.png "Integráció állapota")


## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](box-tutorial.md)

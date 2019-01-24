---
title: 'Oktatóanyag: Mező konfigurálása a felhasználók automatikus átadása az Azure Active Directory |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Box között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: jeedes
ms.openlocfilehash: 58ab80b9cdf6fbcfa8aebeeac837687ded41ae39
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821822"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Oktatóanyag: Mező konfigurálása a felhasználók automatikus átadása

Ez az oktatóanyag célja szemlélteti a lépéseket kell elvégeznie a mezőbe, és az Azure AD automatikus kiépítés és megszüntetni hozzárendeléseket felhasználói fiókokat az Azure AD-be.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Box, a következőkre van szükség:

- Az Azure AD-bérlő
- A Box üzleti terve vagy jobb

> [!NOTE]
> Ha ebben az oktatóanyagban a lépéseket, azt javasoljuk, hogy *nem* éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Felhasználók hozzárendelése a Boxba 

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "", az alkalmazások az Azure AD szinkronizálása.

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók és/vagy az Azure AD-csoportokat a felhasználók, akik hozzáférhetnek a Box alkalmazás a jelölik. Ha úgy döntött, rendelhet ezeket a felhasználókat a Box alkalmazás a következő utasításokat:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Felhasználók és csoportok hozzárendelése
A **Box > felhasználók és csoportok** lap az Azure Portalon lehetővé teszi, hogy adja meg, hogy mely felhasználók és csoportok szabad megadni a hozzáférést a Boxba. Hozzárendelése egy felhasználóhoz vagy csoporthoz történjen az alábbiakat eredményezi:

* Az Azure AD lehetővé teszi, a hozzárendelt felhasználói (akár közvetlen hozzárendelés vagy a csoporttagság) mezőhöz történő hitelesítéséhez. Ha egy felhasználóhoz nincs hozzárendelve, az Azure AD nem engedélyezi azokat jelentkezzen be a mezőbe, és hibaüzenetet jelenít meg az Azure AD bejelentkezési oldal.
* A Box alkalmazáscsempe bekerül a felhasználó [alkalmazásindítójában](../manage-apps/end-user-experiences.md).
* Ha az Automatikus kiépítés engedélyezve van, majd a hozzárendelt felhasználók és csoportok kerülnek automatikusan ki kell építeni az üzembe helyezési üzenetsorba.
  
  * Ha csak a felhasználói objektumok lettek konfigurálva, ki kell építeni, majd az üzembe helyezési várólista összes közvetlenül hozzárendelt felhasználók kerülnek, és minden felhasználó, amelyek tagjai semmilyen hozzárendelt csoportok kerülnek, az üzembe helyezési várólista. 
  * Ha voltak beállítva a csoportobjektumokhoz ki kell építeni, majd az összes hozzárendelt csoportot objektumok lesznek kiépítve, be, és minden olyan felhasználó, ezek a csoportok tagjai. A csoport- és csoporttagság alapján ír be megmaradnak.

Használhatja a **attribútumok > egyszeri bejelentkezés** lap segítségével konfigurálhatja, hogy mely felhasználói attribútumok (vagy a jogcímszolgáltatói) jelennek meg a Boxba SAML-alapú hitelesítés során, és a **attribútumok > kiépítési** lapról Állítsa be, hogyan felhasználói és csoportattribútum flow Azure AD-ből a Boxba operations kiépítése során.

### <a name="important-tips-for-assigning-users-to-box"></a>Felhasználók hozzárendelése Box fontos tippek 

*   Javasoljuk, hogy egyetlen Azure ad-ben a felhasználói be az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése a box, ki kell választania egy érvényes felhasználói szerepkörnek. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítéshez.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti a csatlakozás az Azure AD-be a felhasználói fiók üzembe helyezési API és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissíteni, és tiltsa le a hozzárendelt felhasználói fiókok Azure AD-ben a felhasználó és csoport-hozzárendelések alapján a boxban.

Ha az Automatikus kiépítés engedélyezve van, majd a hozzárendelt felhasználók és csoportok kerülnek automatikusan ki kell építeni az üzembe helyezési üzenetsorba.
    
 * Ha csak a felhasználói objektumok úgy kell létrehozni, akkor az üzembe helyezési várólista közvetlenül hozzárendelt felhasználók kerülnek, és minden felhasználó, amelyek tagjai semmilyen hozzárendelt csoportok kerülnek, az üzembe helyezési várólista vannak konfigurálva. 
    
 * Ha voltak beállítva a csoportobjektumokhoz ki kell építeni, majd az összes hozzárendelt csoportot objektumok lesznek kiépítve, be, és minden olyan felhasználó, ezek a csoportok tagjai. A csoport- és csoporttagság alapján ír be megmaradnak.

> [!TIP] 
> Választhatja azt is, engedélyezve van a SAML-alapú egyszeri bejelentkezés a Box, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurálása automatikus felhasználói fiók kiépítése:

Ez a szakasz célja engedélyezése az Active Directory felhasználói fiókok a Boxba kidolgozására.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

2. Ha már konfigurált be egyszeri bejelentkezést, keresse meg a Box, a keresőmező használatával példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **Box** az alkalmazás-katalógusában. Jelölje be a keresési eredmények között, és adja hozzá az alkalmazások listáját.

3. Válassza ki a Box-példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **Kiépítési mód** való **automatikus**. 

    ![kiépítés folyamatban](./media/box-userprovisioning-tutorial/provisioning.png)

5. Alatt a **rendszergazdai hitelesítő adataival** területén kattintson **engedélyezés** egy új böngészőablakban a Box bejelentkezési párbeszédpanel megnyitásához.

6. Az a **hozzáférés biztosítása a Box bejelentkezési** lapon adja meg a szükséges hitelesítő adatokat, és kattintson a **engedélyezés**. 
   
    ![Felhasználók automatikus kiépítés engedélyezése](./media/box-userprovisioning-tutorial/IC769546.png "engedélyezése a felhasználók automatikus átadása")

7. Kattintson a **hozzáférést biztosít a Boxba** engedélyezni ezt a műveletet, és térjen vissza az Azure Portalon. 
   
    ![Felhasználók automatikus kiépítés engedélyezése](./media/box-userprovisioning-tutorial/IC769549.png "engedélyezése a felhasználók automatikus átadása")

8. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat a Box alkalmazás. Ha a kapcsolat hibája esetén, győződjön meg arról, a Box-fiók csapat rendszergazdai engedélyekkel rendelkező, majd próbálkozzon a **"Engedélyezés"** lépés újra.

9. Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

10. Kattintson a **mentéséhez.**

11. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók a Boxba.**

12. Az a **attribútumleképezések** területen tekintse át a felhasználói attribútumok a Boxba szinkronizált Azure AD-ből. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveleteket a felhasználói fiókokat a boxban. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

13. Az Azure AD létesítési szolgáltatás Box engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** beállítások szakaszában

14. Kattintson a **mentéséhez.**

Felhasználók és/vagy a felhasználók és csoportok szakaszban be hozzárendelt csoportokat a kezdeti szinkronizálás, amely elindítja. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a kiépítési szolgáltatást, a Box alkalmazás a által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

A Box bérlőben szinkronizált felhasználók be vannak felsorolva **felügyelt felhasználók** a a **felügyeleti konzol**.

![Integráció állapotát](./media/box-userprovisioning-tutorial/IC769556.png "integrációs állapota")


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](box-tutorial.md)

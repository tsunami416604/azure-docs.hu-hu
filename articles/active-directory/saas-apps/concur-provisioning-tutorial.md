---
title: 'Oktatóanyag: Konfigurálja a Concur-t az Azure Active Directoryval való automatikus felhasználói kiépítéshez| Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Concur között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441aa9805f2a453e22f207238315125d2a281838
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60280411"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Oktatóanyag: A Concur konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a lépéseket, amelyeket végre kell hajtania a Concur és az Azure AD automatikus kiépítése és a de-provision felhasználói fiókok azure AD concur.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

*   Egy Azure Active Directory-bérlő.
*   A Concur egyszeri bejelentkezésre engedélyezett előfizetés.
*   Felhasználói fiók a Csoportfelügyeleti engedélyekkel való egyeztetésben.

## <a name="assigning-users-to-concur"></a>Felhasználók hozzárendelése a Concur-hoz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva van.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azon felhasználókat, akiknek hozzáférésre van szükségük a Concur alkalmazáshoz. Miután eldöntötte, ezeket a felhasználókat hozzárendelheti a Concur alkalmazáshoz az alábbi utasításokat követve:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Fontos tippek a felhasználók concur hozzárendeléséhez

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Concur a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

*   Amikor egy felhasználót a Concur-hoz rendel, érvényes felhasználói szerepkört kell választania. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítés.

## <a name="enable-user-provisioning"></a>Felhasználókiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-t a Concur felhasználói fiók létesítési API-jával való csatlakoztatásával, és konfigurálja a kiépítési szolgáltatást a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a Concur-ban az Azure AD-ben a felhasználó- és csoport-hozzárendelés alapján.

> [!Tip] 
> Az [Azure Portalon](https://portal.azure.com)megadott utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a Concur számára. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-user-account-provisioning"></a>A felhasználói fiókok kiépítésének konfigurálása:

Ez a szakasz célja, hogy körvonalazza, hogyan engedélyezheti az Active Directory felhasználói fiókok kiépítését a Concur számára.

A költségszolgáltatásban lévő alkalmazások engedélyezéséhez megfelelően kell beállítani és használni a webszolgáltatás-felügyeleti profilt. Ne adja hozzá a Ws Admin szerepkört a T&E felügyeleti funkciókhoz használt meglévő rendszergazdai profilhoz.

A Concur Consultants vagy az ügyfélrendszergazdának külön webszolgáltatás-rendszergazdai profilt kell létrehoznia, és az ügyfélrendszergazdának ezt a profilt kell használnia a WebServices Administrator függvényekhez (például az alkalmazások engedélyezéséhez). Ezeket a profilokat el kell különtartani az ügyféladminisztrátor napi T&E rendszergazdai profiljától (a T&E rendszergazdai profilhoz nem rendelhető hozzá wsadmin szerepkör).

Amikor létrehozza az alkalmazás engedélyezéséhez használandó profilt, írja be az ügyfélrendszergazdának a nevét a felhasználói profil mezőbe. Ez tulajdonjogot rendel a profilhoz. Egy vagy több profil létrehozása után az ügyfélnek ezzel a profillal kell bejelentkeznie, hogy a Web Services menüben kattintson a Partner alkalmazás "*gombra.*

A következő okok miatt ezt a műveletet nem szabad a normál T&E. alkalmazáshoz használt profillal elvégezni.

* Az ügyfélnek kell az "*Igen*" gombra kattintania a párbeszédablakban, amely az alkalmazás engedélyezése után jelenik meg. Ez a kattintás elismeri, hogy az ügyfél hajlandó a Partner alkalmazásszámára az adataikhoz való hozzáférésre, így Ön vagy a Partner nem kattinthat az Igen gombra.

* Ha egy olyan ügyfélrendszergazda, aki engedélyezte a T&E rendszergazdai profilját használó alkalmazást, elhagyja a vállalatot (ami a profil inaktiválását eredményezi), az adott profil használatával engedélyezett alkalmazások nem működnek, amíg az alkalmazás nem engedélyezett egy másik aktív WS Admin profillal. Ez az oka annak, hogy külön WS Admin profilokat kell létrehoznia.

* Ha egy rendszergazda kilép a vállalatból, a Ws Admin profilhoz társított név szükség esetén a helyettesítő rendszergazdára módosítható anélkül, hogy ez befolyásolna az engedélyezett alkalmazást, mert a profilt nem kell inaktiválni.

**A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Concur** bérlőbe.

2. A **Felügyelet** menüben válassza a **Web Services parancsot.**
   
    ![Concur bérlő](./media/concur-provisioning-tutorial/IC721729.png "Concur bérlő")

3. A bal oldalon, a **Webszolgáltatások** ablaktáblán válassza a **Partneralkalmazás engedélyezése**lehetőséget.
   
    ![Partneralkalmazás engedélyezése](./media/concur-provisioning-tutorial/ic721730.png "Partneralkalmazás engedélyezése")

4. Az **Alkalmazás engedélyezése** listában válassza az **Azure Active Directory**lehetőséget, majd kattintson az **Engedélyezés gombra.**
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. A **Művelet megerősítése** párbeszédpanel bezárásához kattintson az **Igen** gombra.
   
    ![Művelet megerősítése](./media/concur-provisioning-tutorial/ic721732.png "Művelet megerősítése")

6. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

7. Ha már konfigurálta a Concur-t egyszeri bejelentkezéshez, keresse meg a Concur példányát a keresőmező használatával. Ellenkező esetben válassza **a Hozzáadás** és **keresés** lehetőséget az alkalmazásgyűjteményben. Válassza a Concur elemet a keresési eredmények között, és adja hozzá az alkalmazások listájához.

8. Válassza ki a Concur példányát, majd válassza a **Kiépítés** lapot.

9. Állítsa a **létesítési módot** **Automatikus**ra. 
 
    ![Kiépítés](./media/concur-provisioning-tutorial/provisioning.png)

10. A **Rendszergazdai hitelesítő adatok** csoportban adja meg a Concur rendszergazdájának **felhasználónevét** és **jelszavát.**

11. Az Azure Portalon kattintson a **Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozhasson a Concur alkalmazáshoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Concur-fiók rendelkezik a Csoport felügyeleti engedélyével.

12. Adja meg annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia az **Értesítési e-mail** mezőben, és jelölje be a jelölőnégyzetet.

13. Kattintson a **Mentés gombra.**

14. A leképezések szakaszban válassza **az Azure Active Directory felhasználóiszinkronizálása a concur lehetőséget.**

15. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD-ről concur-ra szinkronizált felhasználói attribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok a Concur felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

16. Az Azure AD-kiépítési szolgáltatás engedélyezése a Concur számára módosítsa a **Kiépítés állapotát** **be beállításra** a **Beállítások** szakaszban

17. Kattintson a **Mentés gombra.**

Most már létrehozhat egy tesztfiókot. Várjon akár 20 percet is, hogy ellenőrizze, hogy a fiók szinkronizálva van-e a Concur-val.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](concur-tutorial.md)


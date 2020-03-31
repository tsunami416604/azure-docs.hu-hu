---
title: 'Oktatóanyag: A StarLeaf konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat a StarLeaf szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 520373fc6a05bcaada973273e3553f9da623c669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064291"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Oktatóanyag: A StarLeaf konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a StarLeaf és az Azure Active Directoryban (Azure AD) végrehajtandó lépéseket, hogy az Azure AD automatikusan kiépítse és dekontizáljon felhasználókat és/vagy csoportokat a StarLeafbe.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy StarLeaf bérlő.](https://www.starleaf.com/solutions/)
* A StarLeaf rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assign-users-to-starleaf"></a>Felhasználók hozzárendelése a StarLeafhez
Az Azure Active Directory egy hozzárendelések nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói üzembe építés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és csoportjainak kell hozzáférniük a StarLeafhez. Ezután a felhasználókat és csoportokat hozzárendelheti a StarLeafhez [az alábbi utasítások szerint.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Fontos tippek a felhasználók StarLeafhez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a StarLeafhez az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és csoportok is hozzárendelhetők.

* Amikor egy felhasználót hozzárendelsz a StarLeafhez, a hozzárendelési párbeszédpanelen ki kell választanod egy érvényes alkalmazásspecifikus szerepkört (ha van ilyen). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-starleaf-for-provisioning"></a>A StarLeaf beállítása kiépítésre

Mielőtt konfigurálja a StarLeafet az Azure AD-vel való automatikus felhasználói kiépítésre, konfigurálnia kell az SCIM-kiépítést a StarLeafben:

1. Jelentkezzen be a [StarLeaf Admin konzoljára.](https://portal.starleaf.com/#page=login) Keresse meg az **Integrációk integrációt.** > **Add integration**

    ![StarLeaf hozzáadása SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. Válassza ki a Microsoft Azure Active Directoryként szeretné beírni a **típust.** Adjon meg egy megfelelő nevet a **Név mezőbe.** Kattintson az **Alkalmaz** gombra.

    ![StarLeaf hozzáadása SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  Ekkor megjelennek az **SCIM alap URL-címe** és **az Access token** értékei. Ezeket az értékeket a **bérlői URL-cím** és a **titkos jogkivonat** mezők ben a StarLeaf-alkalmazás kiépítése az Azure Portalon. 

    ![StarLeaf token létrehozása](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>StarLeaf hozzáadása a galériából

A StarLeaf konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, hozzá kell adnia a StarLeaf-et az Azure AD-alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a StarLeaf-et az Azure AD alkalmazásgyűjteményből, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **StarLeaf**, válassza **a StarLeaf** az eredménypanelen.
    ![StarLeaf az eredménylistában](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Automatikus felhasználói kiépítés konfigurálása a StarLeaf szolgáltatásba

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a StarLeafben az Azure AD-ben lévő felhasználói és/vagy csoport-hozzárendelések alapján.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **StarLeaf**lehetőséget.

    ![A StarLeaf hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A Rendszergazdai hitelesítő adatok szakaszban adja meg az **SCIM alap URL-címét** és **a hozzáférési jogkivonat** értékeit, amelyeket korábban a **bérlői URL-cím,** illetve a **titkos jogkivonat** korábbi részeként kért be. Kattintson **a Kapcsolat tesztelése** gombra, hogy az Azure AD csatlakozni tudhessen a StarLeafhez. Ha a kapcsolat nem sikerül, győződj meg róla, hogy StarLeaf-fiókod rendelkezik admin engedéllyel, majd próbáld újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és ellenőrizze az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása a StarLeaf szolgáltatással**lehetőséget.

    ![StarLeaf token létrehozása](media/starleaf-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD és a StarLeaf között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok a StarLeaf felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![StarLeaf token létrehozása](media/starleaf-provisioning-tutorial/userattribute.png)


10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.


11. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a StarLeaf, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a StarLeaf-be, ha kiválasztja a kívánt értékeket a **Hatókör** területen a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a StarLeafen végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésének jelentéskészítése című témakörben](../app-provisioning/check-status-user-account-provisioning.md) olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* A StarLeaf jelenleg nem támogatja a csoportkiépítést. 
* A StarLeaf használatához az **e-mail** és **a userName** értékek azonos forrásértékkel rendelkeznek.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* További információ a [naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések becsatornázásáról.](../app-provisioning/check-status-user-account-provisioning.md)

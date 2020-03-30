---
title: 'Oktatóanyag: A Druva konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a Druva felhasználói fiókjainak automatikus kiépítésére és kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 3d1bb0bcbc0df98d7a884004cf96fe9810589185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058110"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Oktatóanyag: A Druva konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Druva és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és dekontitálta a felhasználókat és/vagy csoportokat a Druva számára.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [A Druva bérlő](https://www.druva.com/products/pricing-plans/).
* A Druva rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-druva"></a>Felhasználók hozzárendelése a Druva-hoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Druva-hoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a Druva-hoz rendelheti az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Fontos tippek a felhasználók Druva-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve druva az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót druva-hoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-druva-for-provisioning"></a>Druva beállítása kiépítéshez

A Druva konfigurálása az Azure AD automatikus felhasználói kiépítéshez, engedélyeznie kell az SCIM-kiépítést a Druva-n.

1. Jelentkezzen be a [Druva Admin konzolba.](https://console.druva.com) Keresse meg a **Druva > inSync**.

    ![Druva admin konzol](media/druva-provisioning-tutorial/menubar.png)

2. Keresse meg a**Központi telepítések felhasználóinak** >  **kezelése** > **lehetőséget.**

    ![Druva Hozzáadása SCIM](media/druva-provisioning-tutorial/manage.png)

3.  Nyissa meg a **Beállítások lapot.** Kattintson **a Jogkivonat létrehozása gombra.**

    ![Druva Hozzáadása SCIM](media/druva-provisioning-tutorial/settings.png)

4.  Másolja az **Auth token** értékét. Ezt az értéket a Druva-alkalmazás Kiépítés lapján a **Titkos jogkivonat** mezőben adja meg az Azure Portalon.
    
    ![Druva Hozzáadása SCIM](media/druva-provisioning-tutorial/auth.png)

## <a name="add-druva-from-the-gallery"></a>Druva hozzáadása a galériából

A Druva konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia a Druva-t az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**Druva hozzáadása az Azure AD alkalmazáskatalógusból, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Druva**kifejezést, válassza a **Druva** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Druva az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Automatikus felhasználói kiépítés konfigurálása a Druva rendszerbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Druva-ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Azt is választhatja, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Druva számára, a [Druva Single sign-on oktatóanyag](druva-tutorial.md)utasításait követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>A Druva automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Druva**lehetőséget.

    ![A Druva hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5.  A Rendszergazdai hitelesítő adatok `https://apis.druva.com/insync/scim` csoportban adja meg a **bérlői URL-címet.** Adja meg az **auth token** értékét **titkos jogkivonatban.** Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD képes legyen csatlakozni a Druvához. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Druva-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és válassza az **E-mail értesítés küldése hiba esetén**lehetőséget.

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása druvával**lehetőséget.

    ![Druva felhasználói leképezések](media/druva-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD és a Druva között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Druva felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Druva felhasználói attribútumok](media/druva-provisioning-tutorial/userattribute.png)


10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Druva, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Druva-ba a kívánt értékek kiválasztásával a **Beállítások** szakasz **hatókörében.**

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Druva-n végrehajtott összes műveletet.

    Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.
    
## <a name="connector-limitations"></a>Összekötő korlátai

* Druva igényel **e-mail** kötelező attribútum. 

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókkiépítési kezelésének kezelése.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések becsatornázásáról.](../app-provisioning/check-status-user-account-provisioning.md)

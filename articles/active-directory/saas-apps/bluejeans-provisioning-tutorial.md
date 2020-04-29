---
title: 'Oktatóanyag: a BlueJeans konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcb3fe009a6482c8e512899a952694beaed361a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77059012"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés BlueJeans konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a BlueJeans és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a BlueJeans.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Azure AD-bérlő
* BlueJeans-bérlő a [saját céges](https://www.BlueJeans.com/pricing) csomaggal vagy jobb engedélyezéssel
* Rendszergazdai jogosultságokkal rendelkező BlueJeans felhasználói fiók

> [!NOTE]
> Az Azure AD-kiépítés integrációja a [BLUEJEANS API](https://BlueJeans.github.io/developer)-ra támaszkodik, amely a standard csomagon vagy annál jobb BlueJeans csapatoknak érhető el.

## <a name="adding-bluejeans-from-the-gallery"></a>BlueJeans hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus BlueJeans konfigurálása előtt hozzá kell adnia a BlueJeans az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha BlueJeans szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **BlueJeans**kifejezést, válassza az **BlueJeans** elemet az eredmények panelen, majd válassza a **Hozzáadás** gombot az alkalmazás hozzáadásához.

    ![BlueJeans az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Felhasználók kiosztása a BlueJeans

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában a rendszer csak azokat a felhasználókat és/vagy csoportokat szinkronizálja, akik az Azure AD-alkalmazáshoz lettek rendelve.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a BlueJeans való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a BlueJeans az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Fontos Tippek a felhasználók BlueJeans való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a BlueJeans-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor BlueJeans rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Automatikus felhasználó-kiépítés beállítása a BlueJeans

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy BlueJeans alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a BlueJeans számára, az [BlueJeans egyszeri bejelentkezés oktatóanyagában](bluejeans-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a BlueJeans az Azure AD-ben:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **BlueJeans**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **BlueJeans**lehetőséget.

    ![Az BlueJeans hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a BlueJeans-fiókjának **rendszergazdai felhasználónevét**és **rendszergazdai jelszavát** . Ilyen értékek például a következők:

   * A **rendszergazdai Felhasználónév** mezőben töltse ki a rendszergazdai fiók felhasználónevét a BlueJeans-bérlőn. Példa: admin@contoso.com.

   * A **rendszergazdai jelszó** mezőben töltse ki a rendszergazdai felhasználónévhez tartozó jelszót.

6. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen csatlakozni a BlueJeans. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a BlueJeans-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Kattintson a **Save** (Mentés) gombra.

9. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a BlueJeans**lehetőséget.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található BlueJeans. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a BlueJeans felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Az Azure AD-kiépítési szolgáltatás BlueJeans való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Adja meg a BlueJeans kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a BlueJeans-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Bluejeans nem engedélyezi a 30 karakternél hosszabb felhasználóneveket.

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png

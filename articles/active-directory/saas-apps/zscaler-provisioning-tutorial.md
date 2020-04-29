---
title: 'Oktatóanyag: a Zscaler konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Zscaler.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 8add1f57b566d746d464c1ca165938fc112a9784
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062710"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Zscaler konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Zscaler és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Zscaler.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../active-directory-saas-app-provisioning.md).
>

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Egy Azure AD-bérlő.
* Egy Zscaler-bérlő.
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Zscaler-ben.

> [!NOTE]
> Az Azure AD-kiépítés integrációja a Zscaler SCIM API-ra támaszkodik, amely Zscaler-fejlesztők számára elérhető a vállalati csomaggal rendelkező fiókok számára.

## <a name="adding-zscaler-from-the-gallery"></a>Zscaler hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Zscaler konfigurálása előtt hozzá kell adnia a Zscaler az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha Zscaler szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler**kifejezést, válassza a **Zscaler** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Zscaler az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Felhasználók kiosztása a Zscaler

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában a rendszer csak azokat a felhasználókat és/vagy csoportokat szinkronizálja, akik az Azure AD-alkalmazáshoz lettek rendelve.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Zscaler való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Zscaler az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Fontos Tippek a felhasználók Zscaler való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Zscaler-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Zscaler rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Automatikus felhasználó-kiépítés beállítása a Zscaler

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Zscaler alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Zscaler számára, az [Zscaler egyszeri bejelentkezés oktatóanyagában](zscaler-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Zscaler az Azure AD-ben:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Zscaler**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Zscaler**lehetőséget.

    ![Az Zscaler hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Zscaler **-fiók bérlői URL-címét** és **titkos jogkivonatát** a 6. lépésben leírtak szerint.

6. A **bérlői URL-cím** és a **titkos jogkivonat**beszerzéséhez navigáljon az **Adminisztráció > hitelesítési beállítások** elemre a Zscaler-portál felhasználói felületén, és kattintson az **SAML** elemre a **Hitelesítés típusa**alatt.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Az **SAML konfigurálása** elemre kattintva nyissa meg a **konfigurációs SAML** -beállításokat.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Válassza az **scim kiépítés engedélyezése** lehetőséget az **alap URL-cím** és a **tulajdonosi jogkivonat**lekéréséhez, majd mentse a beállításokat. Másolja az **alap URL-címet** a **bérlői URL**-címre, és a **tulajdonosi jogkivonatot** a Azure Portal **titkos jogkivonatára** .

7. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen csatlakozni a Zscaler. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Zscaler-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/test-connection.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba**esetén jelölőnégyzetet.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/notification.png)

9. Kattintson a **Save** (Mentés) gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Zscaler**lehetőséget.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Zscaler. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a Zscaler**lehetőséget.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Tekintse át az Azure AD-ből szinkronizált Zscaler az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](./../active-directory-saas-scoping-filters.md).

15. Az Azure AD-kiépítési szolgáltatás Zscaler való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Adja meg a Zscaler kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/scoping.png)

17. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Zscaler kiépítés](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Zscaler-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../active-directory-saas-provisioning-reporting.md)helyezéséhez.

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png

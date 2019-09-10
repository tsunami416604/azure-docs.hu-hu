---
title: 'Oktatóanyag: A Wrike konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Wrike.
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
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: f94c12ed355b25e6a10c8d71e176d9bf3bf78758
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70847100"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Oktatóanyag: Wrike konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Wrike és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Wrike.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a Microsoft Azure-előnézetek [kiegészítő használati](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)feltételeit.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy Wrike-bérlő](https://www.wrike.com/price/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Wrike-ben.

## <a name="assign-users-to-wrike"></a>Felhasználók Wrike rendelése
Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Wrike való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Wrike az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Fontos Tippek a felhasználók Wrike való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Wrike-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Wrike rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-wrike-for-provisioning"></a>Wrike beállítása a kiépítés számára

Az Azure AD-vel való automatikus Wrike konfigurálása előtt engedélyeznie kell a SCIM-létesítést a Wrike-on.

1. Jelentkezzen be a [Wrike felügyeleti konzolra](https://www.Wrike.com/login/). Navigáljon a bérlői AZONOSÍTÓhoz. Válassza az **alkalmazások & integrációk**lehetőséget.

    ![Wrike felügyeleti konzol](media/Wrike-provisioning-tutorial/admin.png)

2.  Navigáljon az **Azure ad**-hoz. Válassza ki.

    ![Wrike felügyeleti konzol](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Válassza ki a SCIM. Másolja ki az **alap URL-címet**.

    ![Wrike felügyeleti konzol](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Kattintson az **API** > válassza az **Azure scim**lehetőséget.

    ![Wrike-SCIM hozzáadása](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Ekkor megnyílik egy előugró ablak. Adja meg ugyanazt a jelszót, amelyet korábban hozott létre a fiók létrehozásához 

    ![Wrike-létrehozási jogkivonat](media/Wrike-provisioning-tutorial/password.png)

6.  Másolja ki a **titkos jogkivonatot** , és illessze be az Azure ad-be. Kattintson a **Save (Mentés** ) gombra a kiépítési beállítás befejezéséhez a Wrike.

    ![Wrike-létrehozási jogkivonat](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Wrike hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Wrike konfigurálása előtt hozzá kell adnia a Wrike az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha Wrike szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Wrike**kifejezést, válassza az **Wrike** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Wrike az eredmények listájában](common/search-new-app.png)


## <a name="configuring-automatic-user-provisioning-to-wrike"></a>Automatikus felhasználó-kiépítés beállítása a Wrike 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Wrike alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Wrike számára, az [Wrike egyszeri bejelentkezés oktatóanyagában](wrike-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két szolgáltatás kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Wrike az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Wrike**lehetőséget.

    ![Az Wrike hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a kiépítés lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg a **bérlői URL** -címben és a **titkos jogkivonatban** korábban lekért **alap URL-címet** és **állandó hozzáférési jogkivonat** -értékeket. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Wrike. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Wrike-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

8. Kattintson a **Save** (Mentés) gombra.

9. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Wrike**lehetőséget.

    ![Wrike felhasználói leképezések](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Wrike. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Wrike felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Wrike felhasználói attribútumai](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Az Azure AD-kiépítési szolgáltatás Wrike való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

13. Adja meg a Wrike kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

14. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz hatókörében meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, hogy [mennyi ideig tart a felhasználók kiépítése](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Wrike-on végrehajtott összes műveletet ismertetik. További információ: [a felhasználó kiépítési állapotának ellenõrzése](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../manage-apps/check-status-user-account-provisioning.md)során.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)

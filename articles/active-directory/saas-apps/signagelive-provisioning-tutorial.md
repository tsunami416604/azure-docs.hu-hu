---
title: 'Oktatóanyag: a Signagelive konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Signagelive.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: bbd0d3c60c5cc4056d5cbadfc7c6d90ae2a37ca6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063245"
---
# <a name="tutorial-configure-signagelive--for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Signagelive konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Signagelive és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Signagelive.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Signagelive-bérlő](https://signagelive.com/pricing/)
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Signagelive-ben.

## <a name="assigning-users-to-signagelive"></a>Felhasználók kiosztása a Signagelive   

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Signagelive való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Signagelive az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-signagelive"></a>Fontos Tippek a felhasználók Signagelive való hozzárendeléséhez   

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Signagelive-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Signagelive rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-signagelive--for-provisioning"></a>Signagelive beállítása a kiépítés számára

Az Azure AD-vel való automatikus Signagelive konfigurálása előtt engedélyeznie kell a SCIM-létesítést a Signagelive-on.

  A SCIM-kiépítés konfigurálásához szükséges titkos token beszerzéséhez [Signagelive](mailto:development@signagelive.com) el.

## <a name="add-signagelive-from-the-gallery"></a>Signagelive hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Signagelive konfigurálásához hozzá kell adnia a Signagelive az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha Signagelive szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Signagelive**kifejezést, válassza az **Signagelive** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Signagelive az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-signagelive"></a>Automatikus felhasználó-kiépítés beállítása a Signagelive    

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Signagelive alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
>  Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Signagelive számára, az [Signagelive egyszeri bejelentkezés oktatóanyagában](Signagelive-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-signagelive--in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Signagelive az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Signagelive**lehetőséget.

    ![Az Signagelive hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja ` https://samlapi.signagelive.com/scim/v2` meg a **bérlői URL-címet**. A **titkos jogkivonat** mezőben adja meg a mérnöki fejlesztési csapat által megadott **tulajdonosi jogkivonat** értékét. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Signagelive. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Signagelive-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.
    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Signagelive**lehetőséget.

    ![Signagelive felhasználói leképezések](media/signagelive-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Signagelive. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Signagelive felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Signagelive felhasználói attribútumai](media/signagelive-provisioning-tutorial/userattribute.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoport szinkronizálása a Signagelive**lehetőséget.

    ![Signagelive felhasználói leképezések](media/signagelive-provisioning-tutorial/groupmapping.png)

11. Tekintse át az Azure AD-ből szinkronizált Signagelive az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Signagelive tartozó csoportfiókok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Signagelive felhasználói attribútumai](media/signagelive-provisioning-tutorial/groupattribute.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD-kiépítési szolgáltatás Signagelive való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a Signagelive kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, hogy [mennyi ideig tart a felhasználók kiépítése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users). 

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Signagelive-on végrehajtott összes műveletet ismertetik. További információ: [a felhasználó kiépítési állapotának ellenõrzése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../app-provisioning/check-status-user-account-provisioning.md)során.

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

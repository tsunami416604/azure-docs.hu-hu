---
title: 'Oktatóanyag: a Workgrid konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Workgrid.
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
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: 94d70447117c73a309959ddf66972c921aa5e687
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062808"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Workgrid konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Workgrid és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Workgrid.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Workgrid-bérlő](https://www.workgrid.com/)
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Workgrid-ben.

## <a name="assigning-users-to-workgrid"></a>Felhasználók kiosztása a Workgrid 

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Workgrid való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Workgrid az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>Fontos Tippek a felhasználók Workgrid való hozzárendeléséhez 

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Workgrid-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Workgrid rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-workgrid-for-provisioning"></a>Workgrid beállítása a kiépítés számára

Az Azure AD-vel való automatikus Workgrid konfigurálása előtt engedélyeznie kell a SCIM-létesítést a Workgrid-on.

1. Jelentkezzen be a Workgrid-be. Navigáljon a **felhasználók > a felhasználó üzembe**helyezése.

    ![Workgrid](media/Workgrid-provisioning-tutorial/user.png)

2. A **FIÓKKEZELÉS API**alatt kattintson a **hitelesítő adatok létrehozása**elemre.

    ![Workgrid](media/Workgrid-provisioning-tutorial/scim.png)

3. Másolja a **scim-végpontot** és a **hozzáférési jogkivonat** értékeit. Ezek a Workgrid alkalmazás kiépítés lapjának **bérlő URL-címe** és **titkos jogkivonat** mezőjében lesznek megadva a Azure Portal.

    ![Workgrid](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>Workgrid hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Workgrid konfigurálásához hozzá kell adnia a Workgrid az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha Workgrid szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Workgrid**kifejezést, válassza az **Workgrid** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Workgrid az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>Automatikus felhasználó-kiépítés beállítása a Workgrid  

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Workgrid alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Workgrid számára, az [Workgrid egyszeri bejelentkezés oktatóanyagában](Workgrid-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Workgrid az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Workgrid**lehetőséget.

    ![Az Workgrid hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg a **scim-végpontot** és a **hozzáférési jogkivonat** azon értékeit, amelyeket korábban a **bérlői URL-címben** és a **titkos jogkivonatban** lekért. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Workgrid. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Workgrid-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Workgrid**lehetőséget.

    ![Workgrid felhasználói leképezések](media/Workgrid-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Workgrid. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Workgrid felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Workgrid felhasználói attribútumai](media/Workgrid-provisioning-tutorial/userattribute.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a Workgrid** lehetőséget.

    ![Workgrid felhasználói leképezések](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. Tekintse át az Azure AD-ből szinkronizált Workgrid az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Workgrid felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Workgrid felhasználói leképezések](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Az Azure AD-kiépítési szolgáltatás Workgrid való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

15. Adja meg a Workgrid kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

16. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, hogy [mennyi ideig tart a felhasználók kiépítése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Workgrid-on végrehajtott összes műveletet ismertetik. További információ: [a felhasználó kiépítési állapotának ellenõrzése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../app-provisioning/check-status-user-account-provisioning.md)során.

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

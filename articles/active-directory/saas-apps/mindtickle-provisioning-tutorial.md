---
title: 'Oktatóanyag: A MindTickle konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a MindTickle.
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
ms.openlocfilehash: 50658c10f9a7e011177ecb52d549e681b1120020
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576211"
---
# <a name="tutorial-configure-mindtickle-for-automatic-user-provisioning"></a>Oktatóanyag: MindTickle konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a MindTickle és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a MindTickle.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a Microsoft Azure-előnézetek [kiegészítő használati](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)feltételeit.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [MindTickle-bérlő](https://www.mindtickle.com/)
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a MindTickle-ben.

## <a name="assigning-users-to-mindtickle"></a>Felhasználók kiosztása a MindTickle

Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a MindTickle való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a MindTickle az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mindtickle"></a>Fontos Tippek a felhasználók MindTickle való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a MindTickle-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor MindTickle rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-mindtickle-for-provisioning"></a>MindTickle beállítása a kiépítés számára

Az Azure AD-vel való automatikus MindTickle konfigurálása előtt engedélyeznie kell a SCIM-létesítést a MindTickle-on.


1.  Ismerje meg a [MindTickle támogatási csapatát](mailto:help@mindtickle.com) az scim-létesítés konfigurálásához szükséges JWT-token beszerzéséhez.


## <a name="add-mindtickle-from-the-gallery"></a>MindTickle hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus MindTickle konfigurálásához hozzá kell adnia a MindTickle az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha MindTickle szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **MindTickle**kifejezést, válassza az **MindTickle** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![MindTickle az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mindtickle"></a>Automatikus felhasználó-kiépítés beállítása a MindTickle 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy MindTickle alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a MindTickle számára, az [MindTickle egyszeri bejelentkezés oktatóanyagában](mindtickle-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-mindtickle-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a MindTickle az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **MindTickle**lehetőséget.

    ![Az MindTickle hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a kiépítés lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban `https://admin.mindtickle.com/scim` adja meg a **bérlői URL-címet**. Adja meg a titkos token szövegmezőben korábban beolvasott **JWT-jogkivonat** értékét, írja be a **JWT jogkivonat** értékét, amelyet a MindTickle-támogatási csapat adott meg. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a myPolicies. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a MindTickle-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a MindTickle**lehetőséget.

    ![MindTickle felhasználói leképezések](media/mindtickle-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum** -hozzárendelési szakaszban található MindTickle. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a MindTickle felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![MindTickle felhasználói leképezések](media/mindtickle-provisioning-tutorial/userattribute.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD-kiépítési szolgáltatás MindTickle való engedélyezéséhez módosítsa a kiépítési **állapotot** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a MindTickle kiépíteni kívánt felhasználókat és/vagy csoportokat a Settings ( **Beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz hatókörében meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, hogy [mennyi ideig](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)tart a felhasználók kiépítése. 

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a MindTickle-on végrehajtott összes műveletet ismertetik. További információ: [a felhasználó kiépítési állapotának ellenõrzése](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók](../manage-apps/check-status-user-account-provisioning.md)kiépítés során.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

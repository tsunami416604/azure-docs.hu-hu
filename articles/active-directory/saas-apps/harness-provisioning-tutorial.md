---
title: 'Oktatóanyag: a felhasználói kiépítés automatikus kihasználásának beállítása a Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a kiaknázáshoz.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 34d05d6392e00757bf1e5562ffd8341ad04cc9dc
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807757"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Oktatóanyag: a felhasználói kiépítés automatikus kihasználásának beállítása

Ennek az oktatóanyagnak a célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a hám és Azure Active Directory (Azure AD) számára az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat a kihasználni.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy hám bérlő](https://harness.io/pricing/)
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók.

## <a name="assigning-users-to-harness"></a>Felhasználók kiosztása a hám számára

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD-beli felhasználók és/vagy csoportok számára szükséges-e a kiaknázás. A döntés után ezeket a felhasználókat és/vagy csoportokat kihasználhatja a következő utasítások követésével:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-harness"></a>Fontos Tippek a felhasználók kiosztásához

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* A felhasználók a kiosztáshoz való hozzárendelésekor ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-harness-for-provisioning"></a>Kihasználás beállítása a kiépítés számára

1. Jelentkezzen be a [hám felügyeleti konzolba](https://app.harness.io/#/login). Navigáljon a **folyamatos biztonság > hozzáférés-kezelés**elemre.

    ![Hám felügyeleti konzol](media/harness-provisioning-tutorial/admin.png)

2.  Kattintson az **API-kulcsok**elemre.

    ![Hám hozzáadása SCIM](media/harness-provisioning-tutorial/apikeys.png)

3. Kattintson az **új kulcs hozzáadása**lehetőségre. Az **API-kulcs hozzáadása** párbeszédpanelen adjon meg egy **nevet** , és válasszon ki egy lehetőséget a legördülő menüből **örökölt engedélyek** közül. Kattintson a **Küldés** gombra.

    ![Új kulcs hozzáadása a hevederhez](media/harness-provisioning-tutorial/addkey.png)

    ![Új kulcs hozzáadása párbeszédpanel](media/harness-provisioning-tutorial/title.png)

3.  Másolja a **kulcsot**. Ez az érték a Azure Portalban lévő hám-alkalmazás létesítés lapjának titkos jogkivonat mezőjében lesz megadva.

    ![Hám létrehozása jogkivonat](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Hám hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus felhasználó-kiépítés előtt az Azure AD-alkalmazás-katalógusban fel kell vennie a hevedert a felügyelt SaaS-alkalmazások listájára.

**Az Azure AD-alkalmazás-katalógusból az alábbi lépéseket követve adhatja hozzá a hám alkalmazást:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **hám**kifejezést, válassza ki a **hám** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Hám az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-harness"></a>Automatikus felhasználó-kiépítés beállítása a kihasználat állapotba 

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein az Azure AD-ben felhasználói és/vagy csoport-hozzárendelések alapján a felhasználók és/vagy csoportok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a hám számára a [hám egyszeri bejelentkezés oktatóanyagában](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)ismertetett utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció kiegészíti egymást

> [!NOTE]
> Ha többet szeretne megtudni a hám SCIM-végpontról, tekintse meg [ezt](https://docs.harness.io/article/smloyragsm-api-keys)

### <a name="to-configure-automatic-user-provisioning-for-harness-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **hám**elemet.

    ![A hám hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a `https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw` a **bérlői URL-címben**. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen csatlakozni a hám szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a hám-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók**számára lehetőséget.

    ![Felhasználói leképezések kiaknázása](media/harness-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez használt felhasználói fiókokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Felhasználói attribútumok kiaknázása](media/harness-provisioning-tutorial/userattributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása**lehetőséget.

    ![A hám csoport leképezései](media/harness-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD-ből szinkronizált csoport-attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez használt hám csoportjaival egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![A hám csoport attribútumai](media/harness-provisioning-tutorial/groupattributes.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a hám számára, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára kiépíteni kívánja a kihasználni kívánt értékeket a **Settings (beállítások** ) szakasz **hatókörében** .

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti az üzembe helyezési tevékenység jelentésre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a hevederen végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../manage-apps/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)

---
title: 'Oktatóanyag: a Airstack konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Airstack.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 94738612-b7ab-48c5-a3a9-2c019281aba1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: Zhchia
ms.openlocfilehash: b2f8d5dacbf5f3a59a6e6c9e794cee5f67a34b2c
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530029"
---
# <a name="tutorial-configure-airstack-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Airstack konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Airstack és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Airstack.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy Airstack-bérlő](https://airstack.com/pricing/)
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Airstack-ben.

## <a name="assigning-users-to-airstack"></a>Felhasználók kiosztása a Airstack

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Airstack való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Airstack az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-airstack"></a>Fontos Tippek a felhasználók Airstack való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Airstack-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Airstack rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-airstack-for-provisioning"></a>Airstack beállítása a kiépítés számára

1. Jelentkezzen be a [Airstack felügyeleti konzolra](https://airstack-qa.lenovosoftware.com/). Navigáljon a **Beállítások**elemre.

    ![Airstack felügyeleti konzol](media/airstack-provisioning-tutorial/airstackadminmenu.png)

2.  A képernyő bal oldalán található menüben navigáljon az **Azure config** -ra.

    ![Airstack-SCIM hozzáadása](media/airstack-provisioning-tutorial/azureconfig.png)

3.  Kattintson a **generált** gombra. Másolja az **Azure titkos jogkivonatát**. Ez az érték a Airstack alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjében jelenik meg a Azure Portal.

    ![Airstack-létrehozási jogkivonat](media/airstack-provisioning-tutorial/generatetoken.png)

## <a name="add-airstack-from-the-gallery"></a>Airstack hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Airstack konfigurálása előtt hozzá kell adnia a Airstack az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha Airstack szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Airstack**kifejezést, válassza az **Airstack** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Airstack az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-airstack"></a>Automatikus felhasználó-kiépítés beállítása a Airstack 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Airstack alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Airstack számára, az [Airstack egyszeri bejelentkezés oktatóanyagában](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial)megadott utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-airstack-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Airstack az Azure AD-ben:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Airstack**lehetőséget.

    ![Az Airstack hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a `https://api-as.lenovosoftware.com/0/as/common/scim` a **bérlői URL-címben**. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Airstack. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Airstack-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Airstack**lehetőséget.

    ![Airstack felhasználói leképezések](media/airstack-provisioning-tutorial/mappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Airstack. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Airstack felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Airstack felhasználói attribútumai](media/airstack-provisioning-tutorial/attributes.png)

10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Az Azure AD-kiépítési szolgáltatás Airstack való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a Airstack kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Airstack-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../manage-apps/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)


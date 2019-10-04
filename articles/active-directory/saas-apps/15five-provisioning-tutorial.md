---
title: 'Oktatóanyag: A 15Five konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a 15Five.
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
ms.openlocfilehash: c49d6fff50e74e67a4fef4ba9915efb72698a5b4
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641798"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Oktatóanyag: 15Five konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a 15Five és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a 15Five.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a Microsoft Azure-előnézetek [kiegészítő használati](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)feltételeit.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy 15Five-bérlő](https://www.15five.com/pricing/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a 15Five-ben.

## <a name="assigning-users-to-15five"></a>Felhasználók kiosztása a 15Five

Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a 15Five való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a 15Five az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-15five"></a>Fontos Tippek a felhasználók 15Five való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a 15Five-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor 15Five rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-15five-for-provisioning"></a>15Five beállítása a kiépítés számára

Az Azure AD-vel való automatikus 15Five konfigurálása előtt engedélyeznie kell a SCIM-létesítést a 15Five-on.

1. Jelentkezzen be a [15Five felügyeleti konzolra](https://my.15five.com/). Navigáljon a **funkciók > integrációk elemre**.

    ![15Five felügyeleti konzol](media/15five-provisioning-tutorial/integration.png)

2.  Kattintson a **SCIM 2,0**elemre.

    ![15Five felügyeleti konzol](media/15five-provisioning-tutorial/image00.png)

3.  Navigáljon a **scim-integráció > OAuth-token létrehozásához**.

    ![15Five-SCIM hozzáadása](media/15five-provisioning-tutorial/image02.png)

4.  Másolja a **SCIM 2,0 alap URL-cím** és a **hozzáférési jogkivonat**értékeit. Ez az érték a **bérlői URL-cím** és a **titkos jogkivonat** mezőben jelenik meg a 15Five alkalmazás kiépítés lapján a Azure Portalban.
    
    ![15Five-SCIM hozzáadása](media/15five-provisioning-tutorial/image03.png)

## <a name="add-15five-from-the-gallery"></a>15Five hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus 15Five konfigurálásához hozzá kell adnia a 15Five az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha 15Five szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **15Five**kifejezést, válassza az **15Five** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![15Five az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-15five"></a>Automatikus felhasználó-kiépítés beállítása a 15Five 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy 15Five alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a 15Five számára, az [15Five egyszeri bejelentkezés oktatóanyagában](15five-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a 15Five az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **15Five**lehetőséget.

    ![Az 15Five hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a kiépítés lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5.  A rendszergazdai hitelesítő adatok szakaszban adja meg a **SCIM 2,0 alap URL-címét és a hozzáférési jogkivonat** azon értékeit, amelyeket a **bérlői URL-cím** és a **titkos jogkivonat** korábban lekért. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a 15Five. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a 15Five-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a 15Five**lehetőséget.

    ![15Five felhasználói leképezések](media/15five-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum** -hozzárendelési szakaszban található 15Five. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a 15Five felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![15Five felhasználói attribútumai](media/15five-provisioning-tutorial/userattribute.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a 15Five**lehetőséget.

    ![15Five csoport-hozzárendelések](media/15five-provisioning-tutorial/groupmapping.png)

11. Tekintse át az Azure AD-ből szinkronizált 15Five az attribútumok leképezése szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a 15Five tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![15Five csoport attribútumai](media/15five-provisioning-tutorial/groupattribute.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD-kiépítési szolgáltatás 15Five való engedélyezéséhez módosítsa a kiépítési **állapotot** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a 15Five kiépíteni kívánt felhasználókat és/vagy csoportokat a Settings ( **Beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a **Beállítások** szakasz hatókörében meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a 15Five-on végrehajtott összes műveletet ismertetik.

    Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../manage-apps/check-status-user-account-provisioning.md) helyezésével kapcsolatban.
    
## <a name="connector-limitations"></a>Összekötő korlátozásai

* a 15Five nem támogatja a felhasználók számára a merevlemezek törlését.

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókok üzembe](../manage-apps/configure-automatic-user-provisioning-portal.md)helyezésének kezelése.
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* Megtudhatja, [Hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md).

---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés automatikus méretezésének konfigurálása Azure Active Directory | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a nagyításhoz.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: cd832a9dfec4680222d2c985f49aba499a56aaac
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062770"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Oktatóanyag: nagyítás konfigurálása automatikus felhasználó-kiépítési művelethez

Ennek az oktatóanyagnak a célja, hogy bemutassa a nagyítás és Azure Active Directory (Azure AD) végrehajtásának lépéseit az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat a nagyításhoz.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Nagyítási bérlő](https://zoom.us/pricing)
* Felhasználói fiók a nagyításban rendszergazdai engedélyekkel

## <a name="add-zoom-from-the-gallery"></a>Nagyítás hozzáadása a katalógusból

Az Azure AD-vel való automatikus kiépítés konfigurálása előtt hozzá kell adnia a nagyítást az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájára.

**Ha az Azure AD-alkalmazás-katalógusból szeretne nagyítani, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Nagyítás**kifejezést, válassza a **Nagyítás** lehetőséget az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Nagyítás az eredmények listájában](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Felhasználók kiosztása a nagyításhoz

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ban mely felhasználók és/vagy csoportok férhetnek hozzá a nagyításhoz. Miután eldöntötte, a következő utasításokat követve rendelheti hozzá ezeket a felhasználókat és/vagy csoportokat a nagyításhoz:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Fontos Tippek a felhasználók nagyításhoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen kiosztva a nagyításhoz a felhasználó automatikus kiépítési konfigurációjának teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor a felhasználó számára a nagyítást rendeli hozzá, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Automatikus felhasználó-kiépítés beállítása a nagyításhoz 

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein, hogy a felhasználók és/vagy a csoportok hozzárendelései az Azure AD-ben való létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Dönthet úgy is, hogy az SAML-alapú egyszeri bejelentkezést is engedélyezi a nagyításhoz, a [nagyítási egyszeri bejelentkezés oktatóanyagában](zoom-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Automatikus felhasználó-kiépítés beállítása a nagyításhoz az Azure AD-ben

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Nagyítás**lehetőséget.

    ![A nagyítási hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a `https://api.zoom.us/scim` a **bérlői URL-címben**. A nagyítási fiók **titkos jogkivonatának** lekéréséhez kövesse a 6. lépésben leírt útmutatót.

6. Jelentkezzen be a [nagyítási felügyeleti konzolba](https://zoom.us/signin). A bal oldali navigációs panelen navigáljon a **fejlesztők speciális > nagyításához** .

    ![Nagyítási integrációk](media/zoom-provisioning-tutorial/zoom01.png)

    Navigáljon a **kezelés** elemre a lap jobb felső sarkában. 

    ![Nagyítás telepítése](media/zoom-provisioning-tutorial/zoom02.png)

    Navigáljon a létrehozott Azure AD-alkalmazáshoz. 
    
    ![Alkalmazás nagyítása](media/zoom-provisioning-tutorial/zoom03.png)

    Válassza ki az **alkalmazás hitelesítő adatait** a bal oldali navigációs ablaktáblán.

    ![Alkalmazás nagyítása](media/zoom-provisioning-tutorial/zoom04.png)

    Kérje le az alább látható JWT-jogkivonat értékét, és írja be ezt az Azure AD **titkos jogkivonat** mezőjébe. Ha új, nem lejáró tokenre van szüksége, akkor újra kell konfigurálnia a lejárati időt, amely automatikusan létrehoz egy új tokent. 

    ![Nagyítás telepítése](media/zoom-provisioning-tutorial/zoom05.png)

7. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tud a nagyításhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a nagyítási fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók nagyításhoz**lehetőséget.

    ![Felhasználói leképezések nagyítása](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútumok leképezése** szakasz nagyításához. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez használt nagyítási műveletek felhasználói fiókjainak egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.
    
     ![Felhasználói leképezések nagyítása](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a nagyításhoz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.
    
    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne kiépíteni a nagyításhoz, és válassza ki a kívánt értékeket a **hatókör** területen a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a nagyításkor végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A nagyítás nem támogatja a csoportok üzembe helyezését.

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

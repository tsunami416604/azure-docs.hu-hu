---
title: 'Oktatóanyag: Szervezeti diagram most az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a szervezeti diagram most között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: d34b40195a5f8effb794f3fbefc7949740509e27
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835662"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Oktatóanyag: Szervezeti diagram most az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a szervezeti diagram most az Azure Active Directory (Azure AD).
Szervezeti diagram most integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a szervezeti diagram most az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett a szervezeti diagram most (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Szervezeti diagram most az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Szervezeti diagram most az egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Szervezeti diagram mostantól támogatja a **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-orgchart-now-from-the-gallery"></a>Szervezeti diagram most hozzáadása a katalógusból

Az Azure AD-be a szervezeti diagram most az integráció konfigurálásához hozzá kell szervezeti diagram most a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Szervezeti diagram most hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **szervezeti diagram most**, jelölje be **szervezeti diagram most** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Szervezeti diagram most az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és a szervezeti diagram most az Azure AD egyszeri bejelentkezés tesztelése alapján nevű tesztfelhasználó **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó szervezeti diagram most hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a szervezeti diagram most tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Szervezeti diagram most egyszeri bejelentkezés konfigurálása](#configure-orgchart-now-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Szervezeti diagram most tesztfelhasználó létrehozása](#create-orgchart-now-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása szervezeti diagram most.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szervezeti diagram most az Azure AD egyszeri bejelentkezés konfigurálása, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **szervezeti diagram most** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre a következő lépést:

    ![Szervezeti diagram most tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-identifier.png)

    Az a **azonosító** szövegmezőbe írja be egy URL-címe:  `https://sso2.orgchartnow.com`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![image](common/both-preintegrated-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` van a **az Azure AD-azonosító** átmásolta a **állítsa be a szervezeti diagram most** szakaszban, az oktatóanyag egy későbbi része ismerteti.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

7. Az a **állítsa be a szervezeti diagram most** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-orgchart-now-single-sign-on"></a>Szervezeti diagram most egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **szervezeti diagram most** oldalon kell küldenie a letöltött **összevonási metaadatainak XML** és az Azure Portalról másolt URL-címek megfelelő [szervezeti diagram most támogatási csoport ](mailto:ocnsupport@officeworksoftware.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a szervezeti diagram most az Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **szervezeti diagram most**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **szervezeti diagram most**.

    ![Az alkalmazások listáját a szervezeti diagram most hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-orgchart-now-test-user"></a>Szervezeti diagram most tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be szervezeti diagram most az Azure AD-felhasználók, akkor ki kell építeni szervezeti diagram most be. 

1. Szervezeti diagram most támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Új felhasználó próbál hozzáférni a szervezeti diagram most, ha még nem létezik jön létre. A just-in-time-felhasználó kiépítési szolgáltatás csak létrehoz egy **csak olvasható** egy egyszeri bejelentkezési kérelem érkezik egy felismert identitásszolgáltató és az e-mailt az SAML-előfeltétel a felhasználók listájában nem található felhasználó. Az Automatikus kiépítés funkció egy nevű-hozzáférési csoport létrehozására szeretne **általános** szervezeti diagram most. Kérjük, kövesse az alábbi lépéseket egy hozzáférési csoport létrehozására:

    a. Nyissa meg a **csoportok kezelése** kattintás után a beállítást a **fogaskerék** a felhasználói felület jobb felső sarkában található.

    ![Szervezeti diagram most csoportok](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Válassza ki a **Hozzáadás** ikon és a csoport nevét **általános** kattintson **OK**. 

    ![Szervezeti diagram most hozzáadása](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Válassza ki az általános vagy csak olvasható felhasználók érhetik el kívánja a mappák:

    ![Szervezeti diagram most mappák](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zárolási** mappákat úgy, hogy csak a rendszergazda felhasználók módosíthatja őket. Nyomja le az **OK**.

    ![Szervezeti diagram zárolják](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Hozhat létre **rendszergazdai** felhasználók és **olvasási/írási** felhasználók, manuálisan kell létrehoznia a felhasználó annak érdekében, hogy az egyszeri bejelentkezés használatával a jogosultsági szint eléréséhez. Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:

    a. Szervezeti diagram most egy biztonsági-rendszergazdaként jelentkezzen be.

    b.  Kattintson a **beállítások** jobb felső sarokban, majd lépjen **felhasználók kezelése**.

    ![Szervezeti diagram most beállításai](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Kattintson a **Hozzáadás** , és hajtsa végre az alábbi lépéseket:

    ![Szervezeti diagram most kezelése](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Az a **felhasználói azonosító** szövegmezőbe írja be például a felhasználói azonosító **brittasimon\@contoso.com**.

    * A **E-mail cím** szöveget adja meg az e-mailt, például a felhasználó **brittasimon\@contoso.com**.

    * Kattintson a **Hozzáadás** parancsra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a szervezeti diagram most csempére kattint, akkor kell automatikusan megtörténik a terjed, a szervezeti diagram, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


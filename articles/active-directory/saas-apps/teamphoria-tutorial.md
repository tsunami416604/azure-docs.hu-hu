---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Teamphoria |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Teamphoria között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 11d0a0a67d1ce5049166cab3d9ca3e4903b6b460
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277204"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Teamphoria

Ebben az oktatóanyagban elsajátíthatja, hogyan Teamphoria integrálása az Azure Active Directory (Azure AD).
Teamphoria integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Teamphoria Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Teamphoria (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Teamphoria az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Teamphoria egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Teamphoria **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-teamphoria-from-the-gallery"></a>Teamphoria hozzáadása a katalógusból

Az Azure AD integrálása a Teamphoria konfigurálásához hozzá kell Teamphoria a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Teamphoria hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Teamphoria**válassza **Teamphoria** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Teamphoria](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Teamphoria nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Teamphoria hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Teamphoria tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Teamphoria egyszeri bejelentkezés konfigurálása](#configure-teamphoria-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Teamphoria tesztfelhasználót](#create-teamphoria-test-user)**  – egy megfelelője a Britta Simon Teamphoria, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Teamphoria, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Teamphoria** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Teamphoria tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-intiated.png)

    Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > A bejelentkezési URL-érték nem valódi. Frissíteni ezt az értéket a tényleges bejelentkezési URL-címmel rendelkezik. Kapcsolattartó [Teamphoria ügyfél-támogatási csapatának](https://www.teamphoria.com/) a bejelentkezési URL-Címének lekéréséhez. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Teamphoria beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-teamphoria-single-sign-on"></a>Teamphoria egyszeri bejelentkezés konfigurálása

1. Az egyszeri bejelentkezés konfigurálása **Teamphoria** oldalán, jelentkezzen be rendszergazdaként Teamphoria alkalmazását.

1. Lépjen a **ADMINISZTRÁTORI beállítások** lehetőséget a bal oldali eszköztáron és a konfigurálása lapon kattintson a **EGYSZERI bejelentkezés** az egyszeri bejelentkezés konfigurálása ablak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/admin_sso_configure.png)

1. Kattintson a **hozzáadása új IDENTITÁSSZOLGÁLTATÓ** lehetőséget a jobb felső sarokban az egyszeri bejelentkezéshez a beállítások hozzáadásához a képernyő megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. Adja meg adatait a mezőket, amint az alábbi-

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **MEGJELENÍTETT NÉV**: A rendszergazda oldalon adja meg a beépülő modul a megjelenítendő nevét.

    b. **GOMB NEVE**: Az egyszeri bejelentkezés használatával jelentkezik be a bejelentkezési oldalon jelenik meg a lap neve.

    c. **TANÚSÍTVÁNY**: Nyissa meg a Jegyzettömbben az Azure Portalról korábban letöltött tanúsítványt, másolja a tartalmat az azonos, és illessze be ide a mezőbe.

    d. **BELÉPÉSI PONT**: Illessze be a **bejelentkezési URL-cím** az Azure Portalról korábban vágólapra másolt.

    e. Váltás lehetőségét **ON** , majd kattintson a **mentése**.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Teamphoria Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Teamphoria**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Teamphoria**.

    ![Az alkalmazások listáját a Teamphoria hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-teamphoria-test-user"></a>Teamphoria tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Teamphoria bejelentkezni, akkor ki kell építeni Teamphoria be. Teamphoria, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Teamphoria vállalati webhely.

1. Kattintson a **rendszergazdai** a bal oldali eszköztáron, majd a beállítások a **kezelés** lapon kattintson a **felhasználók** nyissa meg a rendszergazda felhasználók számára.

    ![Alkalmazott hozzáadása](./media/teamphoria-tutorial/admin_manage_users.png)

1. Kattintson a **manuális MEGHÍVÁSA** lehetőséget.

    ![Személyek meghívása](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Ezen a lapon a következő művelet végrehajtása.

    ![Személyek meghívása](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Az a **E-mail cím** szövegmezőbe írja be a **e-mail-cím** BrittaSimon például a felhasználó.

    b. Az a **UTÓNÉV** szövegmezőbe írja be például a felhasználó utónevét **Britta**.

    c. Az a **Vezetéknév** szövegmezőbe írja be például a felhasználó vezetékneve **Simon**.

    d. Kattintson a **MEGHÍVÓ 1 felhasználó**. Fogadja el a meghívást, a rendszer létrehozza a felhasználónak kell.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Teamphoria csempére kattint, meg kell lehet automatikusan bejelentkezett a Teamphoria, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


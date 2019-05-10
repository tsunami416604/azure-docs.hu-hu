---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Displayr |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Displayr között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9694790ea02bc778bf3b9db212e61fabb90a28a8
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441428"
---
# <a name="tutorial-azure-active-directory-integration-with-displayr"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Displayr

Ebben az oktatóanyagban elsajátíthatja, hogyan Displayr integrálása az Azure Active Directory (Azure AD).
Displayr integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Displayr Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Displayr (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Displayr az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Displayr egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Displayr **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-displayr-from-the-gallery"></a>Displayr hozzáadása a katalógusból

Az Azure AD integrálása a Displayr konfigurálásához hozzá kell Displayr a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Displayr hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Displayr**válassza **Displayr** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Displayr](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Displayr nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Displayr hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Displayr tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Displayr egyszeri bejelentkezés konfigurálása](#configure-displayr-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Displayr tesztfelhasználót](#create-displayr-test-user)**  – egy megfelelője a Britta Simon Displayr, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Displayr, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Displayr** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **beállítás egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre a következő lépést:

    ![Displayr tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-intiated.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe:  `https://app.displayr.com/Login`

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Raw)** megfelelően a követelmény, és mentse azt az adott közül a számítógép.

    ![A tanúsítvány letöltési hivatkozás](common/certificateraw.png)

6. Displayr alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a felhasználói attribútumok párbeszédpanel.

    ![image](common/edit-attribute.png)

7. Emellett a fentiekben Displayr alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. Az a **felhasználói attribútumok & jogcímek** szakaszában a **csoportjogcímek (előzetes verzió)** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    a. Kattintson a **toll** melletti **csoportok visszaküldött jogcímek**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Válassza ki **összes csoport** választógomb listájából.

    c. Válassza ki **forrásattribútumának Attribútumhierarchiája** , **csoportazonosító**.

    d. Ellenőrizze **testreszabása a csoportos jogcímet neve**.

    e. Ellenőrizze **gridre bocsáthatja ki csoportokat szerepkör jogcímekként**.

    f. Kattintson a **Save** (Mentés) gombra.

8. Az a **beállítás Displayr** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-displayr-single-sign-on"></a>Displayr egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be Displayr rendszergazdaként.

2. Kattintson a **beállítások** majd keresse meg a **fiók**.

    ![Konfiguráció](./media/displayr-tutorial/config01.png)

3. Váltson **beállítások** a felső menüben, és görgessen lefelé az oldalon kattintson a **konfigurálása egyszeri bejelentkezéshez a (SAML)**.

    ![Konfiguráció](./media/displayr-tutorial/config02.png)

4. Az a **egyszeri bejelentkezéshez a SAML** lapon, a következő lépésekkel:

    ![Konfiguráció](./media/displayr-tutorial/config03.png)

    a. Ellenőrizze a **engedélyezése egyszeri bejelentkezés a SAML** mezőbe.

    b. Az a **kibocsátó** szöveg mezőbe illessze be az értékét **az Azure AD-azonosító**, az Azure Portalról másolt.

    c. Az a **bejelentkezési URL-cím** szöveg mezőbe illessze be az értékét **bejelentkezési URL-cím**, az Azure Portalról másolt.

    d. Az a **kijelentkezési URL-címe** szöveg mezőbe illessze be az értékét **kijelentkezési URL-címe**, az Azure Portalról másolt.

    e. A Jegyzettömb alkalmazásban nyissa meg a tanúsítvány (Raw), másolja a tartalmat, és illessze be azt a **tanúsítvány** szövegmezőben.

    f. **Csoport-hozzárendelések** megadása nem kötelező.

    g. Kattintson a **Save** (Mentés) gombra.  

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Displayr Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Displayr**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Displayr**.

    ![Az alkalmazások listáját a Displayr hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-displayr-test-user"></a>Displayr tesztfelhasználó létrehozása

Engedélyezi az Azure AD-felhasználók, jelentkezzen be a Displayr, hogy azok ki kell építeni Displayr be. Displayr a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként Displayr.

2. Kattintson a **beállítások** majd keresse meg a **fiók**.

    ![Displayr konfiguráció](./media/displayr-tutorial/config01.png)

3. Váltson **beállítások** a felső menüben, és görgessen lefelé az oldalon, amíg **felhasználók** területen, majd kattintson a **új felhasználó**.

    ![Displayr konfiguráció](./media/displayr-tutorial/config07.png)

4. Az a **új felhasználó** lapon, a következő lépésekkel:

    ![Displayr konfiguráció](./media/displayr-tutorial/config06.png)

    a. A **neve** szöveget adja meg például a felhasználó nevét **Brittasimon**.

    b. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó `Brittasimon@contoso.com`.

    c. Válassza ki a megfelelő **csoporttagság**.

    d. Kattintson a **Save** (Mentés) gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Displayr csempére kattint, meg kell lehet automatikusan bejelentkezett a Displayr, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


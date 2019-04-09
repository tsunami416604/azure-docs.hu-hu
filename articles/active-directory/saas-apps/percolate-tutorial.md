---
title: 'Oktatóanyag: Szűrjön át az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory, és Szűrjön át között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: d399e83dbd1324827cc395cbf7834672baffa791
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275844"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Oktatóanyag: Szűrjön át az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Szűrjön át az Azure Active Directory (Azure AD).
Szűrjön át integrálása az Azure ad-vel nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Szűrjön át az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve (egyszeri bejelentkezés) az Azure AD-fiókjukat Szűrjön át.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Szűrjön át az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókot](https://azure.microsoft.com/free/).
* Szűrjön át egyetlen bejelentkezési engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Szűrjön át támogatja **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-percolate-from-the-gallery"></a>Szűrjön át hozzáadása a katalógusból

Az Azure AD integrálása a Szűrjön át konfigurálásához hozzá kell Szűrjön át a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Szűrjön át hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Szűrjön át**válassza **Szűrjön át** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Szűrjön az eredménylistában át](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni, és szűrjön a nevű tesztfelhasználó alapján át az Azure AD egyszeri bejelentkezés tesztelése **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Szűrjön át a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Szűrjön át tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Szűrjön át egyszeri bejelentkezés konfigurálása](#configure-percolate-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre tesztfelhasználót Szűrjön át](#create-percolate-test-user)**  – egy megfelelője a Britta Simon Szűrjön át, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Szűrjön át, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Szűrjön át** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett módban a felhasználónak nincs bármely lépése végrehajtani, mert az alkalmazás már előre integrált az Azure-ral.

    ![Szűrjön a tartomány és URL-címeket egyetlen bejelentkezési adatait át](common/preintegrated.png)

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Szűrjön a tartomány és URL-címeket egyetlen bejelentkezési adatait át](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe:  `https://percolate.com/app/login`

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

7. Az a **Szűrjön át beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-percolate-single-sign-on"></a>Szűrjön át egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be Szűrjön át rendszergazdaként.

2. A kezdőlap bal oldalán kattintson a **beállítások**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/percolate-tutorial/configure01.png)

3. A menüsávon a bal oldali menüjében kattintson a **SSO** alatt **szervezet**.

    ![Egyszeri bejelentkezés konfigurálása](./media/percolate-tutorial/configure02.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.

    b. A a **Entitásazonosító** szövegmezőjébe illessze be az értéket, **az Azure AD-azonosító** Azure Portalról másolt.

    c. A Jegyzettömbben, nyissa meg az Azure Portalról letöltött base-64 kódolású tanúsítványt, másolja a tartalmat, és illessze be azt a **x509 tanúsítványok** mezőbe.

    d. Az a **E-mail attribútum** szövegmezőbe írja be **emailaddress**.

    e. **Identity provider metaadatok URL-címe** mező kitöltése nem kötelező, és ha azok **alkalmazás összevonási metaadatainak URL-címe** másolta az Azure Portalról, illessze be a **Identity provider metaadatok URL-címe** szövegmező.

    f. Válassza ki **nem** , **AuthNRequests kell aláírni?**.

    g. Válassza ki **nem** , **SSO engedélyezése automatikus kiépítés**.

    h. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be brittasimon@yourcompanydomain.extension. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Szűrjön át Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Szűrjön át**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Szűrjön át**.

    ![A hivatkozás Szűrjön át az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-percolate-test-user"></a>Szűrjön át tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a Szűrjön át, akkor ki kell építeni Szűrjön át azokat. Szűrjön át, a kiépítés a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként Szűrjön át.

2. A menüsávon a bal oldali menüjében kattintson a **felhasználók** alatt **szervezet** , és keresse meg **új felhasználók**.

    ![Egyszeri bejelentkezés konfigurálása](./media/percolate-tutorial/configure03.png)

3. Az a **felhasználók létrehozása** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/percolate-tutorial/configure04.png)

    a. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó brittasimon@contoso.com.

    b. A **Fullname** szöveget adja meg például a felhasználó nevét **Brittasimon**.

    c. Kattintson a **felhasználók létrehozása**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen Szűrjön át csempére kattint, meg kell lehet automatikusan bejelentkezett, amelynek beállítása egyszeri bejelentkezés Szűrjön át. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


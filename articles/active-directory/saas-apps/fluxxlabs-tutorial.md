---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az Fluxx Labs |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Fluxx Labs között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2a2f8fb9cd84e9177ec351eae986a87c184f8f00
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543610"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Oktatóanyag: Az Azure Active Directory-integráció az Fluxx Labs

Ebben az oktatóanyagban elsajátíthatja, hogyan Fluxx laborok integrálása az Azure Active Directory (Azure AD).
Fluxx laborok integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Fluxx Labs Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve Fluxx Labs (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Fluxx Labs, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Fluxx Labs egy bejelentkezési engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Fluxx Labs **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-fluxx-labs-from-the-gallery"></a>Fluxx Labs hozzáadása a katalógusból

Fluxx laborok integrálása az Azure AD beállítása, hozzá kell Fluxx Labs a galériából a felügyelt SaaS-alkalmazások listájára.

**Fluxx Labs hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Fluxx Labs**válassza **Fluxx Labs** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Fluxx Labs](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az úgynevezett tesztfelhasználó alapján Fluxx Labs **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Fluxx Fejlesztőlaborokban lévő hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Fluxx Labs tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Fluxx Labs egyszeri bejelentkezés konfigurálása](#configure-fluxx-labs-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Fluxx Labs tesztfelhasználót](#create-fluxx-labs-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon Fluxx Labs-környezetben, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Fluxx tanulhat, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Fluxx Labs** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon, a következő lépésekkel:

    ![Fluxx Labs tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím:

    | Környezet | Az URL-minta|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io` |
    | Üzem előtti | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:

    | Környezet | Az URL-minta|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Üzem előtti | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [Fluxx Labs ügyfél-támogatási csapatának](mailto:travis@fluxxlabs.com) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Fluxx laborok** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-fluxx-labs-single-sign-on"></a>Fluxx Labs egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Fluxx Labs vállalati hely rendszergazdaként.

2. Válassza ki **rendszergazdai** alább a **beállítások** szakaszban.

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/config1.png)

3. A rendszergazda panelen válassza ki a **modulok** > **Integrációk** majd **SAML SSO-(Disabled)**

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/config2.png)

4. Az attribútum a szakaszban a következő lépésekkel:

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/config3.png)

    a. Válassza ki a **SAML SSO** jelölőnégyzetet.

    b. Az a **kérelem elérési útját** szövegmezőbe írja be **saml/auth/**.

    c. Az a **visszahívási elérési** szövegmezőbe írja be **/auth/saml/callback**.

    d. Az a **helyességi feltétel fogyasztói szolgáltatás Url(Single Sign-On URL)** szövegmezőbe írja be a **válasz URL-cím** érték, amely az Azure Portalon megadott.

    e. Az a **célközönség (SP entitás azonosítója)** szövegmezőbe írja be a **azonosító** érték, amely az Azure Portalon megadott.

    f. Az a **Identity Provider SSO cél URL-cím** szövegmezőjébe illessze be a **bejelentkezési URL-cím** érték, amely az Azure Portalról másolta.

    g. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **szolgáltató Identitástanúsítványt** szövegmezőbe.

    h. A **névazonosító formátuma** szövegmezőbe írja be az értéket `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Miután menti a tartalmat, a mező üres, a biztonság megjelenik, de az érték a konfiguráció mentése.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Fluxx Labs Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Fluxx Labs**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Fluxx Labs**.

    ![Az alkalmazások listáját a Fluxx Labs hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-fluxx-labs-test-user"></a>Fluxx Labs tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a Fluxx Labs, akkor ki kell építeni Fluxx Labs be. Fluxx Labs, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Fluxx Labs vállalati webhely.

2. Kattintson a az alábbiakban látható **ikon**.

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/config6.png)

3. Az irányítópulton kattintson a az alább megjelenő ikonra kattintva nyissa meg a **új személyek** kártya.

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/config4.png)

4. Az a **új személyek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs egyedi azonosítóként e-mailek használata Egyszeri bejelentkezéshez. Töltse fel a **SSO UID** mezőt a felhasználó e-mail címét, az e-mail-címét, amelyek használják az Egyszeri bejelentkezés megfelelő a.

    b. Kattintson a **Save** (Mentés) gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Fluxx Labs csempére kattint, meg kell lehet automatikusan bejelentkezett a Fluxx Labs, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


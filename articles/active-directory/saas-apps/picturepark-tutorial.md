---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Picturepark |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Picturepark között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 32af532fcb3b3c5a294590bb7a1fa610d1068e25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094451"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Picturepark

Ebben az oktatóanyagban elsajátíthatja, hogyan Picturepark integrálása az Azure Active Directory (Azure AD).
Picturepark integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Picturepark Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Picturepark (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Picturepark az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Picturepark egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Picturepark **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-picturepark-from-the-gallery"></a>Picturepark hozzáadása a katalógusból

Az Azure AD integrálása a Picturepark konfigurálásához hozzá kell Picturepark a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Picturepark hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Picturepark**válassza **Picturepark** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Picturepark](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Picturepark nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Picturepark hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Picturepark tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Picturepark egyszeri bejelentkezés konfigurálása](#configure-picturepark-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Picturepark tesztfelhasználót](#create-picturepark-test-user)**  – egy megfelelője a Britta Simon Picturepark, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Picturepark, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Picturepark** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Picturepark tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.picturepark.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [Picturepark ügyfél-támogatási csapatának](https://picturepark.com/about/contact/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **szerkesztése** gombra kattintva nyissa meg a **SAML-aláíró tanúsítvány** párbeszédpanel.

    ![SAML-aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

6. Az a **SAML-aláíró tanúsítvány** területén másolja a **ujjlenyomat** , és mentse a számítógépre.

    ![Másolja ki az ujjlenyomat értéket](common/copy-thumbprint.png)

7. Az a **Picturepark beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe. A **bejelentkezési URL-cím**, használja az értéket a következő mintával: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ a bérlő azonosítója, az Azure AD-előfizetés.

    ![Másolja a konfigurációs URL-címek](./media/picturepark-tutorial/configurls.png)

    a. Azure AD-azonosító

    b. Kijelentkezési URL

### <a name="configure-picturepark-single-sign-on"></a>Picturepark egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Picturepark vállalati hely rendszergazdaként.

2. A felső eszköztáron kattintson **felügyeleti eszközök**, és kattintson a **felügyeleti konzol**.
   
    ![Felügyeleti konzol](./media/picturepark-tutorial/ic795062.png "felügyeleti konzol")

3. Kattintson a **hitelesítési**, és kattintson a **Identitásszolgáltatók**.
   
    ![Hitelesítési](./media/picturepark-tutorial/ic795063.png "hitelesítés")

4. Az a **identitás-szolgáltató konfigurációjának** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Identitás-szolgáltató konfigurációjának](./media/picturepark-tutorial/ic795064.png "identitás szolgáltató konfigurációja")
   
    a. Kattintson a **Hozzáadás**lehetőségre.
  
    b. Adjon meg egy nevet a konfigurációnak.
   
    c. Válassza ki **alapértelmezett**.
   
    d. A **kibocsátó URI** szövegmező, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.
   
    e. A **megbízható kiállító ujjlenyomata** szövegmező, illessze be az értéket a **ujjlenyomat** másolta amely **SAML-aláíró tanúsítvány** szakaszban. 

5. Kattintson a **JoinDefaultUsersGroup**.

6. Beállítása a **Emailaddress** az attribútum a **jogcím** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` kattintson **mentése**.

      ![Konfigurációs](./media/picturepark-tutorial/ic795065.png "konfiguráció")

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Picturepark Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Picturepark**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Picturepark**.

    ![Az alkalmazások listáját a Picturepark hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-picturepark-test-user"></a>Picturepark tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a Picturepark, akkor ki kell építeni Picturepark be. Picturepark, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Picturepark** bérlő.

1. A felső eszköztáron kattintson **felügyeleti eszközök**, és kattintson a **felhasználók**.
   
    ![Felhasználók](./media/picturepark-tutorial/ic795067.png "felhasználók")

1. Az a **felhasználók áttekintése** lapra, majd **új**.
   
    ![Felhasználókezelés](./media/picturepark-tutorial/ic795068.png "felhasználók kezelése")

1. Az a **Create User** párbeszédpanelen a következő lépésekkel, egy érvényes Azure Active Directory-felhasználó szeretne üzembe helyezni:
   
    ![Felhasználó létrehozása](./media/picturepark-tutorial/ic795069.png "felhasználó létrehozása")
   
    a. Az a **E-mail cím** szövegmezőbe írja be a **e-mail-cím** felhasználó `BrittaSimon@contoso.com`.  
   
    b. Az a **jelszó** és **jelszó megerősítése** szövegmezőből, írja be a **jelszó** BrittaSimon az. 
   
    c. Az a **Utónév** szövegmezőbe írja be a **Utónév** felhasználó **Britta**. 
   
    d. Az a **Vezetéknév** szövegmezőbe írja be a **Vezetéknév** felhasználó **Simon**.
   
    e. Az a **vállalati** szövegmezőbe írja be a **cégnév** felhasználó. 
   
    f. Az a **ország** szövegmezőbe, válassza ki a **ország/régió** felhasználó.
  
    g. Az a **ZIP** szövegmezőbe írja be a **irányítószám** az városa.
   
    h. Az a **Város** szövegmezőbe írja be a **városnév** felhasználó.

    i. Válassza ki a **nyelvi**.
   
    j. Kattintson a **Create** (Létrehozás) gombra.

>[!NOTE]
>Eszközt is használhat bármilyen más Picturepark felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése Picturepark által biztosított API-k.
>

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Picturepark csempére kattint, meg kell lehet automatikusan bejelentkezett a Picturepark, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


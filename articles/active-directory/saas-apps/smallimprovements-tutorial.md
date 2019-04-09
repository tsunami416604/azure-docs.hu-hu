---
title: 'Oktatóanyag: Kisebb fejlesztések az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a kisebb fejlesztések között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 19d9624c5bb60f47ef4bfa1b0629327780c2a9c7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266302"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Oktatóanyag: Kisebb fejlesztések az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan kis fejlesztései integrálása az Azure Active Directory (Azure AD).
Kisebb fejlesztések integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a kisebb fejlesztések az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve kis fejlesztései (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Kisebb fejlesztések az Azure AD-integráció konfigurálása, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Kisebb fejlesztések egy bejelentkezési engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Kisebb fejlesztések támogatja **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-small-improvements-from-the-gallery"></a>Kisebb fejlesztések hozzáadása a katalógusból

Kisebb fejlesztések integrálása az Azure AD beállítása, hozzá kell kis fejlesztései a galériából a felügyelt SaaS-alkalmazások listájára.

**Kisebb fejlesztések hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **kis fejlesztései**, jelölje be **kis fejlesztései** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Kisebb fejlesztések a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó kis javításait **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó kis fejlesztései a hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés kis javításait tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Kisebb fejlesztések egyszeri bejelentkezés konfigurálása](#configure-small-improvements-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Kisebb fejlesztések tesztfelhasználó létrehozása](#create-small-improvements-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon kis eredményez, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Kisebb fejlesztések az Azure AD egyszeri bejelentkezés konfigurálása, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **kis fejlesztései** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Kisebb fejlesztések tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.small-improvements.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [kis fejlesztései ügyfél-támogatási csapatának](mailto:support@small-improvements.com) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **kis fejlesztései beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-small-improvements-single-sign-on"></a>Kisebb fejlesztések egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a kisebb fejlesztések vállalati hely rendszergazdaként.

1. A fő irányítópult-oldalon, kattintson az **felügyeleti** a bal oldali gomb.

    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Kattintson a **SAML SSO** gombra **Integrációk** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Az egyszeri bejelentkezés beállítása lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Az a **HTTP-végpontot** szövegmező, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    b. Nyissa meg a letöltött tanúsítvány a Jegyzettömbben, másolja a tartalmat, és illessze be azt a **x509 tanúsítvány** szövegmezőbe. 

    c. Ha szeretne egyszeri Bejelentkezést és a bejelentkezési képernyőn hitelesítési lehetőséget a felhasználók számára elérhető rendelkezik, akkor ellenőrizze a **bejelentkezési/jelszó keresztüli hozzáférés engedélyezésére túl** lehetőséget.  

    d. Nevezze el az Egyszeri bejelentkezés gombra a megfelelő értéket a **SAML Rákérdezés** szövegmezőbe.  

    e. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban engedélyezze Britta Simon hozzáférést biztosít a kisebb javítások által használandó Azure egyszeri bejelentkezést.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **kis fejlesztései**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **kis fejlesztései**.

    ![Az alkalmazások listáját a kisebb fejlesztések hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-small-improvements-test-user"></a>Kisebb fejlesztések tesztfelhasználó létrehozása

Kisebb fejlesztések jelentkezzen be az Azure AD-felhasználók engedélyezéséhez, ki kell építeni be kis fejlesztései. Kisebb fejlesztések esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a kisebb fejlesztések vállalati webhelyre rendszergazdaként.

1. A kezdőlapon nyissa meg a menüben kattintson a bal oldali, a **felügyeleti**.

1. Kattintson a **felhasználói címtárba** felhasználókezelés szakaszban gombra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Kattintson a **felhasználók hozzáadása**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. Az a **felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket: 

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Adja meg a **Utónév** például a felhasználó **Britta**.

    b. Adja meg a **Vezetéknév** például a felhasználó **Simon**.

    c. Adja meg a **E-mail** például a felhasználó **brittasimon@contoso.com**.

    d. Azt is beállíthatja, adja meg a személyes üzenet a **értesítő** mezőbe. Ha nem szeretne az értesítés elküldéséhez, akkor törölje ezt a jelölőnégyzetet.

    e. Kattintson a **felhasználók létrehozása**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a kisebb fejlesztések csempére kattint, meg kell kell automatikusan bejelentkezett a kisebb fejlesztések, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
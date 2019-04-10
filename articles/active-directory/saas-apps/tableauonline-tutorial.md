---
title: 'Oktatóanyag: A Tableau Online az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Tableau Online között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.openlocfilehash: 5ccf978ab33226dc029d534a343a87a796ab69e8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278105"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Oktatóanyag: A Tableau Online az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Tableau Online az Azure Active Directoryval (Azure AD).
Az Azure AD integrálása Tableau Online nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Tableau Online az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett a Tableau Online (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Tableau Online, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* A tableau Online egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Online támogatja a tableau **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-tableau-online-from-the-gallery"></a>A katalógusból Tableau Online hozzáadása

Az Azure AD integrálása a Tableau Online konfigurálásához hozzá kell Tableau Online a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Tableau Online a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Tableau Online**válassza **Tableau Online** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában a tableau Online](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Tableau Online nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a Tableau Online hivatkozás kapcsolata kell hozható létre.

Konfigurálása és az Azure AD egyszeri bejelentkezés Tableau online-nal való teszteléséhez hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[A Tableau Online egyszeri bejelentkezés konfigurálása](#configure-tableau-online-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Tableau Online tesztfelhasználót](#create-tableau-online-test-user)**  - a-megfelelője a Britta Simon rendelkezik a Tableau online-ban, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés, a Tableau Online, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Tableau Online** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A tableau Online tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be az URL-cím: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be az URL-cím: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Megjelenik a `<entityid>` értéket a **állítsa be a Tableau Online** szakaszt ebben az oktatóanyagban. Az entitás azonosítója érték **az Azure AD-azonosító** értékét **állítsa be a Tableau Online** szakaszban.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **állítsa be a Tableau Online** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-tableau-online-single-sign-on"></a>A Tableau Online egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban bejelentkezés, a Tableau Online alkalmazás. Lépjen a **beállítások** , majd **hitelesítési**.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. SAML, a engedélyezéséhez **hitelesítési típusok** szakaszban. Ellenőrizze **engedélyezése egy további hitelesítési módszerként** , majd ellenőrizze **SAML** jelölőnégyzetet.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Görgessen lefelé, akár **metaadatait tartalmazó fájl importálása a Tableau Online szolgáltatásba** szakaszban.  Kattintson a Tallózás gombra, és importálja a metaadatokat fájlt, amely már letöltötte az Azure ad-ből. Kattintson a **alkalmaz**.

   ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Az a **felel meg a helyességi feltételek** szakaszban, a megfelelő identitásszolgáltató helyességi feltétel nevét a **e-mail-cím**, **Utónév**, és **Vezetéknév**. Ez az információ lekérése az Azure ad-ből: 
  
    a. Az Azure Portalon lépjen a **Tableau Online** alkalmazás integráció lapján.

    b. Az a ** felhasználói attribútumok & jogcímek x szakaszban kattintson a Szerkesztés ikonra.

   ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/attributesection.png)

    c. Másolja a névtér értéke attribútumait: givenname, e-mailek és a Vezetéknév, az alábbi lépések segítségével:

   ![Az Azure AD egyszeri bejelentkezés](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Kattintson a **user.givenname** érték

    e. Másolja az értéket a **Namespace** szövegmezőbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/attributesection2.png)

    f. Az e-mailek és a Vezetéknév értékeit másolja ki a névteret ismételje meg a fenti lépéseket.

    g. Váltson a Tableau Online alkalmazást, majd állítsa be a **felhasználói attribútumok & jogcímek** szakasz az alábbiak szerint:

    * E-mail cím: **mail** vagy **userprincipalname**

    * Utónév: **givenname**

    * Vezetéknév: **vezetékneve**

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

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

Ebben a szakaszban engedélyezze Britta Simon Tableau online-hoz való hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Tableau Online**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Tableau Online**.

    ![Az alkalmazások listáját a Tableau Online hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-tableau-online-test-user"></a>A Tableau Online tesztfelhasználó létrehozása

Ebben a szakaszban egy a Tableau Online Britta Simon nevű felhasználó hoz létre.

1. A **Tableau Online**, kattintson a **beállítások** , majd **hitelesítési** szakaszban. Görgessen le a **felhasználók kezelése** szakaszban. Kattintson a **felhasználó hozzáadása** majd **adja meg E-mail címeket**.
  
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Válassza ki **hozzá felhasználókat (SAML) hitelesítés**. Az a **adjon meg e-mail címeket** szövegmező hozzáadása britta.simon@contoso.com
  
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Kattintson a **felhasználók hozzáadása**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen, a Tableau Online csempére kattint, akkor kell automatikusan megtörténik a a Tableau online, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

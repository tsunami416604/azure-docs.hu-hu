---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ThousandEyes |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és ThousandEyes között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 587d6f8ecdc447672c14ce400ebc64609e4d472d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273906"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ThousandEyes

Ebben az oktatóanyagban elsajátíthatja, hogyan ThousandEyes integrálása az Azure Active Directory (Azure AD).
ThousandEyes integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá ThousandEyes Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve ThousandEyes (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

ThousandEyes az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* ThousandEyes egyszeri bejelentkezéses engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a ThousandEyes **SP** által kezdeményezett egyszeri bejelentkezés

* Támogatja a ThousandEyes [ **automatikus** felhasználók átadása](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

## <a name="adding-thousandeyes-from-the-gallery"></a>ThousandEyes hozzáadása a katalógusból

Az Azure AD integrálása a ThousandEyes konfigurálásához hozzá kell ThousandEyes a katalógusból a felügyelt SaaS-alkalmazások listájára.

**ThousandEyes hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **ThousandEyes**válassza **ThousandEyes** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában ThousandEyes](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az ThousandEyes nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó ThousandEyes hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az ThousandEyes tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[ThousandEyes egyszeri bejelentkezés konfigurálása](#configure-thousandeyes-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre ThousandEyes tesztfelhasználót](#create-thousandeyes-test-user)**  – egy megfelelője a Britta Simon ThousandEyes, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés ThousandEyes, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **ThousandEyes** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![ThousandEyes tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe:  `https://app.thousandeyes.com/login/sso`

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **ThousandEyes beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-thousandeyes-single-sign-on"></a>ThousandEyes egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be a **ThousandEyes** rendszergazdaként a vállalati webhely.

2. A felső menüben kattintson **beállítások**.

    ![Beállítások](./media/thousandeyes-tutorial/ic790066.png "beállításai")

3. Kattintson a **fiók**

    ![Fiók](./media/thousandeyes-tutorial/ic790067.png "fiók")

4. Kattintson a **biztonság és hitelesítés** fülre.

    ![A biztonság és hitelesítés](./media/thousandeyes-tutorial/ic790068.png "biztonság és hitelesítés")

5. Az a **beállítása egyszeri bejelentkezéshez** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés beállítása](./media/thousandeyes-tutorial/ic790069.png "egyszeri bejelentkezés beállítása")

    a. Válassza ki **egyszeri bejelentkezés engedélyezése**.

    b. A **bejelentkezési oldal URL-cím** szövegmezőjébe illessze be **bejelentkezési URL-cím**, az Azure Portalról másolt.

    c. A **kijelentkezési URL-címe** szövegmezőjébe illessze be **kijelentkezési URL-címe**, amely az Azure Portalról másolt.

    d. **Identitásszolgáltató kibocsátója** szövegmezőjébe illessze be **az Azure AD-azonosító**, amely az Azure Portalról másolt.

    e. A **ellenőrző tanúsítvány**, kattintson a **fájl kiválasztása**, majd töltse fel a tanúsítványt, hogy letöltötte az Azure Portalról.

    f. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ThousandEyes Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **ThousandEyes**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **ThousandEyes**.

    ![Az alkalmazások listáját a ThousandEyes hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-thousandeyes-test-user"></a>ThousandEyes tesztfelhasználó létrehozása

Ez a szakasz célja ThousandEyes Britta Simon nevű felhasználó létrehozásához. ThousandEyes támogatja a felhasználók automatikus átadása, amely alapértelmezés szerint van engedélyezve. További részleteket talál [Itt](thousandeyes-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

**Hozza létre a felhasználó manuálisan kell, ha hajtsa végre a következő lépéseket:**

1. Jelentkezzen be rendszergazdaként a ThousandEyes vállalati webhely.

2. Kattintson a **beállítások**.

    ![Beállítások](./media/thousandeyes-tutorial/IC790066.png "beállításai")

3. Kattintson a **fiók**.

    ![Fiók](./media/thousandeyes-tutorial/IC790067.png "fiók")

4. Kattintson a **fiókok és a felhasználók** fülre.

    ![Fiókok és a felhasználók](./media/thousandeyes-tutorial/IC790073.png "fiókok és a felhasználók")

5. Az a **hozzáadása felhasználók és fiókok** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Felhasználói fiókok hozzáadása](./media/thousandeyes-tutorial/IC790074.png "felhasználói fiókok hozzáadása")

    a. A **neve** szövegmezőbe írja be a nevet a felhasználó például **Britta Simon**.

    b. A **E-mail** szövegmezőbe írja be az e-mailt, felhasználó, például brittasimon@contoso.com.

    b. Kattintson a **új felhasználó hozzáadása a fiókhoz**.

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos fog kapni egy e-mailt és a telepítőre mutató erősítse meg, és a fiók aktiválásához.

> [!NOTE]
> Bármely más ThousandEyes felhasználói fiók létrehozása eszközöket használhatja, vagy API-k által biztosított ThousandEyes üzembe helyezni az Azure Active Directory felhasználói fiókokat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ThousandEyes csempére kattint, meg kell lehet automatikusan bejelentkezett a ThousandEyes, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Felhasználók átadásának konfigurálása](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

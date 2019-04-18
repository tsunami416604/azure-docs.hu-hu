---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ScreenSteps |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és ScreenSteps között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 570f789d0f399c5ffa7535101136ab65ba58ccd5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59277085"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ScreenSteps

Ebben az oktatóanyagban elsajátíthatja, hogyan ScreenSteps integrálása az Azure Active Directory (Azure AD).
ScreenSteps integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá ScreenSteps Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve ScreenSteps (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

ScreenSteps az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* ScreenSteps egyszeri bejelentkezéses engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a ScreenSteps **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-screensteps-from-the-gallery"></a>ScreenSteps hozzáadása a katalógusból

Az Azure AD integrálása a ScreenSteps konfigurálásához hozzá kell ScreenSteps a katalógusból a felügyelt SaaS-alkalmazások listájára.

**ScreenSteps hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **ScreenSteps**válassza **ScreenSteps** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában ScreenSteps](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az ScreenSteps nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó ScreenSteps hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az ScreenSteps tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[ScreenSteps egyszeri bejelentkezés konfigurálása](#configure-screensteps-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre ScreenSteps tesztfelhasználót](#create-screensteps-test-user)**  – egy megfelelője a Britta Simon ScreenSteps, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés ScreenSteps, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **ScreenSteps** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![ScreenSteps tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Az érték nem valódi. Frissítse a tényleges bejelentkezési URL-cím, az oktatóanyag későbbi részében ismertetett ezt az értéket.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **ScreenSteps beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-screensteps-single-sign-on"></a>ScreenSteps egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a ScreenSteps vállalati hely rendszergazdaként.

1. Kattintson a **Fiókbeállítások**.

    ![Fiókkezelés](./media/screensteps-tutorial/ic778523.png "fiókkezelés")

1. Kattintson a **egyszeri bejelentkezési**.

    ![Távoli hitelesítés](./media/screensteps-tutorial/ic778524.png "távoli hitelesítés")

1. Kattintson a **egyszeri bejelentkezési végpont létrehozása**.

    ![Távoli hitelesítés](./media/screensteps-tutorial/ic778525.png "távoli hitelesítés")

1. Az a **létrehozása egyszeri bejelentkezési végpont** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Hozzon létre egy hitelesítési végpont](./media/screensteps-tutorial/ic778526.png "hitelesítési végpont létrehozása")

    a. Az a **cím** szövegmezőbe, a címet írja be.

    b. Az a **mód** listáról válassza ki **SAML**.

    c. Kattintson a **Create** (Létrehozás) gombra.

1. **Szerkesztés** az új végpont.

    ![Végpont szerkesztése](./media/screensteps-tutorial/ic778528.png "végpont szerkesztése")

1. Az a **szerkesztése egyszeri bejelentkezési végpont** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Távoli hitelesítési végpontja](./media/screensteps-tutorial/ic778527.png "távoli hitelesítési végpontja")

    a. Kattintson a **új SAML-tanúsítvány fájlfeltöltés**, majd töltse fel a tanúsítványt, amely már letöltötte az Azure Portalról.

    b. Beillesztés **bejelentkezési URL-cím** érték, amely az Azure Portalról történő másolta a **távoli bejelentkezési URL-cím** szövegmezőbe.

    c. Beillesztés **kijelentkezési URL-címe** érték, amely az Azure Portalról történő másolta a **kijelentkezési URL-cím** szövegmezőbe.

    d. Válassza ki a **csoport** kiépítésüket amikor a felhasználók hozzárendelése.

    e. Kattintson az **Update** (Frissítés) elemre.

    f. Másolás a **SAML ügyfél URL-címe** a vágólapra, és illessze be a a **bejelentkezési URL-** szövegmezőjébe **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    g. Lépjen vissza a **egyszeri bejelentkezési végpont szerkesztése**.

    h. Kattintson a **alapértelmezett fiók** ezt a végpontot használja az összes felhasználó számára ScreenSteps bejelentkezés gombot. Másik lehetőségként kattinthat a **helynek** gombra kattintva ezt a végpontot használja az adott webhelyekhez **ScreenSteps**.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ScreenSteps Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **ScreenSteps**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **ScreenSteps**.

    ![Az alkalmazások listáját a ScreenSteps hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-screensteps-test-user"></a>ScreenSteps tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű ScreenSteps hoz létre. Együttműködve [ScreenSteps ügyfél-támogatási csapatának](https://www.screensteps.com/contact) a felhasználók hozzáadása az ScreenSteps platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ScreenSteps csempére kattint, meg kell lehet automatikusan bejelentkezett a ScreenSteps, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
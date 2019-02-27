---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező BlueJeans |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és BlueJeans között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de0e20eb096ba127790019c1c07fbdd504d3a480
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877911"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező BlueJeans

Ebben az oktatóanyagban elsajátíthatja, hogyan BlueJeans integrálása az Azure Active Directory (Azure AD).
BlueJeans integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá BlueJeans Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve BlueJeans (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

BlueJeans az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* BlueJeans egyszeri bejelentkezéses engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a blueJeans **SP** által kezdeményezett egyszeri bejelentkezés

* Támogatja a blueJeans [ **automatikus** felhasználók átadása](bluejeans-provisioning-tutorial.md)

## <a name="adding-bluejeans-from-the-gallery"></a>BlueJeans hozzáadása a katalógusból

Az Azure AD integrálása a BlueJeans konfigurálásához hozzá kell BlueJeans a katalógusból a felügyelt SaaS-alkalmazások listájára.

**BlueJeans hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **BlueJeans**válassza **BlueJeans** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában blueJeans](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az BlueJeans nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó BlueJeans hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az BlueJeans tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[BlueJeans egyszeri bejelentkezés konfigurálása](#configure-bluejeans-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre BlueJeans tesztfelhasználót](#create-bluejeans-test-user)**  - a-megfelelője a Britta Simon BlueJeans a felhasználó Azure ad-ben reprezentációja kapcsolódó rendelkezik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés BlueJeans, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **BlueJeans** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![BlueJeans tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [BlueJeans ügyfél-támogatási csapatának](https://support.bluejeans.com/contact) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **BlueJeans beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-bluejeans-single-sign-on"></a>BlueJeans egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be a **BlueJeans** rendszergazdaként a vállalati webhely.

2. Lépjen a **rendszergazdai \> CSOPORTBEÁLLÍTÁSOK \> biztonsági**.

    ![Rendszergazdai](./media/bluejeans-tutorial/IC785868.png "rendszergazda")

3. Az a **biztonsági** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezési SAML](./media/bluejeans-tutorial/IC785869.png "SAML egyszeri bejelentkezés")

    a. Válassza ki **egyszeri bejelentkezési SAML**.

    b. Válassza ki **automatikus kiépítés engedélyezése**.

4. Helyezze az alábbi lépéseket követve:

    ![Tanúsítvány-elérési út](./media/bluejeans-tutorial/IC785870.png "tanúsítvány elérési útja")

    a. Kattintson a **fájl kiválasztása**, töltse fel az base-64 kódolású tanúsítványt, az Azure Portalról letöltött.

    b. Az a **bejelentkezési URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.

    c. Az a **URL-Címének jelszó módosítása** szövegmezőjébe illessze be az értéket a **jelszó URL-Címének módosítása** Azure Portalról másolt.

    d. Az a **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezési URL-címe** Azure Portalról másolt.

5. Helyezze az alábbi lépéseket követve:

    ![Módosítások mentése](./media/bluejeans-tutorial/IC785874.png "módosítások mentése")

    a. Az a **felhasználóazonosító** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Az a **E-mail** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Kattintson a **MENTSE a MÓDOSÍTÁSOKAT**.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés BlueJeans Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **BlueJeans**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **BlueJeans**.

    ![Az alkalmazások listáját a BlueJeans hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-bluejeans-test-user"></a>BlueJeans tesztfelhasználó létrehozása

Ez a szakasz célja BlueJeans Britta Simon nevű felhasználó létrehozásához. BlueJeans támogatja a felhasználók automatikus átadása, amely alapértelmezés szerint van engedélyezve. További részleteket talál [Itt](bluejeans-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

**Hozza létre a felhasználó manuálisan kell, ha hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **BlueJeans** rendszergazdaként a vállalati webhely.

2. Lépjen a **rendszergazdai \> felhasználók kezelése \> felhasználó hozzáadása**.

    ![Rendszergazdai](./media/bluejeans-tutorial/IC785877.png "rendszergazda")

    >[!IMPORTANT]
    >A **felhasználó hozzáadása** lap csak akkor érhető el, ha az a **biztonsági lapon**, **automatikus kiépítés engedélyezése** nincs bejelölve. 

3. Az a **felhasználó hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Felhasználó hozzáadása](./media/bluejeans-tutorial/IC785886.png "felhasználó hozzáadása")

    a. A **Utónév** szöveget adja meg például a felhasználó utónevét **Britta**.

    b. A **Vezetéknév** szöveget adja meg például a felhasználó vezetékneve **simon**.

    c. A **BlueJeans felhasználónevet válasszon** szöveget adja meg például a felhasználó felhasználóneve **Brittasimon**

    d. A **hozzon létre egy jelszót** szöveget adja meg a jelszót.

    e. A **vállalati** szöveget adja meg a vállalati.

    f. A **E-mail cím** szöveget adja meg az e-mailt, például a felhasználó **brittasimon@contoso.com**.

    g. A **hozzon létre egy BlueJeans értekezlet i.** szöveget adja meg az értekezlet azonosítóját.

    h. A **válasszon ki egy moderátor PIN-kód** szöveget adja meg a PIN-kódját.

    i. Kattintson a **FOLYTATÁS**.

    ![Addd felhasználói](./media/bluejeans-tutorial/IC785887.png "Addd felhasználó")

    J. Kattintson a **felhasználó hozzáadása**.

> [!NOTE]
> Eszközt is használhat bármilyen más BlueJeans felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése BlueJeans által biztosított API-k.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a BlueJeans csempére kattint, meg kell lehet automatikusan bejelentkezett a BlueJeans, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


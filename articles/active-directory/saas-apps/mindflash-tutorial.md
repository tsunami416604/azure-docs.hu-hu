---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Mindflash |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Mindflash között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bdf91993-aaaa-4598-89b7-77ef8ca065d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 592628cb036972cdae2ab14501fc09fa8ddcb7cc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761719"
---
# <a name="tutorial-azure-active-directory-integration-with-mindflash"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Mindflash

Ebben az oktatóanyagban elsajátíthatja, hogyan Mindflash integrálása az Azure Active Directory (Azure AD).
Mindflash integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Mindflash Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Mindflash (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Mindflash az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Mindflash egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Mindflash **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-mindflash-from-the-gallery"></a>Mindflash hozzáadása a katalógusból

Az Azure AD integrálása a Mindflash konfigurálásához hozzá kell Mindflash a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Mindflash hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Mindflash**válassza **Mindflash** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Mindflash](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Mindflash nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Mindflash hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Mindflash tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Mindflash egyszeri bejelentkezés konfigurálása](#configure-mindflash-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Mindflash tesztfelhasználót](#create-mindflash-test-user)**  – egy megfelelője a Britta Simon Mindflash, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Mindflash, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Mindflash** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Mindflash tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.mindflash.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.mindflash.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [Mindflash ügyfél-támogatási csapatának](https://www.mindflash.com/contact/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **Mindflash beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-mindflash-single-sign-on"></a>Mindflash egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **Mindflash** oldalon kell küldenie a letöltött **összevonási metaadatainak XML** és az Azure Portalról másolt URL-címek megfelelő [Mindflash támogatási csapatának](https://www.mindflash.com/contact/). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Mindflash Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Mindflash**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Mindflash**.

    ![Az alkalmazások listáját a Mindflash hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-mindflash-test-user"></a>Mindflash tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Mindflash szolgáltatásba való bejelentkezéshez, akkor ki kell építeni Mindflash be. Mindflash, esetén kiépítése a manuális feladat.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>A felhasználói fiókok kiépítése, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a **Mindflash** rendszergazdaként a vállalati webhely.

1. Lépjen a **felhasználók kezelése**.
   
    ![Felhasználók kezelése](./media/mindflash-tutorial/ic787140.png "felhasználók kezelése")

1. Kattintson a **felhasználó hozzáadása**, és kattintson a **új**.

1. Az a **az új felhasználók hozzáadása** szakaszban, a következő lépésekkel egy érvényes Azure AD-fiókot kíván üzembe helyezni:
   
    ![Új felhasználók hozzáadása](./media/mindflash-tutorial/ic787141.png "új felhasználók hozzáadása")
   
    a. Az a **Utónév** szövegmezőbe írja be **Utónév** , a felhasználó **Britta**.

    b. Az a **Vezetéknév** szövegmezőbe írja be **Vezetéknév** , a felhasználó **Simon**.
    
    c. Az a **E-mail** szövegmezőbe írja be **E-mail cím** , a felhasználó **BrittaSimon@contoso.com**.

    b. Kattintson a **Hozzáadás** parancsra.

>[!NOTE]
>Bármely más Mindflash felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Mindflash által biztosított API-k. 
> 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Mindflash csempére kattint, meg kell lehet automatikusan bejelentkezett a Mindflash, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, Marketo-|} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Marketo között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 0b6fed02aa7480f3c9e7be68c5d818e956cc436d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766325"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Oktatóanyag: Az Azure Active Directory-integráció, Marketo-

Ebben az oktatóanyagban elsajátíthatja, hogyan Marketo integrálása az Azure Active Directory (Azure AD).
A Marketo integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Marketo Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett a marketo eszközhöz (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Marketo, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* A Marketo egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Marketo **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-marketo-from-the-gallery"></a>A Marketo hozzáadása a katalógusból

Az Azure AD-be a Marketo-integráció konfigurálásához, hozzá kell Marketo a galériából a felügyelt SaaS-alkalmazások listájára.

**A Marketo hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Marketo**válassza **Marketo** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Marketo a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Marketo nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó a marketo rendszerében hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Marketo tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[A Marketo egyszeri bejelentkezés konfigurálása](#configure-marketo-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Marketo tesztfelhasználót](#create-marketo-test-user)**  –, amely kapcsolódik az Azure AD felhasználói ábrázolása a Marketo-megfelelője a Britta Simon van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálásához a Marketo, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Marketo** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon, a következő lépésekkel:

    ![Marketo-tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://saml.marketo.com/sp`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [Marketo-ügyfél-támogatási csapatának](http://investors.marketo.com/contactus.cfm) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **állítsa be a Marketo** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-marketo-single-sign-on"></a>A Marketo egyszeri bejelentkezés konfigurálása

1. Az alkalmazás Munchkin azonosítójának lekéréséhez, jelentkezzen be a Marketo-rendszergazda hitelesítő adataival, és hajtsa végre a következő műveleteket:
   
    a. Jelentkezzen be rendszergazdai hitelesítő adatokkal a Marketo-alkalmazást.
   
    b. Kattintson a **rendszergazdai** gombot a felső navigációs ablaktáblán.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Az integrációs menüben keresse meg és kattintson a **Munchkin hivatkozás**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Másolja a képernyőn megjelenő Munchkin azonosítója, és a válasz URL-cím az Azure AD konfigurációs varázsló befejezéséhez.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Az alkalmazás az egyszeri bejelentkezés konfigurálásához kövesse az alábbi lépéseket:
   
    a. Jelentkezzen be rendszergazdai hitelesítő adatokkal a Marketo-alkalmazást.
   
    b. Kattintson a **rendszergazdai** gombot a felső navigációs ablaktáblán.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Az integrációs menüben keresse meg és kattintson **az egyszeri bejelentkezést**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Az SAML-beállítások engedélyezéséhez kattintson **szerkesztése** gombra.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Engedélyezett** egyszeri bejelentkezés beállításai.
   
    f. Illessze be a **az Azure AD-azonosító**, a a **kibocsátó azonosító** szövegmezőbe.
   
    g. Az a **Entitásazonosító** szövegmezőbe írja be az URL-cím `http://saml.marketo.com/sp`.
   
    h. Válassza ki a felhasználói azonosító helye legyen **Névazonosító elem**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Ha a felhasználói azonosító nem egyszerű felhasználónevet, majd módosítsa az értéket az attribútumnak lapon.
   
    i. Töltse fel a tanúsítványt, amely már letöltötte az Azure AD-konfigurációs varázsló. **Mentés** a beállításokat.
   
    j. Az átirányítási hibalapok beállításainak szerkesztése.
   
    k. Illessze be a **bejelentkezési URL-cím** a a **bejelentkezési URL-cím** szövegmezőbe.
   
    l. Illessze be a **kijelentkezési URL-címe** a a **kijelentkezési URL-címe** szövegmezőbe.
   
    m. Az a **hiba URL-cím**, másolása a **Marketo-példány URL-címe** kattintson **mentése** gombra kattintva mentse a beállításokat.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_10.png)

3. A felhasználók számára az egyszeri bejelentkezés engedélyezéséhez hajtsa végre a következő műveleteket:
   
    a. Jelentkezzen be rendszergazdai hitelesítő adatokkal a Marketo-alkalmazást.
   
    b. Kattintson a **rendszergazdai** gombot a felső navigációs ablaktáblán.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Keresse meg a **biztonsági** menüben, majd kattintson **bejelentkezés beállításai**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Ellenőrizze a **megkövetelése SSO** lehetőséget és **mentése** a beállításokat.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_14.png)

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

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a marketo eszközhöz Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Marketo**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Marketo**.

    ![A Marketo-hivatkozás alkalmazásainak listájában](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-marketo-test-user"></a>A Marketo tesztfelhasználó létrehozása

Ebben a szakaszban egy a marketo rendszerében Britta Simon nevű felhasználó hoz létre. kövesse az alábbi lépéseket egy felhasználó létrehozásához a Marketo-platformon.

1. Jelentkezzen be rendszergazdai hitelesítő adatokkal a Marketo-alkalmazást.

2. Kattintson a **rendszergazdai** gombot a felső navigációs ablaktáblán.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Keresse meg a **biztonsági** menüben, majd kattintson **felhasználók és szerepkörök**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Kattintson a **új felhasználó meghívása** hivatkozásra a felhasználók lapon
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. Az új felhasználó meghívása varázslóban adja meg a következő információkat
   
    a. Adja meg a felhasználó **E-mail** címét a szövegmezőben
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Adja meg a **Utónév** be a szövegmezőbe
   
    c. Adja meg a **Vezetéknév** be a szövegmezőbe
   
    d. Kattintson a **Tovább** gombra

6. Az a **engedélyek** lapon jelölje be a **userRoles** kattintson **tovább**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kattintson a **küldése** gombra kattintva a felhasználó meghívó küldése
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Felhasználó az e-mailben értesítést kap, és kattintson a hivatkozásra, és módosítsa a jelszót a fiók aktiválásához. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Marketo csempére kattint, meg kell kell automatikusan bejelentkezett a Marketo, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


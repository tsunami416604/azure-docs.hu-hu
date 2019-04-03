---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező felismerése |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés felismerése és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: bd772a10cd64b4198e994fdefa671444447c8a53
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849553"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező felismerése

Ebben az oktatóanyagban elsajátíthatja, hogyan felismerése integrálása az Azure Active Directory (Azure AD).
Felismerése integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá felismerése Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve felismerése (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása felismerése, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Egyszeri bejelentkezés engedélyezve előfizetés felismerése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Ismeri fel az támogatott **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-recognize-from-the-gallery"></a>Felismerése hozzáadása a katalógusból

Konfigurálása az Azure AD integrálása a felismerése, hozzá kell felismerése a galériából a felügyelt SaaS-alkalmazások listájára.

**Felismerése hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **felismerése**, jelölje be **felismerése** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában felismerése](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az nevű tesztfelhasználó alapján felismerése **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó felismerése hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az felismerése tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Ismeri fel a egyszeri bejelentkezés konfigurálása](#configure-recognize-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre felismerése tesztfelhasználót](#create-recognize-test-user)**  - a-megfelelője a Britta Simon szerepel, amely kapcsolódik az Azure AD felhasználói ábrázolása felismerése.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés felismerése, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **felismerése** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszt, ha rendelkezik **szolgáltató metaadatait tartalmazó fájl**, hajtsa végre az alábbi lépéseket:

    >[!NOTE]
    >Megjelenik a **szolgáltató metaadatait tartalmazó fájl** a a **konfigurálása felismerni egyszeri bejelentkezés** az oktatóanyag szakaszában.

    a. Kattintson a **metaadatfájl feltöltése**.

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltése után a **azonosító** érték első alapszintű SAML-konfigurációja szakasz automatikusan.

    ![Ismeri fel a tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

     Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Ha a **azonosító** értéke nem kérdezhető le automatikusan, nyissa meg a Service Provider metaadatok URL-címe az egyszeri bejelentkezési beállításainak szakaszban később a kifejtett az azonosító értéket kap a **ismeri fel egyetlen konfigurálása Bejelentkezés** az oktatóanyag szakaszában. A bejelentkezési URL-érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [ügyfél ismeri fel a támogatási csapat](mailto:support@recognizeapp.com) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **felismerése beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-recognize-single-sign-on"></a>Konfigurálása egyszeri bejelentkezéshez ismeri fel

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a felismerése bérlő.

2. Kattintson a jobb felső sarokban **menü**. Lépjen a **vállalati rendszergazdai**.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/recognize-tutorial/tutorial_recognize_000.png)

3. A bal oldali navigációs panelén kattintson **beállítások**.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Hajtsa végre a következő lépéseket **egyszeri bejelentkezési beállításainak** szakaszban.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Mint **SSO engedélyezése**válassza **ON**.

    b. Az a **Identitásszolgáltató Entitásazonosító** szövegmező, illessze be az értéket a **az Azure AD-azonosító** Azure Portalról másolt.
    
    c. Az a **Sso cél URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.
    
    d. Az a **Slo-cél URL-cím** szövegmezőjébe illessze be az értéket, **kijelentkezési URL-címe** Azure Portalról másolt. 
    
    e. Nyissa meg a letöltött **tanúsítvány (Base64)** fájlt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **tanúsítvány** szövegmezőbe.
    
    f. Kattintson a **beállítások mentése** gombra. 

5. Mellett a **egyszeri bejelentkezési beállításainak** területén másolja az URL-CÍMÉT a **Service Provider metaadatok URL-címe**.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Nyissa meg a **metaadatok URL-Címhivatkozás** egy üres böngészőt, és töltse le a metaadat-dokumentum alapján. Ezután a EntityDescriptor value(entityID) átmásolhatja a fájlt, és illessze be a **azonosító** szövegmezőjébe **alapszintű SAML-konfigurációja** az Azure Portalon.
    
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/recognize-tutorial/tutorial_recognize_004.png)

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés felismerése Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **felismerése**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **felismerése**.

    ![Az alkalmazások listáját a felismerése hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-recognize-test-user"></a>Felismerése tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók felismerése szolgáltatásba való bejelentkezéshez, akkor ki kell építeni felismerése be. Felismerése, esetén kiépítése a manuális feladat.

Ez az alkalmazás nem támogatja az SCIM-kiépítés, de egy másik felhasználó szinkronizálási felhasználókat kiépítő rendelkezik. 

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a felismerése vállalati hely rendszergazdaként.

2. Kattintson a jobb felső sarokban **menü**. Lépjen a **vállalati rendszergazdai**.

3. A bal oldali navigációs panelén kattintson **beállítások**.

4. Hajtsa végre a következő lépéseket **felhasználói szinkronizálási** szakaszban.
   
    ![Új felhasználó](./media/recognize-tutorial/tutorial_recognize_005.png "új felhasználó")
   
    a. Mint **-szinkronizálást engedélyező**válassza **ON**.
   
    b. Mint **válasszon szinkronizálási szolgáltató**válassza **Microsoft / Office 365**.
   
    c. Kattintson a **felhasználói Sync futtatásával**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a felismerése csempére kattint, meg kell kell automatikusan bejelentkezett a felismerése, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


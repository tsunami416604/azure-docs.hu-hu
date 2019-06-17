---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Veracode |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Veracode között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: jeedes
ms.openlocfilehash: 1024f0c09d6ab67b412a8f65d8964f3ac9d43254
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087785"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Veracode

Ebben az oktatóanyagban elsajátíthatja, hogyan Veracode integrálása az Azure Active Directory (Azure AD).
Veracode integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Veracode Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Veracode (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Veracode az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Veracode egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Veracode **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

* Támogatja a Veracode **igény szerinti** felhasználók átadása

## <a name="adding-veracode-from-the-gallery"></a>Veracode hozzáadása a katalógusból

Az Azure AD integrálása a Veracode konfigurálásához hozzá kell Veracode a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Veracode hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Veracode**válassza **Veracode** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Veracode](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Veracode nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Veracode hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Veracode tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Veracode egyszeri bejelentkezés konfigurálása](#configure-veracode-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Veracode tesztfelhasználót](#create-veracode-test-user)**  – egy megfelelője a Britta Simon Veracode, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Veracode, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Veracode** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, a felhasználónak nincs bármely lépése végrehajtani, mert az alkalmazás már előre integrálva van az Azure-ral.

    ![Veracode tartomány és URL-címeket egyetlen bejelentkezési adatait](common/preintegrated.png)

5. Veracode alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

6. Emellett a fentiekben Veracode alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. A a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:

    | Name (Név) | Adatforrás-attribútum|
    | ---------------| --------------- |
    | Keresztnév |User.givenname |
    | Vezetéknév |User.surname |
    | email |User.mail |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

8. Az a **Veracode beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-veracode-single-sign-on"></a>Veracode egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Veracode vállalati hely rendszergazdaként.

2. A felső menüben kattintson **beállítások**, és kattintson a **rendszergazdai**.
   
    ![Felügyeleti](./media/veracode-tutorial/ic802911.png "felügyelete")

3. Kattintson a **SAML** fülre.

4. Az a **szervezet SAML-beállítások** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Felügyeleti](./media/veracode-tutorial/ic802912.png "felügyelete")
   
    a.  A **kibocsátó** szövegmezőjébe illessze be az értéket, **az Azure AD-azonosító** Azure Portalról másolt.
    
    b. Az Azure Portalról letöltött tanúsítvány feltöltéséhez kattintson **fájl kiválasztása**.
   
    c. Válassza ki **önkiszolgáló regisztráció engedélyezése**.

1. Az a **önkiszolgáló regisztrációs beállításaira** szakaszt, hajtsa végre az alábbi lépéseket, és kattintson a **mentése**:
   
    ![Felügyeleti](./media/veracode-tutorial/ic802913.png "felügyelete")
   
    a. Mint **új felhasználó az aktiválás**válassza **nincs szükség aktiválásra**.
   
    b. Mint **felhasználói adatok frissítése**válassza **szabályozó Veracode felhasználói adatok**.
   
    c. A **SAML attribútum részletei**, jelölje be az alábbiakat:
      * **Felhasználói szerepkörök**
      * **Csoportházirendet felügyelő rendszergazda**
      * **Felülvizsgáló**
      * **Biztonsági vezető**
      * **Executive**
      * **Submitter**
      * **Létrehozója**
      * **Az összes vizsgálat típusa**
      * **A csoporttagságot**
      * **Alapértelmezett csoport**

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Veracode Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Veracode**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Veracode**.

    ![Az alkalmazások listáját a Veracode hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-veracode-test-user"></a>Veracode tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Veracode szolgáltatásba való bejelentkezéshez, akkor ki kell építeni Veracode be. Veracode, esetén egy automatizált tevékenység kiépítése. Nincs művelet elem az Ön számára. Felhasználók automatikusan jönnek létre szükség esetén az első egyszeri bejelentkezési kísérlet során.

> [!NOTE]
> Eszközt is használhat bármilyen más Veracode felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése Veracode által biztosított API-k.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Veracode csempére kattint, meg kell lehet automatikusan bejelentkezett a Veracode, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


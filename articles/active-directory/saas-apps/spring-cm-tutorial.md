---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező SpringCM |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és SpringCM között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04-08-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49f63fe3b006b704291da1613627019e5866cd32
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500090"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező SpringCM

Ebben az oktatóanyagban elsajátíthatja, hogyan SpringCM integrálása az Azure Active Directory (Azure AD).
SpringCM integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá SpringCM Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve SpringCM (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

SpringCM az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* SpringCM egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a SpringCM **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-springcm-from-the-gallery"></a>SpringCM hozzáadása a katalógusból

Az Azure AD integrálása a SpringCM konfigurálásához hozzá kell SpringCM a katalógusból a felügyelt SaaS-alkalmazások listájára.

**SpringCM hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen, kattintson a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SpringCM**, jelölje be **SpringCM** az eredmény panelen, majd kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában SpringCM](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SpringCM nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó SpringCM hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az SpringCM tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[SpringCM egyszeri bejelentkezés konfigurálása](#configure-springcm-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre SpringCM tesztfelhasználót](#create-springcm-test-user)**  – egy megfelelője a Britta Simon SpringCM, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés SpringCM, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **SpringCM** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SpringCM tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [SpringCM ügyfél-támogatási csapatának](https://knowledge.springcm.com/support) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **(Raw)tanúsítvány** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificateraw.png)

6. Az a **SpringCM beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-springcm-single-sign-on"></a>SpringCM egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be a **SpringCM** rendszergazdaként a vállalati webhely.

1. A felső menüben kattintson **Ugrás**, kattintson a **beállítások**, majd a **fiók beállítások** területén kattintson **SAML SSO**.
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. Az Identity Provider konfigurációs szakaszban hajtsa végre az alábbi lépéseket:
   
    ![Identitás-szolgáltató konfigurációjának](./media/spring-cm-tutorial/ic797052.png "identitás szolgáltató konfigurációja")
    
    a. A letöltött Azure Active Directory-tanúsítvány feltöltéséhez kattintson **válassza ki a tanúsítványt kibocsátó** vagy **kibocsátói tanúsítvány módosítása**.
    
    b. Az a **kibocsátó** szövegmezőjébe illessze be **az Azure AD-azonosító** érték, amely az Azure Portalról másolta.
    
    c. Az a **Service Provider (SP) kezdeményezett végpont** szövegmezőjébe illessze be **bejelentkezési URL-cím** érték, amely az Azure Portalról másolta.
            
    d. Válassza ki **SAML engedélyezett** , **engedélyezése**.

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
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SpringCM Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SpringCM**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **SpringCM**.

    ![Az alkalmazások listáját a SpringCM hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-springcm-test-user"></a>SpringCM tesztfelhasználó létrehozása

Ahhoz, hogy az Azure Active Directory-felhasználók a SpringCM bejelentkezni, akkor ki kell építeni SpringCM be. SpringCM, esetén kiépítése a manuális feladat.

> [!NOTE]
> További információkért lásd: [hozhat létre és módosíthat egy SpringCM felhasználó](https://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Üzembe helyez egy felhasználói fiókot SpringCM, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **SpringCM** rendszergazdaként a vállalati webhely.

1. Kattintson a **GOTO**, és kattintson a **CÍMJEGYZÉK**.
   
    ![Felhasználó létrehozása](./media/spring-cm-tutorial/ic797054.png "felhasználó létrehozása")

1. Kattintson a **felhasználó létrehozása**.

1. Válassza ki a **felhasználói szerepkör**.

1. Válassza ki **aktiválási e-mailt**.

1. Írja be az Utónév, Vezetéknév és egy érvényes Azure Active Directory felhasználói fiók, a kapcsolódó szövegmezőkben kiépítendő e-mail-címe.

1. Adja hozzá a felhasználót egy **biztonsági csoport**.

1. Kattintson a **Save** (Mentés) gombra.

   > [!NOTE]
   > Bármely más SpringCM felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését SpringCM által biztosított API-k.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SpringCM csempére kattint, meg kell lehet automatikusan bejelentkezett a SpringCM, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


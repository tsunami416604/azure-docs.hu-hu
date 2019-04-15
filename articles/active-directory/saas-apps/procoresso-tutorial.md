---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Procore SSO-val |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Procore egyszeri bejelentkezés és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4be92cae0042da7341b716a6c3c497b6248eed6a
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563644"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Oktatóanyag: Az Azure Active Directory-integráció Procore SSO-val

Ebben az oktatóanyagban elsajátíthatja, hogyan Procore SSO integrálása az Azure Active Directory (Azure AD).
Procore SSO integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Procore egyszeri bejelentkezés az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve Procore egyszeri bejelentkezés (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Procore SSO-val, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Procore SSO egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Procore egyszeri Bejelentkezést támogatja **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-procore-sso-from-the-gallery"></a>Procore SSO hozzáadása a katalógusból

Procore SSO integrálása az Azure AD beállítása, hozzá kell Procore SSO a galériából a felügyelt SaaS-alkalmazások listájára.

**Procore SSO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Procore SSO**, jelölje be **Procore SSO** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában procore egyszeri bejelentkezés](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó Procore SSO-val **Britta Simon**.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználót és a kapcsolódó felhasználó Procore egyszeri bejelentkezési hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés Procore SSO-val tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Procore SSO egyszeri bejelentkezés konfigurálása](#configure-procore-sso-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Procore SSO tesztfelhasználót](#create-procore-sso-test-user)**  – egy megfelelője a Britta Simon Procore SSO, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Procore SSO-val, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Procore SSO** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, a felhasználónak nincs bármely lépése végrehajtani, mert az alkalmazás már előre integrálva van az Azure-ral.

    ![Procore SSO-tartomány és URL-címek egyszeri bejelentkezési adatait](common/preintegrated.png)

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **Procore egyszeri bejelentkezés beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-procore-sso-single-sign-on"></a>Procore SSO egyszeri bejelentkezés konfigurálása

1. Az egyszeri bejelentkezés konfigurálása **Procore SSO** oldalán, jelentkezzen be rendszergazdaként procore vállalat webhelye.

2. Az eszközkészlet legördülő lefelé, kattintson a **rendszergazdai** az egyszeri bejelentkezés beállításai lap megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/procore_tool_admin.png)

3. Illessze be az értékeket a mezőkben, amint az alábbi-

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Az a **egyszeri bejelentkezést a kibocsátó URL-cím** szöveg mezőbe illessze be az értékét **az Azure AD-azonosító** az Azure Portalról másolt.

    b. Az a **SAML bejelentkezési cél URL-cím** mezőbe illessze be az értékét **bejelentkezési URL-cím** az Azure Portalról másolt.

    c. Ezután nyissa meg a **összevonási metaadatainak XML** az Azure Portalról a fent letöltött és másolja a tanúsítványt a kódban nevű **X509Certificate**. Illessze be a másolt érték a **az egyszeri bejelentkezést x509 tanúsítvány** mezőbe.

4. Kattintson a **Save Changes** gombra.

5. Ezek a beállítások után meg kell küldenie a a **tartománynév** (például: **contoso.com**) keresztül, amely jelentkezik be a Procore a [Procore támogatási csapatának](https://support.procore.com/) és csatlakoznak az aktiválja az összevont egyszeri bejelentkezés ahhoz a tartományhoz.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Procore SSO Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Procore SSO**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Procore SSO**.

    ![Az alkalmazások listáját a Procore egyszeri bejelentkezési hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-procore-sso-test-user"></a>Procore SSO tesztfelhasználó létrehozása

Kérjük, kövesse az alábbi lépéseket Procore tesztfelhasználó létrehozása Procore egyszeri bejelentkezési oldalán.

1. Jelentkezzen be rendszergazdaként a procore vállalati webhely.    

2. Az eszközkészlet legördülő lefelé, kattintson a **Directory** a vállalati címtár lap megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Kattintson a **adjon hozzá egy személy** nyissa meg a képernyőt, és adja meg a beállítás hajtsa végre a következő beállítások -

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_user_add.png)

    a. Az a **Utónév** szövegmezőbe írja be a felhasználó első neve például **Britta**.

    b. Az a **Vezetéknév** szövegmezőbe írja be a felhasználó vezetékneve például **Simon**.

    c. Az a **E-mail cím** szövegmezőbe írja be a felhasználó e-mail címét, például BrittaSimon@contoso.com.

    d. Válassza ki **jogosultsági sablon** , **jogosultsági sablon alkalmazása később**.

    e. Kattintson a **Create** (Létrehozás) gombra.

4. Ellenőrizze, és az újonnan hozzáadott kapcsolattartó adatainak frissítése.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_user_check.png)

5. Kattintson a **mentés és küldés meghívó** (ha szükséges mail keresztül) vagy **mentése** (mentse közvetlenül) a felhasználói regisztráció befejezéséhez.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Procore SSO csempére kattint, meg kell kell automatikusan bejelentkezett a Procore SSO, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


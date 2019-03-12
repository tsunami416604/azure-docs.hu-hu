---
title: 'Oktatóanyag: Az Azure Active Directory-integráció lejárati emlékeztetővel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a lejárati emlékeztető között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3df3d73e-80a4-4b35-bd3d-90bc4f6f6afd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c9f4129df81f4660f57db4a1f40b9a9afc2f9dd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763928"
---
# <a name="tutorial-azure-active-directory-integration-with-expiration-reminder"></a>Oktatóanyag: Az Azure Active Directory integrációja az elévülési emlékeztető

Ebben az oktatóanyagban elsajátíthatja, hogyan lejárati emlékeztető integrálása az Azure Active Directory (Azure AD).
Lejárati emlékeztető integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a lejárati emlékeztető Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve lejárati emlékeztető (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a lejárati emlékeztető, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Emlékeztető lejárati egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a lejárati emlékeztető **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-expiration-reminder-from-the-gallery"></a>Lejárati emlékeztető hozzáadása a katalógusból

Az Azure AD integrálása a lejárati emlékeztető konfigurálásához hozzá kell lejárati emlékeztető a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Lejárati emlékeztető hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **lejárati emlékeztető**válassza **lejárati emlékeztető** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában lejárati emlékeztető](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó lejárati emlékeztetővel **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó a lejárati emlékeztető hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés lejárati emlékeztetővel tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Emlékeztető lejárati egyszeri bejelentkezés konfigurálása](#configure-expiration-reminder-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre lejárati emlékeztető tesztfelhasználót](#create-expiration-reminder-test-user)**  - a-megfelelője a Britta Simon szerepel lejárati emlékeztetőt, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés lejárati emlékeztető, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **lejárati emlékeztető** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Lejárati emlékeztető tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe:  `https://app.expirationreminder.net/account/sso`

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **(Raw)tanúsítvány** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificateraw.png)

6. Az a **lejárati Emlékeztető beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-expiration-reminder-single-sign-on"></a>Emlékeztető lejárati egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **lejárati emlékeztető** oldalon kell küldenie a letöltött **tanúsítvány (Raw)** és az Azure Portalról másolt URL-címek megfelelő [lejárati emlékeztető támogatási csoport](mailto:support@expirationreminder.net). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

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

Ebben a szakaszban engedélyezze Britta Simon lejárati azt, hogy a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **lejárati emlékeztető**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **lejárati emlékeztető**.

    ![Az alkalmazások listáját a lejárati emlékeztető hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-expiration-reminder-test-user"></a>Lejárati emlékeztető tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű lejárati emlékeztető a felhasználó hoz létre. Együttműködve [lejárati emlékeztető támogatási csapatának](mailto:support@expirationreminder.net) a felhasználók hozzáadása az elévülési emlékeztető platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a lejárati emlékeztető csempére kattint, akkor kell automatikusan megtörténik a, hogy a lejárati, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

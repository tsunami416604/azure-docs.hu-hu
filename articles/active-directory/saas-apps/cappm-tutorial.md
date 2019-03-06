---
title: 'Oktatóanyag: Az Azure Active Directoryval integrált CA PPM |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a hitelesítésszolgáltató PPM között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca9d5e71-e429-4891-8d10-3498e7210e89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11d7287ca87add611e7fcb6e24491c6c9321f3ad
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441627"
---
# <a name="tutorial-azure-active-directory-integration-with-ca-ppm"></a>Oktatóanyag: A hitelesítésszolgáltató PPM az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan hitelesítésszolgáltató PPM integrálása az Azure Active Directory (Azure AD).
Hitelesítésszolgáltató PPM integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Hitelesítésszolgáltatói PPM Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett a hitelesítésszolgáltató PPM (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a hitelesítésszolgáltató PPM, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Hitelesítésszolgáltató PPM egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a hitelesítésszolgáltató PPM **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-ca-ppm-from-the-gallery"></a>Hitelesítésszolgáltató PPM hozzáadása a katalógusból

Az Azure AD integrálása a hitelesítésszolgáltató PPM konfigurálásához hozzá kell hitelesítésszolgáltató PPM a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Hitelesítésszolgáltató PPM hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **hitelesítésszolgáltató PPM**, jelölje be **hitelesítésszolgáltató PPM** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Hitelesítésszolgáltató PPM a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a hitelesítésszolgáltató PPM nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó a CA PPM hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a hitelesítésszolgáltató PPM tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hitelesítésszolgáltató PPM egyszeri bejelentkezés konfigurálása](#configure-ca-ppm-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Hitelesítésszolgáltatót PPM tesztfelhasználót](#create-ca-ppm-test-user)**  – egy megfelelője a Britta Simon hitelesítésszolgáltató PPM, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

A hitelesítésszolgáltató PPM konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **hitelesítésszolgáltató PPM** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon, a következő lépésekkel:

    ![Hitelesítésszolgáltató PPM tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://ca.ondemand.saml.20.post.<companyname>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be, mint: `https://fedsso.ondemand.ca.com/affwebservices/public/saml2assertionconsumer`

    > [!NOTE]
    > Az érték nem valódi. Ez az érték frissítse a tényleges azonosítója. Kapcsolattartó [hitelesítésszolgáltató PPM ügyfél-támogatási csapatának](mailto:catechnicalsupport@ca.com) lekérni ezt az értéket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **állítsa be a hitelesítésszolgáltató PPM** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-ca-ppm-single-sign-on"></a>Hitelesítésszolgáltató PPM egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **hitelesítésszolgáltató PPM** oldalon kell küldenie a letöltött **tanúsítvány (Base64)** és az Azure Portalról másolt URL-címek megfelelő [hitelesítésszolgáltató PPM támogatási csapatának](mailto:catechnicalsupport@ca.com) . Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés hitelesítésszolgáltató PPM Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **hitelesítésszolgáltató PPM**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **hitelesítésszolgáltató PPM**.

    ![Az alkalmazások listáját a hitelesítésszolgáltató PPM hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-ca-ppm-test-user"></a>Hitelesítésszolgáltató PPM tesztfelhasználó létrehozása

Ebben a szakaszban egy, CA PPM Britta Simon nevű felhasználó hoz létre. Együttműködve [hitelesítésszolgáltató PPM támogatási csapatának](mailto:catechnicalsupport@ca.com) a felhasználók hozzáadása a CA PPM platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a hitelesítésszolgáltató PPM csempére kattint, meg kell kell automatikusan bejelentkezett a hitelesítésszolgáltató PPM, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
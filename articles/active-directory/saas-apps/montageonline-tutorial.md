---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Montázs Online-nal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Montázs Online között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c5e8c6f-e4fb-43fe-8841-e371f568ebed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 0d27490fc1748b03bb056c8c0d66ddfa9832740e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761668"
---
# <a name="tutorial-azure-active-directory-integration-with-montage-online"></a>Oktatóanyag: Az Azure Active Directory-integráció Montázs Online-nal

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja Montázs Online az Azure Active Directoryval (Azure AD).
Az Azure AD integrálása Montázs Online nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Montázs Online az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett Montázs online (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Montázs Online-nal, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Montázs Online egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Online támogatja Montázs **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-montage-online-from-the-gallery"></a>A katalógusból Montázs Online hozzáadása

Az Azure AD-be az Online Montázs integráció konfigurálásához, hozzá kell Montázs Online a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Montázs Online a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Montázs Online**válassza **Montázs Online** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Online Montázs](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Montázs Online nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Montázs online hivatkozás kapcsolata kell létrehozni.

Konfigurálása és az Azure AD egyszeri bejelentkezés Montázs online-nal való teszteléséhez hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Montázs Online egyszeri bejelentkezés konfigurálása](#configure-montage-online-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Montázs Online tesztfelhasználót](#create-montage-online-test-user)**  – egy megfelelője a Britta Simon Montázs Online, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Montázs Online-nal, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Montázs Online** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Montázs Online tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe:

    Éles környezetben: `https://<subdomain>.montageonline.co.nz/`

    Tesztelési környezetben: `https://build-<subdomain>.montageonline.co.nz/`

    b. Az a **azonosító** szövegmezőbe írja be egy URL-címe:

    Éles környezetben: `MOL_Azure`

    Tesztelési környezetben: `MOL_Azure_Build`

    > [!NOTE] 
    > A bejelentkezési URL-érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Montázs Online ügyfél-támogatási csapatának](https://www.montage.co.nz/contact-us/) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Montázs Online beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-montage-online-single-sign-on"></a>Montázs Online egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **Montázs Online** oldalon kell küldenie a letöltött **tanúsítvány (Base64)** és az Azure Portalról másolt URL-címek megfelelő [Montázs Online támogatási csoport ](https://www.montage.co.nz/contact-us/). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

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

Ebben a szakaszban engedélyezze Britta Simon Montázs online-hoz való hozzáférés biztosításával az Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Montázs Online**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Montázs Online**.

    ![Az alkalmazások listáját a Montázs Online hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-montage-online-test-user"></a>Online Montázs tesztfelhasználó létrehozása

Ebben a szakaszban egy Montázs online Britta Simon nevű felhasználó hoz létre. Együttműködve [Montázs Online támogatási csoport](https://www.montage.co.nz/contact-us/) a felhasználók hozzáadása az Online Montázs platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Montázs Online csempére kattint, akkor kell automatikusan megtörténik a a Montázs online, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


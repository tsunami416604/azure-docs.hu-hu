---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az & frankly |} A Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory közötti és & frankly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d702060-1b89-4e9d-9f01-ede4f1171c73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c244b9ea85368744c7848d498df8cf10e9fe990
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407561"
---
# <a name="tutorial-azure-active-directory-integration-with-frankly"></a>Oktatóanyag: Az Azure Active Directory-integráció az & frankly

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a & frankly az Azure Active Directoryval (Azure AD).
Integrálás & frankly az Azure ad-vel biztosít a következő előnyökkel jár:

* Szabályozhatja, aki hozzáféréssel rendelkezik a & frankly Azure AD-ben.
* Engedélyezheti a felhasználók automatikusan bejelentkezett a & frankly (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció & frankly, akkor kell a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* & frankly egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* & frankly támogatja **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-frankly-from-the-gallery"></a>Hozzáadás & frankly a katalógusból

Konfigurálja az integrációt a & frankly az Azure AD-be, akkor hozzá kell adnia & frankly a felügyelt SaaS-alkalmazások listájára a katalógusból.

**Adjon hozzá & frankly katalógusból, hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **& frankly**válassza **& frankly** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![& frankly a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés az & frankly alapján nevű tesztfelhasználó **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolat & frankly kell hozható létre.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés az & frankly, meg kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja és frankly egyszeri bejelentkezés](#configure-frankly-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[& Frankly tesztfelhasználó létrehozása](#create-frankly-test-user)**  szeretné, hogy egy megfelelője a Britta Simon – a felhasználó Azure ad-ben ábrázolása a & frankly legyen társított.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálhatja az Azure AD egyszeri bejelentkezés az & frankly, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **& frankly** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![& frankly tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/metadata.php/<tenant id>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/saml2-acs.php/<tenant id>`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![& frankly tartomány és URL-címek egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://andfrankly.com/saml/okta/?saml_sso=<tenant id>`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címet. Kapcsolattartó [& frankly ügyfél-támogatási csapatának](mailto:help@andfrankly.com) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

7. Az a **akár & frankly beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-frankly-single-sign-on"></a>Konfigurálja és frankly egyszeri bejelentkezés

Az egyszeri bejelentkezés konfigurálása **& frankly** oldalon kell küldenie a letöltött **összevonási metaadatainak XML** és az Azure Portalról másolt URL-címek megfelelő [& frankly támogatási csoport ](mailto:help@andfrankly.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

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

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a & frankly Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **& frankly**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **& frankly**.

    ![A & frankly hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-frankly-test-user"></a>& Frankly tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű frankly és a felhasználó hoz létre. Együttműködve [& frankly támogatási csoportjának](mailto:help@andfrankly.com) tartozó felhasználók hozzáadása a & frankly platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Kattintva & frankly csempére a hozzáférési panelen, meg kell automatikusan jelentkeznie & frankly, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
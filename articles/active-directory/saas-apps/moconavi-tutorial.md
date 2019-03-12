---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező moconavi |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és moconavi között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e1916224-e1c2-426f-b233-0a2518fa41db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: e63dbdd6a2556768cea55293b618975522e15cbb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57781493"
---
# <a name="tutorial-azure-active-directory-integration-with-moconavi"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező moconavi

Ebben az oktatóanyagban elsajátíthatja, hogyan moconavi integrálása az Azure Active Directory (Azure AD).
Moconavi integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá moconavi Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve moconavi (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Moconavi az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* moconavi egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* támogatja a moconavi **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-moconavi-from-the-gallery"></a>Moconavi hozzáadása a katalógusból

Az Azure AD integrálása a moconavi konfigurálásához hozzá kell moconavi a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Moconavi hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **moconavi**válassza **moconavi** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![az eredmények listájában moconavi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az moconavi nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó moconavi hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az moconavi tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Egyszeri bejelentkezés moconavi konfigurálása](#configure-moconavi-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre moconavi tesztfelhasználót](#create-moconavi-test-user)**  – egy megfelelője a Britta Simon a felhasználó Azure ad-ben reprezentációja kapcsolódó moconavi rendelkeznie.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés moconavi, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **moconavi** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![moconavi tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<yourserverurl>/moconavi-saml2/saml/login`

    b. Az a **azonosító** mezőbe írja be a következő minta használatával URL-cím: `https://<yourserverurl>/moconavi-saml2`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<yourserverurl>/moconavi-saml2/saml/SSO`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [moconavi ügyfél-támogatási csapatának](mailto:support@recomot.co.jp) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **moconavi beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-moconavi-single-sign-on"></a>Moconavi egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **moconavi** oldalon kell küldenie a letöltött **összevonási metaadatainak XML** és az Azure Portalról másolt URL-címek megfelelő [moconavi támogatási csapatának](mailto:support@recomot.co.jp). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

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

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés moconavi való hozzáférést.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **moconavi**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **moconavi**.

    ![Az alkalmazások listáját a moconavi hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-moconavi-test-user"></a>Moconavi tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű moconavi hoz létre. Együttműködve [moconavi támogatási csapatának](mailto:support@recomot.co.jp) a felhasználók hozzáadása az moconavi platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

1. Moconavi telepítse a Microsoft store-ból.

2. Indítsa el a moconavi.

3. Kattintson a **beállítás csatlakozás** gombra.

    ![Egyszeri bejelentkezés tesztelése](./media/moconavi-tutorial/testing1.png)

4. Adja meg `https://mcs-admin.moconavi.biz/gateway` be **csatlakozhat az URL-cím** szövegmezőbe, és kattintson a **kész** gombra.

    ![Egyszeri bejelentkezés tesztelése](./media/moconavi-tutorial/testing2.png)

5. Az alábbi képernyőfelvételen látható hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés tesztelése](./media/moconavi-tutorial/testing3.png)

    a. Adja meg **bemeneti hitelesítési kulcs**:`azureAD` be **bemeneti hitelesítési kulcs** szövegmezőbe.

    b. Adja meg **Input Felhasználóazonosító**: `your ad account` be **Input Felhasználóazonosító** szövegmezőbe.

    c. Kattintson a **bejelentkezési**.

6. Az Azure AD-jelszó a bemeneti **jelszó** szövegmezőbe, és kattintson a **bejelentkezési** gombra.

    ![Egyszeri bejelentkezés tesztelése](./media/moconavi-tutorial/testing4.png)

7. Az Azure AD-hitelesítés akkor sikeres, ha a menüben jelenik meg.

    ![Egyszeri bejelentkezés tesztelése](./media/moconavi-tutorial/testing5.png)

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


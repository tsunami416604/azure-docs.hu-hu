---
title: 'Oktatóanyag: Az Azure Active Directory-integráció SilkRoad életre Suite-tal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés SilkRoad életre Suite és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: b5aeb874f86c9283d1ff869c9963e109aeb88112
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59697328"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Oktatóanyag: Az Azure Active Directory-integráció SilkRoad életre Suite-tal

Ebben az oktatóanyagban elsajátíthatja, hogyan SilkRoad életre Suite integrálható az Azure Active Directory (Azure AD).
SilkRoad életre Suite integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá SilkRoad életre Suite Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett SilkRoad életre Suite (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az SilkRoad életre Suite, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* SilkRoad életre Suite egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a SilkRoad életre Suite **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>SilkRoad életre Suite hozzáadása a katalógusból

Az Azure AD-be SilkRoad életre Suite integrációjának konfigurálása, hozzá kell SilkRoad életre Suite a galériából a felügyelt SaaS-alkalmazások listájára.

**SilkRoad életre Suite hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SilkRoad életre Suite**, jelölje be **SilkRoad életre Suite** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában SilkRoad életre Suite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SilkRoad életre Suite nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó SilkRoad életre Suite hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az SilkRoad életre Suite tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[SilkRoad életre Suite egyszeri bejelentkezés konfigurálása](#configure-silkroad-life-suite-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre SilkRoad életre Suite tesztfelhasználót](#create-silkroad-life-suite-test-user)**  – egy megfelelője a Britta Simon SilkRoad életre csomag, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása SilkRoad életre Suite-tal, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **SilkRoad életre Suite** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszt, ha rendelkezik **szolgáltató metaadatait tartalmazó fájl**, hajtsa végre az alábbi lépéseket:

    > [!NOTE]
    > Megjelenik a **szolgáltató metaadatait tartalmazó fájl** magyarázni az oktatóanyag későbbi részében.

    a. Kattintson a **metaadatfájl feltöltése**.

    ![image](common/upload-metadata.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![image](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltését követően a **azonosító** és **válasz URL-cím** értékeket automatikusan alapszintű SAML-konfigurációja szakasz első:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Ha a **azonosító** és **válasz URL-cím** értékek nem jelennek meg az automatikus polulated, majd adja meg az értékeket manuálisan a követelmény alapján.

    d. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.silkroad-eng.com/Authentication/`

5. Az a **alapszintű SAML-konfigurációja** szakaszra, ha nem rendelkezik **szolgáltató metaadatait tartalmazó fájl**, hajtsa végre az alábbi lépéseket:

    ![SilkRoad életre Suite tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Az a **azonosító** mezőbe írja be a következő minta használatával URL-cím:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP`|
    | `https://<subdomain>.silkroad.com/Authentication/SP`|

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/`|
    | `https://<subdomain>.silkroad.com/Authentication/`|

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [SilkRoad életre Suite ügyfél-támogatási csapatának](https://www.silkroad.com/locations/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

7. Az a **SilkRoad életre Suite beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Suite SilkRoad életre egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be rendszergazdaként a SilkRoad vállalati webhely.

    > [!NOTE]
    > A Microsoft Azure AD-összevonás konfigurálása a SilkRoad életre Suite hitelesítési alkalmazáshoz való hozzáférést, forduljon SilkRoad támogatása vagy az Ön SilkRoad szolgáltatások képviselőjével.

1. Lépjen a **szolgáltató**, és kattintson a **összevonási részletek**.

    ![Az Azure AD egyszeri bejelentkezés](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Kattintson a **összevonási metaadatok letöltése**, majd mentse a metaadat-fájlt a számítógépen. Összevonási metaadatok letölteni, használja a **szolgáltató metaadatait tartalmazó fájl** a a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    ![Az Azure AD egyszeri bejelentkezés](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. Az a **SilkRoad** alkalmazást, kattintson a **hitelesítési források**.

    ![Az Azure AD egyszeri bejelentkezés](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Kattintson a **hitelesítési forrás hozzáadása**.

    ![Az Azure AD egyszeri bejelentkezés](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. Az a **hitelesítési forrás hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Az Azure AD egyszeri bejelentkezés](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. A **2. lehetőség – metaadatait tartalmazó fájl**, kattintson a **Tallózás** az Azure Portalról letöltött metaadatfájl feltöltése.
  
    b. Kattintson a **fájladatok használatával hozzon létre identitásszolgáltató**.

1. Az a **hitelesítési források** területén kattintson **szerkesztése**.

    ![Az Azure AD egyszeri bejelentkezés](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. Az a **hitelesítési forrás szerkesztése** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Az Azure AD egyszeri bejelentkezés](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Mint **engedélyezve**válassza **Igen**.

    b. Az a **EntityId** szövegmezőjébe illessze be az értéket, **az Azure AD-azonosító** Azure Portalról másolt.

    c. Az a **identitásszolgáltató leírás** szövegmezőbe írja be a konfiguráció leírása (például: *Az Azure AD egyszeri bejelentkezés*).

    d. Az a **metaadatait tartalmazó fájl** szövegmezőbe feltöltése a **metaadatok** fájlt, amely az Azure Portalról letöltött.
  
    e. Az a **identitásszolgáltató neve** szövegmezőbe írja be a konfigurációs adott nevét (például: *Azure SP*).
  
    f. Az a **kijelentkezési szolgáltatás URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezési URL-címe** Azure Portalról másolt.

    g. A a **bejelentkezési szolgáltatás URL-** szövegmezőjébe illessze be az értéket, **bejelentkezési URL-cím** Azure Portalról másolt.

    h. Kattintson a **Save** (Mentés) gombra.

1. Tiltsa le minden más hitelesítési forrás.

    ![Az Azure AD egyszeri bejelentkezés](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus `brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával SilkRoad életre Suite Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SilkRoad életre Suite**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **SilkRoad életre Suite**.

    ![Az alkalmazások listáját a SilkRoad életre Suite hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-silkroad-life-suite-test-user"></a>SilkRoad életre Suite tesztfelhasználó létrehozása

Ebben a szakaszban egy SilkRoad életre Suite Britta Simon nevű felhasználó hoz létre. Együttműködve [SilkRoad életre Suite ügyfél-támogatási csapatának](https://www.silkroad.com/locations/) a felhasználók hozzáadása az SilkRoad életre Suite platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SilkRoad életre Suite csempére kattint, akkor kell automatikusan megtörténik a az SilkRoad életre Suite, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
